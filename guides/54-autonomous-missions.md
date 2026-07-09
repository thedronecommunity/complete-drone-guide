# AI Autonomous Drone, Part 9: The Closed Loop

> Where it all comes together. Until now the drone has responded to commands one at a time. This guide is about the drone acting on its own, perceiving, reasoning, and acting in a continuous loop, deciding for itself what to do next.

Created by [The Drone Community](https://github.com/thedronecommunity)

---

## What You Will Learn

- The difference between responding to commands and true autonomy
- How the perceive, reason, act loop works
- Why open-ended autonomy still lives inside the same safe limits

Every piece is now in place. This guide connects them into a system that runs itself.

---

## From Commanded to Autonomous

There is a real distinction between a drone that does what you tell it and a drone that decides what to do. So far we have built the first. You type a command, it interprets, checks, and acts, then waits for the next command. Useful, but still fundamentally driven by you.

True autonomy removes the waiting. The drone takes off and then, on its own, repeatedly asks itself what to do next, given what it currently sees and knows. It might scan its surroundings, notice something interesting, move to investigate, take a closer look, and eventually decide it has seen enough and return home. No single one of those steps was commanded. The drone chose each in turn, based on the situation it found itself in.

This is the difference between a tool and an agent. A tool acts when used. An agent acts on its own toward a purpose. The same components we have built support both, the shift is in how they are wired together.

---

## The Loop

Autonomy, at its core, is a loop with three steps repeated over and over, and it is worth understanding as a rhythm the drone settles into.

First it perceives. It gathers the current picture: what the camera sees, described in the way from [Part 6](51-context-telemetry-perception.md), along with its own telemetry, its height, battery, position, and heading.

Then it reasons. It hands that picture to the language brain and asks a single question: given all this, what is the best next action? The model, choosing as always from the safe menu, decides. Perhaps to turn and look somewhere new, perhaps to approach something it noticed, perhaps to come home because the battery is running down.

Then it acts. The chosen action passes through the safety layer, exactly as a typed command would, and if allowed, it executes. The drone moves, or turns, or looks.

And then the loop begins again. Having acted, the drone perceives its new situation, reasons about it afresh, and acts again. Round and round, each cycle informed by the results of the last. This continuous perceive, reason, act rhythm is what autonomy actually is, underneath all the sophistication. Not a grand plan computed once, but a steady stream of small, situated decisions.

---

## Planning and Reacting Together

A good autonomous system balances two instincts that sound opposed but work together.

One is to have a sense of purpose, a general intent that shapes its choices, to explore, to find something, to cover an area. Without this the drone would wander aimlessly. The intent gives its decisions direction.

The other is to react to what actually happens. Plans made in advance meet a world that does not cooperate. Something appears that was not expected. A path is not what was imagined. A rigid plan shatters against reality, but a drone that re-evaluates every cycle simply folds the surprise into its next decision. It planned to explore, it sees a person, it adapts to investigate, then returns to exploring. The loop makes this natural, because every cycle is a fresh chance to reconsider.

This is why the loop re-reasons each time rather than computing one plan and marching through it. Re-deciding continuously is what lets the drone stay sensible in a world that keeps changing.

---

## Open-Ended, But Still Bounded

It would be easy to hear "the drone decides everything itself" and feel a flicker of alarm. That instinct is correct, and the design answers it.

Open-ended autonomy does not mean unlimited autonomy. Every single decision the drone makes still passes through the safety layer from [Part 5](50-safety-layer.md). The drone is free to choose what to do, but only from the safe menu, and only within the altitude, distance, battery, and position limits the safety layer enforces. The loop cannot decide to fly too high or too far, because those are not choices available to it. It can explore freely inside a fenced yard, but it cannot leave the yard, however it reasons.

There are also hard limits on the loop itself. It runs for a bounded number of steps, not forever. It watches the battery and brings the drone home when it runs low. It respects the geofence. And beneath all of it, the flight controller's own protections and the pilot's radio override remain in force. The autonomy is genuine, the drone really is deciding, but it decides inside a cage it cannot open. That combination, real freedom within fixed bounds, is the whole art of making autonomy that is both capable and safe.

---

## Why This Is the Payoff

This loop is what everything else was building toward. The link let the brains talk. Perception gave the drone sight. The language model gave it understanding. The safety layer made it trustworthy. Context made its decisions sharp. The dashboard made it visible. Position sensing let it fly. And now the loop takes all of that and lets the drone run on its own, perceiving and deciding and acting without a hand on the controls, while never stepping outside the limits that keep it safe.

That is an autonomous drone in the fullest sense. Not a remote-controlled aircraft, and not a scripted routine, but a machine that senses its situation and chooses its own actions toward a purpose, moment after moment, safely.

---

## Related Guides

- [Part 8: Flying Without GPS](53-optical-flow-gps-denied.md)
- [Part 5: The Safety Layer](50-safety-layer.md)
- [Part 10: From Simulation to Real Flight](55-simulation-to-real.md)

---

*Autonomy is not one big decision. It is a thousand small ones, each made in its moment.*

Happy Flying

The Drone Community
