---
title: "Snowball Showdown"
featured: true
description: "A 4-player local multiplayer party game about snowball-throwing penguins, built in Unreal Engine as a class project with a team of 3."
summary: "Competitive party game with snowball-wielding penguins"
tags: ["Game Design", "Systems Design", "Level Design", "Local Multiplayer", "Unreal Engine"]
tagline: "Unreal Engine, Rapid Prototyping"
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
When I came onto the team, my friend had already built a small demo. The demo was split screen, with each player getting their own third-person, over-the-shoulder camera. You could spawn a snowball in your hand and throw it at the other players. Personally, I didn't enjoy it. It was hard to tell where a snowball was actually going to travel once it left your hand, so most throws felt like guesses. It was also hard to understand when you'd been hit. With the camera tucked behind your own shoulder, things happened off-screen or sometimes your own player would block the action, and you'd often only realize you'd taken a hit after the fact. A game about throwing snowballs at your friends depends on those two moments, the throw and the hit, and neither one was coming across.

When I talked to my friend about it, I found out he felt the same way. He wasn't attached to the shooter-style setup. What he wanted was a **party game**, something closer to the games that inspired the project in the first place, like *Mario Party* and *Gang Beasts*, where everyone is crowded around one screen and the fun comes from the shared chaos.

That conversation gave me a clear direction. The demo had some of the right ingredients, but it was built like a shooter, and we wanted to build a party game. My job as the game designer was to bridge that gap.

## Fixing the Camera
The camera was the biggest thing holding the design back, so I fixed it first. I moved the game from split screen to a single shared view, inspired by party games like *Mario Party* and *Gang Beasts*. 

[Pictures of mario party and gang beasts camera]

A shared screen means everyone gets to enjoy the chaos as both a participant and an observer, so a penguin launched off the far edge becomes a moment the whole room reacts to.

It also made the game fairer. There's a difference between chaotic and random: in a skill-based game, getting hit by something you never saw coming feels unfair, the problem is exacerbated with 3 other players constantly throwing things. With everyone on one screen, players can see and analyze every threat, so the game stays wild, but you always understand why you won or lost.

[new camera]

## Getting the Core Combat Loop Feeling Great
With the camera fixed, the combat loop became the most important part of the design as it was the core of the whole game.

### Making the Throw Make Sense
I decided to start with the throw, and the original had two major problems. Aiming was unintuitive: snowballs arced and there was no reticle, so it was hard to tell where a throw would land. Throws also felt cheap: snowballs spawned instantly in your hand, so players rapid-fired until one landed, and even then the hit only nudged the target back.

**Problem 1: You couldn't see where the snowball would go.** I replaced the arc with a straight line. An arc demanded too much accuracy, which worked against the chaotic, anyone-can-play feel I wanted. I added a throw arrow that shows the snowball's path. This helped the thrower, but I also wanted opponents to be able to read it. Seeing incoming attack lines lets targets weave between threats, which creates a fun game of juking and tracking.

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

#### Before and After

[GIF of nothing applied]

**Before**

[GIF of ragdoll with screen shake, hitstop, and Niagara]

**After**

### Adding Stakes to Packing a Snowball

The last thing I wanted to address was tension. The payoff for hitting someone with a snowball was already really good, but I wanted to give the player a little more tension in setting up that shot, to make it feel even better. Building tension and releasing it.

In the original demo, snowballs spawned instantly in your hand. I replaced that with a packing phase: the player has to stop and spend a few seconds packing a snowball before they can throw it. During this time, the player is completely vulnerable and unable to move. This gave a sense of risk to the player when moving from defense to offense.

[Gif of a penguin packing a snowball]

What I found was that it added a whole new layer to the game:
- Players would dodge and weave, trying to get their opponents to waste a snowball so they could quickly finish packing and hit their opponents while they were bent down making their own.
- When all the players had thrown their snowballs, there would be a mad dash to get a snowball first.
- People would run around packing their snowball in increments to avoid exposing themselves, creating a risk-and-reward loop of deciding how long to pack before it became too unsafe.

Adding a bit of struggle raised the tension and made the game more chaotic and more fun, which is exactly the party-game feel we were going for.

## Round 1 Playtest Findings

With all the components of the gameplay loop in place, along with a mechanism to reset the match once only one penguin remained on the platform, we started doing more extensive playtesting. During this time we ran 7 playtests over the course of 1 day with 5 or so in following weeks.

### The Good
People had very positive reactions to the game. Out of fifteen playtesters ten playtesters asked when it would be released on Steam, which was not one of our original goals. But was a goal we considered after turning it in.

It was also very apparent from all the playtests, people were really getting into the party aspect of the game. One slightly embarrassing sign of that came up during a few playtests. Something we noticed was people were having so much fun that they got really rowdy, laughing, whooping, and hollering as they played, which was exactly the kind of experience we were going for. However, sometimes they got so loud that other students told us to quiet down. This happened more than once and it was embarrassing every time, but as a game designer, I couldn't help but be proud that people were having so much fun with a game *I* had designed.

### The Bad 

One of the major issues that I wasn't expecting is that people had issues with the controls

There were still a lot of bugs.

### What We Decided Going Forward

After my friend turned in the game, we chatted, and I told him I saw potential in it. People were enjoying it, and I really wanted to put it out on Steam, as some of our playtesters had suggested. He agreed and initially wanted to join, but it became clear he would be too busy moving forward. So with his permission, I kept working on it on my own, polishing it up and adding a few new features.

## Fixes from Round 1 playtests

## New Levels

### Ring Around the Rosie

### Orca

## Conclusion

### What worked

### What I would change if I had more time
Honestly the