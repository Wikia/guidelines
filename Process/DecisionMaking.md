**CAUTION!** This document refers to Wikia internal structures, groups, processes, and procedures that may not make sense in isolation, or which may be misleading without the appropriate Wikia organizational context.

If you'd like to learn more or become a volunteer developer for Wikia, please check out our [Volunteer Developer program](http://dev.wikia.com/wiki/Volunteer_Developers) for more details.

---
# Technical Decision Making - Process


## Motivation

As Wikia scales, we need more complete and documented processes for making important technical decisions and then communicating them.

## Principles

Decisions should be made as low in the organization as possible<br>
… while avoiding local optimizations that are not correct globally<br>
… while broadly informing anyone impacted or interested<br>
… while improving everyone’s ability to make good decisions

## Types of Decisions
See the table below. As we go down this table, decisions

* get increasingly complex
* get increasingly impactful
* get increasingly expensive if wrong

| Type of Decision | Decision Maker | Reviewers / Inputs | Use Cases |
| ------------ | ------------- | ------------ | ---------- |
| Intra-extension / module / feature implementation <br> * including localized changes to MW core | Individual  | Code Review process <br> Follow team & organization accepted best practices | VE Upstream Sync |
| New interface between Wikia teams | Individual or Team | Tech Lead of team implementing the interface. <br><br>Code review process <br><br> Team Tech Lead of owning team <br><br> Implementing team is responsible for communicating to all affected parties such that they understand the consequences and are allowed to give feedback <br><br> (As of December 2014 the “Engineering Teams Technical Update” is a good forum for this.)<br> Consuming team is responsible for communicating constraints | Hubs V3 API used by external partners |
| New technology | Individual and Technical Lead of owning Team |Architect is responsible for defining criteria for new technology and for globally optimization of our technology stack.<br><br> Operations is responsible for defining operational boundaries. | * Scala for Phalanx<br> * Clojure for Thumbnailer |
| New API or Externally visible service or incompatible change  | Individual and Technical Lead of Owning Team | Architect is responsible for defining development criteria for new technology (eg readability, conventions, "hit by a bus factor")<br> Operations is responsible for defining operational criteria (eg expensive, high availability characteristics needed etc) <br> Engineering Management is responsible for defining SLA requirements  | External Partner APIs and RSS Feeds |
| New internal service<br>or radically changed service including new infrastructure (such as key-value store) | Technical Lead of implementing team for new service. | Technical Leads of all client teams for existing service<br><br>Architect is responsible for defining criteria for new technology and for globally optimization of our technology stack.<br><br>Operations is responsible for defining operational boundaries. |*  Video service<br> * New event processing pipeline for NLP/Search/AQ<br> * Phalanx<br> * Key-value store<br> * Consul / Zookeeper as a way of managing configuration |
| New external service vendor or new use of existing external service such as AWS |Technical lead of whichever team does the analysis and is the motivating consumer of the service.<br><br>Including business criteria such as cost/benefit, build . buy analysis, vendor viability, etc.  | Product Owners of consuming teams will provide functional needs<br><br>Operations defines operational criteria (eg expensive, high availability characteristics needed etc)<br><br>Engineering Management will provide business and budget guidance. | Optimizely (done by Consumer Team)<br>Keynote -> Catchpoint |
| |  | |  |
| Consistent global standards, code ownership, code review policy, coding standards | TBD. We have variety of answers. These examples are (so far) a mix of top-down decisions and ad-hoc groups of cross-functional domain experts. | | Non-idiomatic code<br>FE Coding Standard<br>Python Standards |

## Boundaries

Decisions are made in the context of Wikia’s business and the history of preceding decisions. These create boundaries around sets of decisions that are viable and those that aren’t. For example, decisions are influenced and sometimes constrained by
* Budget
* Current technology stack
* Skill set
* Product strategy
* Short term goals

Boundaries can and do change over time, but typically relatively slowly compared to the rate of decision making. For example
* We can adjust the budget to spend more in some areas and less than others, but that’s not a change we would make without careful global consideration.
* We can expand our technology stack when appropriate.
* Skills sets can be changed with hiring or by investing in learning new skills, but both take time and involve other tradeoffs

## Reviewer

The main goal of the Reviewers is to ensure that decisions are within important boundaries. Reviewers do not make an alternative decision. Their responsibility is to give clear feedback about whether the decision is inside the boundaries or not. If a decision violates a boundary, they will inform (and clarify) the boundary to the decision maker.

In addition, Reviewers should provide feedback about alternatives.
## Process

1. Someone at Wikia realizes that a decision is being considered which falls into one of the types described in the table.
1. Decision Maker and Reviewers are identified according to the table.
1. Decision Maker characterizes the problem / opportunity.
1. Decision Maker solicits advice from techteam members with expertise. This is likely to include the Reviewers but is also likely to include others.
1. Decision Maker puts together a proposal for the decision and ask Reviewers for feedback
1. Reviewers give feedback
1. Decision Maker makes the decision which must be inside the boundaries. The decision is final unless new information surfaces which invalidates some of the assumptions
1. Decision Maker communicates the decision to all Reviewers and the rest of Engineering

In the rare event that the Decision Maker and Reviewers cannot agree whether a decision is inside the boundaries, or if anyone feels like the boundaries need to be re-considered, the issue should be escalated to the Architect or up the engineering management chain.
## Visibility and Knowledge Sharing

Essentially everything below the first row in the table should be communicated widely via the [Knowledge Sharing](https://one.wikia-inc.com/wiki/Engineering/Knowledge_Sharing) process. The [Technical Leads](https://docs.google.com/a/wikia-inc.com/document/d/15_v7eJnOrd9OViusK5AuVKU2hGDGkGBueJ-EyqDewA0/edit?usp=sharing) are responsible for ensuring this happens.
