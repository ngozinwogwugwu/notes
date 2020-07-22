When we model something, we want to _abstract out_ everything related to the problem and _ignore_ everything else. Your aim isn't realism.
**A software's domain**: everything that a user applies a program to. Ideally, it's
  - organized
  - abstracted

It's easy for programmers to ignore the domain in favor of the technical side of things. This is a mistake. The domain is important because is has a profound effect on:
- the design of your software
- how members of a team _think_ about a problem
- how members of a team _talk_ about a problem

## Developing Models as a Team
**Here's the wrong way to create a model**: As an expert to explain the domain to an analyst. Then have the analyst describe things to a programmer
  - there's slow (or no) feedback
  - programmers won't understand the underlying logic

**Here's the alternative**: create models as a team
  - devs can create prototypes right away
  - everyone can see which concepts are unimportant
  - everyone gets immediate feedback

## Continuous Learning
Non-technical domains seem less daunting, but that's because we don't know what we don't know. We're probably making a bunch of false assumptions
- all projects leak knowledge. Solutions to this are to pair, keep documentation, and to make good models

## Knowledge rich design
"find the nouns" isn't enough -> you also have to consider business activities and rules

Business rules can be complex
  - domain experts can be unaware of the complexity of their domain
  - there are often inconsistencies
 
For business rules, write functions. Don't store the logic in the main code
  - makes code more readable
  - allows for a shared language and understanding of rules

## Deep Models
Sometimes the obvious stuff (like the state of an object in the physical world) is not relevant. The relevant thing could be surprising (like the legal status of that object)
