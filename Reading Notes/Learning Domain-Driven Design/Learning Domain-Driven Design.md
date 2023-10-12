# Learning Domain-Driven Design

Date: September 13, 2022

Tags: architecture

URL: https://www.oreilly.com/library/view/learning-domain-driven-design/9781098100124/

# Introduction

- 70% of software projects fail (not delivered on time, on budget, or according to the client’s requirements).
- A common reason for failure is **communication**.

# Strategic Design

- Problem → Solution → Implementation
- Strategic design deals with Why? and What?
- Tactical design deals with How?

## Analyzing Business Domains

> To design and build an effective solution, you have to understand the problem.
To understand the problem, you have to understand the context within which it exists.
> 
- **Business Domain**: main area of activity
    - FedEx: courier delivery; Starbucks: coffee; Walmart: retail
- **Subdomain**: fine-grained area of business activity
    - Core subdomains: differentiators, cost of switching, competitive advantage, strategy, secret sauce.
    - Generic subdomains: battle-tested generic implementations.
    - Supporting subdomains: non-generic, but also no competitive advantage; e.g. simple CRUD parts. Ideally can be turned into generic subdomains.
    - Companies should maximize their time on core subdomains.
    - Subdomains don’t need to be technical.
    - How to differentiate
        - Would someone pay for it on its own? → Core subdomain
        - Would it be simpler and cheaper to hack your own implementation, rather than integrating an external one? → Supporting subdomain
            
            ![https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_0101.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_0101.png)
            
    - When to stop decomposing subdomains?
        - Identify sets of coherent use cases that operate on the same data
- Who are the domain experts?
    - Business stakeholders
    - Not the engineers (but there can be overlap. E.g. ML team)

## Discovering Domain Knowledge

> A software project’s success depends on the effectiveness of knowledge sharing between domain experts and software engineers.
Effective knowledge sharing between domain experts and software engineers requires effective communication.
> 
- Traditional software development lifecycle implies top-down *translations* (domain knowledge → analysis model → requirements → system design → source code)
- Instead of translations, define a **Ubiquitous language** shared among all stakeholders
    - Language of the business
    - System should use the same language
    - Avoid:
        - Technical jargon
        - Ambiguous terms
        - Synonymous terms
        - Implicit assumptions
        - Extraneous details
- Tools
    - Glossary (Wiki)
    - Tests (Gherkin)

## **Managing Domain Complexity**

> On an organizational scale, the domain experts’ mental models can be inconsistent themselves. Different domain experts can use different models of the same business domain.
> 
- Inconsistencies among domain experts
    - Not good:
        - Making a model fit everything → ineffective
        - Making terms more complex → cognitive load, implementation misaligned with ubiquitous language
    - Good: **Bounded context** (model boundary)
- A ubiquitous language is *not* universal.
- **Subdomains are discovered, bounded contexts are designed.**
- Bounded contexts and ubiquitous language enable modeling, defining implementation scopes and ownership (1 team per bounded context)

## **Integrating Bounded Contexts**

> Multiple bounded contexts may represent the same business entities but model them to solve a different problem. Although they can evolve independently, they have to integrate with one another. **Contracts** are touchpoints between bounded contexts, in particular regarding core subdomains.
> 
- Integration models
    - Cooperation
        - Partnership
            - Bounded contexts are integrated in an ad hoc manner.
        - Shared Kernel
            - Multiple bounded contexts share a limited overlapping model that belongs to all participating bounded contexts.
            - Continuous integration: Changes to the shared kernel must trigger integration tests for all the affected bounded contexts.
    - Customer-Supplier
        - Conformist
        - Anticorruption layer
        - Open-Host Service
            - uses published language (decoupled from ubiquitous language)
- Non-integration
    - Useful for generic subdomains (re-implementing is cheap) or wildly different models (cooperation is expensive)
    

# Tactical Design

## **Simple Business Logic**

- Transaction Script
    - Foundational building block
    - Simple but can become difficult to maintain
- Active Record
    - Data structure with CRUD access (ORM)

## Complex Business Logic

- Domain Model
    - Good for entangled dependencies and rules
- Building blocks
    - Value object
        - same value is same instance
        - immutable
    - Entity
        - same value is different instance
        - mutable
        - needs identity field
    - Aggregate
        - entities + consistency enforcement
        - only aggregate’s business logic is allowed to modify state
        - concurrency management (e.g. a version field)
        - all strongly consistent entities should be part of one aggregate. eventually consistent entities may be in different aggregates.
        - one entity should be the aggregate root
        - communicate changes to the outside using domain events
    - Domain services
        - stateless object to host business logic affecting multiple aggregates
- Complexity = degrees of freedom = data points needed to describe a system’s state

## Modeling Time

- Event Sourcing
    - All changes to entities are recorded as events
    - Good when required audit log or complex analytics
    - https://github.com/pyeventsourcing/eventsourcing
    - [https://pypi.org/project/eventsourcing-django/](https://pypi.org/project/eventsourcing-django/)
- Forgettable Payload
    - Store sensitive information encrypted. To delete, just throw away the key.

## **Architectural Patterns**

- Layered Architecture
    - Presentation Layer (GUI, CLI, API) = user interface layer
    - (extra) Service Layer (Actions) = application layer
    - Business Logic Layer (Entities, rules) = domain layer = model layer
    - Data Access Layer (DB, message bus, object storage) = infrastructure layer
- Ports & Adapters
    - dependency inversion
    - decoupling from infrastructure
- Command-Query Responsibility Segregation (CQRS)
    - Same data can be represented in multiple models

## **Communication Patterns**

- Model Translation
- Outbox

# **Applying Domain-Driven Design in Practice**

## **Design Heuristics**

> Refactoring logical boundaries is considerably less expensive than refactoring physical boundaries. When designing bounded contexts, start with wider boundaries. If required, decompose the wide boundaries into smaller ones as you gain domain knowledge.
> 
- Business Logic

![https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1003.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1003.png)

- Architecture

![https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1004.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1004.png)

- Testing

![https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1005.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1005.png)

![https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1006.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1006.png)

## **Evolving Design Decisions**

> As an organization grows and evolves, it’s not unusual for some of its subdomains to morph from one type to another.
> 
- Strategic

![https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1101.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1101.png)

- Tactical
    - Refactorings/upgrades towards more complex patterns
- Organizational
    - When splitting up teams, need to re-define integration contracts.
- Domain Knowledge

> As time goes by, documentation often becomes stale, people who were working on the original design leave the company, and new functionality is added in an ad hoc manner until, at one point, the codebase gains the dubious status of a legacy system. **It’s vital to prevent such degradation of domain knowledge proactively.**
> 
- Heuristics
    - When a subdomain’s functionality is expanded, try to identify more finer-grained subdomain boundaries that will enable you to make better design decisions.
    - Don’t allow a bounded context to become a “jack of all trades.” Make sure the models encompassed by bounded contexts are focused to solve specific problems.
    - Make sure your aggregates’ boundaries are as small as possible. Use the heuristic of strongly consistent data to detect possibilities to extract business logic into new aggregates.

## **EventStorming**

- Low-tech (pen and paper) group activity to discover and share domain knowledge
    - Scope: the business process that the group is interested in exploring
    - Explore the process as a series of domain events, represented by sticky notes, over a timeline
    - Enhance with additional concepts—actors, commands, external systems, etc.
- Steps
    1. Unstructured Exploration: brainstorming of domain events (past tense). Orange sticky notes
    2. Timelines: organize events in order, starting with “happy path”, then adding alternative scenarios
    3. Pain Points: identify bottlenecks, manual steps that require automation, missing documentation, or missing domain knowledge. Rotated pink sticky notes
    4. Pivotal Events: identify significant business events indicating a change in context or phase. Mark with vertical bar
    5. Commands: describe what triggered the event or flow of events. Blue sticky notes
    6. Policies: when commands are automatically executed when a specific domain event occurs. Purple sticky notes
    7. Read Models: the view of data within the domain that the actor uses to make a decision to execute a command. Green sticky notes.
    8. External Systems: Pink sticky notes.
    9. Aggregates
    10. Bounded Contexts

## In the real world

- Strategic Analysis
    - What is the organization’s business domain(s)?
    - Who are its customers?
    - What service, or value, does the organization provide to customers?
    - What companies or products is the organization competing with?
    - How is the company organized?
    - Define subdomains
    - Explore the Current Design
        - Evaluate strategy and tactics
- Modernization Strategy
    - Think big but start small
    - Reorganize the bounded context’s modules to reflect the business subdomains’ boundaries rather than technical implementation patterns
    - Strangler
        
        ![https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1304.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781098100124/files/assets/lddd_1304.png)
        
    - Refactoring
        - Small steps!
    

# Additional Resources

- [https://khalilstemmler.com/articles/domain-driven-design-intro/](https://khalilstemmler.com/articles/domain-driven-design-intro/)