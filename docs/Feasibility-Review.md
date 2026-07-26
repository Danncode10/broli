# BROLI Engineering Feasibility Review

## Conclusion

BROLI's core concept is feasible: a differential-drive robot using wheel encoders, a 2D LiDAR, an ESP32 for low-level control, and a Raspberry Pi 5 running ROS 2 can map a controlled indoor space and navigate slowly between defined waypoints.

The current documents describe a strong product vision, but the listed hardware and roadmap do **not yet make the complete robot build-ready**. In particular, the power/charging design, near-field safety sensors, cleaning mechanism, and docking design require explicit engineering decisions and testing before BROLI can operate autonomously around people.

The recommended thesis claim is therefore:

> A supervised autonomous indoor service-robot prototype for a controlled library or office test area.

This is a credible, demonstrable outcome. Features such as full voice conversation, cloud analytics, gesture tracking, congestion heatmaps, and fully unattended docking should be treated as stretch goals until the mobile platform is reliable and safe.

---

## Feasibility by Capability

| Capability | Assessment | Conditions for a valid claim |
| --- | --- | --- |
| Manual differential-drive motion | Feasible now | Correct motor wiring, encoder reading, motor limits, master switch, and physical emergency stop. |
| Live LiDAR visualization | Feasible now | Stable USB driver and a rigid LiDAR mount for mobile tests. |
| Mapping and waypoint navigation | Feasible | Calibrated wheel odometry, IMU, robot geometry, ROS 2 frames, and a controlled test environment. |
| Dynamic obstacle avoidance | Feasible with limits | Slow speed, conservative safety margins, and additional near-field/drop protection. Do not claim guaranteed collision prevention. |
| Basic debris collection | Possible but unproven | An actual intake, dust bin, filter, and measured debris-collection test. A blower alone may scatter debris. |
| Autonomous docking | Possible, high risk | Closed-loop final alignment, robust contacts, charging confirmation, retry/recovery behavior, and an approved battery-charging design. |
| Touchscreen book lookup and QR guidance | Feasible | A known library data source and a functional local/web interface. |
| Voice/NLP and live library database integration | Feasible but external dependency | Network fallback, API costs, privacy approval, and a confirmed database/API integration path. |
| Webcam/gesture tracking, noise enforcement, analytics | Stretch scope | Explicit privacy policy, data retention rules, and successful completion of the core robot first. |

---

## Mandatory Gaps Before Autonomous Operation

### 1. Power and charging system

The current list includes a 3S 11.1 V LiPo, an XL4015 buck converter, an IP2326 charging board, and a USB-C/QC wall adapter. This is not yet a complete or approved mobile power design.

Required decisions and tests:

- Use a battery and charger arrangement that explicitly supports the exact 3S LiPo pack, including balance charging and appropriate protection.
- Create one reviewed wiring diagram showing battery, fuse, master switch, emergency stop, charger, motors, blower, regulators, and all grounds.
- Place a correctly rated fuse close to the battery positive terminal.
- Use a regulated 5 V rail with sufficient continuous and peak current for the Raspberry Pi 5 and peripherals. The Raspberry Pi 5 recommends 5 V at 5 A; its USB peripheral allowance is reduced when supplied with less power.
- Keep motor/blower power separate from the computer's regulated rail, with a common ground only where required by the motor-driver/control design.
- Measure voltage sag and current during wheel acceleration, stalled-wheel protection tests, blower operation, and full-compute load.
- Monitor battery voltage and preferably current consumed; voltage alone is not a reliable state-of-charge measurement under motor load.

**Do not begin autonomous docking or unattended charging until this design has passed supervised bench tests.**

### 2. Safety system

A 2D LiDAR observes only one horizontal plane. It can miss stair edges, low obstacles, hanging objects, transparent or highly reflective surfaces, and parts of people outside its scan plane.

Minimum safety equipment:

- Latching physical emergency-stop button that disables motor power or motor-driver enable independently of the browser, Wi-Fi, Raspberry Pi, and ROS 2.
- Command watchdog on the ESP32: loss of valid commands must stop the motors and blower within a defined timeout.
- Front bumper/contact switches.
- Cliff/drop sensors for stairs and level changes.
- IMU for orientation/odometry fusion.
- Conservative maximum speed, acceleration, obstacle-inflation radius, and braking-distance limits.
- Manual supervised testing area with no public operation until the stop behavior has been measured repeatedly.

The dashboard's stop button is useful, but it is an administrative control, not the primary safety mechanism.

### 3. Odometry, localization, and robot geometry

Wheel encoders alone drift because of wheel slip, unequal traction, and calibration error. The plan should add an IMU and publish a valid ROS 2 transform chain:

```text
map -> odom -> base_link -> lidar_link
                         -> camera_link
```

Plain-language explanation:

- An IMU is a small motion sensor. For BROLI, its most useful part is the gyroscope, which measures how fast the robot is turning.
- Odometry is the robot's running estimate of where it is based on its own movement sensors.
- Wheel encoders estimate movement by counting wheel rotation. This is useful, but it can be fooled when a wheel slips, one wheel grips more than the other, or the wheel size/spacing is slightly miscalibrated.
- IMU and odometry fusion means software combines the wheel encoder estimate with the IMU's measured rotation to create a better short-term movement estimate.

For BROLI, think of it like this:

```text
wheel encoders -> distance traveled and approximate turns
IMU gyro       -> actual turning motion
                 |
                 v
          fused odometry
                 |
                 v
      smoother local navigation
```

This fused odometry still drifts over time. It is not a perfect global position. SLAM or localization then uses the LiDAR and map to correct long-term error. In ROS/Nav2 terms, the usual split is:

```text
map       = corrected global position from SLAM/localization
odom      = smooth short-term position from encoder/IMU fusion
base_link = BROLI's physical center, usually between the drive wheels
```

Example: if BROLI drives down an aisle and the left wheel slips, the encoders may think the robot moved straight when it actually rotated a little. The IMU catches that rotation. Later, LiDAR localization can compare the shelves/walls it sees against the map and correct the remaining position error.

Required configuration and calibration:

- Wheel radius, track width, encoder counts per revolution, motor direction, and maximum velocity.
- Encoder electrical compatibility with the ESP32's 3.3 V inputs; add level shifting or conditioning if the chosen encoder outputs require it.
- LiDAR height, orientation, and rigid transform relative to `base_link`.
- Odometry covariance and encoder/IMU fusion strategy.
- Robot footprint, inflation radius, minimum turning radius, and stop distance in the navigation costmaps.

Mapping and navigation should first be proven at low speed in one uncluttered test zone, then tested against a fixed set of ordinary obstacles.

### 4. Cleaning mechanism

The present bill of materials specifies a centrifugal blower, but not the rest of a vacuum or debris-collection system. A moving blower is not sufficient evidence of floor cleaning.

Before claiming cleaning capability, specify and build:

- Floor intake/nozzle geometry.
- Brush or other debris-agitation method, if needed.
- Sealed airflow path, removable dust bin, and filter.
- Blower current draw, switching method, fuse, and thermal behavior.
- A cleaning test protocol: surface, debris type and mass, lane size, robot speed, and percentage collected.

Until this test is passed, describe the subsystem as a **light debris-collection experiment**.

### 5. Docking and charging interface

A map waypoint can bring the robot near the dock. It cannot by itself guarantee contact alignment. Repeatedly mating a conventional USB-C plug and receptacle using robot motion is mechanically fragile.

The docking design should require:

- A pre-dock pose and controlled final-approach state machine.
- Fiducial detection such as an AprilTag, plus close-range alignment sensing.
- Mechanical guide rails that tolerate small lateral and angular errors.
- Durable spring/pogo charging contacts or another connector explicitly designed for repeated docking, rather than relying on forced USB-C insertion.
- Dock-contact and actual charging-current confirmation.
- Timeout, back-out, retry, and assistance-needed states.
- A safe failure policy: low battery must leave enough reserve to stop and alert rather than repeatedly attempting to dock.

Changing a dock coordinate in the dashboard must be validated against the current map, floor clearance, pre-dock pose, and final-alignment marker before the robot accepts it.

---

## Thesis 1 Review: Connected MVP

The Thesis 1 scope is appropriate if it is framed as a **supervised, manually driven integration prototype**.

The LiDAR-to-MacBook USB arrangement is adequate for live scans and mapping experiments, but it does not prove onboard autonomous obstacle avoidance: the LiDAR cable and host remain external to the moving robot. The basic obstacle rule should therefore be an operator-assist warning or stop demonstration, not a claim of autonomous safety.

Thesis 1 acceptance gates:

1. Physical emergency stop halts motor power independently of software.
2. ESP32 command watchdog stops motion after a tested communications timeout.
3. Both motors drive in the correct direction, brake/stop predictably, and encoder values are stable.
4. The LiDAR produces a stable scan while mounted on the test base or during controlled mapping runs.
5. The dashboard reports command, connection, encoder, and safety state accurately.
6. The obstacle demonstration uses a defined stop distance and slow test speed.
7. The cleaning test demonstrates measured debris collection, or is honestly reported as blower operation only.

Buying the Raspberry Pi in Thesis 2 remains reasonable, but the team should use a Linux-capable development environment early enough to validate the actual ROS 2 LiDAR, transform, SLAM, and navigation workflow. Do not leave the first full ROS 2 hardware integration to the final semester.

---

## Thesis 2 Review: Standalone Robot

Thesis 2 should be ordered by technical dependency, not by feature attractiveness.

1. Complete validated portable power and safety hardware.
2. Move LiDAR and high-level software to the Raspberry Pi.
3. Add IMU, calibrated odometry, ROS 2 transforms, mapping, and localization.
4. Demonstrate slow autonomous waypoint navigation and safe stop behavior.
5. Demonstrate the measurable cleaning subsystem.
6. Add supervised docking with charge confirmation.
7. Add touchscreen book lookup and QR flow.
8. Add cloud, voice, camera, analytics, and optional interaction features only if the core platform remains stable.

Core navigation must continue to work when the touchscreen, webcam, cloud connection, audio system, and NLP API are disconnected or unavailable.

---

## External Dependencies and Responsible Deployment

The software features need non-technical agreements as well as code:

- Confirm that the target library system exposes a usable API, export, or approved read-only data source. Do not assume access to a live inventory database.
- Define an offline fallback: touchscreen search from a cached dataset, simple facility directions, or a clear "service unavailable" state.
- Budget API use and protect credentials; never embed cloud API keys in the dashboard client.
- Obtain approval for microphone use, webcam processing, noise logging, and any congestion or interaction analytics. Define what is recorded, retained, displayed, and deleted.
- Treat QR links as public, short-lived or non-sensitive references. Respect e-library licensing and access-control rules.
- Keep cloud configuration separate from the real-time safety path. Validate map, no-go-zone, and dock updates before activating them on the robot.

---

## Recommended Scope Statement

### Core deliverable

BROLI autonomously and slowly navigates a mapped, controlled indoor test environment; stops safely for defined obstacles; performs a measurable light-debris collection task; reports status locally; and provides touchscreen-based library/facility guidance.

### Stretch deliverables

- Reliable autonomous recharge docking.
- Wake-word and cloud voice conversation.
- Live library-inventory integration.
- Camera-based face/gesture interaction.
- Ambient-noise reminders.
- Congestion heatmaps and predictive-maintenance analytics.
- Cloud map editing in live deployment.

---

## Evidence Required for the Final Demonstration

The final evaluation should include repeatable evidence rather than feature descriptions alone:

| Test | Required evidence |
| --- | --- |
| Emergency stop | Recorded repeated stops from normal motion; motors cannot restart until reset. |
| Communications loss | Measured watchdog timeout and safe motor/blower stop. |
| Navigation | Multiple waypoint trials with success rate, time, path deviation, and intervention count. |
| Obstacle handling | Defined obstacles, approach speed, minimum stop distance, and outcome for each trial. |
| Localization | Map, starting-pose variation, relocalization outcome, and position error measurement. |
| Runtime | Battery voltage/current and runtime under navigation-only and navigation-plus-cleaning load. |
| Cleaning | Initial/final debris mass and area covered using a defined test protocol. |
| Docking, if attempted | Approach success rate, contact confirmation, charging-current confirmation, retries, and failure recovery. |
| User services | Touchscreen/QR or database-query success rate, offline behavior, and privacy controls. |

## Source Notes

- [RPLIDAR A1M8 datasheet](https://bucket.download.slamtec.com/8e7a1f4490a235717b43fccaf7dcae325dda7dc8/LD108_SLAMTEC_rplidar_datasheet_A1M8_v2.1_en.pdf): use the LiDAR within its stated range and scan-rate limits; validate performance on the actual floor, walls, glass, and furniture.
- [Nav2 navigation concepts](https://docs.nav2.org/concepts/): navigation requires coherent `map`, `odom`, and robot-base transforms; encoder and IMU data are commonly fused for local odometry.
- [Raspberry Pi 5 power guidance](https://www.raspberrypi.com/documentation/computers/getting-started.html?m=p): plan the 5 V power rail for the Pi and peripherals before selecting the converter and dock supply.
