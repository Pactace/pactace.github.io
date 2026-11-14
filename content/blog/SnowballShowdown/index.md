---
title: "Snowball Showdown"
featured: true
description: "My capstone game"
summary: "Competitive Party Game with Snowball Weilding Penguins"
tags: ["Horror", "Level Design", "System Design", "3"]
tagline: Rapid Prototyping, 3D Modeling Software
homepageOrder: 3
---
## About
A game about penguins fighting each other in a snowball fight.
{{< accordion mode="collapse" >}}
{{< accordionItem title="The Story Behind The Game" md=true >}}
I have always loved local multiplayer games. Some of my fondest memories growing up were going to a friend’s house after school or during the summer and gaming it up until dinner time. Those memories were filled with laughs, feuds, strategy, and the feeling that you could beat your friends if you just kept playing.

As online multiplayer became more common, many of those experiences moved online. While I feel like online multiplayer has given me some of those experiences, I believe something special is lost when you aren't sitting next to the people you're playing with. There are still games that capture that feeling, though. Games like *Gang Beasts*, *Mario Party*, and *Party Animals* bring us together in the same room and create the kind of chaotic, memorable moments I loved growing up.

So when my friend asked me to be the gameplay programmer and designer for a local multiplayer game about penguins battling in snowball fights, I jumped at the opportunity. I wanted to create something that captured that same feeling: chaotic matches, friendly rivalries, plenty of laughs, and the feeling that you could beat your friends if you just played one more round.

**And I think we captured some of that magic.**
{{< /accordionItem >}}
{{< /accordion >}}

## Starting Out
When I came onto the team, my friend had already built a small demo. The demo was split screen, with each player getting their own third-person, over-the-shoulder camera. You could spawn a snowball in your hand and throw it at the other players. Personally, I didn't enjoy it. It was hard to tell where a snowball was actually going to travel once it left your hand, so most throws felt like guesses. It was also hard to understand when you'd been hit. With the camera tucked behind your own shoulder, things happened off-screen or all at once, and you'd often only realize you'd taken a hit after the fact. A game about throwing snowballs at your friends depends on those two moments, the throw and the hit, and neither one was coming across.

When I talked to my friend about it, I found out he felt the same way. He wasn't attached to the shooter-style setup. What he wanted was a **party game**, something closer to the games that inspired the project in the first place, like *Mario Party* and *Gang Beasts*, where everyone is crowded around one screen and the fun comes from the shared chaos.

That conversation gave me a clear direction. The demo had some of the right ingredients, but it was built like a shooter, and we wanted to build a party game. My job as the game designer was to bridge that gap.

## Fixing the Camera
The camera was the biggest thing holding the design back, so I fixed it first. I moved the game from split screen to a single shared view, inspired by party games like *Mario Party* and *Gang Beasts*. 

[Pictures of mario party and gang beasts camera]

A shared screen means everyone gets to enjoy the chaos as both a participant and an observer, so a penguin launched off the far edge becomes a moment the whole room reacts to.

It also made the game fairer. There's a difference between chaotic and random: in a skill-based game, getting hit by something you never saw coming feels unfair, especially with three other players right next to you. With everyone on one screen, players can see and size up every threat, so the game stays wild but you always understand why you won or lost.

[new camera]

## Getting the Core Combat Loop Feeling Great
With the camera fixed, the combat loop became the most important part of the design as it was the core of the whole game.

### Making the Throw Feel Great
I decided to start with the throw, and the original had two major problems. Aiming was unintuitive: snowballs arced and there was no reticle, so it was hard to tell where a throw would land. Throws also felt cheap: snowballs spawned instantly in your hand, so players rapid-fired until one landed, and even then the hit only nudged the target back.

**Problem 1: You couldn't see where the snowball would go.** I removed the arch because you had to be too accurate to use it ruining the intended chaotic and any one can play gameplay loop. I added a throw arrow that shows the snowball's path. This helped the thrower, but I also wanted opponents to be able to read it. Seeing incoming attack lines lets targets weave between threats, which creates a fun game of juking and tracking.

[Gif of static arrow]

**Problem 2: Throws felt cheap.** Matches were ending too quickly because throwing had no stakes. I made throws charge: the longer you aim, the longer the vector and the farther the throw. I also tied knockback to charge, so a long-range snipe hits harder than a quick toss. Now every throw is a decision between a fast shot and a powerful one.

[Gif of expanding arrow]

The combat loop was feeling better, but we still had some issues

### Giving the Hit a Little Juice

As we ran our first few playtests to see how people liked throwing, I noticed a major issue: while the core game was becoming more fun, the payoff for actually landing a hit didn't feel as good as it could.

Here's what I added to fix that:

1. **Screen shake and hitstop:** I added screen shake and hitstop, and after a few iteration loops I discovered that I needed to strike a balance between the two. The goal was a hit that felt impactful, but not so impactful that it detracted from the gameplay decisions being made by the other players.

[Screen shake and hitstop with pushback]

2. **Niagara:** I made a short poof with Niagara to give a visual indicator of the impact. This made it easier to distinguish who had been hit and from where.

[Niagara Poof]

3. **Physics-simulated ragdolls:** Instead of pure knockback that just pushed players back, I added a simple capsule component once the penguin mesh was imported, and I simulated it for a few seconds when the player got hit. Now they tumble across the ice. If they stay on the stage, they get back up and keep fighting. If not, they tumble to their comical doom.

The ragdolls turned out to be the funniest part of the game. Watching a penguin fly off the stage, or slide across and barely stay on, creates exactly the kind of chaotic, funny moments I was going for.

[Ragdolls Applied]

[GIF of nothing applied]

**Before**

[GIF of ragdoll with screen shake, hitstop, and Niagara]

**After**

## Making





## Ongoing Documentation
This write-up is still in progress. I’ll be expanding this section soon with deeper design and technical breakdowns.