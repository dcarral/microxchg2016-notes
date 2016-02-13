# microXchg 2016 - Day 2 (February 5, Berlin)

Recorded talks are available at: https://www.youtube.com/channel/UCGCbB8TPtYMQmJwYVogcPjg

Below you can find some notes taken live during day 2 of the conference.

## Wait, what!? Our microservices have actual human users? (Stefan Tilkov @stilkov)

- Backend for Frontend: It's not something you want, it's something you find along the way.
- UIs matter more than the services.
- Frontend could be a problem because you end up mixing pieces from different services.
- Not every service needs an interface.

Wrong assumptions:
- "#microservices matter the most". It turns out that UI does.
- "Front-end technology is an implementation detail". It's not.

Microservices platforms goals (for both back and front-end):
  - As few assumptions as possible
  - No implementation dependencies
  - Small interface surface
  - Based on standards
  - Parallel development
  - **Independent deployment**
  - Autonomous operations

You should pursue loosely coupled small frontends.

According to a funny tweet from Maciej Ceglowski (@baconmeteor), there are 2 steps to optimize web apps performance:

1. Make sure most important elements render first.
2. Stop there.

More wrong assumptions:
- "Front-end monoliths are OK". Sometimes.
- "JS-centric web apps can be as good as native appps". At least they shouldn't be as bad!

**"Any sufficiently complicated JS client application contains an ad hoc, informally-specified, bug-ridden, slow implementation of half a browser".** So... Don't build one single page app.

Summary:
 - Few organizations are in the business of delivering APIs
 - UIs matter
 - Front-end monoliths are just as good, or bad, as back-end monoliths
 - Nothing beats the browser with regards to modular front-end delivery


## Analyzing Response Time Distributions for Microservices (Adrian Cockcroft @adrianco)

A microservice architecture is much more challenging than just a large number of machines.
Some tools can show the request flow across a few services.
Interesting architectures have __a lot__ of microservices! Flow visualization is a big challenge.

[spigo](github.com/adrianco/spigo) simulates protocol interactions in Go & visualizes them with D3.

Open Zipkin is a common format for trace annotations.

So... how can you simulate multi-service architectures?

1. Define an architecture in a JSON
2. Run Spigo

Reasoning about response times within complex architectures is a hard thing to think about.
Simply adding together response times doesn't work. Then, how do you add together distributions? Fortunately this problem has been resolved in other domains.

Ozzie Gooen crafted [Guesstimate](github.com/getguesstimate/guesstimate-app) with lots of model for predicting stuff. Basically, it's an awesome tool to add asymmetric distributions. Take a look at www.getguesstimate.com/models/1307

You can find a Spigo histogram collection using Go-Kit metrics @ adrianco/spigo/collect (WIP)

Once you collect the histograms, you end up with a bunch of CSV files containing the measured data.

By the way, CDD (Conference-Driven Development) is a thing: there's a Go package for generating Guesstimate models (written @microXchg day 1) available at http://github.com/adrianco/goguesstimate

So... what's next? Some trends to watch out:

Serverless architectures - AWS lambda
Teraservices - using terabytes of memory
  - Diablo Memory1: Flash DIMM


## Don’t Fly Blind: Logging and Metrics in Microservice Architectures (Tammo van Lessen @taval, Alexander Heusingfeld @goldstift)

Requirements:

- Apply a well-thought logging concept
  - Use Thread Contexts / MDCs
  - Define QoS for Log Messages:

    - Log messages may have different QoS.
    - Use markers and filters to enable fine-grained routing of messages to dedicated appenders.
    - Use filters and lookups to dynamically configure logging.


- Aggregate logs in different formats from different systems

- Search & correlate

  Notice that logging has become cheaper and cheaper. Nowadays you could potentially log everything, but obviously that's not a good idea.

  If one team builds a project and then another one handles the operational stuff, it's difficult to understand what information you actually need to operate it. It's way easier if you have to handle the system working on production yourself.

  __Logstash__ provides a great platform to handle (almost) all kinds of logging needs.

  __ELK-Stack__: Elastic Search, Logstash & Kibana

  __Distributed Logstash setup__: Redis is used as a broker. Logstash shippers send to local (to them) Redis instances. Then a Logstash indexer pulls from the Redis instances, making the data available to Elastic Search. Beware! If you don't monitor this closely enough, you can go into serious trouble (e.g.: overflooding Redis instances). A possible solution might be to use Apache Kafka instead of Redis. Kafka requires a Zookeeper cluster to run though. So your infrastructure for just logging gets bigger and bigger. Be aware of operational costs!

- Visualize & drill-down (Kibana comes into the game)

  Notice that is considered counter-productive Elastic Search with less than 60GB of memory. (aka have your infrastructure ready for the challenge)

  In the free version of Elastic Search you couldn't filter out private data. Elastic Shield is able to filter your data based in authorizations, Greylock for Kibana might come handy too. Besides that, at least you could use Logstash to anonymize your data.

- Alerting

  It might be interesting to filter log stream for alerts. Simply achievable by defining rules.
  Be careful when defining rules against log messages. Once rules are set, if afterwards you change how log messages are formatted, you're screwed. (Important to remark because a lot of developers see logging as something private to the development team)

----
----

Logging is cool, metrics too.

Kind of metrics:
  - Business metrics (e.g.: longest approval request)
  - Application metrics (e.g.: requests per seconds, errors rate...)
  - System metrics (e.g.: CPU load, disk space...)

Recommendation: not focus only on Application Metrics. If you have all the 3, you would then be able to correlate them @ Elastic Search level. And that's helpful.

When I approach a development team and suggest to collect metrics, I often get as answer: "Why should a developer care about metrics? That's a DevOps thing, isn't it?". After all, we get measured by metrics, so... why not measuring them by ourselves?

(Example of a project Tammo was involved) He suggests to take metrics when running your test suites on ALL environments. That way, you could correlate concrete changesets to impact on the business (can make the developer proud of it).

Types of metrics:
  - Gauges
  - Counters
  - Meters: measures the rate at which a set of events occur.
  - Histograms: measures the distribution of values.
  - Timers: a timer is a histogram over a duration.

Distributed Metrics Architecture: Measure -> Collect & Sample -> Store -> Query & Graph

Push vs Pull: There are advantages and disadvantages on both sides. If you go for a pull model, you'd anyways need to apply the push for some use cases (e.g.: short-lived services which could be missed). So, basically, it depends on your scenarios (as usual).

Some recommendations regarding metrics:

- Think about what metrics are of importance for operating your application.
- Consider retention policies.
- Carefully design your dashboards.
- Think about non-standard graph types.

Conclusions:
- Create and document concepts for logging and metrics as guidelines for your teams.
- Collect & aggregate distributed logs and metrics.
- Create dashboards tailored for your audience.
- Correlate your data to make conscious decisions.
- Don't create your very own big data problem.


## Microservices UX: The technical journey to microservices (Russell Miles @russmiles)

- Highway to (Microservices) hell (don't miss the video! ;))

A definition of Microservices:
- Funny: http://credera.com/blog/technology-insights/java/mustard-seeds-microservices
- For real: "Loosely coupled service oriented architecture with bounded contexts"
- I like to say: loosely coupled services that __can change__.

...

There are 2 fantastic lies that we use:
1. Technical debt
2. Refactoring

...

What is the single biggest force limiting the change in your software? It´s YOU and your comprehension. Software is not the things, it's the things that happen. We should focus on the activity.

Some companies absolutely needed stability. (Real story as former agile coach: everyone looked like so excited to become agile, but no one could explain why. Management just wanted "to make people happy" in the company.)

Some value speed. Some value adaptabilty. Some value volatility. Some value antifragility.

Microservices __can__ enable speed, adaptability and antifragility. But if you go the wrong way, it won't. That's the real danger!

So, how should we do these things?

* Please take a look at DDD (Domain-Driven Design) if you haven't done it yet.

- Events: remember it's not the things, it's the things that happen. And we have a name for that: events.
- Aggregates: They take events and they transform them to knowledge.
- Views: Aggregates will tell views what's happened. Views are always consistent to the events they see. By the way, don't do cross-aggregates.

[__Event sourcing__](http://martinfowler.com/eaaDev/EventSourcing.html) helps you worrying less about migration of state in your system.

(40 slides in 2 minutes). Everything you need to create a simple To-do list app.

Developing UX for microservices is painful, is really hard. Currently I'm a product developer, and I'm currently simplifying microservices (the same way Java development has been simplified, microservices could be simplified too). If you have any idea about how to do it, please contact me. It's a good time to get involved.

The book I'm working on: ["Antifragile Software: Building Adaptable Software with Microservices"](http://www.leanpub.com/antifragilesoftware)


## Microservices <3 Domain Driven Design, why and how? (Michael Plöd @bitboss)

DDD helps us with microservices in four areas:

- Strategic Design
- Large Scale Structure
- (Internal) Building Blocks
- Destillation

Let's elaborate each of them throughout the talk.

- __Strategic Design__ is mainly about the bounded context, but not exclusively. It's also about context map and some patterns around these concepts (shared kernel, customer / supplier, conformist, anticorruption layer, separate ways, open / host service, published language).

  The business domain consists of a bunch of __Bounded Contexts__. Each Bounded Context contains models and maybe other contexts.

  The Bounded Context by itself doesn't deliver an overview of the system. By introducing a __Context Map__ we describe the contract between models and contexts. The Concept Map is also a great starting point for future transformations.

  Let's talk about several patterns regarding the interactions between these contexts:

  - __Shared Kernel__: Two teams share a subset of the domain model including code and maybe the database. The shared kernel is often referred to as the core domain.

  - __Customer / Supplier__: There is a customer / supplier relationship between two teams. The downstream team is considered to be the customer, sometimes with veto rights.

  - __Conformist__: Integration pattern where the downstream team conforms to the model of the upstream team. There is no translation of models and no vetoing. If the upstream model is a mess, it propagates to the downstream model.

  - __Anticorruption Layer__: Layer that isolates a client's model from another system's model by translation. Very interesting option when you migrate to a microservice architecture (it protects against sheet outside of the system).

  - __Separate Ways__: There's no connection between the bounded contexts of a system. This allows teams to find their own solutions in their domain. Of course, this is a very good way to achieve a high dynamic way to achieve deployable-independent artifacts and so on. Of course it's difficult to achieve if coming from an existing system.

  - __Open / Host Service__: Each Bounded Context offers a defined set of services that expose functionality for other systems. Any downstream system can then implement their own integration. This is especially useful for integration requirements with many other systems. This is also a pattern which comes handy when we want to integrate with other systems.

  - __Published Language__: This one is quite similar to Open / Host Service. However it goes as far as to model a Domain as a common language between bounded contexts.

  (Example of a context map with notation used by Eric Evans in his talks)

  Convay's Law: these patterns affect teams communication.

  - Customer / Supplier & Conformist => tightly coupled.
  - Anticorruption Layer, Separate Ways & Open / Host service => independent teams


- __Building Blocks__ help designing the internals of bounded contexts.

  "Personally, I'm a big fan of the Aggregates pattern along with Entities and Value Objects."

  - __Entities__ represent the core business objects of a bounded context's model. Each entity has a constant identity and its own lifecycle.

  - __Value Objects__ derive their identity from their values. They don't have their own lifecycle, they inherit it from entities that are referencing them. You should always consider value objects for your domain model.

    If an object can be considered an entity or a value object always depends on the (bounded) context it resides in.

  - __Aggregates__ (don't underestimate its power! ;). Aggregates group entities. The Root Entity is the lead in terms of access to the object graph and lifecycle.

- __Large scale structure__ helps evolving our Microservice landscapes. Here we have 3 patterns which can help us:

  - __Evolving Order__:

    - Let large structures evolve, don't overconstrain design principles.
    - These large structures should be applicable across bounded contexts.
    - However there should be some practical constraints.

    Sounds familiar in a microservice environment? You should create your microservices in a way that they can evolve.

  - __Responsibility Layers__: Each of these microservices is structured according to a bounded context. Inside these context developers have the chance to use building blocks. However we could structure our bounded context according to responsibilities.

    If you combine this with the idea of the evolving order, you could have different rules for every kind of responsibility.

    In the DDD book, they have covered responsibility layers mainly for internal structures of bounded contexts. However you can go further and apply it to broader contexts.

  - __System Metaphor__ isn't gonna be covered in this talk, doesn't fit 100% in microservices and we don't have time for it.

- __Destillation__: Helps extracting microservices out of an existing monolithic application.

  DDD community suggests to go ahead and __identify the core domain__ in your system. The __vision statement__ defines what is in the core domain and what is not in the core domain. The nwe create a destillation document, where we describe all the details of the core domain (how it should be extracted).Next step would be to __extract subdomains__. Without a clear vision and without a clear knowledge of the business capabilities, we run the risk of not get the bounded contexts right in this place.

To sum up, I think that DDD & microservices fit perfectly together.

## Cloud in your Cloud, how we build DigitalOcean (Matthew Campbell @kanwisher)

As a matter of fact, most of our new developments are in Go.

- __Monorepo__: Most controversial thing about how we work. You know, it's basically you put all your company's code into a single repo. Microservices is separation at the network level. It's the biggest accelerant in our work. The main drawback is because GitHub doesn't have a good support for this, so a lot of people get notified everytime we commit something.

- __Pull request driven development__: Nothing gets commited into master. We wrote a bot so every single piece of code gets reviewed by at least 1 other colleague. For me this is the 1st step towards continuous delivery.

  By the way, I think that it's a big win to limit the number of languages the teams can choose between when building a new service. Maybe 2 or 3 can be a better approach than complete freedom (so everyone can jump into any part of the codebase)

- __Service Discovery__: We use Consul for discovery. It's able to scale pretty well (we run an experiment 2 months ago with thousands of servers). Small-kernel tweak in Linux kernel to increment the cache, and we were able to make it work smoothly ;) Another cool thing about Consul are the multi-region replicas, I'm a big fan!

- __Artifacts__ created on every build. We have a Docker/artifact registry internally. We do Chef to do our deployments and it works really well for us.

- __Feature flags__ instead of branches. Everytime you create a new feature, you create a flag for that.

Let's talk about __monitoring__ now:

- __Prometheus__ is built by the SoundCloud guys. 10 or 20 thousand nodes being monitored by Prometheus. What it's cool about it, it pulls for metrics. Every machine exposes an endpoint with the metrics on it. This allows us to easily create aggregates of a certain region (check out the video/slidedeck for a diagram representing it).

- __Grafana__ is absolutely the best thing out there. We use it for all our internal metrics, all our internal dashboards. It finds Prometheus nodes via Consul, so we don't even need to configure Grafana.

- __Structured Logging__ (aka your logs are computer-readable). Nowadays we use JSON-formatted logs. For Syslog, JSON now is the standard format.

- __Kibana__'s WebUI to show our aggregated structured logs. Every microservice writes Syslog. Every region uses a RSyslog Aggregator (btw, in memory) and acts as a funnel to our Elastic Search Cluster (located at NY Data Center). Every region has a local aggregator.

  It's cool to build dashboards from your structured logs.

- __Distributed tracing__: we're just barely doing this. The question is: how do I know the flow of the system? We use a Transaction ID at a high level for every API call. Every microservice below it has its own child transaction ID. You can register all of this in your structured log, so you are able to aggregate them afterwards.

  We're just using Kibana. The other tool I know about is Zipkin (from Google). From my point of view, it's still not mature enough (a lot of effort to set it up, it's not worth it in my opinion)
