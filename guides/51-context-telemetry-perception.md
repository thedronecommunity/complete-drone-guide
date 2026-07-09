# AI Autonomous Drone, Part 6: Feeding the AI Good Context

> A decision is only as good as the information behind it. This guide is about giving the language model a rich, honest picture of the drone's state and surroundings, so its choices are grounded in reality rather than guesswork.

Created by [The Drone Community](https://github.com/thedronecommunity)

---

## What You Will Learn

- Why the quality of a decision depends on the quality of its context
- How the drone's telemetry and perception become something a language model can reason over
- Why a text-only model can still make good use of what the camera sees

By now the drone can understand you and judge safety. This guide is about making its understanding smarter by feeding it better information.

---

## Context Is Everything

A language model decides based on what it is told. Ask it what to do next while telling it nothing about the situation, and it can only guess. Tell it the drone is low on battery, near the edge of its allowed area, and looking at a person just ahead, and it can make a genuinely sensible choice. The difference between a shallow decision and a thoughtful one is almost entirely the context provided.

This is a general truth about working with language models, and it applies sharply here. The model is not psychic. It reasons over the words you give it. So the task is to hand it, every time it decides, a clear and honest summary of two things: how the drone is doing, and what it can see.

---

## Turning Telemetry into Understanding

The flight controller streams a constant flow of numbers: mode, altitude, speed, heading, battery level, whether it has a position lock, satellite count, attitude. On their own these are just readings. To be useful to a language model, they need to be presented as a clear statement of the drone's condition.

So the raw telemetry is gathered and phrased plainly: the drone is in this mode, at this height, moving at this speed, with this much battery, with or without a position lock. Presented this way, the model can reason about the drone as a situation rather than a spreadsheet. It can notice that the battery is getting low and lean toward returning home. It can notice there is no position lock and understand why caution is warranted.

The important design point is that this summary must be current and truthful. It is assembled from the live telemetry the companion computer already receives over the link from [Part 2](47-companion-mavlink-link.md). It is not guessed and it is not stale. A model given an out-of-date picture will make decisions for a situation that no longer exists, which is worse than making them blind.

---

## Turning Perception into Description

The camera and accelerator from [Part 3](48-edge-vision-hailo.md) produce detections. To feed a language model, those detections are turned into a description of the scene: how many objects, what kinds, where each sits in the frame, and roughly how close.

So instead of a list of coordinates, the model receives something like a spoken observation: the camera sees a person ahead and close, and a chair to the left and far. That is something a language model can reason about directly. It can decide to investigate the person, or to turn toward an area it has not looked at yet.

This description is the bridge between seeing and thinking. Perception produces boxes, the boxes become words, and the words become something the decision-maker can use.

---

## The Photograph Problem

Here is a subtlety that matters, and being honest about it leads to a better design.

A small language model running on the drone reads text, not images. It cannot literally look at a photograph. So how can it reason about what the camera sees?

The answer is that we do not send it the photograph. We send it the description of the photograph, produced by the vision system. The accelerator has already done the hard work of turning pixels into labelled objects, so the scene arrives at the language model already translated into words. In effect, the vision system is the drone's eyes and the language describing what it sees is the drone telling its own brain what is in front of it.

This is not a compromise so much as a sensible division of labour. Recognising objects in an image is exactly what the vision accelerator is built for, and it does it fast. Reasoning about goals and choosing actions is what the language model is good at. Handing the description from one to the other lets each do what it does best. A single model that tried to both see and reason would be slower and heavier, and on a drone, weight and speed are everything.

There is a more advanced path, using a model that genuinely understands images, but on a small flying computer that is slow and demanding, and for command-level decisions the described-scene approach gives most of the benefit for a fraction of the cost.

---

## Good Context, Good Decisions

When you put rich telemetry and a clear scene description together, the model's decisions visibly improve. It stops making choices in a vacuum and starts responding to the actual moment: cautious when the battery is low, curious when something interesting is in view, sensible about its position and limits.

And because this same context can be shown to you, it doubles as insight. When you can see what the drone knew at the moment it decided, its choices stop looking arbitrary. You understand why it did what it did, because you can see what it was working with. That transparency is the subject of the next guide.

---

## Related Guides

- [Part 5: The Safety Layer](50-safety-layer.md)
- [Part 3: Giving the Drone Eyes](48-edge-vision-hailo.md)
- [Part 7: The Live Dashboard](52-live-dashboard.md)

---

*Intelligence is mostly good information, well presented. Give the model a clear picture and it will surprise you.*

Happy Flying

The Drone Community
