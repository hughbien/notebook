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

# Pragmatic Paranoia

# Bend or Break

# Concurrency

# While You're Coding

# Before the Project

# Pragmatic Projects
