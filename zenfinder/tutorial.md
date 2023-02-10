---
layout: default
title: Tutorial
parent: Zenfinder
---

A tutorial!

##### TABLE OF CONTENTS
* TOC
{:toc}

# Zenfinder Tutorial
This is a (hopefully) rather complete tutorial that covers how to get
Zenfinder up and running. Note that this won't cover more advanced topics,
but this should be enough to get you started.

## How does Zenfinder work?
Before we write any code, it's important to figure out how Zenfinder works,
so that you know how to use it effectively.

There are two parts to robot - the drive train and odometry system. There
are corresponding interfaces in Zenfinder: `Drive` and `Odometry`. To
use Zenfinder, you'll need to implement both of these. Their goals are
relatively simple: the drive train should move your robot around the field,
and the odometry system should report your robot's position. This is important
physical implementation.

Please note that most of this tutorial assumes you have a perfectly-working
implementation of Zenfinder - odometry is always accurate, the drive train
has no issues, the wheels don't slip, etc, etc.

### Implementing a drive train
This right here is a placeholder - I'll write more about it later, but check
out the general documentation if you need to know more now.

### Implementing an odometry system
This right here is a placeholder - I'll write more about it later, but check
out the general documentation if you need to know more now.

### Operating Zenfinder: trajectory queue
Zenfinder has a queue system at its very core. Telling your robot to follow
a new trajectory acctually just adds that new trajectory to a queue. 

```java
public class UsingTheQueue {
    public void example() {
        Zenfinder Zenfinder = Zenfinder.newSimulatedZenfinder(0.01);
        
        // adds a trajectory to the provided point to the queue
        Zenfinder.goTo(new PointXYZ(0, 0, 0));

        // tick Zenfinder until it has finished the previous trajectory
        // ... after this is done, the queue will be EMPTY
        Zenfinder.tickUntil();

        // queue TWO trajectories at the same time.
        Zenfinder.goTo(new PointXYZ(10, 10, 0));
        Zenfinder.goTo(new PointXYZ(20, 20, 0));

        // execute the trajectories, in sequence.
        // this will go to 10, 10, then to 20, 20
        while (true) {
            Zenfinder.tick();

            if (!Zenfinder.isActive())
                break;
        }

        // let's say we accidentally add a trajectory that we don't actually
        // want to follow. how can we fix that?
        Zenfinder.goTo(new PointXYZ(10, 10, 0));
        Zenfinder.goTo(new PointXYZ(20, 20, 0));

        // that's how! you can use the clear() method to clear all of the
        // followers/trajectories that Zenfinder is following.
        // note that this clears ALL of them, so the queue is currently
        // now empty.
        Zenfinder.clear();

        // and of course, we can't forget about the point we ACTUALLY
        // wanted to go to
        Zenfinder.goTo(new PointXYZ(20, 20, 0));

        // tick Zenfinder until we're done
        Zenfinder.tickUntil();
    }
}
```

## Basic field navigation
Basic navigation is incredibly simple with Zenfinder.

```java
public class BasicFieldNavigation {
    public void example() {
        Zenfinder Zenfinder = Zenfinder.newSimulatedZenfinder(0.01);

        // go to a new position (0, 0, facing 0) and then tick Zenfinder
        // until that position is reached. this is an example of method
        // chaining, which Zenfinder encourages for the sake of readability.
        Zenfinder.goTo(new PointXYZ(0, 0, Angle.fromDeg(0))).tickUntil();

        // go to (10, 10, facing 45 degrees) and tick Zenfinder until it
        // reaches the target point, with one special exception: if it takes
        // longer than 10 seconds to execute the path, Zenfinder will
        // automatically finish the path, even if it's not actually done
        Zenfinder.goTo(new PointXYZ(10, 10, Angle.fromDeg(45))).tickUntil(10_000);
    }
}
```
