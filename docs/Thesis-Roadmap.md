# BROLI Thesis 1 and Thesis 2 Roadmap

## Purpose

BROLI will be developed in two phases so the team can validate the core robot concept within the first semester budget, then convert the prototype into a standalone autonomous robot in Thesis 2.

The main strategy is to purchase the **Slamtec RPLIDAR A1M8 first** and use a MacBook as the temporary high-level computer in Thesis 1. The Raspberry Pi 5 and portable battery system are deferred to Thesis 2, after the robot's LiDAR, movement, dashboard, and cleaning functions have been proven.

## System Roles

| Component | Responsibility | Thesis 1 Location | Thesis 2 Location |
| --- | --- | --- | --- |
| MacBook | Dashboard, LiDAR visualization, mapping experiments, and manual-control interface | Temporary high-level computer | Development and remote operator computer only |
| Raspberry Pi 5 | Onboard ROS 2, LiDAR processing, navigation, and robot services | Not purchased or required | Permanent onboard high-level computer |
| ESP32 | Motor commands, encoder reading, low-level sensors, and emergency-stop behavior | On the robot | Remains on the robot |
| RPLIDAR A1M8 | 2D environment scanning and obstacle data | Connected to the MacBook by USB | Connected to the Raspberry Pi by USB |
| Cytron dual-channel motor driver | Drives left and right wheel motors | On the robot | Remains on the robot |
| GA25P-370 motors with encoders | Differential-drive movement and odometry | On the robot | Remains on the robot |
| Blower/vacuum deck | Basic floor-cleaning operation | Tethered or bench-powered during tests | Powered from the robot battery system |

## Thesis 1: Connected MVP

### Goal

Demonstrate that BROLI can move under manual control, show live LiDAR data, detect obstacles, run a basic cleaning mechanism, and expose its state through a web dashboard. Thesis 1 is a connected prototype, not yet a fully independent robot.

### Components to Use or Purchase

| Component | Price from `Hardware.md` | Status in Thesis 1 | Reason |
| --- | ---: | --- | --- |
| Slamtec RPLIDAR A1M8 | ₱6,126 | Purchase and use | This is the first major sensor needed to prove scanning, visualization, and obstacle detection. |
| ESP32 | Not yet listed | Purchase and use | Handles low-level motor control without needing a Raspberry Pi. |
| GA25P-370 motors with encoders, quantity 2 | ₱2,542 | Use | Provides a differential-drive base for manual movement and odometry experiments. |
| Cytron 10 A dual-channel motor driver | ₱1,499 | Use | Safely drives both wheel motors from ESP32 commands. |
| 12 V blower/vacuum deck | ₱1,175 | Use for supervised tests | Demonstrates the cleaning function. Use a separate tethered supply if the battery system is deferred. |
| PETG filament, 3 kg | ₱1,185 | Use for chassis and mounts | Supports 3D-printed structural parts, sensor mounts, and prototype revisions. |
| Chassis, wiring, fuses, switches, and connectors | Not yet listed | Use or acquire as needed | Required to safely assemble the rolling test platform. |
| MacBook | Existing team equipment | Use as the temporary high-level computer | Runs the dashboard, LiDAR tools, mapping experiments, and manual-control service. |
| Raspberry Pi 5, microSD card, touchscreen | Deferred to Thesis 2 | Defer | These are not required to validate the connected MVP. |
| LiPo battery, charger, and buck converter | Deferred to Thesis 2 where practical | Defer | Early demos can use supervised tethered power. Buy only if untethered testing becomes necessary and the safety design is ready. |

**Thesis 1 listed-component subtotal: ₱12,527**, excluding the ESP32 and unpriced chassis, wiring, fuse, switch, and connector costs.

### Scope and Deliverables

1. Assemble a safe, manually drivable differential-drive test base.
2. Connect the ESP32 to the Cytron motor driver and read wheel encoders.
3. Connect the RPLIDAR to the MacBook by USB and show a live 2D scan.
4. Build a Next.js dashboard that displays robot connection status, LiDAR data or scan status, movement controls, and cleaning status.
5. Send dashboard movement commands from the MacBook to the ESP32 over USB serial or local Wi-Fi.
6. Add a basic obstacle warning or stop rule for manual driving.
7. Demonstrate the blower/vacuum deck while the robot moves in a controlled test area.
8. Record test evidence: LiDAR scan, commanded movement, encoder feedback, obstacle response, dashboard screenshots, and cleaning demonstration.

### Thesis 1 Architecture

```text
RPLIDAR A1M8 -- USB --> MacBook
                           |
                           | USB serial or local Wi-Fi
                           v
                         ESP32 -- control signals --> Cytron motor driver --> Wheel motors
                           |
                           +--> Encoder and safety-switch input

MacBook --> Next.js dashboard --> Manual movement and cleaning commands
Blower/vacuum --> supervised tethered power for early demonstrations
```

### Important Constraints

- The MacBook should not be treated as a permanent robot computer. It is a development and demonstration host in Thesis 1.
- Keep motor control and emergency stopping on the ESP32. A browser dashboard, VM, or Wi-Fi connection must never be the only safety mechanism.
- Run the dashboard and LiDAR visualization natively on macOS when possible. A 4 GB Ubuntu VM can be used for ROS 2 experiments, but USB pass-through may make live LiDAR work less reliable.
- Do not connect the LiPo battery until the motor, blower, charger, fuse, switch, wiring, and voltage conversion requirements have been tested and reviewed.

## Thesis 2: Standalone Autonomous Robot

### Goal

Move the validated Thesis 1 software and hardware onto the robot so BROLI can operate without a MacBook or a tethered power supply. Thesis 2 focuses on ROS 2 integration, autonomous navigation, battery operation, user interaction, and final-system evaluation.

### Components to Purchase or Activate

| Component | Price from `Hardware.md` | Status in Thesis 2 | Reason |
| --- | ---: | --- | --- |
| Raspberry Pi 5, 8 GB RAM | ₱12,995 | Purchase and install | Becomes the onboard high-level computer for ROS 2, LiDAR processing, and navigation. |
| 64 GB microSD card | ₱1,249 | Purchase and install | Stores Raspberry Pi OS, ROS 2 packages, maps, logs, and application data. |
| RPLIDAR A1M8 | Reused from Thesis 1 | Reuse from Thesis 1 | Move its USB connection from the MacBook to the Raspberry Pi. |
| ESP32, motors, encoders, and Cytron driver | Reused from Thesis 1 | Reuse from Thesis 1 | Retains reliable low-level movement and safety control. |
| 3S 11.1 V LiPo battery | ₱899 | Install after power validation | Powers the mobile platform. Capacity and current capability must be verified against the actual motor and blower load. |
| XL4015 buck converter | ₱75 | Install after voltage testing | Steps battery voltage down for the required low-voltage rail. |
| IP2326 charging module | ₱64 | Install after charging-safety validation | Supports the approved charging design. |
| QC 3.0 wall adapter | ₱199 | Install after charging-safety validation | Powers the approved charging design. |
| 7-inch touchscreen | ₱2,150 | Install as interaction features are completed | Supports the BROLI interface. |
| USB webcam | ₱600-₱800 | Install as docking features are completed | Supports visual docking experiments. |
| USB microphone/speaker setup | ₱300-₱500 | Install as voice features are completed | Supports wake-word and voice interaction. |

**Thesis 2 listed-component subtotal: ₱18,531-₱18,931**, excluding components already bought in Thesis 1 and the still-unpriced ESP32, chassis, wiring, fuse, switch, and connector costs.

### Scope and Deliverables

1. Install Raspberry Pi OS and ROS 2 on the Raspberry Pi 5.
2. Run the LiDAR driver and robot services on the Raspberry Pi.
3. Port the Thesis 1 control interface so the MacBook dashboard communicates with the onboard robot over local Wi-Fi.
4. Integrate encoder odometry, LiDAR scans, and robot geometry for mapping and localization.
5. Create a map of the test environment and demonstrate waypoint navigation with obstacle avoidance.
6. Install and test the battery, regulated power rails, charging solution, fuse, master power switch, and low-battery monitoring.
7. Add the touchscreen and human-interaction features as the navigation platform becomes stable.
8. Demonstrate autonomous patrol, basic cleaning, return-to-dock behavior, and system logging in a controlled environment.

## Migration Plan: Thesis 1 to Thesis 2

The migration should replace only the high-level computer. The ESP32 and the motor-control protocol should remain stable so the proven movement base does not need to be redesigned.

| Step | Thesis 1 State | Thesis 2 Action | Acceptance Check |
| --- | --- | --- | --- |
| 1. Preserve interfaces | MacBook sends documented control messages to ESP32 | Keep the same serial or network message format on the Raspberry Pi | Pi can command forward, reverse, left, right, stop, and cleaning states without ESP32 firmware changes. |
| 2. Move LiDAR host | RPLIDAR is connected to MacBook USB | Connect RPLIDAR to Raspberry Pi USB and install its driver | Pi receives a stable 2D scan and publishes or serves it to the dashboard. |
| 3. Move backend services | Dashboard backend runs on MacBook | Deploy services to the Pi, preferably in containers or documented setup scripts | Dashboard can reconnect to the robot through its Wi-Fi address. |
| 4. Introduce ROS 2 navigation | Mapping is experimental or MacBook-hosted | Configure robot frame, wheel odometry, LiDAR topic, SLAM, and navigation stack on Pi | Robot can build a map and reach a simple waypoint. |
| 5. Add portable power | Robot is tethered or bench-powered | Install battery, charger, converter, fuse, and master switch after load tests | Robot has stable voltage under wheel and blower load, with a tested emergency stop. |
| 6. Add final interfaces | MacBook displays the dashboard | Add touchscreen and optional webcam/audio peripherals | Core navigation still works when optional interaction peripherals are disconnected. |
| 7. Validate end-to-end | Connected MVP | Run supervised autonomous cleaning and docking trials | Logs show navigation outcome, battery state, obstacle events, and cleaning status. |

## Software Practices That Make Migration Easier

- Define one versioned command format between the dashboard/high-level computer and ESP32, such as `drive`, `stop`, `cleaning`, and `status` messages.
- Keep hardware pin assignments, motor limits, wheel dimensions, and encoder calibration in configuration files rather than inside dashboard code.
- Build the Next.js dashboard as a client of a robot API. It should not directly depend on whether the API runs on a MacBook or Raspberry Pi.
- Create a small hardware test checklist for every change: emergency stop, motor direction, encoder values, LiDAR scan, power-rail voltage, and blower switching.
- Keep a repeatable Raspberry Pi setup document or deployment script so the final system can be rebuilt if the microSD card fails.

## Budget Direction

| Semester | Listed Component Cost | Budget Position | Deferred Spend |
| --- | ---: | --- | --- |
| Thesis 1 | ₱12,527 plus ESP32 and integration materials | Leaves **₱7,473** from the ₱20,000 semester allowance before the unpriced parts | Raspberry Pi 5, microSD card, touchscreen, and full battery/charging integration |
| Thesis 2 | ₱18,531-₱18,931 | Fits within ₱20,000 with **₱1,069-₱1,469** remaining before unpriced parts | Optional refinements after core navigation and safety requirements are met |

The combined listed total is **₱31,058-₱31,458**, which matches the total in `Hardware.md`. That total excludes the ESP32, whose price is not yet listed, and also excludes chassis, wiring, fuses, switches, and connectors. Add those prices to `Hardware.md` before treating the per-semester budget as final.

## Definition of Success

**Thesis 1 succeeds** when BROLI is a safe connected MVP: it can be manually driven from the dashboard, presents live LiDAR information, responds to a basic obstacle rule, reports its state, and demonstrates cleaning in a supervised test.

**Thesis 2 succeeds** when BROLI operates without the MacBook: the Raspberry Pi runs the onboard robotics services, the robot navigates a mapped indoor environment using LiDAR and encoders, the power system supports mobile operation safely, and the dashboard and user-facing features communicate with the standalone robot.
