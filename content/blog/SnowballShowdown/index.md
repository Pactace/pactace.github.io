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

## Fixing the Camera and Movement

**Problem 1: You couldn't see where the snowball would go.** I added a throw vector that shows the snowball's path. My first instinct was an arc, but an arc demanded too much accuracy and cut against the chaotic energy the game needed. I went with a straight line instead, which is easier to read and forgiving enough to keep matches loose.

**Problem 2: Throws felt cheap.** Matches were ending too quickly because throwing had no stakes. I made throws charge: the longer you aim, the longer the vector and the farther the throw. I also tied knockback to charge, so a long-range snipe hits harder than a quick toss. Now every throw is a decision between a fast shot and a powerful one.

**Problem 3: It still lacked punch.** The mechanics worked, but hits didn't feel impactful, especially long-range snipes. I added:
- **Screen shake** on hit
- **A quick Niagara effect** once the penguin mesh was imported
- **Physics-simulated ragdolls:** instead of pure knockback, penguins now tumble across the ice. If they stay on the stage, they get back up and keep fighting.

The ragdolls turned out to be the funniest part of the game. Watching a penguin fly off the stage, or slide across and barely stay on, creates exactly the kind of moments people laugh about afterward.

**What I learned:** [one takeaway, e.g. "feel" came from layers of small feedback, not one big mechanic]

## Making the Throw Feel Great

## Making





## Ongoing Documentation
This write-up is still in progress. I’ll be expanding this section soon with deeper design and technical breakdowns.