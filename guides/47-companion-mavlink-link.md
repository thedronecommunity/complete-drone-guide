# AI Autonomous Drone, Part 2: Linking the Two Brains

> The connection everything else sits on. Before the drone can be intelligent, the companion computer and the flight controller have to be able to talk. This guide explains how that link works and how to tell when it is healthy.

Created by [The Drone Community](https://github.com/thedronecommunity)

---

## What You Will Learn

- How the Raspberry Pi and the flight controller actually communicate
- What a heartbeat is and why it matters more than you think
- How to reason about the link when something goes wrong

In [Part 1](46-ai-autonomous-drone-overview.md) we said the two brains talk over MAVLink. This guide is about making that real and understanding it deeply.

---

## Why Ethernet, and Why It Matters

There are several ways to connect a companion computer to a flight controller. Serial over the telemetry port is common. We use ethernet, because the CUAV V6X supports it and it gives a fast, reliable, low-latency link that behaves like an ordinary network connection.

Thinking of the link as a network connection is useful. The V6X gets its own IP address. The Pi gets an address on the same subnet. They exchange MAVLink messages over UDP, the same way any two devices on a network exchange data. Once you hold that picture, the whole thing stops being mysterious. It is just two computers on a tiny two-device network, one of which happens to be a flight controller.

The V6X network settings are stored on the flight controller itself and set once, over USB, using CUAV's configuration software. After that they persist. The Pi's address, on the other hand, usually needs to be set each time it boots, because by default it forgets network settings on the ethernet port between restarts. This asymmetry, the flight controller remembers, the Pi forgets, is worth internalising, because most link problems come down to the Pi's address not being set after a reboot.

---

## The Heartbeat

MAVLink devices announce themselves with a heartbeat, a small message sent about once a second that says "I am here and I am this kind of device". The flight controller sends heartbeats constantly. The companion computer listens for them.

The heartbeat is not just a hello. It is the foundation of link safety. If the heartbeats stop arriving, the Pi knows the connection to the flight controller has been lost, even if nothing threw an error. A drone that keeps flying a plan after it has lost contact with its own flight controller is dangerous. A drone that notices the heartbeat has gone silent and responds, by holding or landing, is behaving correctly.

So when we later build a safety layer, one of the first things it checks is the age of the last heartbeat. If the newest heartbeat is more than a few seconds old, the link is treated as lost, no matter what else is happening. The whole system's trust in "am I still connected" rests on this one steady pulse.

---

## What Healthy Looks Like

A healthy link has a few observable signs, and knowing them means you can diagnose problems by observation rather than guesswork.

First, the Pi can reach the flight controller on the network at all. This is the most basic check, the equivalent of confirming two devices can see each other before worrying about what they say.

Second, heartbeats arrive steadily, roughly one per second, without long gaps. A single heartbeat proves the connection exists. A steady stream proves it is stable. These are different things, and a link that connects once but then goes quiet is a link with a problem.

Third, telemetry flows. Beyond the heartbeat, the flight controller streams its state: mode, battery, attitude, position estimate. When that data updates continuously on the Pi, the link is doing its real job of keeping the companion computer informed.

---

## One Reader, One Connection

There is a subtle idea here that causes real trouble if you miss it, so it is worth stating plainly. A single MAVLink connection should be read by one part of your software, not several.

Messages arrive in a stream. If two different parts of the program both try to pull messages from the same connection, they steal messages from each other. One part grabs a heartbeat the other was waiting for, and now the second part thinks the link has gone quiet when it has not. The result is a system that reports "link lost" while the link is perfectly healthy.

The fix is architectural, not a patch. One piece of the software owns the connection and reads everything from it, then shares what it read with the rest of the system. Everyone else asks that owner for the latest known state rather than reading the wire themselves. Hold this principle and a whole category of baffling intermittent failures never happens.

---

## Reasoning About Link Problems

When the link misbehaves, the cause is almost always one of a small number of things, and you can reason through them in order.

If the Pi cannot reach the flight controller at all, the physical connection or the addresses are wrong. Check that both devices have power and a live ethernet connection, then check that the Pi's address was actually set after its last reboot, since that is the most common single cause.

If the Pi can reach the flight controller but sees no heartbeat, the flight controller may still be starting up, or its network settings may not be sending data to the right place. Give it time to boot, then verify the flight controller's stored network configuration.

If heartbeats arrive but then stop, either the link is genuinely marginal, worth checking power and cabling, or something in the software is stealing messages, which brings you back to the one-reader principle above.

Reasoning this way, from symptom to likely cause, is far more reliable than trying random fixes.

---

## Why This Is the Foundation

Everything in the rest of this series assumes this link works. The vision system's decisions are meaningless if they cannot reach the flight controller. The language model's understanding is useless if its chosen action never arrives. The safety layer cannot judge the drone's state if telemetry is not flowing.

Get this link solid and observable first. When you can watch a steady heartbeat and live telemetry arriving on the Pi, you have the ground the whole system stands on.

---

## Related Guides

- [Part 1: Overview and Architecture](46-ai-autonomous-drone-overview.md)
- [Guide 08: Companion Computers](08-raspberry-pi-integration.md)
- [Part 3: Edge Vision with the Hailo-8L](48-edge-vision-hailo.md)

---

*A drone that cannot talk to its own flight controller is not autonomous. It is just heavy.*

Happy Flying

The Drone Community
