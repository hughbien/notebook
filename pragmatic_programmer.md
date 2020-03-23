# Pragmatic Programmer

Notes on [The Pragmatic Programmer](https://pragprog.com/book/tpp20/the-pragmatic-programmer-20th-anniversary-edition)
by David Thomas and Andy Hunt.

This book is for programmers who want to be more effective and productive. A Pragmatic Programmer
has a few characteristics:

* early/fast adopter
* inquisitive
* critical thinker
* realistic
* jack of all trades
* cares about your craft
* may work in large teams, but realizes there's room for individuality and craftsmanship
* realizes it's a continuous process - "Kaizen"

# Pragmatic Philosophy

*It's your life. You own it, you run it, you create it.* If your work environment sucks, your job
is boring, fix it! Either fix what's wrong with your job or change jobs. The industry has plenty
of opportunities. Be proactive and take them.

*Take responsibility.* Provide options and don't make excuses. Be upfront about your own shortcomings,
whether it's ignorance or mistakes. Figure out what actions you can take to learn or to not repeat
bad issues.

*Don't live with software rot.* Entropy refers to the amount of disorder in a system and the law
of physics guarantees that entropy in the universe grows towards a maximum, unless energy is spent
fixing it. Software is the same. Spend time fixing technical debt.

*Be the catalyst for change.* If there's a big project that needs to be done, make the first step
yourself. It's easier to recruit people after you've made the initial changes.

*The scope and quality of a system should be discussed.* Software quality is a requirements issue.
When is it good enough to ship? Don't waste additional time making it better for no reason.

*Build and invest regularly in your knowledge portfolio.* Invest regularly as a habit, diversify,
balance between high-risk and high-reward, review and re-balance periodically. Try learning a new
language every year. Read a technical or non-technical book every month. Take classes. Participate
in local user groups. Experiment with different environments. Stay current. Think critically:
ask the five whys, who does this benefit, what's the context, when/where would this work, why is
this a problem.

*Communicate effectively.* Know your audience, know what you want to say, choose the right moment,
choose a style, make it look good, involve your audience, be a listener. Build documentation in
instead of bolting it on.

# Pragmatic Approach

*Code is well designed when it's ETC (Easier to Change).* That's why decoupling, single responsibility,
and naming are so important. It makes things easier to change.

*DRY - Don't Repeat Yourself.* Don't duplicate knowledge or intent. When a single fact of code has
to change, do you need to change it in multiple places? Don't duplicate in documentation either.
Make things easy to reuse to discourage duplication.

*Code should be orthogonal.* Eliminate the effects between unrelated things. This makes changes
localized, promotes reuse, makes systems less fragile, and reduces risk. An easy test: ask yourself
if I dramatically change the requirements of a particular function, how many modules are affected?
Keep your code decoupled, avoid global data, avoid similar functions (use the Strategy pattern).

*There are no final decisions.* Try your best to not lock in a certain third-party vendor. When
making decisions in your project, consider if its reversibility.

*Use tracer bullets for debugging.* Tracer bullets are loaded alongside regular ammunition in guns.
When shot, they leave a pyrotechnic trail. If tracers hit their target, then so do regular bullets.
Soldiers use them to refine their aim in the dark. It's a real-time feedback system under actual
conditions. We can do that for production systems too. A software tracer bullet is as simple as
printing "Hello World" to make sure it compiles and run.

*Prototype to learn.* When building a prototype, you can ignore: correctness, completeness, robustness,
and style. Prototypes are made for learning and then throwing away.

*Program close to the problem domain by using domain languages.* Programming languages influence how
you think about a problem. Writing code using the vocabulary of the domain makes things easier.
Example domain languages: RSpec, Cucumber, Phoenix Routes, Ansible.

*Estimate to avoid surprises.* The recommended unit to estimate in:

|   Duration | Quote Estimate In |
| ---------: | ----------------- |
|  1-15 days | days              |
|  3-6 weeks | weeks             |
| 8-20 weeks | months            |
|  20+ weeks | break it down...  |

Choose the unit that best conveys the accuracy you intend. Build a model of the system and break it
into components to help with estimates. Keep track of your estimates and their accuracy over time.
Hedge your estimates with qualifiers and risk. When asked for an estimate you're not prepared to
give, just say "I'll get back to you."

# Basic Tools

*Keep knowledge in plain text.* Binary formats artificially divorces data from its meaning, it
may as well be encrypted. Plain text -- which can be structured such as HTML, JSON, or YAML -- is
insurance against obsolescence. It's usable with virtually every tool: editors, version control
software, or command line scripts. It's easier to test. It's the lowest common denominator.

*Use the power of command shells.* Get familiar with the shell and you'll find your productivity
soaring. Want to create a list of unique package names explicitly imported by your Java code?

```sh
grep 'import ' *.java |
  sed -e 's/^import  *//' -e 's/;.*$//' |
  sort -u > list
```

In the same way a woodworker customizes their workspace, a developer should customize their shell.
Make it your own by: setting color themes, configuring a prompt, setting aliases/functions, and
adding command completions.

*Achieve editor fluency.* The main benefit is that you'll no longer have to think about the mechanics
of editing. Examples of tasks you should be able to do without a mouse:

* move/make selections by character/word/line/paragraphs
* move by syntactic units (delimiters, functions, modules, ...)
* move to specific line number
* re-indent code following changes
* comment/uncomment blocks of code
* undo/redo changes
* split editor window into multiple panels and navigate them
* search a string or by regular expression
* temporarily create multiple cursors based on a selection pattern match, edit text in parallel
* display compilation errors
* run project's tests

Common tasks should become muscle memory. You should be able to grow your editor by adding your
own features via plugins.

*Always use version control.* Version control lets you undo mistakes, track changes, find authors.
It allows multiple authors to work concurrently on the same file. It allows branches of isolation.
It works as a great project hub, where it's the center of: pull requests, automated builds and tests,
reporting, automated push to production. Some things to put in version control besides project code:
user preferences, dotfiles, editor configuration, Ansible scripts.

*Get into the debugging mindset.* Fix the problem, not the blame. Don't panic. Either watch the user
reproduce the bug or get exact reproduction steps (including actual vs expected results). Create a
failing test before fixing code. Add logging/tracing. Remember that system calls isn't broken - it's
your code that's broken. Consider why this bug wasn't caught earlier and how you can fix the process.

*Learn a text manipulation language.* Like awk, sed, Python, or Ruby. They let you quickly hack up
utilities or prototype ideas. For example, Ruby was used to build the book: layout, syntax highlighting,
website update, equations, and index generation.

*Keep a journal.* An engineering daybook is a journal where you can record: what you did, what you
learned, sketches of ideas, readings, meeting notes, debugging notes, reminders, wild ideas,
basically anything about work.

# Pragmatic Paranoia

*Design by contract.* Use preconditions (what must be true before routine is called), postconditions
(what must be true after routine is called), and class invariants (what always must be true). In
Elixir, use guard clauses. Use assertions if your language doesn't support guard clauses.

*Crash early.* Detect problems as soon as possible and crash early. The alternative is to continue
with corrupted data, perhaps writing it to a database and entering a non-recoverable state. Have
supervisor processes which are responsible for managing crashes.

*Use assertions to prevent the impossible.* If something should never happen, add an assertion. Leave
assertions turned on in production.

*A routine should finish what it starts.* Be symmetric with your routines. For example, if it allocates
a resource, make sure it deallocates at the end. If it opens a file, make sure it closes the file
at the end of the routine.

*Take small, deliberate steps -- check for feedback to adjust before proceeding.* Small steps include
using your REPL to understand APIs and algorithms. Or utilizing unit tests. Or getting feedback from
user demos before committing to a UI.

# Bend or Break

*Decoupled code is easier to change.* Minimize chains of method calls, globalization, and inheritance.
**Tell, don't ask.** Encapsulate behavior in the class. For example, don't ask for a calculation of
a discount and then apply it to an order object. Have a method on the order object that does the
calculation and state change itself.

*Design responsive architectures.* Responsive architectures are made up of: events, finite state
machines, observers, publish/subscribe, reactive programming and streams. An event represents the
availability of information: a UI event or stock quote update. FSMs help untangle messes by
organizing your code into states and transitions. The observer and pub/sub patterns decouple
asynchronous events. Reactive programming and streams are extremely helpful for constant flow of
events, such as UIs.

*Design programs around transforming data.* Programming is about code, but programs are about data.
Think about how Unix commands can be chained: `find . -type f | xargs wc -l | sort -n | tail -6 `.
Or how the pipe operator works in Elixir. Don't hoard state, pass it around. In a transformational
model, don't think of data as being spread out everywhere. Instead data should be a river that flows.

*Don't overuse inheritance.* Inheritance is coupling. A child is coupled to its parent and ancestors.
Code that uses the child is coupled to all ancestors. Instead consider using: interfaces, protocols,
delegation, mixins, or traits. Interfaces are great for polymorphism. Delegation works for "has-a"
relationships whereas "is-a" can be reserved for inheritance. Use mixins to share functionality.

*Parameterize your app by using external configuration.* Common configuration includes: credentials,
logging, port/ip, environment variables, formatting details, or license keys. Configure these outside
the main body of code. Consider wrapping your configuration in a thin API.

# Concurrency

*Break temporal coupling.* Look for ways to improve concurrency and parallelism in your application.

*Shared state is incorrect state.* Shared state causes non-atomic updates. You'll have to create
code to implement mutual exclusions.

*Use actors and processes.* An actor is an independent virtual processor with its own local state.
A process is a more general purpose virtual processor, implemented by the OS. Actors can be used
for concurrency without shared state.

*Use the blackboard approach for concurrency.* With this approach, clients don't need to know the
existence of others. It's a form of laissez faire concurrency.

# While You're Coding

*Listen to your instincts.* You've accumulated experience and wisdom as a developer. When you feel
a nagging doubt or experience reluctance, heed it. Give yourself a little time and space to let your
brain organize itself. Try externalizing the issue. Embrace playtime. Try prototyping.

*Program deliberately instead of by coincidence.* Don't rely on undocumented errors or boundary
conditions. Fix phantom patterns, such as a log file that shows intermittent error every 1000 requests,
and prove the fix with a test. Always be aware of what you're doing. Be able to explain your code
in detail. Rely on reliable things. Document assumptions. Test your code and assumptions.

*Estimate the order of your algorithms.* Use common sense to determine if your algorithm will fall
within: simple loops, nested loops, binary chop, divide and conquer, or combinatoric. Use Big O
notation. Test your estimates with code profilers.

*Refactor early and often.* You should refactor when you've learned something. Don't hesitate to
change it. Refactor to remove duplication, non-orthogonal design, out-dated knowledge, updated
usage, improved performance, small scale tests passing. Don't refactor and add functionality at the
same time. Make sure you have good passing tests first. Take short deliberate steps.

*Write tests.* Testing is more than about finding bugs, testing should also drive design. Tests
are the first users of your code. Use TDD: decide on a small piece of functionality to add, write
a test, run test to verify it fails, write smallest about of code to get it to pass, refactor. Do
it in very small, deliberate cycles. But don't worry about having 100% test coverage, don't make
redundant tests. Use pre and post conditions.

*Build end-to-end, not top-down or bottom-up.* Top down means starting with the big picture, breaking
it up into smaller pieces, and implementing those chunks. Bottom up means building the foundation
for some abstractions, then slowly adding layers until the initial problem is solved. Neither works
because we don't know what we're doing when we start. Build software incrementally, in small pieces,
learning about the problem as you go.

*Use property-based testing to validate assumptions.* Code has contracts and invariants. Once those
are worked out, you can use them to automate testing with property-based testing. This helps asserts
your inputs and makes you think of your code in terms of invariants and contracts. It's complementary
to unit testing. You supply contracts about what sort of inputs are allowed and a framework will
generate random inputs that meet those criteria to ensure output is still correct.

*Minimize attack surfaces.* Code complexity leads to attack vectors, can you make things simpler?
Never trust data from an external entity, it needs to be sanitized. Keep the number of authorized
users to an absolute minimum. Don't leak data from output data (eg "Password is used by another
user") or debugging info. Use the least amount of privilege for the shortest time. Don't let
processes be signed in as root permanently. Secure application defaults. Encrypt sensitive data.
Maintain security updates. Don't do crypto yourself, rely on a third party library.

*Name well, rename when needed.* When naming things, consider answering "why?" Instead of naming
a variable `user`, consider using `customer` or `buyer`. Instead of a method named `deductPercent`,
use `applyDiscount`. Name things with clarity. Also consider consistency, for example stick with
camel case or snake case for class names or method names.

# Before the Project

*No one knows exactly what they want.* Programmers help people understand what they want. Requirements
are learned in a feedback loop. It's a process that goes back and forth between your client and you.
Work with a user to think like a user. Walk in your client's shoes and your user's shoes.

*Maintain a project glossary.* Users and domain experts will use terms that have specific meaning
to them. A project glossary is one place that defines those specific terms. All participants in the
project should use it to ensure consistency.

*When faced with an impossible problem, enumerate all possible avenues.* Find the real constraints
of the problem. Some solutions may seem impossible but are do-able. Why are you solving this problem?
What's the benefit of solving it? Are there edge cases you can eliminate? Is there a simpler, related
problem you can solve?

*Don't go into code alone.* Try pair programming. Then try mob programming that involves more than
two programmers. Build the code, not your ego. Criticize the code, not the person. Listen and try
to understand others' viewpoints. Conduct frequent retrospectives to try and improve for the next
session.

*Agile is how you do things.* Value individuals and interactions over processes and tools, working
software over documentation, customer collaboration over contract negotiation, responding to change
over following a plan.

# Pragmatic Projects

*Maintain small, stable teams.* Pragmatic teams are small, under 10-12 members. Schedule time for
team improvement: system maintenance, process reflection and refinement, new tech experiments,
learning and skill improvements. Give each member the ability to shine in their own way. Give just
enough structure to support them.

*Do what works, not what's fashionable.* Figure out what works by trying it out.

*Use version control, regression testing, and full automation.* Use version control to drive builds,
tests, and releases. Test early, often, and automatically. Do unit testing, integration testing,
validation and verification, performance testing, saboteur testing, tests coverage. Don't use
manual procedures.

*Delight your users.* Don't just deliver code. Think about the underlying expectations of value
in your project. What are your users' expectations?

*Sign your work.* Be proud of your work. You wrote it, stand behind it.
