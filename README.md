# microXchg 2016 - Day 2 (February 5, Berlin)

Find below live-taken notes of microXchg 2016 conference, day 2.

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


## Analyzing Response Time Distributions for Microservices (Adrian Cockcroft @adrianco)

It's much more challenging than just a large number of machines.
Some tools can show the request flow across a few services.
Interesting architectures have __a lot__ of microservices! Flow visualization is a big challenge.

[spigo](github.com/adrianco/spigo) simulates protocol interactions in Go & visualizes them with D3.
Open Zipkin is a common format for trace annotations.

So... how can you simulate multi-service architectures?
1.- Define an architecture in a JSON
2.- Run Spigo

Reasoning about response times within complex architectures is a hard thing to think about.
Simply adding together response times doesn't work. How do you add together distributions? Fortunately this problem has been resolved in other domains.

www.getguesstimate.com/models/1307
Ozzie Gooen github.com/getguesstimate/guesstimate-app (lots of model for predicting stuff). Useful (awesome) tool to add asymmetric distributions.

Spigo histogram collection using Go-Kit metrics @ adrianco/spigo/collect (wip)
Once you collect the histograms, you end up with a bunch of CSV files containing the measured data.
Conference-driven development is a thing: there's a Go package for generating Guesstimate models (written @microXchg day 1). github.com/adrianco/goguesstimate

What's next? Some trends to watch out:

Serverless architectures - AWS lambda
Teraservices - using terabytes of memory
  - Diablo Memory1: Flash DIMM


## Don’t Fly Blind: Logging and Metrics in Microservice Architectures (Tammo van Lessen @taval, Alexander Heusingfeld @goldstift)

Requirements:

- Apply a well-thought logging concept
  Use Thread Contexts / MDCs
  Define QoS for Log Messages
    Log messages may have different QoS
    Use markers and filters to enable fine-grained routing of messages to dedicated appenders
    Use filters and lookups to dynamically configure logging
- Aggregate logs in different formats from different systems
- Search & correlate

  Notice that logging has become cheaper and cheaper. Nowadays you could potentially log everything, but obviously that's not a good idea.
  If one team builds a project and then another one handles the operational stuff, it's difficult to understand what information you actually need to operate it. It's much way easier if you have to handle the system working on production yourself.

  __Logstash__ provides a great platform to handle (almost) all kinds of logging needs.
  __ELK-Stack__: Elastic Search, Logstash & Kibana
  __Distributed Logstash setup__: Redis is used as a broker. Logstash shippers send to local (to them) Redis instances. Then a Logstash indexer pulls from the Redis instances, making the data available to Elastic Search. Beware! If you don't monitor this closely enough, you can go into serious trouble (e.g.: overflooding Redis instances). A possible solution might be to use Apache Kafka instead of Redis. Kafka requires a Zookeeper cluster to run though. So your infrastructure for just logging gets bigger and bigger. Be aware of operational costs!

- Visualize & drill-down (Kibana comes into the game)

  Notice that is considered counter-productive Elastic Search with less than 60GB of memory. (aka have your infrastructure ready for the challenge)
  In the free version of Elastic Search you couldn't filter out private data. Elastic Shield is able to filter your data based in authorizations, Greylock for Kibana might come handy too. Besides that, at least you could use Logstash to anonymize your data.

- Alerting

  It might be interesting to filter log stream for alerts. Simply achievable by defining rules.
  Be careful when defining rules against log messages. Once rules are set, if afterwards you change how log messages are formatted, you're screwed. (Important to remark because a lot of developers see logging as something private to the dev. team)

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


## Microservices <3 Domain Driven Design, why and how? (Michael Plöd @bitboss)


## Cloud in your Cloud, how we build DigitalOcean (Matthew Campbell @kanwisher)
