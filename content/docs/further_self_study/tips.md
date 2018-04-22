---
title: "Tips and Tricks"
lesson: 1
chapter: 5
cover: "https://unsplash.it/400/300/?random?BoldMage"
date: "01/07/2018"
category: "akkatecture"
type: "docs"
tags:
    - further-self-study
    - akkatecture
    - csharp
    - dotnet
---
Whenever creating an application that uses cqrs and   event sourcing there are several
things you need to keep in mind to make it easier and minimize the
potential bugs, and headaches.


## Events

Make sure that when your aggregate events are JSON serialized, they
produce clean JSON as it makes it easier to work with and enable you to
easier deserialize the events in the future. Make sure that the events have:

-  No type information
-  No runtime information

Here's an example of good clean event JSON produced from a create user
event.

```json
{
    "Username": "root",
    "PasswordHash": "1234567890ABCDEF",
    "EMail": "root@example.org",
}
```

**Keep Aggregate Events As Slim As Possible**

A good rule of thumb is to minimize the amount of noisey data that can exist in the aggregate event. If your state event applying method uses all of the event member variables on invokation, then you have a good event. adding bloat and extra data to events waters down how true the events are. Events embody 'facts which happened' in your domain. If you add unessacary 'facts' that dont pertain to that event in your event model, then that event becomes less 'fact-y'.

**Idempotent Apply Methods**

Your apply methods should be functional and idempotent, meaning the application of the same event over the state object multiple times should produce the same result as if it were applied only once.

**Keep Old Event Types**

Keep in mind, that you need to keep the event types in your code for as long as these events are in the event source, which in most cases are *forever* as storage is cheap and information, i.e., your domain events, are expensive. Distinguish between old event types and broken event types with high scrutiny as these are the fundamental building blocks of your domain.

[//]: # (TODO LINK)
However, you should still clean your code, have a look at how you can
`upgrade and version your events <event-upgrade>` for details on
how Akkatecture supports you in this.

## Unit Testing

Unit test your aggregates and sagas in isolation from one another. By using black box style  testing you can follow this approach.

**Aggregate Testing**
 - Arrange - setup the aggregate actor, listen to the aggregate actors possible emitted events.
 - Act - send the aggregate actor a command.
 - Assert - check to see if the aggregate actor emits the domain event as desired.

 **Saga Testing**
 - Arrange - setup the saga actor, listen to the saga actors issued commands by mocking its actor reference to a test probe actor
 - Act - send the saga actor a domain event.
 - Assert - check to see if the saga actor tells the probe actor the desired command.

It will be most advantageous to learn akka.net's [test kit](http://getakka.net/articles/actors/testing-actor-systems.html)

## Make Your Domain Expressive

Your domain code is your business model codified. Make sure that you apply the principle of a ubiquitous language to your domain by being explicit but terse in your naming conventions, this leads to a far more enjoyable developer expirience for those who share the code base. With the fall in popularity of UML diagrams and other forms of non-code business domain models, code written in a ddd way is your business domain model.

## Plan For Uncertainty & Inconsistency

Note about message guarantees, message based systems etc