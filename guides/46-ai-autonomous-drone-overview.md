# AI Autonomous Drone, Part 1: Overview and Architecture

> The mental model for the whole series. Before you wire anything or run any software, you need to understand how an autonomous drone is actually organised. Get this right and every later guide makes sense.

Created by [The Drone Community](https://github.com/thedronecommunity)

---

## What You Will Learn

- Why an autonomous drone has two separate brains, and what each one is responsible for
- How the pieces fit together into one system
- Where the intelligence actually lives
- What you can build up to across this series

This guide has no setup steps. It is the map you will follow for the next nine guides.

---

## The Two Brains

The single most important idea in autonomous flight is that the work is split between two computers that are good at very different things.

The first is the **flight controller**. On our builds this is the CUAV V6X running ArduPilot. Its whole job is to keep the aircraft stable and do what it is told. It reads the gyroscope and accelerometer hundreds of times a second and adjusts the motors to hold attitude, altitude, and position. No human can react that fast, and no higher-level software should try to. This brain thinks in milliseconds.

The second is the **companion computer**. On our builds this is a Raspberry Pi 5. Its job is to decide what the drone should do next. Where should it go? What is it looking at? Is this command safe? This brain thinks in seconds, and it thinks about goals and meaning rather than motor speeds.

The reason to separate them is that stability and decision-making are fundamentally different problems. Stability must be fast, simple, and utterly reliable. Decision-making can be slower, richer, and occasionally wrong without the drone falling out of the sky, because the flight controller underneath is always holding things steady.

Autonomy, then, is not one clever program. It is the companion computer telling the flight controller where to go, while the flight controller quietly handles the physics of getting there.

---

## How They Talk

The two brains speak to each other over a protocol called MAVLink. If you have used a ground station like Mission Planner or QGroundControl, you have already used MAVLink without thinking about it. The ground station sends commands and reads telemetry over exactly the same protocol.

The companion computer is really just a ground station that happens to be bolted to the drone and driven by AI instead of a human. That framing is worth holding onto. Everything the Pi does, arm, take off, fly to a point, read the battery, is a MAVLink message, the same kind your laptop sends.

---

## Where the Intelligence Lives

It helps to be precise about where each kind of "smart" actually runs, because it is easy to imagine the intelligence is in one magical place when it is really spread across the system.

The flight controller holds the flight intelligence: how to stay level, how to hold a position, how to return home if the link drops. This is decades of refined control theory, and you mostly configure it rather than program it.

The companion computer holds the decision intelligence. This is where a language model interprets a spoken command, where a vision model recognises objects, and where a safety layer judges whether an action is allowed. This is the part this series builds.

The vision accelerator, a Hailo-8L in our case, holds the perception intelligence. It runs the neural network that turns camera pixels into a list of recognised objects. It does one thing extremely fast so the Pi does not have to.

No single component is "the AI". The behaviour you see comes from these parts working together.

---

## The Full Stack

Here is the whole system in one picture, top to bottom.

```
You, speaking or typing in plain language
        |
Language model (on the Pi, or in the cloud)
   turns your words into one safe action
        |
Safety layer (on the Pi)
   checks the action, allows, warns, or blocks
        |
Flight controller (CUAV V6X, ArduPilot)
   actually flies the aircraft
        |
Perception (Pi camera through the Hailo-8L)
   sees the world and feeds the decision above
```

Read it as a loop rather than a straight line. Perception feeds the language model's understanding, the language model proposes, the safety layer judges, the flight controller acts, and the camera sees the result, which feeds the next decision.

---

## What Each Piece Is Responsible For

| Piece | Responsible for | Not responsible for |
| --- | --- | --- |
| Flight controller | Staying stable, executing motion, position hold | Deciding where to go |
| Companion computer | Decisions, language, safety, vision | Low-level flight control |
| Language model | Turning words into an intended action | Judging whether it is safe |
| Safety layer | Judging and bounding every action | Understanding language |
| Vision accelerator | Recognising what the camera sees | Deciding what to do about it |
| Position sensor | Knowing where the drone is | Anything else |

The clean division of responsibility is what makes the system understandable and safe. Each part does its own job and trusts the others to do theirs.

---

## What This Series Builds Toward

By the end of these guides you will understand a drone that can be given a goal in plain English, perceive its surroundings with on-board AI, decide its own actions within safe limits, and fly those actions out, all while running on the aircraft itself.

We build it in the order the system depends on. First the link between the two brains, because nothing works without it. Then perception, so the drone can see. Then the language brain, so it can understand you. Then the safety layer, so it can be trusted. Then the context that makes its decisions good, the dashboard that makes it all visible, the position sensing that unlocks real flight, the autonomous loop that ties it together, and finally the disciplined path from simulation to a real aircraft in the air.

Take them in order. Each one assumes you understand the ones before it.

---

## Related Guides

- [Guide 08: Companion Computers](08-raspberry-pi-integration.md)
- [Guide 10: AI and Computer Vision](10-ai-integration.md)
- [Part 2: Companion Computer to Flight Controller](47-companion-mavlink-link.md)

---

*The best way to learn is to build. But first, understand what you are building.*

Happy Flying

The Drone Community
