---
layout: post
hidden: true
date: 2013-06-20 07:27:32
title: "Time For Some New Game Mechanics"
---

Our next step was obvious. Build some more characters and test out the
game mechanics already in place. That went pretty well. We built a
spider, and a base class for menus for things like pausing the game, and
"choosing" which form at each morph machine.

Once the morph menu was working it became glaringly obvious that the
bulk of the work left on the actual game mechanics revolved around
Morph's different characters.

So we tackled them all*.

The Boat was easy. We added water and waves to our terrain, made them
deadly for all entities apart from the boat, and gave the boat the
ability to move right/left and shoot cannonballs.

The frog was a bit more complicated and still needs a little work. He
can jump exceptionally high and moves farther on each keystroke while
he's in the air. This gives him an extraordinary ability of moving
across a level very quickly. I'm starting to see a lot of possibilities
of creating a solid puzzle game around just a few of these form/terrain
combinations.

Next on my list was the airplane. I had been dreading this. The plane is
the only form of Morph which requires truly different game mechanics. Of
course, it's not a drastic difference. It turned out the only thing that
had to change for sky levels as opposed to regular ones was the fact
that the plane has a constant postive x velocity and the viewport has a constant
negative x velocity. This gives the side-scrolling illusion we all know
and love. Morph stays put, everything else moves around him, yet somehow
we still perceive it as a high-paced, flying adventure.

The jellyfish was a matter of allowing for another entity to collide
with water safely, giving him a lesser gravitational force, and giving
him a sort of Mario-like swimming pattern that's more like a underwater
jump than anything.

The clock needed a ticking animation, a short jump, and of course, the
ability to stop time. This was a matter of isolating our update calls
and collision checks to Morph only. Not too shabby.
