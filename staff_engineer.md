# Staff Engineer

Notes on [Staff Engineer](https://staffeng.com/book) by Will Larson.

Larson runs <https://staffeng.com> - where you can find plenty of resources on being a staff plus
engineer. As an individual contributor, the career ladder looks like:

1. early-career
2. mid-level
3. senior
4. staff
5. principal
6. distinguished

Where 4-6 operate at the "staff plus" level. This is a separate ladder from the management track:

1. early-career
2. mid-level
3. senior
4. manager
5. senior manager
6. director
7. vice president

There's a lot of ambiguity around staff-plus roles. What skills should you develop? Are technical
abilities alone sufficient? What is your manager's role in helping you? Will you enjoy it? This book
will answer these questions.

The Staff Engineer Archetypes:

* *Tech Lead* - guides approach/execution of a single team. Partners closely with single manager.
  Many companies use a Tech Lead role that rotates, some use a Tech Lead title that's permanent.
  Most common archetype.
* *Architect* - responsible for direction/quality/approach within a critical area. Combines in-depth
  knowledge of technical constraints, user needs, organization level leadership.
* *Solver* - digs deep into arbitrarily complex problems to find path forward. Some focus on given
  area for long periods. Others bounce from hotspot to hotspot.
* *Right Hand* - extends executive's attention, borrowing scope/authority to operate complex
  organizations. Provide additional leadership bandwidth to leaders of large scale organizations.

What do Staff engineers actually do:

* set technical direction - think of them like a part-time product manager for technology. More
  focus on long-term trend of progress.
* mentorship and sponsorship - you're more likely to change a company's long-term trajectory by
  growing engineers around you than personal heroics. Best way to do that is mentorship/sponsorship.
  Share your experience/advice, build ongoing relationship.
* provide engineering perspective - when processes are being developed or roadmaps are planned,
  staff engineers are pulled into the room to help with decisions to provide engineering insight
* exploration - often times you'll need to research and develop a proof of concept, to explore
  possible solutions for particularly hairy/unknown problems.
* being glue - doing the needed (but often invisible) tasks to keep the team moving forward and
  shipping. Not glamorous but high impact.

Most Staff Engineers write some code, some write none, none write as much as they used to earlier
in their career.

# Operating at Staff

**Work on what matters** since we have a finite amount of time to live, with a fraction of that
dedicated to work. Ruthlessly prioritize. What is high impact, low effort work? Next -- what is
high impact, high effort work (avoid snacking on low impact work).

There are four quadrants of work:

1. low effort, high impact
2. high effort, high impact
3. low effort, low impact
4. high effort, low impact

You want to tackle work in the first quadrant first: low effort, high impact.  Obviously high
effort, low impact work should be de-prioritized. But what about quadrant 2 vs 3? Avoid snacking
on low effort, low impact work. Prioritize high effort, high impact work instead. Always prioritize
high impact work. Avoid **preening** - or doing low impact high visibility work.

Work where there's room and attention. Find work that's worthy of attention, that's a good
opportunity to invest in. Foster growth. One area that's often underinvested but can be highly
leveraged: growing the team around you. This includes: hiring (usually invested in more than rest), 
onboarding, mentoring, coaching. Devote a few hours a week to developing the team around you.

**Write an engineering strategy** to guide your organization via its architecture, technology
selection, and organizational structure. Write five design documents, pull the similarities out,
that's your strategy. Write five strategies, forecast their implications two years out, that's your
vision.

Design docs (also called RFCs or tech specs) describe the decisions/tradeoffs you've made in a
specific project. A good design doc describes a specific problem, surveys possible solutions, and
explains the selected approach's details. There are many templates on the internet to choose from.

You should write a design doc for any project: that lays the foundation for future projects, that
will be used by future projects, that has meaningful impact on users, any work that takes more
than a month of engineering time. Recommendations:

1. start from the problem
2. keep the template simple
3. gather and review together, write alone
4. prefer good over perfect

Synthesize five design docs into a strategy. Look for controversial decisions that came up in
multiple designs. Good strategies guide tradeoffs and explain rationale behind the guidance. Advice:

1. start where you are - don't wait for missing information, whatever you write will need to be changed
2. write the specifics - write until you start to generalize, then stop writing. Being specific
   creates alignment. Generic statements create illusion of alignment.
3. be opinionated
4. show your work - it builds confidence, show the reasoning behind your opinions

For a useful vision, focus on: writing two/three years out, ground in your business/users,
being optimistic rather than audacious, staying concrete/specific, keeping it one/two pages long.

**Curate technical quality** to maintain the quality of company's architecture and software as it
grows.

Low technical quality isn't a crisis; it's expected. Engineers generally make reasonable quality
decisions. Successful companies will raise their quality bar over time as they scale/pivot. Closing
the gap should be a routine, essential part of effective engineering leadership. Things to do:

1. fix the hot spots that are causing immediate problems -- learn from it
2. adopt best practices that are known to improve quality
3. prioritize leverage points that preserve quality as software changes
4. align technical vectors in how your org changes software -- by giving direct feedback, refining
   the engineering strategy, encapsulating approaches in workflows/tooling, training new members,
   and curating technology change
5. measure technical quality to guide deeper investment -- what percentage of code is statically
   typed, test coverage, public vs private code, preferred libraries usage, do API endpoints respond
   under 500ms, etc...
6. spin up a technical quality team to create systems/tools for quality
7. run a quality program to measure/track/create accountability

Technical quality is a long-term game. There's no winning, only learning while you keep playing.

**Stay aligned with authority** to remain an effective leader. Never surprise your manager. Don't
let your sponsor surprise you. During 1:1s, dig for the feedback. Ask if there are other areas you
should be focused on. Proactively feed your manager's context -- don't bring them a problem to solve,
just convey information you think will be useful.

**To lead, you have to follow** to blend your vision with the visions from your peers and leadership.

Effective leaders move in/out of leadership/follower roles with folks around them:

1. be clear what your true priorities are, don't dilute yourself across everything that comes up. If
   you disagree in a minor way, let others take the lead.
2. give support quickly to other leaders working to make improvements. Even if you disagree with
   their approach, someone trustworthy will almost always get a good outcome. If they continue down
   a path you disagree after giving your feedback, consider why you're struggling with influencing
   or if you're bad at feedback.
3. make feedback explicitly non blocking. For example: "optional nit".

**Learn to never be wrong** shift away from being right and towards understanding/communication.
Stop spending social capital repairing relationships frayed by conflict. Collaborate with folks
with different priorities/perspectives.

Listen through questions - active listening with the goal of understanding the rest of the room's
perspectives. Ask good questions with good intent to open conversation. Ask with the desire to learn.

Define the purpose - make sure to define the goal. "Just to check, our goal here is to decide
whether to postpone launching the project by two weeks?"

Read the room - often folks get frustrated with a conversation and try to force agreement, which
doesn't conclude well. Consider escalating to the appropriate party outside the room.

Jerks don't compromise, don't listen. Two best ways to deal with jerks are: include someone they
can't be a jerk to in the meeting (like their manager), invest heavily into aligning with them before
the meeting so they feel heard.

**Create space for others** so your team grows stronger than your contribution. You'll need to
transition from being the "go-to" person for the organization. Your team/organization must
operate without your contributions.

* shift contribution towards asking questions
* pull people into the conversation, it works best when you pull in exactly one person at a time
* be the one to take notes
* if you realize someone is missing, pull them in for the next occurrence of the meeting

When critical work comes your way, ask "who could be both successful with and grow by doing this
work?". Sponsor others to get to the staff-plus role. Let it be their work. Be available to
counsel, give advice, provide context, mentor.

**Build a network of peers** to vet difficult decisions and give you honest feedback.

* never fight feedback
* don't evade responsibility or problems
* don't present a question without an answer
* avoid academic style presentations
* don't fixate on your preferred outcome

# Getting the Title Where You Are

Beyond the "Senior" career level, you'll want to:

1. work on your promotion packet, the foundational tool to demystify the staff promotion
2. realize a "staff project" isn't necessary, but is a common approach to attain the level
3. get into the room where the decisions happen, and stay there
4. become visible internally

Opportunities at a given company is usually unevenly distributed. Some companies view infrastructure
engineering as more complex/leveraged than product engineering. Your work might be more visible if
you work in the company headquarters vs a distributed office.

The general template for promotion packets:

* what are your staff projects? What did you do? What was the project's impact? What made the
  project complex? Keep it short and link to supporting design documents.
* what are high leverage ways you've improved the organization
* what is the quantifiable impact of your projects (did you increase revenue by $10 million?)
* who have you mentored and through what accomplishments?
* what glue work do you do for the organization? What's the impact?
* which teams/leaders are you familiar with and advocates for your work? What do they value about
  your work? Include data or survey data when possible.
* do you have a real or perceived skill or behavior gaps that might hold you back? For each, how
  would you address the concern?

Some advice: answer why you're doing this. Temper your expectations -- it'll take a while. Bring
your manager into the fold. Write the promotion packet, edit it, edit it with peers, edit it with
your manager, periodically review it with your manager.

Find your sponsor -- the second most important member of the team guiding your promotion after
yourself. Usually this is your direct manager, but you may need additional sponsorship.

A large minority of Staff engineers complete a Staff project (though not all!). It's a viable path
to Staff. A Staff project is considered complex and important enough that the person who completes
it has proven themselves as a Staff engineer.

While you can reach Staff without it, completing a Staff project will grow/stretch you as an
engineer. They're effective because they are:

* complex and ambiguous
* have numerous and divided stakeholders
* named bet where failure matters - important enough that senior leadership talks about it, so
  failures will be visible

To get a Staff project: stay aligned with leadership team, be visible and known for technical
aptitude, being patient for whenever the opportunity becomes available.

# Deciding to Switch Companies

You can deliberately choose to interview at earlier stage companies who are likely to value your
experience more highly. It also brings an automatic sponsor via the hiring manager.

Find a place that disproportionately values you. For example, Stripe valued an engineer's work on
compilers for their work on Sorbet.

Most companies will be looking for one or two specific archetypes. Figure that out before
interviewing.
