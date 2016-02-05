# microXchg 2016 - Day 2

## Wait, what!? Our microservices have actual human users? (Stefan Tilkov @stilkov)

- Backend for Frontend: It's not something you want, it's something you find along the way.
- UIs matter more than the services.
- Frontend could be a problem because you end up mixing pieces from different services.
- Not every service needs an interface.

Wrong assumption: "#microservices matter the most". It turns out that UI does. @stilkov opening day 2 @microxchg
Wrong assumption: "Frontend technology is an implementation detail". It's not.

Microservices backend (& frontend) platform goals:
  - As few assumptions as possible.
  - No implementation dependencies.
  - Small interface surface
  - Based on standards
  - Parallel development
  - Independent deployment (one of the distinguishing factors)
  - Autonomous operations

You should pursue small frontends loosely coupled.

According to Maciej Ceglowski (@baconmeteor), there are 2 steps to optimize web apps performance:
1.- Make sure most important elements render first.
2.- Stop there.

Assumption: "Frontend monoliths are OK". Sometimes.
Assumption: "JS-centric web apps can be as good as native appps". They shouldn't be as bad!.
"Any sufficiently complicated JS client app contains an ad hoc, informally-specified, bug-ridden, slow implementation of half a browser". So... Don't build one single page app.

Summary:
 - Few organizations are in the business of delivering APIs - UIs matter.
 - Frontend monoliths are just as good, or bad, as backend monoliths.
 - Nothing beats the browser with regards to modular frontend delivery.


## Analyzing Response Time Distributions for Microservices (Adrian Cockcroft)


## (T2) Don’t Fly Blind: Logging and Metrics in Microservice Architectures (Tammo van Lessen, Alexander Heusingfeld)



## Microservices UX: The technical journey to microservices (Russell Miles)


## Microservices <3 Domain Driven Design, why and how? (Michael Plöd)


## Cloud in your Cloud, how we build DigitalOcean (Matthew Campbell)
