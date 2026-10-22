# AI Autonomous Drone, Part 4: The Language Brain

> How a drone understands plain English. This guide is about turning "go find a person" into an action the drone can actually take, using a language model, while making sure the model can never ask the drone to do anything reckless.

Created by [The Drone Community](https://github.com/thedronecommunity)

---

## What You Will Learn

- How a language model becomes a drone's command interpreter
- The idea of constrained autonomy, and why it is the key to safe AI control
- The trade-off between running the model on the drone and in the cloud

The drone can now talk to its flight controller and see the world. This guide gives it the ability to understand you.

---

## From Words to Actions

Natural language is how people naturally express intent. "Come back", "look around", "find someone", these are effortless for a human and meaningless to a flight controller, which only understands precise commands like arm, take off to a height, or fly to a coordinate.

A language model bridges that gap. It reads your plain-English request and produces a structured action the rest of the system can execute. You say "come home", and the model produces the "return home" action. You say "see what is around you", and it produces a "scan" action.

The model is acting as an interpreter, translating from the loose, ambiguous language people use into the small, exact vocabulary a drone acts on. That translation is the whole job. The model does not fly the drone. It decides what the drone should be asked to do.

---

## The Crucial Idea: Constrained Autonomy

Here is the most important concept in this entire series, and the reason an AI-controlled drone can be safe at all.

The language model does not get to invent commands freely. It is given a fixed menu of allowed actions, take off, land, move a short distance, rotate, scan, search for an object, follow, approach, return home, hover, stop, and it may only choose from that menu. It cannot decide to climb to a hundred metres, or fly at full speed, or do anything not on the list, because those are simply not options it can pick.

This is called constrained autonomy. The model has genuine freedom to decide what to do, which of the safe actions best fits your request, but no freedom to do something outside the safe set. Even a strange or impossible request gets channelled into the nearest allowed action, or into a safe default like stopping.

Contrast this with the naive alternative, letting a model generate raw flight commands directly. That would put an unpredictable system in direct control of a flying machine, free to produce a command no one anticipated. Constrained autonomy keeps the flexibility of AI decision-making while removing the ability to act dangerously. The model proposes from a safe menu, and as the next guide covers, a separate safety layer still checks even those choices.

The mental picture worth keeping: the model is a smart assistant choosing from a printed menu, not a stranger allowed to write anything they like on a blank cheque.

---

## Showing Its Reasoning

A useful property of language models is that they can explain their choice. Alongside the action, the model gives a short reason: it chose "return home" because "the user asked to come back", or "scan" because "the user wants to see the surroundings".

This reasoning is worth surfacing rather than hiding. It turns the system from a black box that reacts into something you can watch think. When you can see both what the model chose and why, you can judge whether it understood you, and a room full of people watching can see that a real decision is being made rather than a script running.

---

## Two Places to Run the Brain

The language model can run in two very different places, and the choice shapes the character of the whole system.

Running it locally, on the drone's own companion computer, means the intelligence is genuinely on the aircraft. No internet is needed. Nothing about the drone's situation leaves the drone. It is private, it is free to run, and it works in a field with no signal. The cost is speed and size: a computer small enough to fly can only run a modest model, and it thinks more slowly, taking several seconds to decide. For deliberate commands this is perfectly acceptable. This local path is the true "autonomous drone" story, the drone needs nothing but itself.

Running it in the cloud, calling a large model over the internet, gives you far stronger reasoning and faster responses. The cost is that it needs connectivity, it depends on an outside service, and it sends your request off the drone. For a demonstration where you have good internet, this shows what a frontier model does with the same task, and the contrast with the local model is illuminating.

Neither is simply better. The local model embodies self-contained autonomy. The cloud model shows the ceiling of what is possible. A well-built system lets you switch between them and understand the trade-off you are making.

---

## Why This Still Is Not Enough

The language model, even constrained to a safe menu, is not the last word on safety, and it is important to understand why.

The menu limits what kinds of actions are possible, but it does not know the drone's current situation. "Take off" is a perfectly reasonable menu item, but taking off is not safe if the drone has no idea where it is, or if the battery is nearly flat, or if the link is failing. The model choosing a menu item does not mean that item is safe right now.

That judgement, is this specific action safe given the drone's actual state at this moment, is a separate job. It belongs to the safety layer, which every action passes through before it reaches the flight controller. That is the next guide, and it is what makes the difference between a clever demo and a system you can trust.

---

## Related Guides

- [Part 3: Giving the Drone Eyes](48-edge-vision-hailo.md)
- [Guide 10: AI and Computer Vision](10-ai-integration.md)
- [Part 5: The Safety Layer](50-safety-layer.md)

---

*The model decides what you meant. Whether it is safe to do is a different question, answered next.*

Happy Flying

The Drone Community
