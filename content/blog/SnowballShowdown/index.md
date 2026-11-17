---
title: "Snowball Showdown"
featured: true
description: "A 4-player local multiplayer party game about snowball-throwing penguins, built in Unreal Engine as a class project with a team of 3."
summary: "Competitive party game with snowball-wielding penguins"
tags: ["Game Design", "Systems Design", "Level Design", "Local Multiplayer", "Unreal Engine"]
tagline: "Rapid Prototyping and iteration, Level Design"
homepageOrder: 3
---
## About
Snowball Showdown is a 4-player local party game where snowball-wielding penguins try to knock each other off the ice. It started as a class project, and I joined the team as the game designer after my friend, a rigger, had built an early demo. I redesigned the camera, controls, throwing, combat, and arenas, then kept developing the game on my own after the class ended. It releases on Steam on Dec 1st. 2026

- **Role:** Game designer (camera, controls, throwing and combat, level design)
- **Team:** 3 people for the class version, solo for the Steam release
- **Engine:** Unreal Engine, with Blender for level modeling
- **Players:** 1 to 4 local, shared screen
- **Status:** Releasing on Steam Dec 1st. 2026
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

## Playtest Findings

With all the components of the gameplay loop in place, along with a mechanism to reset the match once only one penguin remained on the platform, we started doing more extensive playtesting. During this time we ran 7 playtests over the course of 1 day with 5 or so in following weeks.

### The Good
People had very positive reactions to the game. Out of fifteen playtesters ten playtesters asked when it would be released on Steam, which was not one of our original goals. But was a goal we considered after turning it in.

It was also very apparent from all the playtests, people were really getting into the party aspect of the game. One slightly embarrassing sign of that came up during a few playtests. Something we noticed was people were having so much fun that they got really rowdy, laughing, whooping, and hollering as they played, which was exactly the kind of experience we were going for. However, sometimes they got so loud that other students told us to quiet down. This happened more than once and it was embarrassing every time, but as a game designer, I couldn't help but be proud that people were having so much fun with a game *I* had designed.

### The Bad

One of the major issues I wasn't expecting was the controls. Some playtesters found them very unintuitive at first, until they got used to them. Even after they adjusted, many said the scheme still felt unfamiliar, since no other game they'd played used controls like the ones I had created. [See how I addressed this.](#controls)

There were also still a lot of bugs. [See how I tackled them.](#bugs)

### What We Decided Going Forward

After my friend turned in the game, we chatted, and I told him I saw potential in it. People were enjoying it, and I really wanted to put it out on Steam, as some of our playtesters had suggested. He agreed and initially wanted to join, but it became clear he would be too busy moving forward. So with his permission, I kept working on it on my own, polishing it up and adding a few new features.

## Fixes from Playtests

### Controls
I created the original controls the way I did because I wanted them to be easy to pick up. The original scheme used the left stick for moving, the left bumper to pack a snowball, the right bumper to dash, the right stick to both aim and charge the snowball, and the right trigger to throw. The idea was that your thumbs could always stay on the joysticks to handle positioning and most of the heavy lifting for throwing, while the bumpers handled all the auxiliary movements and actions.

[Picture of old controller scheme]
**Old Controller Scheme**

However, as mentioned earlier, players found it unfamiliar:
- Players, both casual and experienced, generally map bumpers to non-essential actions, so putting packing on the left bumper felt wrong.
- People also became frustrated with using the right stick to aim and charge. Players' thumbs would get jumbled and tired, and the penalty for letting go or messing up was a reset to the charge power, which was just frustrating.
- Finally, more experienced players were used to aiming down sights being initiated with the left trigger, as in shooter games.

So I decided to redo the controller scheme with these points in mind.

[Picture of new controller scheme]
**New Controller Scheme**

With the new scheme, the pack → aim → throw loop became easier. My in-laws, who had played the game months earlier, went from taking about 5 minutes to grasp the controls to about 1 minute, and they were able to attack more frequently, which led to some fun moments.

### Bugs
To combat the bugs, I scrubbed through footage of the playtests and identified a list of issues.

[Screenshots of bug list]

Every week, I would take a few hours and just debug.

## New Levels
Another thing I wanted to do was create some new levels. The first level established the rough idea: a simple cylinder where anyone could fight anyone. It was fun and chaotic, but for a Steam release I wanted to design some more stages.

### Ring Around the Rosie

The first stage I added was Ring Around the Rosie. I used Blender to quickly model a torus, flattened the top for the penguins to walk on, and then added a simple noise modifier to the geometry to get an icy look.

[Picture of modified torus]

I put it into Unreal and added a glacier in the middle, made by our third team member back at the beginning of the project.

My plan for the level was to capture the feeling of trying to stay away from your siblings by running around the counter. I thought this would encourage dashing, defensive play, and long-range snipes as players tried to hit each other.

From the playtests I have done since I implemented the map, I found it did those things, but it also had a funny consequence: people teamed up on whoever was between them, which led to some sick dodges by the person in the middle and fun temporary alliances.

[Gameplay of ring around the rosie]

### Orca

The next stage I wanted to design was something with a bit more chaotic energy. I have always thought the levels in *Gang Beasts* were really fun, especially the ones that become more and more destroyed over time. So I went into Blender and created a simple ice sheet that was segmented and separated into pieces, as well as a killer whale, one of the penguins' natural predators.

[Gif of segmented ice sheet and moving the pieces around in Blender]
**The Ice Sheet**

[Picture of the whale]
**The Orca**

The design was that every 15 to 25 seconds, the whale would come up and break the ice. I set it up so that if a player was on that ice segment, they would be eaten by the whale, making for a very funny KO. However, if players were able to survive the attack, the stage would get smaller and players would be closer to each other, making gameplay more frantic.

[Gameplay of the Orca map]

As I explained earlier in this article, there is a difference between random and chaotic, and I didn't want the whale to kill you out of the blue. So I made a warning sign that gave you 3 seconds to know you were being attacked. What I found, however, was that this actually wasn't that fun, because players would just move out of the way and no one would ever get eaten by the whale. My wife suggested decreasing the time the warning showed up, so I shortened it to just 0.5 seconds. That sounds short, but it ended up being more fun. Players would instinctively stay on the seams between semgents of the ice sheets as the timer got closer, and combat would slow for just a second as people watched for the whale. By shortening the warning, players were surprised more often and therefore had more fun, and it created a layer of intuitive skill on the map.

## Conclusion
I had a lot of fun with this project, and it taught me something I'll carry into every game I design. We only had a few days to build the class version, so I focused on one key mechanic, throwing a snowball to knock your friend off a platform, and made that as fun as possible. That decision paid off long after the deadline. Building on a strong gameplay base meant I never had to wonder whether the game was fun, so every new addition, from the camera to the controls to the Orca stage, was about polish and refinement instead of rescuing the core.

### What I Would Change If I Had More Time
Shipping a focused game was a deliberate choice, but I would have liked to put more into this first Steam release: more game modes, more cosmetics, and online play. If I had more time, online play would come first so people could play with their friends wherever they are, followed by a wave mode. I chose to ship something tight rather than stretch the scope, and I'm proud of that.