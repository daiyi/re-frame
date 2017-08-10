# A re-frame Walkthrough


re-frame is a 6-domino cascade.

![](/images/Readme/Dominoes-small.jpg)

Computationally, each iteration of the loop involves a
six domino cascade.

One domino triggers the next, which triggers the next, et cetera, until we are
back at the beginning of the loop, whereupon the dominoes spring to attention
again, ready for the next iteration of the same cascade.

The six dominoes are ...

### 1st Domino - Event Dispatch

An `event` is sent when something happens - the user
clicks a button, or a websocket receives a new message.

Without the impulse of a triggering `event`, no six domino cascade occurs.
It is only because of `event`s that a re-frame app is propelled,
loop iteration after loop iteration, from one state to the next.

re-frame is `event` driven.

### 2nd Domino - Event Handling

In response to an `event`, an application must decide what action to take. This is known as `event handling`.

Event handler functions compute side effects (known in re-frame simply as `effects`). More accurately, they compute
a **description of `effects`**. This description is a data structure
which says, declaratively, how the world should change (because of the event).

Much of the time, only the "application state" of the SPA itself need
change, but sometimes the outside world must also be affected
(localstore, cookies, databases, emails, logs, etc).

### 3rd Domino - Effect Handling

The descriptions of `effects` are realised (actioned).

Now, to a functional programmer, `effects` are scary in a
[xenomorph kind of way](https://www.google.com.au/search?q=xenomorph).
Nothing messes with functional purity
quite like the need for side effects. On the other hand, `effects` are
marvelous because they move the app forward. Without them, an app stays stuck in one state forever,
never achieving anything.

So re-frame embraces the protagonist nature of `effects` - the entire, unruly zoo of them - but
it does so in a largely hidden way, and in a manner which is debuggable, auditable, mockable and pluggable.

### We're At A Pivot Point

The world just changed and, very often, one particular part of it: the **application state**.

re-frame's `app state` is held in one place - think of it like you
would an in-memory, central database for the app (details later).

When domino 3 changes this `app state`, it triggers the next part of the cascade
involving dominoes 4-5-6.

### There's a Formula

The 4-5-6 domino cascade implements the formula made famous by Facebook's ground-breaking React library:
`v = f(s)`

A view, `v`, is a function, `f`, of the app state, `s`.

Said another way, there are functions `f` that compute which DOM nodes, `v`,
should be displayed to the user when the application is in a given app state, `s`.

Or, differently: **over time**, as `s` changes, `f`
will be re-run each time to compute new `v`, forever keeping `v` up to date with the current `s`.

In our case, domino 3 changes `s`, the application state,
and, in response, dominoes 4-5-6 are concerned with re-running `f` to compute the new `v`
shown to the user.

Except, of course, there are nuances. For instance, there's no single `f` to run.
There may be many functions which collectively build the overall DOM,
and only part of `s` may change at any one time, so only part of the
`v` (DOM) need be re-computed and updated. And some parts of `v` might not
be showing right now.


### Domino 4 - Query

<img align="right" src="/images/Readme/6dominoes.png?raw=true">

Domino 4 is about extracting data from "app state", and providing it
in the right format for view functions (which are Domino 5).

Domino 4 is a novel and efficient de-duplicated signal graph which
runs query functions on the app state, `s`, efficiently computing
reactive, multi-layered, "materialised views" of `s`.

(Relax about any unfamiliar terminology, you'll soon
see how simple the code actually is)

### Domino 5 - View

Domino 5 is one or more **view functions** (aka Reagent components) that compute the
UI DOM that should be displayed to the user.

To render the right UI, they need to source application state, which is
delivered reactively via the queries of Domino 4. They
compute hiccup-formatted data, which is a description of the DOM required.

### Domino 6 - DOM

You don't write Domino 6 - it is handled for you
by Reagent/React. I mention it here
for completeness and to fully close the loop.

This is the step in which the hiccup-formatted
"descriptions of required DOM", returned by the view functions of Domino 5, are made real.
The browser DOM nodes are mutated.


## A Cascade Of Simple Functions

**Each of the dominoes you write are simple, pure functions** which
can be described, understood and
tested independently. They take data, transform it and return new data.

The loop itself is very mechanical in operation.
So, there's a regularity, simplicity and
certainty to how a re-frame app goes about its business,
which leads, in turn, to an ease in reasoning and debugging. This is
key to why re-frame is pleasing to work with - it is just so
straightforward.

## Managing mutation

The two sub-cascades 1-2-3 and 4-5-6 have a similar structure.

In each, it is the second to last domino which
computes "descriptions" of mutations required, and it is
the last domino which does the dirty work and realises these descriptions.

In both cases, you don't need to worry yourself about this dirty work. re-frame looks
after those dominoes.

## As Code Fragments

<img align="right" src="/images/Readme/todolist.png?raw=true">

Let's take this domino narrative further and introduce code fragments.

**Imagine:** We're working on a SPA which displays a list of items. You have
just clicked the "delete" button next to the 3rd item in the list.

In response, what happens within this imaginary re-frame app? Here's a sketch
of the six domino cascade:

> Don't expect
to completely grok the terse code presented below. We're still at 30,000 feet. Details later.

### Code For Domino 1

The delete button for that 3rd item will have an `on-click` handler (function) which looks
like this:
```clj
#(re-frame.core/dispatch [:delete-item 2486])
```

`dispatch` emits an `event`.

A re-frame `event` is a vector and, in this case,
it has 2 elements: `[:delete-item 2486]`. The first element,
`:delete-item`, is the kind of event. The rest is optional, further data about the
`event` - in this case, my made-up id, `2486`, for the item to delete.

### Code For Domino 2

The `event handler`, `h`, associated with
`:delete-item`, is called to compute the `effect` of this event.

This handler function, `h`, takes two arguments: a `coeffects` map
which holds the current state of the world (including app state),
and the `event`. It must return a map of `effects` - a description
of how the world should change. Here's a sketch (we are at 30,000 feet):
```clj
(defn h
[{:keys [db]} event] ;; args: db from coeffect, event
(let [item-id (second event)] ;; extract id from event vector
{:db (dissoc-in db [:items item-id])})) ;; effect is change db
```

There are mechanisms in re-frame (beyond us here) whereby you can place
all necessary aspects of the world into that first `coeffects` argument, on a
per event-kind basis (different events need to know different things
in order to get their job done). The current application state, `db`,
is one aspect of the world which is invariably needed.

On program startup, `h` would have been
associated with `:delete-item` `events` like this:
```clj
(re-frame.core/reg-event-fx :delete-item h)
```
Which says "when you see a `:delete-item` event, use `h` to handle it".

### Code For Domino 3

An `effect handler` (function) puts into action the `effects` returned by `h`:
```clj
{:db (dissoc-in db [:items item-id])}
```
So that's a map. The keys identify the required kinds of `effect`, and the values
supply further details. This map only has one key, so there's only one effect.

A key of `:db` means to update the app state with the key's value.

This update of "app state" is a mutative step, facilitated by re-frame
which has a built in effects handler for the `:db` effect.

Why the name `:db`? re-frame sees "app state" as something of an in-memory
database.

### Code For Domino 4

Because a new version of "app state" has been computed and installed,
a query (function) over this app state is called automatically (reactively),
itself computing the list of items.

Because the items
are stored in app state, there's not a lot to compute in this case. This
subscription acts more like an accessor.
```clj
(defn query-fn
[db _] ;; db is current app state
(:items db)) ;; not much of a materialised view
```

On program startup, such a query-fn must be associated with an id,
(for reasons obvious in the next domino) like this:
```clj
(re-frame.core/reg-sub :query-items query-fn)
```
Which says "when you see a query (subscribe) for `:query-items`, use `query-fn` to handle it".

### Code For Domino 5

Because the query function re-computed a new value, a view (function) which subscribes
to "items", is called automatically (reactively) to re-compute DOM.

It produces
a hiccup-formatted data structure describing the DOM nodes required (no DOM nodes
for the deleted item, obviously, but otherwise the same DOM as last time).

```clj
(defn items-view
[]
(let [items (subscribe [:query-items])] ;; source items from app state
[:div (map item-render @items])) ;; assume item-render already written
```

Notice how `items` is "sourced" from "app state" via `subscribe`. It is called with a query id
to identify what data it needs.

### Code For Domino 6

The computed DOM (hiccup) is made real by Reagent/React. No code from you required. Just happens.

The DOM "this
time" is the same as last time, except for the absence of DOM for the
deleted item, so the mutation will be to remove some DOM nodes.

### 3-4-5-6 Summary

The key point to understand about our 3-4-5-6 example is:
- a change to app state ...
- triggers query functions to rerun ...
- which triggers view functions to rerun
- which causes new DOM

Boom, boom, boom go the dominoes. It is a reactive data flow.

### Aaaaand we're done

At this point, the re-frame app returns to a quiescent state,
waiting for the next event.

## So, your job is ...

When building a re-frame app, you will:
- design your app's information model (data and schema layer)
- write and register event handler functions (control and transition layer) (domino 2)
- (once in a blue moon) write and register effect and coeffect handler
functions (domino 3) which do the mutative dirty work of which we dare not
speak.
- write and register query functions which implement nodes in a signal graph (query layer) (domino 4)
- write Reagent view functions (view layer) (domino 5)






## Where Do I Go Next?

**At this point you
already know 50% of re-frame.** There's detail to fill in, for sure,
but the core concepts, and even basic coding techniques, are now known to you.

Next you need to read read the other three articles in the [Introduction section](/docs#introduction):

* [Application State](/docs/ApplicationState.md)
* [Code Walkthrough](/docs/CodeWalkthrough.md)
* [Mental Model Omnibus](/docs/MentalModelOmnibus.md)

This will get your knowledge to about 70%. The
final 30% will come incrementally with use, and by reading the other
tutorials (of which there's a few).

You can also experiment with these examples: <br>
https://github.com/Day8/re-frame/tree/master/examples

Use a template to create your own project: <br>
Client only: https://github.com/Day8/re-frame-template <br>
Full Stack: http://www.luminusweb.net/

Use these resources: <br>
https://github.com/Day8/re-frame/blob/develop/docs/External-Resources.md

### T-Shirt Unlocked

Good news. If you've read this far,
your insiders T-shirt will be arriving soon - it will feature turtles,
[xkcd](http://xkcd.com/1416/) and something about "data all the way down".
But we're still working on the hilarious caption bit. Open a
repo issue with a suggestion.


[SPAs]:http://en.wikipedia.org/wiki/Single-page_application
[SPA]:http://en.wikipedia.org/wiki/Single-page_application
[Reagent]:http://reagent-project.github.io/
