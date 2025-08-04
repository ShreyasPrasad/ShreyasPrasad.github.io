
Static stability refers to a system's ability to achieve a stable state despite the world around it falling apart. Static stability has a lot of similarities to the guiding philosophy behind stoicism:

> Accept that you cannot control anything but yourself.

## In Nature

After doing some research, I found that the phrase "statically stable" originates from multiple scientific phenomena. Any function in nature that is able to return to its ground state after some "displacing" event can be considered statically stable. Animals do this all the time - just try to stop a dog from wagging it's tail, it's not going to work :). This is a really important property in fields like physics and in particular, aviation; a plane should be able to maintain its altitude and heading even after severe turbulence.
## In Software

Static stability in software refers to the same concept. A system has a number of dependencies and should be able to start independently of any of these dependencies working. That sounds a bit counter-intuitive. How can you expect a service to function if it can't even reach its database?

You can't. And that's fine. But if the database were to recover at any point, then the service would be able to resume handling requests normally. A dependency being down shouldn't affect the service's ability to start up and *try* to do work. That's static stability. 

This may seem pretty un-remarkable. Big deal, a service should be able to start up even if the world is on fire. However, it can mean a lot when you have a bunch of services that depend on each other - and when this dependency graph has a cycle.





