# Part 3 — Why AI Changes More Than the Way We Write Code

By now, you may have noticed a pattern.

Every major advancement in software development has removed friction.

Programming languages made computers easier to program.

The internet made information easier to find.

Open source software made solutions easier to reuse.

Git made collaboration easier.

Cloud platforms made deployment easier.

Each innovation improved the developer's workflow.

But notice something else.

None of these innovations actually participated in software development.

A search engine never understood your project.

A documentation website never inspected your code.

A version control system never suggested a better architecture.

A package manager never explained why your application was slow.

These tools were incredibly valuable.

But they were passive.

They waited for developers to use them.

Artificial Intelligence introduces something fundamentally different.

Instead of simply providing information or performing a single task, AI can actively participate in the development process.

That is the biggest change modern software engineering has experienced in decades.



## Information vs Understanding

Imagine you want to build a new feature for your Shopify store.

The feature should:

* Display recommended products.
* Match the existing design system.
* Support multiple languages.
* Follow your team's coding standards.
* Maintain good performance.
* Work correctly on mobile devices.

Let's compare how different tools help.

#### Google

Google helps you find information.

You still decide which information is useful.

```
Question

↓

Search Google

↓

Read Articles

↓

Choose a Solution

↓

Implement It Yourself
```

#### Documentation

Documentation explains how something works.

It doesn't know anything about your project.

```
Question

↓

Read Documentation

↓

Learn API

↓

Apply It Yourself
```

#### Stack Overflow

Stack Overflow gives you someone else's solution.

Your job is deciding whether it fits your project.

```
Question

↓

Find Similar Problem

↓

Copy Idea

↓

Adapt It

↓

Test
```

Now compare those workflows with an AI coding agent.

```
Goal

↓

Inspect Project

↓

Understand Existing Code

↓

Analyze Requirements

↓

Propose a Plan

↓

Implement Changes

↓

Verify Results

↓

Explain Decisions
```

Notice something remarkable.

The AI isn't simply answering a question.

It is performing work.



## The Shift from Searching to Collaborating

For almost thirty years, developers became experts at searching.

Good developers knew:

* what to search
* where to search
* how to compare different answers

Those skills remain valuable.

But modern developers are learning another skill.

Collaborating.

Instead of asking:

"How do I build a carousel?"

You might ask:

"Inspect this Shopify theme and recommend the best way to build a reusable product carousel that follows the existing architecture."

That is a completely different conversation.

The first question asks for information.

The second asks for engineering judgment.



## The Developer's Role Is Changing

This often surprises people.

Some believe AI means developers will write less code.

That is partly true.

But it is not the most important change.

The biggest change is that developers spend more time making decisions.

Think about the work involved in building a new feature.

Before writing a single line of code, someone must decide:

* What problem are we solving?
* Where should this feature live?
* How should it interact with existing code?
* Will it affect performance?
* Is it accessible?
* Is it maintainable?
* How will it be tested?

None of these questions disappear because AI exists.

If anything, they become even more important.

When implementation becomes faster, good decision making becomes more valuable.



## A Real World Scenario

Imagine you're working on a Shopify Plus store with thousands of daily visitors.

The marketing team asks for a new promotional banner.

A junior developer might immediately start writing code.

An experienced developer usually asks questions first.

* Should this be configurable?
* Should merchants control it through the Theme Editor?
* Will different markets need different content?
* Does it affect page speed?
* Should it be reusable?
* Does a similar component already exist?

Now imagine asking Claude Code to help.

A beginner might say:

> Build a promotional banner.

A more experienced developer might say:

> Inspect the existing theme architecture. Identify similar sections. Recommend whether this should be implemented as a new section or by extending an existing component. Explain the tradeoffs before making any changes.

Notice the difference.

The second request treats AI like a software engineer.

Not a code generator.



## Thinking at a Higher Level

Let's compare how a developer's focus has changed over time.

#### Earlier

```
Write HTML

↓

Write CSS

↓

Write JavaScript

↓

Fix Errors

↓

Ship
```

#### Today

```
Understand the Problem

↓

Design the Solution

↓

Guide the AI

↓

Review the Implementation

↓

Improve the Result

↓

Ship
```

Notice something interesting.

Coding is still present.

But it is no longer the entire job.



## AI Doesn't Remove Engineering

One concern many developers have is this:

"If AI writes code, why do companies still need software engineers?"

The answer becomes clearer once we understand what software engineering actually involves.

Writing code is only one activity.

Engineers also:

* Understand business requirements.
* Design systems.
* Review tradeoffs.
* Improve performance.
* Ensure accessibility.
* Protect security.
* Write tests.
* Review code.
* Communicate with stakeholders.
* Maintain software over many years.

AI can assist with many of these activities.

It cannot eliminate the need for engineering judgment.



## Shopify Example

Imagine Claude Code generates a beautiful new section for your Shopify theme.

The code works.

Everything looks correct.

Should you merge it into production?

Not yet.

A professional developer will still ask questions.

* Does it follow our architecture?
* Does it introduce duplicate logic?
* Is it accessible?
* Does it increase JavaScript size?
* Can merchants configure it easily?
* Does it work across all supported browsers?
* Does it affect Core Web Vitals?

These questions matter regardless of who wrote the code.



## Developer Note

One of the biggest misconceptions about AI is that it reduces the importance of software engineering.

In reality, AI increases the value of strong engineering practices.

When code can be generated quickly, architecture, review, testing, and long term maintainability become the biggest differentiators between average teams and exceptional ones.

Companies don't hire developers because they can type quickly.

They hire developers because they can solve complex problems responsibly.



## Common Mistake

Many beginners evaluate AI based on how much code it produces.

Professional developers evaluate AI based on whether it helps produce better software.

Those are very different goals.

The amount of generated code is rarely the most important measure of success.

Quality is.

Maintainability is.

Correctness is.

Business value is.



## Looking Ahead

Everything we've discussed so far leads to one final idea.

If AI can inspect projects, make plans, use tools, perform tasks, and verify results, what should we call this new style of software development?

That question introduces the central idea of this entire book.

Agentic coding.

In the final part of this chapter, we'll define what agentic coding is at a high level and explain why it represents one of the biggest changes in modern software development.
