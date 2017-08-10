# Why re-frame?

McCoy might report 

> "It's MVC, Jim, but not as we know it". 

And you would respond 

> "McCoy, you trouble maker, why even mention an OO pattern?
re-frame is a **functional framework**."

Being a functional framework, re-frame is about data, and the (pure) functions
which transform that data.


## Derived Values, Flowing

> This, milord, is my family's axe. We have owned it for almost nine hundred years, see. Of course,
sometimes it needed a new blade. And sometimes it has required a new handle, new designs on the
metalwork, a little refreshing of the ornamentation ... but is this not the nine hundred-year-old
axe of my family? And because it has changed gently over time, it is still a pretty good axe,
y'know. Pretty good.

> -- Terry Pratchett, The Fifth Elephant <br>
> &nbsp;&nbsp;&nbsp; reflecting on identity, flow and derived values


## Why Should You Care?

Perhaps:

1. You want to develop an [SPA] in ClojureScript, and you are looking for a framework.
2. You believe Facebook did something magnificent when it created React, and
you are curious about the further implications. Is the combination of
`reactive programming`, `functional programming` and `immutable data` going to
**completely change everything**? And, if so, what would that look like in a language
that embraces those paradigms?
3. You're taking a [Functional Design and Programming course](http://www.eli.sdsu.edu/courses/fall15/cs696/index.html) at San Diego State University
and you have a re-frame/reagent assignment due. You've left the reading a bit late, right?
4. You know Redux, Elm, Cycle.js or Pux and you're
interested in a ClojureScript implementation, **with a data oriented design**.
In this space, re-frame is very old, hopefully in a Gandalf kind of way.
First designed in Dec 2014, it even slightly pre-dates the official Elm Architecture,
although thankfully we were influenced by early-Elm concepts like `foldp` and `lift`, as well as
terrific Clojure projects like [Pedestal App], [Om] and [Hoplon]. Since then,
re-frame has pioneered ideas like event handler middleware,
coeffect accretion, and de-duplicated signal graphs.
5. Which is a lovely segue into the most important point: **re-frame is impressively buzzword compliant**. It has reactivity,
unidirectional data flow, pristinely pure functions,
interceptors, coeffects, conveyor belts, statechart-friendliness (FSM)
and claims an immaculate hammock conception. It also has a charming
xkcd reference (soon) and a hilarious, insiders-joke T-shirt,
ideal for conferences (in design). What could possibly go wrong?

[OM]:https://github.com/swannodette/om
[Hoplon]:http://hoplon.io/
[Pedestal App]:https://github.com/pedestal/pedestal-app


### It is a loop

Architecturally, re-frame implements "a perpetual loop".

To build an app, you hang pure functions on certain parts of this loop,
and re-frame looks after the `conveyance of data`
around the loop, into and out of the transforming functions you
provide - hence a tag line of "Derived Values, Flowing".

### It does Physics

Remember this diagram from school? The water cycle, right?

![re-frame is like the water cycle](/images/the-water-cycle.png)

Two distinct stages, involving water in different phases, being acted upon
by different forces: gravity working one way, evaporation/convection the other.

To understand re-frame, **imagine data flowing around that loop instead of water**.

re-frame provides the conveyance of the data around the loop - the equivalent of gravity, evaporation and convection.
You design what's flowing and then you hang functions off the loop at
various points to compute the data's phase changes.

Sure, right now, you're thinking "lazy sod - make a proper Computer Science-y diagram". But, no.
Joe Armstrong says "don't break the laws of physics" - I'm sure
you've seen the videos - and if he says to do something, you do it
(unless Rich Hickey disagrees, and says to do something else). So,
this diagram, apart from being a plausible analogy which might help
you to understand re-frame, is **practically proof** it does physics.

## It is mature and proven in the large

re-frame was released in early 2015, and has since [been](https://www.fullcontact.com) successfully [used](https://www.nubank.com.br) by [quite](http://open.mediaexpress.reuters.com/) a [few](https://rokt.com/) companies and individuals to build complex apps, many running beyond 40K lines of ClojureScript.

![](/images/scale-changes-everything.jpg)

**Scale changes everything.** 

Frameworks are just pesky overhead at small scale - measure them instead by how they help
you tame the complexity of bigger apps, and in this regard re-frame has
worked out well. Some have been effusive in their praise.

Having said that, re-frame remains a work in progress and it falls
short in a couple of ways - for example it doesn't work as well as we'd
like with devcards, because it is a framework, rather than a library.
We're still puzzling over some aspects and tweaking as we go. All designs
represent a point in the possible design space, with pros and cons.

And, yes, re-frame is fast, straight out of the box. And, yes, it has
a good testing story (unit and behavioural). And, yes, it works in with figwheel to create
a powerful hot-loading development story. And, yes, it has
fun specialist tooling, and a community,
and useful 3rd party libraries.



