# Video script: What I’ve been building lately

**Working title:** I’ve been behind on videos, but I accidentally built a bigger brain  
**Alt title:** What I’ve been building: Hermes, LLM wikis, memory, CMS experiments, and practical agents  
**Target length:** 7–9 minutes  
**Tone:** personal, slightly messy, concrete, anti-hype  
**Primary on-screen asset:** `youtube-comeback-one-pager-2026-06-10.html`

---

## Cold open

I’ve been pretty behind on making videos.

Not in the “I disappeared because I stopped caring” way. More like: every time I sat down to make a video, I realized the thing I actually wanted to talk about had changed underneath me again.

I’d start with “I should make a video about AI agents.”

Then I’d realize I needed to explain memory.

Then I’d realize I needed to explain my second brain.

Then I’d realize I was comparing WordPress, Sveltia, Keystatic, TinaCMS, EmDash, Astro, Cloudflare, and a bunch of agent workflows.

And then, at some point, I had to admit the annoying truth:

I wasn’t just testing tools anymore. I was accidentally building a new operating system for how I think and work.

So this video is a reset.

Not a polished “here is my perfect system” video. It is more of a “here is what I’ve been doing, here is what I think is interesting, and please tell me which part you actually want to see more of” video.

---

## Show the one-pager

[ON SCREEN: show the one-pager]

This is the rough map.

The short version is: I’ve been behind on videos, but not idle.

A lot of my work has shifted from talking about individual tools to building the systems behind the tools.

Memory systems. CMS experiments. Agent workflows. A public knowledge base. A way to turn messy thoughts and conversations into something I can actually reuse.

And honestly, that is the part I’m most interested in right now.

Not “which AI tool is best this week?”

More like: what happens when your tools can remember context, search your past work, update your notes, and help you pick up a thread from three weeks ago without starting from zero?

That feels like the actual shift.

---

## Part 1: The second brain thing

A few weeks ago I wrote a post called “I accidentally expanded my brain.”

That sounds a little dramatic, but it was the most accurate way I could describe it.

What changed was not that I found one magic app.

It was not Obsidian. It was not Notion. It was not Hermes. It was not Claude Code. It was not RAG or MCP or any one of the acronyms.

The change was that I started designing systems around the fact that I am not naturally organized.

That was the important unlock.

For a long time, I think I had this quiet assumption that a good knowledge system required me to become a different kind of person.

Someone who tags things perfectly.
Someone who writes clean meeting notes.
Someone who files every idea in the right place the first time.
Someone who remembers to update the source of truth after every conversation.

That person does not exist. At least not in my house.

So the system I’m building now has a different assumption:

My job is to dump the raw material.

The assistant can help with the filing.

That is the pattern I keep coming back to:

Capture what is happening.  
Turn raw context into structured knowledge.  
Use that knowledge to make better decisions.  
Then automate the pieces that keep repeating.

That’s the whole game.

And that is why I’ve been building this public LLM wiki / second brain thing.

Right now it has about 45 articles across 11 topic areas. AI agents, WordPress, Cloudflare, design-to-dev workflows, Team RAG, CMS architecture, tooling, and a bunch of practical implementation frameworks.

The point is not to collect notes forever.

The point is to metabolize what I’m learning.

There’s a big difference between recognizing a concept and actually knowing what to do with it.

I can say “RAG” or “MCP” or “agent memory” and sound like I know what I’m talking about. But that is not the same as asking:

Where does the agent live?  
What tools can it access?  
What memory does it have?  
Who reviews what it does?  
What happens when it is wrong?  
What should be automatic, and what should stay manual?

Those are the questions that turn vocabulary into working knowledge.

That is the kind of content I want to make more of.

---

## Part 2: Hermes as more than a chatbot

The other big piece here is Hermes.

I know I’ve talked about Hermes before, but I think my mental model for it has changed.

At first, it is easy to think of Hermes as another AI chat interface. You ask it a question, it uses tools, it helps with files, it runs commands, whatever.

But the more interesting thing is Hermes as a persistent agent environment.

That means memory across sessions.

It means session search, so old conversations do not just disappear into the void.

It means skills, which are basically procedural memory. If we figure out a good workflow once, we can save the approach and reuse it later.

It means cron jobs and scheduled research. The agent can go look for something every day, summarize it, and bring it back.

And then there is the LLM wiki piece: taking conversations, research, links, and random build notes, then turning them into durable articles that live in a Git-backed knowledge base.

This is where the Mnemosyne idea comes in for me.

I’m using that word loosely here, not as a perfect formal system. I mean memory as a cycle:

Capture. Recall. Synthesize. Reuse.

That is the part that starts to feel different.

A normal chatbot forgets the work.

A better assistant can remember some preferences.

But a useful working environment can search the history, understand the project, update the wiki, and help me continue from where I actually left off.

That is what I want to explore more.

Because if you are a developer, designer, agency person, content creator, consultant, or just someone with too many half-finished ideas, the hard part is not always generating more ideas.

The hard part is remembering what you already figured out.

---

## Part 3: Company brains that don’t require everyone to become organized

The second related article I wrote was about building a company brain that does not require everyone to become organized.

That is probably the most practical version of this whole thing.

Because personal second brains are one thing. You can build weird systems for yourself. If they are messy, fine. They only have to fit your brain.

Company brains are harder.

Most knowledge systems quietly assume people will maintain them.

They assume someone will update the Notion page.
They assume someone will write down the decision after the meeting.
They assume someone will move the client context into the CRM.
They assume someone will summarize the Slack thread.
They assume someone will remember which doc is the source of truth.

And then real life happens.

People have meetings. They context switch. They make decisions in calls. They answer questions in Slack. They ship the thing. They move on.

The knowledge exists, but it is scattered.

It is in Slack threads, meeting transcripts, Google Docs, Notion pages, GitHub issues, pull requests, client emails, calendar events, random voice notes, and somebody’s head.

So the problem is not always capture.

A lot of teams have plenty of capture. Too much capture, honestly.

The problem is digestion.

A meeting transcript by itself is not company memory. It is raw material.

A Slack thread by itself is not a decision record. It is a conversation.

A Notion page by itself is not a source of truth if nobody knows whether it is still true.

So the model I keep coming back to is:

People create signals. The system turns those signals into memory.

That is where agents can actually be useful.

Not as magic employees. Not as some autonomous army running the company.

More like connective tissue.

They can notice that a decision happened in a meeting.
They can propose an update to a project page.
They can flag stale docs.
They can turn a messy thread into “here is what changed, here is what is unresolved, here is who owns the next step.”
They can help keep memory alive without demanding that every human become a librarian.

That is the practical version of the AI agent story that I care about.

---

## Part 4: WordPress and modern CMS experiments

The other thread I’ve been deep in is CMS work.

WordPress is still central for me. I still think WordPress is incredibly important, especially for real businesses that need an editor-friendly website with plugins, permissions, hosting, maintenance, and a giant ecosystem.

But I’m also increasingly interested in the edges around WordPress.

Astro. Cloudflare Pages. Cloudflare Workers. Sveltia. Keystatic. TinaCMS. EmDash.

Not because I want to replace WordPress in every situation.

That is usually the wrong question.

The better question is: what editing experience does this project need?

Does the client need WordPress because they need the ecosystem, plugins, editorial roles, and familiar admin patterns?

Or do they need a lighter Git-backed CMS because the site is mostly structured content and the team would benefit from a simpler deployment model?

Do they need visual editing?

Do they need structured content modeling?

Do they need performance and Cloudflare-native deployment?

Do they need agents to be able to operate the content safely through scoped tools?

That last one is where EmDash is especially interesting to me.

The reason I’m paying attention to EmDash is not “WordPress killer.” I don’t find that framing useful.

The interesting part is: what would a WordPress-like CMS look like if it were built around Astro, TypeScript, Cloudflare, safer plugins, and AI-operable content workflows from the beginning?

That is a much more interesting question.

And it connects back to the memory stuff.

If agents are going to help with content, documentation, internal knowledge, and websites, then the CMS cannot just be a place where humans click around.

It also becomes an interface for agents.

That means permissions matter. Revisions matter. Approval flows matter. Tool boundaries matter.

This is where CMS architecture and AI agents start to overlap in a way that feels very real to me.

---

## Part 5: Agents, but grounded in workflows

The other thing I’ve been trying to avoid is making “AI agent” content that is basically just demos.

Demos are fun. They are useful sometimes. But they can also trick you.

It is very easy to make an agent look impressive in a controlled environment.

It is harder to make one useful in a real workflow where people are busy, permissions are messy, data is stale, and the output has consequences.

So I’ve been spending more time on the boring questions.

How do you decide whether a workflow should be automated at all?

What should the human review?

What should the agent never be allowed to touch?

How do you evaluate whether the agent is still doing a good job two weeks later?

What do you measure?

Where does the agent write its output?

How does the team know whether to trust it?

That has led me into Claude Code, Codex, Figma MCP, Figma Console MCP, n8n, Team RAG, agent evals, monitoring, and workflow audits.

Again, the pattern is the same:

Don’t start with “add AI.”

Start with the broken workflow.

Then decide if the fix is an agent, an automation, a RAG system, a CMS change, a checklist, or just a better process.

Sometimes the boring fix wins.

That is fine.

Actually, that is probably healthy.

---

## Part 6: Why I’m making this video

Part of why I wanted to make this video is that I’ve felt weirdly stuck with content.

Not because I have nothing to say.

The opposite problem.

There are too many threads.

Hermes and persistent memory.  
The LLM wiki.  
Mnemosyne-style recall.  
WordPress and modern CMS experiments.  
Cloudflare.  
Figma-to-code.  
Agents.  
Team RAG.  
Company brains.  
Automation for messy real teams.

And I do not want to turn the channel into random tool-of-the-week videos.

I’d rather make videos that show the actual process.

The mistakes. The architecture decisions. The weird tradeoffs. The “this looked cool but didn’t work” parts.

So I’m using this as a reset point.

Here is what I’ve been building.

Now I want to know what you want to see more of.

---

## Viewer feedback section

[ON SCREEN: zoom / point to the right side of the one-pager]

If you want to help me decide what to make next, comment with one of these numbers, or just tell me the problem you’re trying to solve.

One: Hermes and persistent memory. How I’m using memory, session search, skills, cron jobs, and Mnemosyne-style recall.

Two: second brain / LLM wiki setup. Obsidian, Git, agents, and how chats turn into durable notes.

Three: WordPress and modern CMS workflows. Sveltia, Keystatic, TinaCMS, EmDash, Astro, Cloudflare, and where each one actually fits.

Four: WordPress plus AI dev workflows. Claude Code, Codex, supervised loops, and what I trust or don’t trust.

Five: Cloudflare and CMS experiments. Workers, Pages, EmDash, and agent-operable content.

Six: Figma to code workflows. MCP, design system APIs, token sync, and avoiding design-to-dev drift.

Seven: practical automation for agencies and small businesses. n8n, forms, inboxes, reports, Notion or CRM workflows.

Eight: Team RAG and company brains. Permissions, ingestion, retrieval, Notion versus Obsidian, and how to build memory systems that do not require everyone to become organized.

I have opinions on all of these. Too many opinions, probably.

But I’d rather make the next few videos around what is actually useful to people watching.

---

## Close

So that’s the update.

I’ve been behind on videos, but I’ve been building.

And the more I work on this stuff, the more convinced I am that the interesting part is not AI as a single tool.

It is AI as part of a working environment.

A place where messy thoughts can become structure.

Where conversations can become memory.

Where old work can be found again.

Where agents can help with the connective tissue instead of just generating more stuff.

And where the system works with normal human messiness instead of pretending everyone is going to become perfectly organized.

That is the thread I want to keep pulling on.

So comment with the number you want to see next, or tell me what you are trying to build.

And hopefully this is me getting back in the swing of making videos again.

---

## Optional pinned comment

I’m trying to decide what to cover next. Comment with a number:

1. Hermes + persistent memory  
2. Second brain / LLM wiki setup  
3. WordPress + modern CMS workflows  
4. WordPress + AI dev workflows  
5. Cloudflare + CMS experiments  
6. Figma → code workflows  
7. Practical automation for agencies/SMBs  
8. Team RAG / company brain

Or ignore the numbers and tell me what problem you’re trying to solve.

---

## Optional description

I’ve been behind on making videos, but I’ve been building a lot behind the scenes: a public LLM wiki / second brain, Hermes workflows with persistent memory and session search, modern CMS experiments around WordPress, Sveltia, Keystatic, TinaCMS, EmDash, Astro and Cloudflare, and more practical AI agent workflows for real teams.

This video is a reset and a request for feedback: what do you want me to make more content about next?

Related posts:

- Building a company brain that doesn’t require everyone to become organized: https://brendan-oconnell.com/building-a-company-brain-that-doesnt-require-everyone-to-become-organized/
- I accidentally expanded my brain: https://brendan-oconnell.com/i-accidentally-expanded-my-brain/
