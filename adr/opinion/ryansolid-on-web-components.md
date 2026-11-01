Extract from [Web Components Are Not the Future](https://dev.to/ryansolid/web-components-are-not-the-future-48bh)

![Ryan Carniato](../img/ryan-carniato.webp) [Ryan Carniato](https://dev.to/ryansolid) posted 2024-09-27, edited 2024-10-08.

2020-03-27 I wrote [Maybe Web Components are not the Future?](https://dev.to/ryansolid/maybe-web-components-are-not-the-future-hfh)
It was a soft-handed look at where they made sense and where things fall apart.
It wasn't geared as a "us against them" argument and I hoped people would come to reasonable conclusions for themselves.

But over the past few years, I've only seen the situation worsen.
Some may have never taken Web Components seriously, but I always have.
I used them in production for 7 years. ...

But my experience then and my experience since only points me to a single conclusion.
Web Components possibly pose the biggest risk to the future of the web that I can see.

## Competing Standards
If the sheer number of JavaScript frameworks is any indicator we are nowhere near reaching a consensus
on how one should author components on the web. And even if we were a bit closer today
we were nowhere near there a decade ago.

The introduction of higher-level primitives can have a positive effect.
Suddenly something harder to do becomes easier. Which initially leads to more exploration.
Web Components caused an increase in the number of JavaScript frameworks in the mid 2010s.
It was an important inspiration for why I created SolidJS. ...

But it also can have a negative effect. If too many assumptions are made it becomes harder
to explore alternative space because everything gravitates around the establishment.
What is more established than a web standard that can never change?

I've struggled with this a ton outside of web standards.
JSX according to the spec has no established semantics but try to convince
the wide range of tooling out there they are assuming too much.
I can only imagine the nightmare it would have been if JSX had been standardized in the browser.
Forgetting how frameworks like Inferno, Solid, and Million, have done way more optimal things
with their JSX transform, even React has changed their transform over time.

## The Cost of Abstraction
So the fundamental problem with Web Components is that they are built on *Custom Elements*.
Elements !== Components. More specifically, Elements are a subset of Components.
One could argue that every Element could be a Component but not all Components are Elements.

It means that every interface needs to go through the DOM.
Some in well-defined ways that aren't a perfect fit, and some in newly defined ways
that augment or change how one would deal with Elements to accommodate extended functionality.

To begin, DOM elements have attributes and properties. This is so that they can be represented as HTML.
Attributes accept only strings while properties being a JS interface can handle any value.
Native DOM elements have many rules around specific attributes/properties like how
some are boolean (existence means they apply) while others are psuedo-boolean (needs an explicit "true"/"false").
Some properties reflect to attributes and others do not.

A goal of templating languages is to solve this in a uniform way.
We can make special rules around known elements and attributes.
But with custom elements we don't know.
So this is why some templating libraries have interesting prefixes to indicate how things should be set.
Even Solid's JSX we have attr:, prop: and bool: prefixes for this reason.
Now every runtime location and compiler hook needs to be aware of this.

You might be thinking we need *better templating as a Standard*.
But like JSX above you need to consider the implications of that decision.
A few years ago most people would have probably agreed that the way something like LitHTML did template rendering
was something was a good approach. Other solutions could output it. However, in the interim, we realized
that reactive rendering, like what Solid does, outperforms it.
It does so by changing the semantics of the templating.
If we had moved ahead we would have a standard that would not be the best way to do templating.

It doesn't end there. *DOM elements can be cloned*. But *Custom Elements* have different behavior
which means they *should be imported* instead. They have DOM-based lifecycles that can trigger
synchronously or asynchronously depending on when they upgrade.
This wreaks havoc on things like Reactivity tracking and Context APIs.
However, these details are important to interface with native DOM behaviors and events.
These are also all things that a JavaScript Component doesn't worry about.

There are other idiosyncrasies like the way event targeting works in the Shadow DOM.
Some events don't "compose" i.e. don't bubble beyond *Shadow DOM* boundaries.
Some events don't bubble consistently because they don't recognize a different target, like in "focusin",
because the *Shadow Host* is always made the target no matter which child element gains focus.
We could talk about these for days but I don't want to divert to much attention here.
Some things are today's shortcomings and some are by design.
But the commonality here is they all require specific consideration that otherwise wouldn't be necessary.

Of course this has a performance overhead: [real-cost-2021]

But even if you consider this performance cost minimal, where does it leave us when going to the server for things like SSR? Yes, you can completely do SSR with Web Components. Hydration is completely achievable. But they are a DOM interface in a place with no DOM. You can make a minimal wrapper to handle most things but this is all extra overhead. All because we tried to make Components something they aren't. ...

## The Real Cost of Web Components
The complete picture is one where the complexity of dealing with native elements increases
to account for the new found flexibility of web components. It is something that as tools support them better,
not only is the user of them paying the price of but all users of that tool.
More code to ship and more code to execute to check these edge cases.
It's a hidden tax that impacts everyone.

I've talked about where early standardization would have been catastrophic.
But it also has the potential to stifle future innovation along certain paths because it assumes too much.
Improvements to hydration, things like Resumability, Partial or Selective Hydration depend on event delegation to work.
But if Shadow DOM messes with that then how could Web Components fit that model?
SSR some might say was an oversight because we didn't think about that much in 2013,
but this gap only continues to grow over time.

If anything with compilers and advancements in build tools, we are moving more in the direction
away from components being anything more than a Developer Experience consideration.
Something you have at authoring time that vanish from the final output.
For optimal user experience we optimize away the components. [pure-overhead]

I'm not saying someone couldn't find some interesting solutions to these problems
but they all imply taking on the hidden cost due to the foundational misalignment
of having the wrong abstraction. This is what makes the dialog here so difficult.
It isn't something you improve. It's just a mistake for a number of things.

Now we can all say different solutions have different tradeoffs.
And maybe something like Web Components could only possibly succeed with support of things
like Standards bodies because of how pervasive it would need to be to work.
It's an ideal we are working towards and just aren't quite there yet.

## Utopia Revisited
We often hit a similar debate when talking about Micro-frontends or Microservices.
It is beneficial for organizational purposes to align projects with your developers. It follows Conway's Law.

The isolation or portability afforded by Web Components means that one could have multiple different components on the page from different sources. Now like the others prudence is in order. Similar to not wanting to write all your Microservices in different languages you might not want to author all your components in different frameworks.

But frontend is a much more restrictive space. The cost of each kilobyte of JS is not insignificant. It isn't only maintenance why you wouldn't want to mix and match but to reduce payload. And this is where the wheels start coming off.

If your goal is to future-proof then you need to be prepared to keep different versions of even the same library on the same page. This is no different for Lit as it is for React. You could choose to never update anything, but that is an option without Web Components too. There are no additional guarantees here. To future-proof the only the choice to freeze things, maintain multiple versions, and load more JS on your page.

Realistically you will update your libraries in lockstep which is also the same with any library. And in those cases if you only have a single library on your page Web Components aren't doing anything for you but adding more overhead. Possibly getting in the way of features that the library provides now and in the future. You might as well use the library without the Web Components.

The second consideration is granularity. If you have a Microfrontend then that is a contained swappable piece. If in the future you decide it isn't the best way to handle things you swap it out. But once you adopt web components everywhere you will need to address each of these touchpoints. Web Components differ from MicroFrontends and Microservices because they can be used cross-sectionally. This is good for standardization, but I've never seen a company that uses jQuery ever completely be able to get rid of it.

The most compelling uses for Web Components are as a sort of Microfrontend container. In that case, you don't pay the scaling costs, the outside communication is minimal, and they are easy to swap in/out. The one-off scenario. In those cases though the friction is low enough that having Web Components isn't necessary. I'd take them for the ergonomics to put a Zendesk widget on my page, but is the abstraction worth the cost?

## Conclusion
And that is what it comes down to. I could not argue that there are not ergonomic benefits to using Web Components in some scenarios. But the cost it imposes everywhere is steep. While I shouldn't be quick to disavow a technology for the types of poor patterns it enables one to do, it is difficult to stand behind it when it never fits in the ideal scenario. At best it is a nominal overhead.

Web Components are a compromise through and through. As we know sometimes we need compromises.
But that is nothing to get excited about. And some compromises are better than others.

It was presented to me that in 10 years from now it's possible no one would be using Solid
but a Web Component would still be there with the same interface as today.
I thought for a moment and responded I'd still build it with Solid because that would be the best choice for today.
In 10 years even if I had to use a 10-year-old version of Solid it would be better than the Web Component version.
10 years doesn't erase that. And hopefully in 10 years I'd be using something better.

The decision is completely orthogonal. So in a sense there's nothing wrong with Web Components
as they are only able to be what they are. It's the promise that they are something that they aren't
which is so dangerous. The way their existence warps everything around them that puts the whole web at risk.
It's a price everyone has to pay.


[real-cost-2021] [2021-06-25 The Real Cost of UI Components Revisited](https://dev.to/playfulprogramming/the-real-cost-of-ui-components-revisited-4d23)

[pure-overhead] [2021-05-10 Components are Pure Overhead](https://dev.to/playfulprogramming/components-are-pure-overhead-hpm)
"The real potential is that our updates are not tied to our components. And that goes beyond one implementation of the idea."

## Comments
[All](https://dev.to/ryansolid/web-components-are-not-the-future-48bh/comments)

**Ryan** [2024-10-09](https://dev.to/ryansolid/web-components-are-not-the-future-48bh#comment-2ijlo)
answer to him having little Web Component experience: ... I've been using Web Components for over a decade.
I spent 7 years supporting a decent size app in production on web components. I introduced my company to them
and did the migration back in 2013 until the software was retired in 2020. I've also been the author and maintainer
of Web Component libraries, built and contributed to polyfills. I have a deep understanding of the specs
and features and the experience working with them every day for many years.

I'm glad that Web Components worked for you. They worked for us too. But that also doesn't satisfy
as a technical argument. I could bury you in technical arguments but I chose to summarize them
as the actual implications are much wider. As I stated in the article the Shadow DOM while problematic
isn't the only problem, component lifecycles, tension with attributes/properties, SSR, performance, and
most importantly the consideration this impacts on future platform decisions are all things we should be looking at.
People can definitely find success with them, but that doesn't mean they are arguably the least efficient way
to solve the problem. And their impact in existing is definitely not free.

**Ryan** answer to **Joe Pea** 2024-09-28 "Have you seen conversations in the Web Components Community Group discord?"
[on solving similar problems]:  Of course. I've been involved on a few of those discussions on the past.
I want to quote this from my article.
> I'm not saying someone couldn't find some interesting solutions to these problems but they all imply taking on the hidden cost
> due to the foundational misalignment of having the wrong abstraction. This is what makes the dialog here so difficult.
> It isn't something you improve. It's just a mistake for a number of things.
I have no doubt that in many cases similar systems to those in frameworks can be made. Context API is perfect example.
But due to the constraints of being DOM Elements the implementation differs in ways that make them less portable
or difficult to make compatible with what the frameworks are doing.
So we are stuck with something that can't be adopted by the framework and is generally incompatible.

**Joe Pea** 2024-09-30 But if an abstraction is not compatible with Solid, it's ok, maybe it is compatible with WCs.
Yeah Solid context API is not designed for Custom Elements, but that doesn't preclude a nice context API from existing for custom elements.

It is true that Solid context API is not portable for custom elements in general, but that's ok. Even without a context API, custom elements are useful. And there are context APIs that work with custom elements in general, and also context APIs specific to custom element frameworks. ...

Following some conventions for example, Lit [context API](https://lit.dev/docs/data/context/), or this simple
[context example](https://codepen.io/trusktr/project/editor/XbEOMk) I made in the Skate.js days, can work
reasonably well when sticking to the same convention in a single app.

It's not like everyone needs the same context implementation. Solid's is only good for Solid users,
it will never be good for other frameworks or even custom elements. The custom element contexts at least
have a chance of working across multiple frameworks, but that's still not good enough across multiple
Custom Elements with differing context APIs, and maybe that'll be impossible forever.

**Joe Pea** 2024-10-01 Ultimately custom elements are more compatible than you are making them out to be.

For example [Lume 3D](https://github.com/lume/lume) elements work in Solid, React, Vue, etc., and even provide
faster declarative data management than [react-three-fiber](https://r3f.docs.pmnd.rs/getting-started/examples).
The abilities it unlocks across all frameworks now and in the future is magnificent,
and truly is a testament to what is possible with them.

Like just because I cannot use a Solid context with Lume custom elements is nowhere near a problem that can outweigh benefits.

Making react-three-fiber work in Solid is a lot more difficult, and vice versa for [solid-three](https://github.com/solidjs-community/solid-three).

If Lume gains a killer feature everyone wants, it'll automatically work everywhere
and the cost will be small compared to the gain in abilities.

Completely slamming Custom Elements is too heavy handed. They're already here to stay and the best frameworks
will ensure they work well in their systems (not saying non-custom-element frameworks needs to output custom elements,
just that custom elements need to be easily consumable in them, that's where the major benefits are because
there are already other frameworks for writing them).

The user experience matters the most, and if I can out together a 3D application in any non-CE framework and
it works out fine by all standards of what a fine app is, then it means the custom elements did their job.

**Ryan** 2025-04-09 I don't have any problems with Web Components being less ambitious and leaving it to frameworks.
The challenge is using an *HTML Element* as the container comes with a lot of side effects.
Making it more difficult to make assumptions about how Elements behave.
This applies to things like property/attribute handling.
Like different casing for those and which ones reflect, values of strings vs other values.

When the ability of elements is constrained we can build to them specifically.
Sure there are weird special cases, so we special case them. We can treat them as the edge of our system.
Almost like a serialization format. This makes web frameworks translate to cross platform quite nicely
as we only need to abstract a DOM-like interface.
And in some cases like Server rendering we can just forget about the DOM and rely on HTML strings.

Now if we are just keeping to small VanillaJS written UI elements, that can represent all their behavior
efficiently through string attributes, that don't leverage the Shadow DOM (that's a whole other can of issues),
use lowercase event names (follow native event naming standards), and that only render in the browser.
There isn't much trouble there. It's anything beyond that where things get messy.

The thing is Web Component specs allow a lot more than this which means we can't make those assumptions.
It isn't even like higher level mechanisms that are at play, but just the way that the features complicate the baseline
that has far reaching implications.

**Ryan** [2025-04-09](https://dev.to/ryansolid/web-components-are-not-the-future-48bh#comment-2n019)
How do you render Web Components on the server? [DSD](../kb/DSD.md) is great as output format but not as an authoring format.
If you write components that require JavaScript to render properly then you aren't server rendering in your other language.
I don't see a problem with just having the webcomponent unrealized on the server and have the client fill it in.
It just isn't a great experience when your client experience expands out..
It's like the classic Login screen where the login prompt doesn't appear until the client side JavaScript runs.. it's odd.

And that's the thing because there is no standardization on the server you could argue they aren't designed
to extend HTML as much as the DOM. SSR is actually a really nasty area for Web Components when you have to consider
property serialization, lifecycle events (like if you use native events for Context APIs), and whatever else comes
from not having the DOM on the server.

It's not that I don't see potential value. It is just a miss. Maybe things can get there with specific solutions.
But then we are just talking about choosing frameworks. A couple years back when I was working on Marko at eBay
we have the Lit devs and WC proponents meet us to talk DSD. My manager was really stoked because he was one of those
back to basics, great at accessibility, sort of guys. And he was really intent to see if we could use more WC stuff.
And basically the response from the team from Google was that you basically need to use a framework on the server..
And then we were like then why wouldn't we use our own framework that is hyper optimized for server rendering
and hydration (eBay's main use case)? And there was no real answer to that.

If we were talking about good solutions it would be something else, but I've seen much better solutions
that don't use this technology and would be hindered by it.

**Ryan** [2025-04-09](https://dev.to/ryansolid/web-components-are-not-the-future-48bh#comment-2n077)
The problem for eBay was bringing the DOM to server is performance suicide. If React is too slow on the server
for big e-commerce so is the DOM. Obviously a non-JavaScript server runtime would be even better but
if you need the content of the component to be rendered on the server and also be updated on the client
and you have enough complexity to do so declaratively you are going to have tons of logic duplication if
you don't run JS on the server. Which takes all the way back to the start and wanting JS on the server to be efficient.

For what people not using JS on the server need, a element selector and script tag would be sufficient.
But the cost of web component support and warping of behavior around them on solutions trying
to be isomorphic in JS is a net negative.

**Ryan** [2025-04-10](https://dev.to/ryansolid/web-components-are-not-the-future-48bh#comment-2n14e)
If you have Web Components in the browser you will have **some** JS logic to render your Shadow DOM.
Like if somebody passes some attributes to your custom element that can change the output of your Shadow DOM.
It can change what attributes you set on those elements or even which elements you choose to show.

Now I'm on the server and there is no JS and I use the same Web Component in multiple places with different attributes being set.
Now I need to generate different DSD for each impacted attribute combination.
I don't have the JavaScript code, so I'm assuming I write the same logic again now in my server templating language?

This is what I mean by duplication. One perspective could be that you just don't render the Web Component on the server and let the browser take over. Just render the outer tag and be done with it. But if you are rendering the DSD that suggests
you have some sort of logic to render it which if applicable on both sides means duplicated logic.
Not to mention how to reconcile what is currently in the DSD with future client updates. We basically have hydration.

This is avoided if you don't ever update client attributes so you don't need the JS code at all. Then you could generate the *DSD* as you see fit and only write the code in a single language. But wouldn't you think it is a weird expectation to have a primarily client side mechanism with Web Components approach to server rendering basically be don't use the client? I don't know who would expect that.

...
