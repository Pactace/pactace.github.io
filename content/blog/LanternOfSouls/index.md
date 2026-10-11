---
title: "Lantern of Souls"
featured: true
description: "My capstone game"
summary: "Stealth Horror Game with Mystical Theming"
tags: ["Horror", "Level Design", "System Design", "3"]
---
## Summary

| Role | Game Designer / Systems Designer |
|---|---|
| Team | 20 |
| Engine | Unreal Engine 5.7.4 |
| Target Platform | PC |
| Repo | Perforce <!-- TODO: link the repo --> |
| Duration | 1 year |

## About
*Lantern of Souls* is the 2027 capstone project from Brigham Young University's Animation and Games Department. This stealth horror game casts you as Thiago, a soul master tasked with guiding a lost soul through the labyrinthine ruins of an underworld temple toward its next life.

But Thiago is not alone. Restless souls haunt the temple's halls, and he must rely on his knowledge and the power of the soul he carries to slip past them, light the braziers, and illuminate the way home.

Developing over the course of a year by a team of eight designers and programmers and 12 artists.

**Project Focus:** Long-term project design · Iteration · Finding the fun

(For more design based breakdowns see my case studies on Snowball Showdown and Lichtenauer. For Technical Breakdowns look at Emily's Interior Design Co.)

{{< alert >}}
**Warning!** This article is still a work in progress, you may look at it, but know this isn't the finished product.
{{< /alert >}}

{{< accordion mode="collapse" >}}
{{< accordionItem title="The Story Behind The Game" md=true >}}
This is the 2027 BYU Capstone, and it is a project I initially did not want to work on.

Each year, our cohort comes together in February before our senior year. A handful of students are selected by the faculty to present their game pitches, and at the end of the process, one project is chosen for the cohort to develop throughout the rest of the year.

I had a pitch of my own, one that I believed was a strong fit for our team thought it would make a great portfolio piece, and several others agreed. However, it wasn't the pitch that was chosen.

Instead, the selected project had a strong artistic vision but lacked a clear gameplay direction. Initially, I was frustrated. I had invested a lot into my own pitch, and it was difficult to watch a different project move forward. But once the decision was made, I decided that wasn't going to stop me from making this the best game we could possibly make.

That night, I organized our Discord. A week later, I organized our first game design and programming meeting six weeks before we were scheduled to officially begin development. We brought in the original pitch creator, picked their brain, and started figuring out what the game could actually become. We met a month before the preproduction class began and had a playable demo before beginning of the preproduction class.

Even then, the game's direction wasn't clear. We tried ideas, scrapped them, built on others, and repeated the process. I stayed late day after day and eventually became the design and programming lead. After roughly three months of development, we finally had a breakthrough: we understood what the game was trying to be.

A stealth horror game.

From there, the project began to take shape. What started as a game I didn't want to work on has become one of my favorite projects I've ever worked on. It has also been my longest development cycle to date, and I think that shows in the final product.

More than anything, this project taught me that some projects begin with a clear direction, while others have to discover their identity through iteration.

I didn't choose this game.

But I chose to make it the best I could.
{{< /accordionItem >}}
{{< /accordion >}}

## Timeline
*Lantern of Souls* is a year-long project, and at the five-month mark, after many rounds of iteration, we've found our direction and built something that's genuinely fun to play. Here's the journey so far, and how we've iterated our way toward the fun.

**A note on "we":** Everyone on the design and programming team was involved in the design process for almost every part of the game, especially early on. As design lead, I took part in every area, so I'll write about the team's decisions as "we" and call out my own contributions where they apply.

### Where we began (Months 1-3)
#### The Pitch
<p style="text-align: center;">
  <a href="https://docs.google.com/presentation/d/1baIBm49Z3-1zA4lFZKd_H-bpf3PipMgu-qrNQgqMfxE/edit?slide=id.g3ae52c7ded1_0_0#slide=id.g3ae52c7ded1_0_0">The First Design Doc: Mailen Cruz</a>
</p>
This is the initial pitch of Lantern of Souls, by the person who pitched it. As a brief summary I will highlight some key points

- **Fantasy**: *"A third-person stealth and adventure game where players assume the role of the Soul Master, a guardian charged with escorting a fragile soul through a ruined temple inhabited by hostile spirits drawn to its light. The challenge is to move and make decisions based on the state of the soul."*
- **Camera**: *"Third person. Moves with the character, zooms out as the light grows so the player can see what’s around him. Camera zooms in when there’s less light so it feels tighter."*
- **Light Management/Risk v Reward**: Light management is a core mechanic. It is a timer that is constantly running out. *"Timer affected by enemies, timer is restored by checkpoints." [...] "The little actions are light management, navigate the darkness, interact with the environment to increase the light. Repelling spirits with light and sneaking around so wanderer spirits can’t see you."*
| Light | Balance visibility and vulnerability. |
|---|---|
| Bright Light:  | Safer pathfinding but attracts enemies. |
| Dim Light: | Offers stealth but risks the soul’s extinction. |
- **Stealth**: Stealth is the other core mechanic
- **Emotional Tone** *"Dark, mysterious, atmospheric."* 

#### First Meeting Outside of Class: April 5, 2026
About a month before the beginning of our preproduction class, the design team met to hear Mailen's pitch again and talk through our questions.

<p style="text-align: center;">
  <a href="https://docs.google.com/document/d/1cSk81gbBJcOGumR_T_1kBP8TQC8zSHuAaFoBujBOD8c/edit?usp=sharing">Read the meeting minutes</a>
</p>

Coming out of the meeting, we wanted to get our hands on the core mechanics as quickly as possible, so we started building a rough demo on April 27, a week before preproduction class began.

**Problem:** The pitch says the game is about retaining light, but we didn't know what that would actually feel like to play.

**Solutions:**
- Make light a bar the player has to preserve.
- Add light stations that let the player recharge.
- Give the soul abilities that cost light, like a sprint that drains light and expands the light radius, making the player easier to spot.
- Let the player crouch, which also drains light but makes them harder to see.
- Use simple cube enemies on patrol paths. If the player's light radius overlaps an enemy, they're detected and the game resets.

**What I worked on:** The player's light radius, the sprint, and the crouch.

**Outcome:** The demo was decently fun, but it wasn't what we were looking for. It was too punishing. The sprint drained so much light that using it could kill the player almost instantly, which made it feel like a trap instead of a tool.

**Takeaway:** Light drain had to feel like a resource to manage, not a penalty for acting.

{{< figure src="earliest-screenshot.png" alt="The earliest screenshot of the project, showing the player and their light radius" caption="The earliest screenshot of the project. Here you can see the player and the light radius." >}}

#### First Meeting In Class: May 4, 2026
We then met with our teacher as a design group and played the demo. Together, we came up with the core questions we needed to answer.

{{< figure src="FirstMeetingInClassLantern.jpg" alt="The whiteboard from our first in-class meeting" caption="The whiteboard from our first in-class meeting." >}}

<p style="text-align: center;">
  <a href="https://docs.google.com/document/d/1ln9ed2PbmXWfgF69FPzjwGj-ce-VkWydbgJMqWOxPFc/edit?usp=sharing">Read the meeting minutes</a>
</p>

**Vision:**
- Make stealth fun by creating:
  1. Sudden moments of quick thinking
  2. Suspense
  3. Opportunities for the player to make intelligent choices against enemies
  4. Many light abilities the player can use against enemies
- Light is both health and a resource (navigating darkness)
- Risk and reward
- The player should feel proactive, not reactive: powerful, not a victim

**Problems:**
- How do we make enemies the challenge, and how are they tied to light?
- What is light? Right now it's a radius enemies can detect, a timer that depletes and has to be kept alive, and a power source.
- How do we give the player many tools to have fun with?/ What are the player's action verbs (movement, abilities), and how do they enable intelligent choices?
- Should the player be acting ahead or paying attention in the moment?
- What requires light?
- How is the player visible to enemies?
- How is the player navigating the level

**Solutions:**
- Let the player interact with lots of enemies.
- Make crouching near enemies a fun, tense experience.
- Fast movement with a purpose.
- Light opens doors for a limited time.
- Improve the AI so it can pursue the player instead of causing an instant death, and add a perception component so enemies don't detect the player just by overlapping.
- Test a range of player abilities.


**What I worked on:** I wanted the sprint and crouch to feel good before moving on to anything else.
- **Crouch:** shrinks the light radius but slows movement, with no cost to the light meter. Moving slowly is a drawback on its own, since the longer you wait, the more your light drains.
- **Sprint:** no longer drains the light meter either, because being more detectable is already its own drawback.

**Outcome:** The game turned out better. With the perception component, enemies felt more dangerous, and movement felt better now that players weren't punished for using the tools they were given. We tried many abilities and scrapped most of them. Two stood out as worth keeping:
- **Distraction** A small object that you could leave behind you to distract the enemy.
- **Shadow dash:** dash through enemies without being detected, at a small cost to light.
We also began exploring with how how the map might  be laid out and what player sub objectives might  be. For example opening doors.

**Takeaway:** The player abilities still needed refinement, and the enemies needed to feel fairer. It was often hard to tell how far away they could see you. We also needed to understand the core game loop.

**The first core loop:** The player enters an area and has to solve the puzzles inside it. Some are basic environmental puzzles, and the rest involve avoiding enemies. From there it plays out like a basic action-adventure sequence: if the player is spotted, the player can run from an enemy wait a moment and can returns, or deliberately get spotted and lure it to specific spots in order to trigger certain puzzles.

{{< figure src="FirstCoreLoop.jpg" alt="Diagram of the first core loop" >}}

{{< figure src="LanternOfSoulsFirstLevel.gif" alt="Gameplay of the first sub-objective, a door" caption="The first sub-objective: a simple door." >}}

{{< figure src="EarlySoulAbilities.gif" caption="Gameplay of early soul abilities, including the Shadow Dash, Distraction and a scrapped Light Pulse ability." >}}


#### Preproduction Class and Into the Summer
As preproduction class went on, we narrowed our open questions down to three:

**Problems:**
1. What is the player-enemy interaction?
2. What are our core abilities, and how can we make them more streamlined and intuitive?
3. How do we condense our core experience into a quick vertical slice?

**A key point in understanding our direction at this time in development:** Based on how the team understood the pitch, we believed the game fit cleanly into the adventure stealth genre, with one caveat: the protagonist can't kill or attack enemies. Mailen, who wrote the pitch and later became the project's director, confirmed this, so that became our direction.

**Research:** With that in mind, we studied a range of adventure stealth games:
- *Aragami*
- *Tom Clancy's Splinter Cell: Chaos Theory*
- *Thief 2*
- *Hitman: World of Assassination*
- *Metal Gear Solid V: The Phantom Pain*
- *Dishonored*
- *Metroid Dread* (the robot sections)
- *Mark of the Ninja*

**What we took from them:**
- Being close to enemies without being detected feels great.
- Many of these games gave the player a teleport ability, which made them feel powerful and let them slip through gaps in enemy patrols.
- Enemies usually had a few states. In a patrol or stationed state, they hold a spot or follow a route through their area, which lets players watch, then act with good information. This rewards slow, observant play and gives the player a sense of agency.
- Enemies also had a detection state. When they spotted the player, there was usually a grace period while detection built up before the enemy attacked.
- Some attacks by the enemies weren't instant deaths, which gave the player a chance to escape while still punishing them for being found. Others killed instantly. We decided to test both.
- Nearly every game had a distraction, used either to take heat off the player or to create an opening to move.
- Operating in the dark generally didn't feel good. Players liked knowing what they were doing so they could make informed decisions.
- Players also liked knowing their destination from the start, so they always knew where they were going. 
- We wanted to encourage slow and methodical gameplay, especially with our restriction on non-killing. We wanted to make sure the player couldn't run and gun through the level.

**What I worked on:** 

- **The Shadow Dash**, which went through four phases.

    1. **Phase 1: A movement option to remain undetectable (May 18, 2026)**

        **Goal:** Give the player a movement option that makes them undetectable by enemies.

        **Iteration:** I iterated until the player could phase through an enemy and, with good timing, pass completely undetected. This finished the initial design of the ability.

        **Outcome:** Players liked slipping past a guard, but the ability felt uncontrollable. The player wasn't being methodical. They were just hitting a button to speed away.

        **Takeaway:** I looked into other stealth games with movement abilities built around the same goal: moving without being detected.

        {{< figure src="ShadowDashPhase1.gif" alt="Gameplay of the early Shadow Dash" caption="The early Shadow Dash." >}}

    2. **Phase 2: A *controlled* movement option to remain undetectable (May 25, 2026)**

        **Problem:** The Shadow Dash was uncontrollable, which doesn't fit a game about methodical movement. How could we make it more precise?

        **Solution:** Instead of pressing a button and flying in one direction, the player now aims the teleport and releases it to go to a specific spot of their choosing. This was inspired by the teleports in *Aragami* and *Dishonored*. The player had to be on the same vertical axis as the destination for it to work, so it still felt like a dash.

        **Outcome:** Players loved the new teleport. It made them feel in control and fulfilled the ability's original purpose.

        **Takeaway:** Other games let the player teleport to high and low places, which gave them more agency and let them climb to vantage points to observe and avoid enemies.

    3. **Phase 3: A controlled movement option, now in three dimensions (June 2, 2026)**

        **Problem:** In other stealth adventure games, players can navigate in three dimensions, up onto ledges and down to the ground. Would that make the player feel more powerful and stealthy?

        **Solution:** I added the ability to teleport up and down. If the player looks at a ledge, they can teleport on top of it, and if they look at the floor, they can teleport down to it. The player can teleport anywhere they can see.

        **Outcome:** Players really enjoyed teleporting up and down. It made them feel in control, and it quickly became a core element of this build.

        {{< figure src="ShadowDashTeleport.gif" alt="Rough gameplay of the three-dimensional Shadow Dash teleport" caption="A rough version of the teleport. Unfortunately, I don't recording of the final polished version." >}}

    4. **Phase 4: Polish (June 2026)**

        **Problem:** The teleport worked and players liked it, but it still had rough edges.

        **Solution:** I spent a week on quality-of-life polish, such as keeping the player from clipping into walls or the ground during a teleport.

        **Outcome:** Players already liked the ability, but I wanted it to feel as good as it could.

I worked on levels
I worked on the crouch and sprint

We made three more enemies

**The Watcher:** A living spotlight when the enemy saw they player they would drain their light, this ended up being really fun and the highlight of many levels

**The Hunter:** Player's found the basic Guard a bit too trivial, so we made the hunter which was supposed to act as the

### The Turning Point

## Ongoing documentation:
I will be adding documentation here soon with more breakdowns on design decisions, reasoning and player experience