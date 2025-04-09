A lot of teams at AWS, my team included, use the AWS CDK to achieve infrastructure as code. Like all abstractions, the edges are rough. The CDK is not a one-size-fit-all solution. There are lots of almost hidden shortcomings that can sneak up on teams as their use case gets more complex over time. I've documented a few of these so hopefully your team can avoid manifesting spaghetti code.

### 1. Working with networking constructs is tricky

Working with AWS EC2 networking resources like VPCs, subnets, and CIDR blocks is really hard if you're attempting anything more than a basic setup. 

For example, the first time you define a VPC, you must specify the `reservedAzs` and `maxAzs` fields as high values (like 50). If you miss this during VPC creation, then you will not be able to easily add more subnets to the same VPC when they become available.  This is tracked in an [open GitHub issue](https://github.com/aws/aws-cdk/issues/6683) that appears to be getting no traction at all at the time of writing.

```js
const vpc = new ec2.Vpc(this, 'TheVPC', { 
	ipAddresses: ec2.IpAddresses.cidr('10.0.0.0/16'), 
	reservedAzs: 50,
	maxAzs: 50
});
```

The reason for this is that these fields tell the VPC how to allocate IP address space among subnets. By default, all IP address space is allocated to the first 3 subnets in a region, not leaving space for any more. That is, unless, you specify `reservedAzs` and `maxAzs` as this tells the VPC construct to reserve IP address space for subnets that are added in the future. There's a lot of subtle problems like this that only surface deep into your architecture implementation.

### 2. Custom resources are more trouble then they're worth

Custom resources sound like a fairy tale at first. Can't meet your use-case with pre-existing AWS CDK resources? That's fine, just implement a few simple CloudFormation handlers in a Lambda, and then attach it to CustomResource. Now you can reference it like any other resource in the CDK and do exactly what you want. **WRONG!**

```js
const handler = new lambda.Function(this , 'my-handler', {
  runtime: lambda.Runtime.NODEJS_20_X,
  handler: 'index.handler',
  code: lambda.Code.fromInline(`
  exports.handler = async (event, context) => {
    return {
      if (event.RequestType == "Delete") {
	    // Delete a resource
	  } else if (event.RequestType == "Create") {
		// Create a resource
	  }
    };
  };`),
});

// Provision a custom resource provider framework
const provider = new cr.Provider(this , 'my-provider', {
  onEventHandler: handler,
});

new CustomResource(this , 'my-cr', {
  serviceToken: provider.serviceToken,
});
```

What isn't immediately obvious to a CustomResource user is that CloudFormation invokes handlers unpredictably. You have to ensure that your handlers are idempotent and only operate on the underlying resource when it is in a stable state. 

For example, if your `Create` handler creates a resource that completes initialization asynchronously, you have to make sure to wait for this to complete. Otherwise, if a `Delete` handler is invoked for the same resource in the meantime, and the asynchronous resource creation later finishes, you could be left with a resource existing when it shouldn't. This is an inconsistent resource state that is hard to reconcile through handler code changes. You can read more about how CloudFormation decides when you invoke your handlers [here](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html).

Debugging these edge cases is a nightmare and makes it impractical to manage complex CustomResources. Instead, keep CustomResource handlers simple. Each handler should ideally have nothing more than a single synchronous API call or even better, simply mutate existing resources rather than create/delete them.

### 3. Don't make your CDK a monolith

The huge advantage of using the CDK is that it allows you to apply software best practices with infrastructure development. You should be using many `Constructs` to modularize your code, and only deploy a handful of stacks.

```
A good directory structure for CDK projects.

stacks/
 frontend-infra.ts
 backend-infra.ts

constructs/
 ddb_table.ts
 worker_lambda.ts
 load_balancer.ts

Each construct is its own self-contained component, and can be re-used across any number of stacks.
```

Using only a few stacks allows you to reduce the probability of a cross-stack dependency occurring. This is a really nasty chicken/egg bug where one stack depends on a resource owned by another stack. This prevents deletion of the owning stack since doing so would invalidate the resource reference the dependent stack holds.

Further, if you reach the hard limit of 500 resources per stack, you can use nested stacks to circumvent this. A stack can contain any number of nested stacks, where each nested stack counts as only a single resource in the parent stack. So you can define *thousands* of resources per parent stack by isolating groups of resources at the nested stack level. This is great for teams that want to simplify their infrastructure by ultimately only deploying a few templates. Our team found that feature-driven nested stacks were a huge benefit; each nested stack contains all the resources associated with a certain feature in the project. Cross-stack dependencies can still happen between nested stacks, but this is less likely as each one is small.

```
An effective feature-driven organization pattern for nested stacks. 

stacks/
 frontend/
   nested/
     api_fleet.ts
     load_tests.ts
     cloudwatch_dashboards.ts
   frontend-infra.ts
```






