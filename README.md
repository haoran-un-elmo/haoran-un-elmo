# /uses

Things I use for work — hardware, software, and how I've set up Claude.

---

## 🖥️ Hardware

- **Monitor:** Samsung 27" Curved monitor
- **Keyboard:** Drop Ctrl + MT3 Susuwatari blank keycaps // Filco Majestouch 2 + MT3 Bleached blank keycaps 
- **Mouse:** Logitech MX Vertical // Corsair Dark Core Pro
- **Headphones:** 

---

## 🛠️ Editor & Terminal

- **Editor:** VS Code
- **Theme:** [placeholder]
- **Font:** [placeholder]
- **Shell:** zsh + Oh My Zsh

---

## 🤖 Claude

Things that make my Claude go better.

- **CLAUDE.md / project instructions:** [brief description or link to your setup]
- **My Custom rules:**
  - [COMMUNICATE.md](claude/rules/COMMUNICATE.md): I use this to manage Claude's text output. I don't like reading essays, and this is how I force it to be concise. I wrote this by hand, but it's inspired by [Caveman](https://github.com/juliusbrussee/caveman).
  - [COMMENTS.md](claude/rules/COMMENTS.md): Claude's comments were getting ridiculous, so I use this to reframe the type of comments I get out of Claude.
  - [PARALLEL_WORK.md](claude/rules/PARALLEL_WORK.md): I normally have a lot of agents running in parallel, but two agents running in the same repo unaware of each other is a recipe for a bad time. 
  - [SESSION_NAMING.md](claude/rules/SESSION_NAMING.md): Claude is probably better at this now, but I needed a way to differentiate multiple agents running at one time. This forces a rename. 
- **My Custom skills:**
  - [Sense Check](claude/skills/cross-repo-handoff/sense-check.md): This is brilliant. Every now and again, you get suspicious of something Claude outputs. This sets us a parallel subagent with ZERO context awareness to check working. This is great at picking up hallucinations.
  - [Interrogate me](claude/skills/cross-repo-handoff/interrogate-me.md): I wrote this ages ago when the motif for good Agentic coding was writing the most detailed and precise essay. I give it a sketch of an idea, and it should use this to grill me until it gets all the answers out of my brain. This is somewhat superseded by Superpowers' Brainstorming, which does something similar, but I wrote his one so I keep it.
  - [Cross Repo Handover](claude/skills/cross-repo-handoff/SKILL.md): I established this as a protocol for having my parallel agents working in tandem. E.g. if I have something working on a frontend repo, and I need something from a backend repo, this is a spec for writing a HANDOFF.md file--telling the backend what I (the frontend) need from it. Agents CAN communicate with each other these days, but this 1) pre-dates that, and 2) this documents what it's up to, in case you like tracking what your agents are up to.
- **Third Party Skills:**
  - [Archify](https://github.com/tt-a1i/archify): This is my new go-to diagramming skill. It gives you interactive architecture diagrams, which are brilliant.
  - [Superpowers](https://github.com/obra/superpowers): Brainstorming & friends: This is what has carried most of my agentic coding workflow this year, before the advent of Flowmo. I'm a massive fan.
