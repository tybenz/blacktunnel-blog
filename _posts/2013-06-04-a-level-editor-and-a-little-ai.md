---
layout: post
date: 2013-06-04 23:01:49
title: A Level Editor And A Little A.I.
---

After getting the first few characters to show up and move around on the
screen, we decided it was time to buckle down and come up with a better
way to create a level from scratch. We had set up our levels to be
hard-coded into a `level.js` file. This usually looked like
[this](https://github.com/blacktunnel/morph/blob/2084801edbf59c0b446667c6cf0a080c6ff9bbac/game/level.js#L33).

We chose it because it's both human and machine readable. When we
initialize a level we simply parse each string to find the correct class
to instantiate. We then keep a running collection of entity objects.

This is obviously easy to manipulate by hand, but it's tedious to create a
brand new level from scratch. So we created a [level
editor](http://blacktunnel.github.io/preview/editor).

The editor app can initialize an instance of the game at any
point (for quick testing), so it has access to all classes under the
`Game.Entity` namespace. Under this namespace it can create a list of
possible entities with which it can populate a level's grid. We also
created a few crude (and buggy) "drawing" tools such as a pencil, an
eraser, and a rectangle tool for large areas of the same entity.

Within the editor you can also switch to a preview mode which
instantiates the game with your new level. This really comes in handy
for testing out quick ideas.

At the end of your editing session, you can click save, and the app will throw
open a new window with the appropriate Javascript ready to be pasted into our `level.js`
file. Pretty cool huh?

##### The Bad Guys

Obviously no platform game is any good without 'em. The few we have designed
sprites for thus far aren't our favorite and will probably be changed
many times down the road. But for now, we wanted to create a few tough
adversaries for Morph. We started with a bird and
a monster but they didn't have any way of attacking.

So for our first *real* enemy we created a turret. The first thing to
nail down were the bullets. This was done easily enough with what our
little game engine already provided. We drew up a 2x2 red dot, defined a
new object, and instantiate it next to the turret when it's time to
fire. The velocity and position vectors take care of the
flying-across-the-screen-ness of the bullet. A quick modification of
Morph's collision handler and the bullets actually damage his health.
Add a time element to the turret's update method, and we have a bullet
flying horizontally every 2 seconds.

With everything in place this was all actually quite easy. We even
created a few variations of the turret called "Quick" and "Smart"
turrets. Quick ones are a little smarter than regular turrets in that
they only shoot when Morph is on their same x-axis. And, when they do
fire, Morph gets a barage of fast and repetive fire. Smart turrets can
track Morph's position (almost) anywhere on screen and hit with a
similar furry. Time to grab your rocks boys and girls...

##### Time for sentient entities

After all that, we moved back to the monster. He needed to do more than
just sit there and chomp his teeth, so we dubbed him our goomba and
decided to put a spring in his step. As we added some logic to get him
walking, we realized we were going to need to standardize on the way we
defined our bad guys' behaviors.

We attempted to create something akin to our animationStates called
"moves". We then realized that an entity's state should ultimately
dictate both it's A.I. actions and animation sequence. So we combined both animations and actions into a single states dictionary.
Here's what it looks like:

{% highlight javascript %}
Game.Entity.Enemy.Monster = Game.Entity.Enemy.extend({
    type: 'Enemy.Monster',
    states: {
        'dying': Game.Entity.Enemy.prototype.states.dying,
        'walking': {
            animation: {
                delta: 150,
                sequence: [ 'monster-open', 'monster-open', 'monster-open', 'monster-open', 'monster-open', 'monster-closing', 'monster-closed', 'monster-closed', ],
                times: 'infinite'
            },
            actions: [
                {
                    delta: 500,
                    action: function() { this.pos.x -= Game.unit; },
                    until: function() {
                        return this.adjacentTo( 'Terrain.Land', 'left' ) || this.adjacentToLevelEdge( 'left' );
                    }
                },
                {
                    delta: 500,
                    action: function() { this.pos.x += Game.unit; },
                    until: function() {
                        return this.adjacentTo( 'Terrain.Land', 'right' ) || this.adjacentToLevelEdge( 'right' );
                    }
                }
            ]
        }
    },
    // ...
});
{% endhighlight %}

Our base class has a handler called `performNextMove` in which
we check our until case to see if we can perform the current or next
move. This is executed at an interval set by the move's delta property.

Pretty neat huh? So we went back and tweaked the turret code to follow
this pattern:

{% highlight javascript %}
states: {
    'dying': Game.Entity.Enemy.prototype.states.dying,
    'shooting': {
        animation: null,
        actions: [{
            delta: TURRET_INTERVAL,
            action: function() { this.shoot(); }
        }]
    }
},
// ...
shoot: function() {
    this.createBullet( this.pos.x, this.pos.y, this.bulletSpeed, 0 );
}
{% endhighlight %}

We're hoping this pattern is flexible enough to cover more complex
interactions later on. We'll let you just how wrong we are when we get
to boss battles.
