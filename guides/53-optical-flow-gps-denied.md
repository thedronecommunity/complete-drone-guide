# AI Autonomous Drone, Part 8: Flying Without GPS

> The sensor that unlocks indoor autonomy. Everything so far runs, but the drone keeps refusing to fly because it does not know where it is. This guide is about how a drone can know its position without GPS, and why that single capability changes everything.

Created by [The Drone Community](https://github.com/thedronecommunity)

---

## What You Will Learn

- Why position, not intelligence, is the thing standing between you and real flight
- How a drone can know where it is indoors, with no GPS
- Why proving position hold is the gate you must not skip

Through the last several guides, one refusal has kept appearing: no position lock. This guide is about removing it honestly.

---

## The Missing Piece

It is worth appreciating the situation we have reached. The drone can understand language, see the world, reason about context, and judge safety. It is, in every sense that involves thinking, complete. And yet it will not fly a single autonomous metre.

The reason is not a failure of intelligence. It is a failure of position. An autonomous drone must know where it is to move safely, because every movement command is really an instruction to change position, and you cannot change a position you cannot measure. Outdoors, GPS provides this. Indoors, where most workshops and early testing happen, there is no GPS, and so the drone is blind to its own location no matter how clever its brain.

This is why the safety layer has been so insistent. It was never being obstructive. It was refusing to let a drone move when it had no way to know the result of moving. Give the drone a sense of position, and that entire category of refusal disappears, and the same commands that were blocked begin to work, with no change to any of the intelligence built earlier.

---

## Seeing the Ground to Know You Are Still

The elegant solution to indoor position is to look at the ground. An optical flow sensor points downward and watches the texture of the surface below. As the drone drifts, that texture appears to move across the sensor, in the same way the ground appears to slide past when you look down from a moving vehicle. From how the texture moves, the sensor works out how the drone is moving.

There is a catch that reveals something important about how this works. Motion seen by the sensor only tells you real-world movement if you also know how high up you are. The same drift looks like a small movement high up and a large one close to the ground, because things farther away appear to move less. So an optical flow sensor needs a companion measurement: height above the ground. Sensors like the MTF-02 include a small downward laser rangefinder for exactly this, measuring height directly so the flow can be turned into true motion.

Put together, downward-looking flow plus a height measurement gives the flight controller what it needs: a continuous estimate of how the drone is moving across the ground, and therefore where it is. That is a position lock, earned without a single satellite.

---

## Why It Must Be Calibrated and Tested

This capability comes with a responsibility, and skipping it is how people crash drones. Optical flow must be set up carefully and proven before it is trusted, because a position estimate that is subtly wrong is more dangerous than none at all. A drone that thinks it is holding still while actually drifting will drift right into a wall, confidently.

The setup involves telling the flight controller which sensor it has and how it is connected, then confirming each layer works before relying on it. The height measurement must read correctly, because everything depends on it. The flow must respond when the drone moves over textured ground, because flow needs texture to see, a blank shiny floor gives it nothing to track. And the sensor's scaling must be calibrated so that the motion it reports matches the motion that actually happened.

None of this is optional, and it must be verified on your actual floor, not assumed from a guide, because the specifics depend on your sensor, your mounting, and your surface.

---

## The Gate You Do Not Skip

There is one test that decides whether the drone is ready for autonomous flight, and it is simple to describe. With everything set up, put the drone in a position-hold mode over textured ground and let go. If it holds its spot, steady, not wandering, then the position system works and the drone genuinely knows where it is. If it drifts, something is wrong, and you must not fly autonomously until it does not.

This position-hold test is the gate. It sits between "the system runs" and "the drone can be trusted to fly itself". Everything before it is preparation. Passing it is the moment the drone becomes capable of real autonomous flight, because it has proven it can do the one thing all the intelligence depends on: know where it is and stay there.

Treat this gate with respect. It is the honest dividing line, and the temptation to skip ahead of it is exactly the temptation that ends in a crash.

---

## What Changes After the Gate

Once position hold is proven, the character of the whole system shifts. The commands you have been watching get refused begin to execute. "Go forward" actually moves the drone forward, a measured, controlled distance, because now the drone can measure the result. The safety layer stops blocking movement, not because it was disabled, but because the condition it was protecting against, moving without knowing position, no longer holds.

Nothing about the intelligence changed. The language model, the perception, the safety layer, the context, all of it is exactly as it was. The only new thing is that the drone knows where it is, and that one addition turns a thinking system into a flying one.

---

## Related Guides

- [Part 7: Making It Visible](52-live-dashboard.md)
- [Guide 15: Advanced Navigation](15-advanced-navigation.md)
- [Part 9: Fully Autonomous Missions](54-autonomous-missions.md)

---

*The drone was never missing a brain. It was missing a place to stand.*

Happy Flying

The Drone Community
