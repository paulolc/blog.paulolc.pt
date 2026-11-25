---
template: post
title: Is TDD Dead?
slug: is-tdd-dead
url: /is-tdd-dead
draft: true
date: 2022-07-08 14:06:43+00:00
description: Notes on the 'Is TDD Dead?' conversations between Kent Beck, David Heinemeier Hansson and Martin Fowler
socialImage: null
categories:
- coding
---
<!-- Draft created 2026-11-25 from the Joplin note "[PROJECT][BLOG][ARTICLE] IsTDDDead  - blog.paulolc.pt" (last updated 2022-07-08). Text is Paulo's, unedited. -->

Context

     IS TDD DEAD?

     A series of conversations between Kent Beck, David Heinemeier Hansson, and myself on the topic of Test-Driven Development (TDD) and its impact upon software design.

     http://martinfowler.com/articles/is-tdd-dead/

Sessions

> Is TDD Dead? [Part III]
> [https://plus.google.com/u/0/events/cf6e4486cs7ejh5632ldf2ks0eo

> Past Sessions

> Is TDD Dead? 1st Session

> Is TDD Dead? [Part II]
> [https://plus.google.com/u/0/events/couv627cogaue9dahj08feoa6b8

Speakers

> David Heinemeier Hansson aka DHH

>      "I am the creator of Ruby on Rails, founder & CTO at Basecamp (formerly 37signals), best-selling author, public speaker, race-car driver, hobbyist photographer, and family man."

> [https://twitter.com/dhh

> [> http://signalvnoise.com/writers/dhh](http://david.heinemeierhansson.com/)
> [http://david.heinemeierhansson.com/
> [http://david.heinemeierhansson.com/archive.html

> Kent Beck

>      Kent Beck is the founder and director of Three Rivers Institute (TRI). His career has combined the practice of software development with reflection, innovation, and communication. His contributions to software development include patterns for software, the rediscovery of test-first programming, the xUnit family of developer testing tools, and Extreme Programming. He currently divides his time between writing, programming, and coaching. Beck is the author/co-author of Implementation Patterns, Extreme Programming Explained: Embrace Change 2nd Edition, Contributing to Eclipse, Test-Driven Development: By Example, Planning Extreme Programming, The Smalltalk Best Practice Patterns, and the JUnit Pocket Guide.  He received his B.S. and M.S. in Computer Science from the University of Oregon.

>      > [https://twitter.com/KentBeck

>      > [http://www.threeriversinstitute.org/blog/

> Martin Fowler

>      "I am an author, speaker, and loud-mouth on the design of enterprise software. I work for ThoughtWorks, a software delivery and consulting company."

>      > [https://twitter.com/martinfowler

Others

     Robert Martin  "Uncle Bob"
          https://twitter.com/unclebobmartin

          http://blog.8thlight.com/uncle-bob/archive.html

     Misko Havery

Articles

> How Does TDD Affect Design? (by James Shore - Test Driven Javascript)
> [http://www.jamesshore.com/Blog/How-Does-TDD-Affect-Design.html

> Hexagonal Rails and The Ludicrous Terminal Application (by Matthew Parker - Unknown.) tweeted by Uncle Bob: "Excellent response to Is TDD Dead? Part II." (> [https://twitter.com/unclebobmartin/status/467873077196763136> )

> [http://pivotallabs.com/hexagonal-rails-and-the-ludicrous-terminal-application/

> The Little Mocker by Uncle Bob (Entertaining dialog explaining Test Doubles, Mocks, Stubs, Spies and Fakes)

> [http://blog.8thlight.com/uncle-bob/2014/05/14/TheLittleMocker.html

> Slow database test fallacy (by DHH on 30th April)
> [http://david.heinemeierhansson.com/2014/slow-database-test-fallacy.html

> Test-induced design damage (by DHH on 29th April)
> [http://david.heinemeierhansson.com/2014/test-induced-design-damage.html

> Test Isolation is about avoiding Mocks
> [https://news.ycombinator.com/item?id=7751610

> "Avoiding numerous or deeply nested mocks is the principal design activity of isolated TDD."

> "(...) isolated tests drive better design. Because an isolated test must set up all collaborators as mocks, the only way to reduce the mock complexity is to reduce the depth of collaboration. If we mock three levels deep, and do nothing to reduce that mock depth, then we're spending the effort to isolate without getting the benefit. If, however, we change the design to reduce coupling, then the mock depth will also be reduced, and isolation will earn its keep."

> "(...) isolated testing done test-first exposes design mistakes before they're made. It translates coupling distributed throughout the module into mock setup centralized in the test, and it does that before the coupling is even written down. "

Ruby Rogues Podcast Episode 56
http://rubyrogues.com/056-rr-david-heinemeier-hansson/

testing stubs spies mocks

[https://encrypted.google.com/search?num=100&safe=off&site=&source=hp&q=testing+stubs+spies+mocks&oq=testing+stubs+spies+mocks](https://encrypted.google.com/search?num=100&amp;safe=off&amp;site=&amp;source=hp&amp;q=testing+stubs+spies+mocks&amp;oq=testing+stubs+spies+mocks)

Mock Roles, not Objects
http://jmock.org/oopsla2004.pdf

TDD is dead. Long live testing. (DHH Blog)
http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html
(HN Discussion: https://news.ycombinator.com/item?id=7633254)

Monogamous TDD (Answer by Un
http://blog.8thlight.com/uncle-bob/2014/04/25/MonogamousTDD.html

ORGANIZE

Why I don't mock ()
http://blog.metaobject.com/2014/05/why-i-don-mock.html
https://news.ycombinator.com/item?id=7809402

http://geekswithblogs.net/theArchitectsNapkin/archive/2014/05/28/informed-tdd-ndash-kata-ldquoto-roman-numeralsrdquo.aspx
