
Static stability refers to a system's ability to achieve a stable state despite the world around it falling apart. Static stability has a lot of similarities to the guiding philosophy behind stoicism:

> Accept that you cannot control anything but yourself.

## In Nature

After doing some research, I found that the phrase "statically stable" originates from multiple scientific phenomena. Any function in nature that is able to return to its ground state after some "displacing" event can be considered statically stable. Animals do this all the time - just try to stop a dog from wagging it's tail, it's not going to work :). This is a really important property in fields like physics and in particular, aviation; a plane should be able to maintain its altitude and heading even after severe turbulence.
## In Software

Static stability in software refers to the same concept. A system has a number of dependencies and should be able to start independently of any of these dependencies working. That sounds a bit counter-intuitive. How can you expect a service to function if it can't even reach its database?

**You can't. And that's fine.** But if the database were to recover at any point, then the service would be able to resume handling requests normally. A dependency being down shouldn't affect the service's ability to start up and *try* to do work. That's static stability. 

This may seem pretty un-remarkable. Big deal, a service should be able to start up even if the world is on fire. However, it can mean a lot when you have a bunch of services that depend on each other - and when this dependency graph has a cycle.

![[cycle.jpg| center]]

In this situation, neither service can start up since they both depend on the other to do so. This kind of dependency cycle is really dangerous and can prevent other downstream services from recovering after an outage. If one of these services were able to stand themselves up independently of the other, then the other would detect the presence of the recovered service and recover itself. Static stability is extremely important for foundational services like DynamoDB and S3 that many other services at AWS (and outside of it) depend on. 

However, for services near the leaves of the dependency tree that have a low likelihood of being involved in a dependency cycle, static stability is not crucial. Taking time to go ahead and implement static stability in these cases might not worth the operational complexity it introduces:

1. Having to think about what your service does in the state where it starts up but can't do meaningful work because dependencies are still down.
2. Hosts in a statically stable system can generally live longer and are less likely to be replaced. If your application does caching, client experiences can become inconsistent if they are handled by different hosts that are in different "stable" states.



