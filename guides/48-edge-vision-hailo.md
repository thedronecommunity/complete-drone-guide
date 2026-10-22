# AI Autonomous Drone, Part 3: Giving the Drone Eyes

> How a drone sees. This guide is about turning a camera into perception: running a real neural network on dedicated hardware so the drone understands what is in front of it, fast enough to act on.

Created by [The Drone Community](https://github.com/thedronecommunity)

---

## What You Will Learn

- The difference between a camera and perception
- Why object detection runs on a separate accelerator, not the Pi's main processor
- What a detection actually tells the drone, and what it does not

With the link from [Part 2](47-companion-mavlink-link.md) in place, the companion computer can command the drone. Now we give it something to base those commands on: sight.

---

## A Camera Is Not Perception

A camera gives you pixels. That is all. A grid of colour values that changes as the world moves. There is no "person" or "chair" in that grid, only numbers. Perception is the act of turning those numbers into meaning: this region is a person, that one is a car, and here is how confident we are.

The tool that does this is an object detection model. You have almost certainly heard of YOLO, which stands for You Only Look Once, a family of models designed to find and label objects in an image very quickly. Given a frame, it returns a set of boxes, each with a label and a confidence score, saying what it found and where.

This is the drone's understanding of the visual world. Everything the AI later decides about what it sees is built on these boxes.

---

## Why a Separate Accelerator

Running a neural network is a lot of arithmetic. A Raspberry Pi's main processor can do it, but slowly, so slowly that by the time it has analysed one frame, the world has moved on. For a drone that needs to react to what it sees, slow perception is close to useless.

This is where edge AI hardware comes in. The Hailo-8L is a small accelerator designed to run neural networks efficiently. It does the detection work in dedicated silicon, freeing the Pi's processor for everything else and returning results fast enough to be useful in real time.

The principle here is broader than one chip. It is the idea of running the neural network at the edge, on the drone itself, on hardware suited to the task, rather than shipping images off to a server. The drone perceives locally and instantly, which is exactly what an autonomous system needs and what keeps it working with no connectivity at all.

The camera provides frames. The accelerator turns them into detections. The Pi reads those detections and reasons about them. Three steps, each on the right hardware.

---

## What a Detection Tells You

Each detection is more informative than just "there is a person". Understanding what is in it shapes what the drone can do with it.

There is a label, the kind of object. There is a confidence, how sure the model is, which matters because a low-confidence detection should be trusted less. There is a position in the frame, which tells you whether the object is to the left, centre, or right of where the drone is looking. And there is a size, how large the object appears, which is a rough clue to how close it is, since nearer things look bigger.

From these simple facts, useful behaviour follows. If a person is detected in the centre of the frame, the drone knows the person is roughly ahead of it. If that same person grows larger over successive frames, the drone is getting closer. This is enough to centre on a target, to search for a particular kind of object, or to notice when something is close.

---

## What Detection Does Not Give You

It is just as important to be honest about the limits, because assuming perception knows more than it does leads to bad decisions.

A detection does not give you true distance. Size is a hint, not a measurement. A small, nearby object and a large, distant one can appear the same size. Without a dedicated depth sensor, the drone is estimating closeness, not measuring it, and that estimate should be treated as rough.

A detection does not give you identity. The model knows "a person" not "which person". Following a specific individual across frames requires extra logic to keep track of which detection is the same object from one moment to the next, because the model itself just reports what it sees each frame with no memory.

And detection quality depends on conditions. Poor light, unusual angles, and objects the model was never trained on all reduce reliability. Perception is powerful but not infallible, and a well-designed system treats it as one input to be sanity-checked, not as ground truth.

---

## What Working Looks Like

The satisfying proof that perception is alive is simple and human. Stand in front of the camera, and a box appears around you with a label and a confidence score that updates live as you move. Hand the moment to someone else and watch it track them.

That live, responsive box is the whole point. It is the drone genuinely seeing, in real time, on its own hardware. Once that works, the drone has eyes, and the next guide gives it the ability to understand what you ask it to do with them.

---

## How Vision Feeds Everything Else

Perception on its own does nothing. Its value is as an input to decisions. When you later ask the drone to find a person, the system is scanning these live detections for the right label. When the drone approaches something, it is watching the box grow to judge closeness. When we build the safety layer, vision can act as a crude check for something large directly ahead.

So this guide is not a standalone feature. It is a sense. The intelligence that uses that sense comes next.

---

## Related Guides

- [Part 2: Linking the Two Brains](47-companion-mavlink-link.md)
- [Guide 10: AI and Computer Vision](10-ai-integration.md)
- [Guide 34: Edge Computing and Embedded AI](34-edge-ai.md)
- [Part 4: The Language Brain](49-language-brain-llm.md)

---

*Sight without judgement is just data. Next we add the judgement.*

Happy Flying

The Drone Community
