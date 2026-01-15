---
title: "Why React is Ruining the Web"
date: 2026-01-15T16:45:00+07:00
description: "React has become the default choice for web development, but at what cost? Let's talk about how the React ecosystem has led us down a path of unnecessary complexity, bloated bundles, and websites that fail basic usability standards."
tags: [React, Web Development, JavaScript, Performance, Opinion]
---

React has become the default choice for web development, but at what cost? Let's talk about how the React ecosystem has led us down a path of unnecessary complexity, bloated bundles, and websites that fail basic usability standards.

I need to start with a confession: I've built dozens of production applications with React. I understand its appeal. The component model is elegant, the ecosystem is vast, and the developer experience can be quite nice. But somewhere along the way, we stopped asking whether we should use React and started assuming we must use it for everything.

The result? A web that's slower, more fragile, and more complicated than it needs to be.

## The Megabyte Problem

Let's talk about bundle sizes. A typical React application today easily ships 200-500KB of JavaScript to the browser. Some ship megabytes. Sure, you can optimize it, code-split it, lazy-load it—but the baseline is still enormous compared to what's actually needed.

Here's what bothers me: a simple blog post is often delivered as a React application. Think about that. To read text and maybe see some images, your browser downloads hundreds of kilobytes of JavaScript, parses it, executes it, and then—finally—renders the content you wanted to see.

The same content could have been served as plain HTML and CSS, loading instantly, working on any device, and consuming a fraction of the bandwidth.

We've normalized this bloat. We've accepted that websites should take seconds to become interactive. We've forgotten that the web was built on the principle of progressive enhancement, not "download half a megabyte of JavaScript or get nothing."

## JavaScript: The Single Point of Failure

Here's a uncomfortable truth: when you build with React, you're making JavaScript a hard requirement for your entire site.

If the JavaScript fails to load (unreliable mobile connection, corporate firewall, ad blocker interference), users get nothing. A blank page. Maybe a loading spinner that spins forever. Your content exists in the JavaScript bundle, and without it, there's simply nothing to show.

Traditional server-rendered HTML doesn't have this problem. The content is in the HTML itself. CSS progressively enhances the presentation. JavaScript adds interactivity. Each layer is optional and builds on the previous one.

React flips this model on its head. Everything depends on JavaScript executing successfully. It's fragile by design.

## The Complexity Spiral

Remember when building a website meant creating some HTML files, styling them with CSS, and maybe adding a bit of JavaScript for interactivity? Those days are gone if you're using React.

Now you need:

* **A build system** (Webpack, Vite, or similar) with complex configuration
* **A package manager** (npm, yarn, pnpm) managing hundreds of dependencies
* **A transpiler** (Babel) to convert your modern JavaScript and JSX
* **A development server** with hot module replacement
* **State management** (Redux, Zustand, Context API, or the library of the week)
* **Routing** (React Router, because browser navigation isn't enough anymore)
* **Testing infrastructure** (Jest, React Testing Library, etc.)
* **Type checking** (TypeScript, because JavaScript wasn't complex enough)

And this is just the basics. Want server-side rendering? Add Next.js. Want static generation? More configuration. Want to handle forms? Better add another library.

Each piece makes sense in isolation, but together they create a cognitive burden that makes web development inaccessible to beginners and exhausting for experienced developers.

## The Framework Treadmill

React's ecosystem moves fast. Too fast. Patterns that were considered best practices last year are now antipatterns. The recommended way to do things changes with each major version.

Class components? Deprecated, use hooks. Higher-order components? No, use render props. Wait, actually use hooks. Redux? Maybe use Context instead. Or Zustand. Or Jotai. Server components? Client components? Islands architecture? It never stops.

This churn has real costs:

* **Constant refactoring** just to stay current
* **Documentation that becomes outdated** within months
* **Stack Overflow answers that no longer apply**
* **Junior developers confused** by conflicting advice
* **Business value lost** to technical debt and migrations

Meanwhile, HTML, CSS, and vanilla JavaScript remain stable. Code written 10 years ago still works. You don't need to rewrite your entire application because the framework decided to change directions.

## Developer Experience vs User Experience

React optimizes for developer experience, often at the expense of user experience.

Yes, components are nice to work with. Yes, hot module replacement is convenient during development. Yes, the ecosystem has solutions for everything.

But your users don't care about any of that. They care about:

* **How fast the page loads**
* **Whether it works on their device**
* **If they can access content without JavaScript**
* **Battery life on mobile devices**

Every kilobyte of JavaScript you ship makes their experience worse. Every framework feature you add increases the chance something breaks. Every abstraction layer you introduce adds latency between their action and the response.

We've prioritized our convenience over their experience, and we've convinced ourselves this is somehow better.

## The "React for Everything" Mentality

The worst part isn't React itself—it's that React has become the default answer to every web development question.

Building a blog? React. Creating a landing page? React. Making a documentation site? React. Building an interactive dashboard? React (okay, this one actually makes sense).

We've lost the ability to choose the right tool for the job because we've been taught there's only one tool: React.

This one-size-fits-all approach leads to:

* **Over-engineering simple projects**
* **Unnecessary complexity for content-focused sites**
* **Poor performance for sites that should be nearly instant**
* **Excluding users on older devices or slow connections**

Not every problem needs a JavaScript framework. Most problems don't need React specifically.

## What Actually Needs React?

Let me be clear: React isn't evil. It's a tool, and like any tool, it has appropriate use cases.

React makes sense for:

* **Complex, highly interactive applications** (admin dashboards, data visualization tools)
* **Real-time collaborative interfaces** (think Google Docs or Figma)
* **Applications with complex state management** across many components
* **True single-page applications** where navigation without page reloads provides genuine value

But a blog? A portfolio site? A company homepage? A documentation site? None of these need React. They need HTML, CSS, and maybe a sprinkle of JavaScript.

## The Path Forward

So what's the alternative? How do we build for the web without falling into the React trap?

**Start with HTML.** Write semantic markup. Use proper headings, paragraphs, lists, and links. This is your content, and it should work without anything else.

**Layer in CSS.** Make it look good. Use modern CSS features like Grid and Flexbox. They're powerful, well-supported, and don't require a build step.

**Add JavaScript sparingly.** Use it to enhance, not to create the foundation. Vanilla JavaScript is more capable than ever—you might not need a framework at all.

**Consider static site generators.** Tools like Hugo, Jekyll, or Eleventy give you templating and organization without shipping a JavaScript framework to the browser.

**Use progressive enhancement.** Build in layers where each layer is optional but makes the experience better if available.

**Choose boring technology.** The cutting-edge is exciting, but boring technology is reliable, well-documented, and won't require a rewrite next year.

## A Call for Restraint

I'm not saying abandon React entirely. I'm saying we need to be more thoughtful about when and why we use it.

Before reaching for React, ask yourself:

* Does this project truly need client-side interactivity?
* Could this be server-rendered HTML with minimal JavaScript?
* Will the complexity of React provide value that outweighs its costs?
* Am I choosing React because it's the best tool, or because it's familiar?

Be honest with your answers. Your users will thank you.

## Final Thoughts

React revolutionized how we think about building user interfaces. Its influence on web development has been profound and largely positive. But its dominance has created blind spots.

We've forgotten that the web is fundamentally built on HTML, CSS, and JavaScript—in that order of importance. We've normalized bloated bundles and complex build processes. We've made web development harder than it needs to be, and we've made the web slower and more fragile in the process.

The web doesn't need to be this complicated. We made it this way by choosing React for everything.

Maybe it's time to choose differently.
