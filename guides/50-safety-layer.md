# AI Autonomous Drone, Part 5: The Safety Layer

> The part that makes AI control trustworthy. A language model can choose a sensible action, but sensible is not the same as safe right now. This guide is about the hard-coded layer that judges every action against the drone's real situation, and why the AI can never get around it.

Created by [The Drone Community](https://github.com/thedronecommunity)

---

## What You Will Learn

- Why safety has to be separate from the AI that makes decisions
- What the safety layer actually checks, and how it responds
- The difference between blocking an action and warning about it

[Part 4](49-language-brain-llm.md) ended on a problem: the language model can pick a safe kind of action without knowing whether it is safe at this moment. This guide solves that problem.

---

## Propose and Dispose

The design principle at the heart of safe autonomy can be said in four words: the AI proposes, the safety layer disposes.

The language model's job is to decide what you want. The safety layer's job is to decide whether the drone may do it right now. These are kept completely separate on purpose. The model is flexible, language-driven, and occasionally wrong. The safety layer is rigid, rule-driven, and predictable. You want both qualities in the system, but you never want them in the same component, because flexibility and reliability pull in opposite directions.

So every action the model chooses, without exception, passes through the safety layer before it reaches the flight controller. The model never talks to the drone directly. It talks to the safety layer, which then decides what actually happens.

---

## Why It Must Be Separate and Hard-Coded

It is tempting to imagine simply instructing the language model to be safe, to tell it in its prompt never to do anything dangerous. This is not enough, and understanding why is important.

A language model's behaviour is probabilistic. It usually follows instructions, but "usually" is not a foundation for flight safety. It can misread a situation, be talked into something by an unusual request, or simply produce an unexpected result. Any safety that lives inside the model shares the model's fallibility.

The safety layer is different because it is ordinary, deterministic code. Given the same situation it always makes the same judgement. It has no creativity to be exploited and no ambiguity to be misread. When it says an action is not allowed because the battery is too low, that is a fixed rule, not an opinion that can be argued with. The model cannot see this code, cannot change it, and cannot route around it. That separation is exactly what makes it trustworthy.

---

## What It Checks

The safety layer holds a set of limits about the drone's real state and judges each action against them. The specific checks are less important to memorise than the pattern they form, but they are worth walking through because each one exists for a concrete reason.

It checks for a position lock. If the drone has no reliable idea of where it is, from GPS outdoors or optical flow indoors, then any command to move is refused, because a drone that moves without knowing its position drifts blindly and crashes. This is the check you meet most often on a bench without sensors, and it is the system being correct, not broken.

It checks altitude against a ceiling, so the drone cannot be sent dangerously high. It checks distance from home against a geofence, so it cannot wander beyond a set boundary. It checks the battery, and when the battery falls too low it stops obeying exploration commands and forces the drone to return or land, because getting home matters more than the current task. It checks the link, and if the heartbeat has gone silent it lands rather than flying on blind. It caps how far a single move can go, so no one command sends the drone across a field. And anything it does not recognise becomes a safe hover, because the safe response to confusion is to stop and hold.

---

## Allow, Clamp, Block, Override

The safety layer does not simply say yes or no. It has a range of responses, and this nuance is what makes it practical rather than obstructive.

Sometimes it allows an action unchanged, because the action is safe as requested. Sometimes it clamps an action, keeping the intent but reducing it to something safe, turning a request to fly ten metres into a shorter move that stays inside the geofence. Sometimes it blocks an action entirely and substitutes a safe hover, because the action cannot be made safe in the current situation. And sometimes it overrides, imposing its own action regardless of what was asked, as when a critically low battery forces a landing no matter what command just came in.

Crucially, whatever it decides, it says why. "Blocked, no position lock." "Clamped to the geofence." "Battery low, returning home." This running explanation is what lets you, and a watching audience, see the safety reasoning happen in real time.

---

## Blocking Versus Warning

There is a meaningful choice in how strict the layer is, and it is worth understanding as a spectrum rather than a switch.

In its normal mode, the safety layer blocks unsafe actions. This is right for real flight, where an unsafe action must not happen.

There is also a gentler mode where the layer warns but does not block. It still evaluates every action and still tells you exactly what it objects to, but it lets the command proceed. This exists for controlled bench work, where you want to see the whole system exercise every command with the propellers off and no possibility of harm, while still seeing what the safety layer would have prevented.

The distinction to hold clearly is that warning mode removes the software guardrail, not the physics. Even with the safety layer set only to warn, the flight controller underneath has its own independent requirements. It will not truly fly to a position it cannot navigate to without a position source. So warning mode is a way to observe and demonstrate the full software pipeline safely, not a way to make a sensorless drone fly. The deepest safety, the flight controller's own refusal to do the impossible, is not something any companion-computer setting can switch off.

---

## The Human Is the Final Layer

One last point completes the safety picture. Above all the software sits the pilot with the radio transmitter. At any moment, a flick of a mode switch on the transmitter takes control away from the AI entirely. No amount of software, and no override or warning mode, changes that. The transmitter is the ultimate safety layer, and it is always yours.

This is why autonomy and safety are not in tension when the system is built correctly. The AI has real freedom within bounded limits, the safety layer enforces those limits in predictable code, and the human holds an override that trumps everything. Each layer covers what the others cannot.

---

## Related Guides

- [Part 4: The Language Brain](49-language-brain-llm.md)
- [Part 6: Feeding the AI Context](51-context-telemetry-perception.md)
- [Guide 05: Failsafes and Safety Systems](05-failsafes.md)

---

*Freedom to decide, within limits that cannot be argued with. That is how you trust a machine to fly itself.*

Happy Flying

The Drone Community
