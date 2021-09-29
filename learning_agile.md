# Learning Agile

Summary on [Learning Agile](https://www.oreilly.com/library/view/learning-agile/9781449363819/)
by Andrew Stallman and Jennifer Greene.

# Overview

Great teams start with great people, goals, practices.

It's not enough to do the right thing, you need to understand why.

Keep team members aligned to goals so they deliver more value. It takes an above average team to
change the way they work. Set people free to do their work. Understand the pros/cons of telling
people what to do. Choose a method that matches your mindset. Remove atmosphere of blame and
consequence of failure.

# Understanding Agile Values

From the Manifesto for Agile Software Development:

* individuals and interactions over processes and tools
* working software over comprehensive documentation
* customer collaboration over contract negotiation
* responding to change over following a plan

Teams that build great software using the waterfall process have a few common characteristics:

* good communication, especially consistently talking to users/managers/executives throughout project
* good practices, especially ones like code reviews and automated testing aimed at finding bugs as
  early as possible in the process
* drawers full of documentation that have rarely been opened, because writing down the plan and the
  questions during that planning is more important that sticking to it afterwards

Those characteristics exist in the Agile practice. For specific members on the team:

* developers: test driven development, automated builds, build servers
* project managers: Scrum, iterations, task board, velocity, burndown charts
* product owners: user stories, release plans
* team leads: daily standups, retrospectives

**User stories** are a great way to express one very specific need that a user has. Example:

> As a bar patron, I want to be able to play the newest hit that was just released today.

Each person on the team sees the story a bit differently:

* project manager: work to be done neatly packaged up and ready to build, keeps track of each status
  on the task board
* developer: small piece of functionality, written in simple easy to understand way. Can break it
  down into tasks, create a card for each task, assign them, build them, move on task board.
* product owner: value to be delivered, clear connection between what team is building and how user
  will use it. Makes sure each story represents something user needs
* team lead: specific goal team can organize around, helps prioritize, keep progress, and keep team
  motivated

Practices for each role:

* project manager: task boards, story points, prioritization, estimation, velocity
* team lead: servant leadership, information radiator, communication, planning
* product owner: backlog items, user stories, iteration, product backlog, relative ranking
* developer: refactoring, continuous integration, pair programming, version control, TDD

Individual team members add agile practices in areas that they're already good at. So the team
improves in all of these areas, together.

Key points:

* better communication helps team change for the better
* planning as a team is more important than over-documenting a plan and following it
* adopting agile practices improve on what teams do today
* adopting better practices leads to better than not doing it results
* user stories are an agile practice using a language that users can understand
* adopting individual practices one at a time is the most common way to adopt agile

Individuals and interactions over processes and tools: don't have your team blindly follow a process.
Agile practices support individuals and interactions. Daily standups and retros are for communication
and learning. User stories are less important than for fostering a conversation about what the story
means.

Working software over comprehensive documentation: software adds value. Documentation tends to
gather dust. TDD lets developers build automated tests, which kind of exist as a form of documentation.
It also helps to reframe how to improve documentation: wireframes, designs, embedded comments, etc...

Customer collaboration over contract negotiation: some teams may spend a lot of time discussing SLAs,
which is great for reducing risk with the boss. But it's better to have a product owner who is a
real, first-class member of the team. Who attends meetings, contribute ideas, and feels ownership for
the final product.

Responding to change over following a plan: constantly look for changes, make sure to respond
appropriately when there's a change in users' needs.

See <scrum.md> for notes about Scrum.

# Agile Principles

Twelve principles of Agile:

1. highest priority is to satisfy customer through early and continuous delivery of valuable software
2. welcome changing requirements, even late in development
3. deliver working software frequently, with preference to shorter timescale
4. most efficient/effective method of conveying information is face-to-face conversation
5. businesspeople and developers must work together daily throughout the project
6. build projects around motivated individuals, give them the environment/support necessary, trust
   them to get the job done
7. working software is the primary measure of progress
8. agile processes promote sustainable development (marathon not a sprint!)
9. continuous attention to technical excellence and good design enhances agility
10. simplicity (maximize amount of work not done) is essential
11. best architectures/requirements/designs emerge from self-organization teams
12. at regular intervals, teams reflect on how to become more effective, and should tune/adjust
  their behavior accordingly.

# Scrum and Self-Organizing Teams

Basic pattern for a Scrum project:

* three roles: Product Owner, Scrum Master, team member
* Product Owner works with team to maintain/prioritize a product backlog of features/requirements
* software built using timeboxed iterations called sprints
* sprint planning at the start of each sprint, to determine which features from backlog makes it in
* daily scrum to update each other on progress made and discuss roadblocks ahead: what have I done
  since last daily scrum, what will I do until next daily scrum, what roadbloacks are in my way?
* Scrum Master keeps project rolling to get past roadblocks
* at end of sprint, working software is demoed to product owner and stakeholders in sprint review,
  team holds retrospective to figure out lessons learned

Everyone must be committed to the project outcomes, not just be involved.

See <scrum.md> for notes about Scrum.

# Scrum Planning and Collective Commitment

Collective commitment means genuinely trying to make your software more useful.

User stories help build features your users will use. A good format to use:

> As a <type of user>, I want to <specific action I'm taking> so that <what I want to happen as a result>.

For example: As a returning user with a large friend list, I want to nominate one friend's video
for an achievement so that all of our mutual friends can vote to give him a star.

Stories should have conditions of satisfaction so developers can know when it's complete. It might
look like:

* a user can nominate a video for an achievement
* a user's friend is notified when his video gets an achievement
* a user can see all of the videos his friends have nominated
* a video with an achievement is displayed with a star next to it

Story points help understand how much effort you'll need to build a specific user story. Common
practices include using the Fibonacci sequence or exponential numbers. As sprints are completed,
you can see the project velocity of the team -- how many points are done in the sprint. Sprint
planning might go like this:

1. start with most valuable user stories from product backlog
2. take story in list, find similar sized story from previous sprint, and assign same number of points
3. discuss with team whether the estimate is accurate
4. keep going until stories in sprint fill up estimated velocity

If the team disagrees on points significantly, they're usually not disagreeing on how much work is
involved. They're disagreeing on what the story means.

Burndown charts help everyone to see at a glance how the sprint is actually progressing.

A sprint can be planned/run using stories, points, tasks, and a task board.

Every story gets broken down into smaller tasks. Stories and tasks are grouped together in the To Do
column of the task board.

# XP and Embracing Change

XP or Extreme Programming is another agile methodology. It's made up of practices, values, and
principles. Practices included:

* programming: TDD, pair programming
* integration: automated build, continuous integration
* planning: stories, themes (larger ideas), slack (team can add minor stories to each cycle)
* team: sit together, informative workspace (eg have a big task board)

# XP, Simplicity, and Incremental Design

Build software that can be extended and changed easily.

Simplicity starts with knowing how not to do too much.

XP teams look for code smells and antipatterns. They do their best to avoid them. Examples include:
shotgun surgery, half baked code, very large classes, duplicated code, spaghetti code, lasagna code,
and leaks between layers.

# Lean, Eliminating Waste, and Seeing the Whole

Lean is a mindset, sometimes called lean thinking. Values are:

* eliminate waste
* amplify learning
* decide as late as possible
* deliver as fast as possible
* empower the team
* build integrity in
* see the whole

What does Lean, XP, and Scrum have in common? Last responsible moment, amplify learning, and focus.

# Kanban, Flow, and Constantly Improving

Kanban's foundational principles:

* start with what you do now
* agree to pursue incremental, evolutionary change
* initially, respect current roles, responsibilities, and job titles

Kanban is not a system for managing projects. It's a method for improving your process. Find a
starting point, then evolve experimentally from there.
