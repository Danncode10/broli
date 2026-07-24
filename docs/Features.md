# Title: BROLI: An Autonomous Navigation and Cleaning Robot Featuring Natural Language Processing and Environmental Monitoring for Libraries and Offices

## BROLI Core Features

## 1. Core Navigation and Autonomous Mobility

### Autonomous SLAM and Indoor Navigation

BROLI is built on ROS 2 and uses LiDAR-based SLAM to autonomously map, localize, and navigate indoor environments such as libraries, offices, and lobby spaces. The robot can generate a 2D map of the environment, estimate its real-time position, and move toward assigned destinations without direct manual control.

Its navigation system is designed for indoor service environments where layouts may change over time due to moved tables, chairs, shelves, carts, or blocked aisles.

Key capabilities:

- Uses ROS 2 as the main robotics software framework.
- Uses LiDAR for mapping, localization, and obstacle detection.
- Supports autonomous movement between defined waypoints.
- Navigates library aisles, study areas, reception zones, and docking areas.
- Can operate using a saved map of the building.
- Allows map updates when the physical environment changes.

### Dynamic Local Costmap Re-Planning

BROLI uses a dynamic local costmap to detect and respond to nearby obstacles in real time. While the global map provides the planned route, the local costmap continuously updates based on sensor readings around the robot.

This allows BROLI to avoid unexpected obstacles while still trying to reach its target destination.

Examples of obstacles BROLI can respond to:

- Shifted chairs or tables
- People walking nearby
- Book carts
- Bags placed in walkways
- Temporarily blocked library aisles
- Cleaning supplies or maintenance equipment

Key capabilities:

- Continuously updates nearby obstacle information.
- Re-plans short-range movement when obstacles appear.
- Performs smooth evasive maneuvers around objects.
- Reduces the need for human intervention during patrols.
- Improves safety in shared indoor spaces.

### Cloud-Configured Docking Waypoint

BROLI’s docking station is treated as a configurable waypoint inside the map editor. Instead of hardcoding the docking station location, administrators can update its position through the cloud-synced dashboard.

If the docking station is moved to a different area, the administrator can simply update the dock waypoint in the 2D floor plan. BROLI will then use the updated coordinate during its next charging routine.

Key capabilities:

- Dock location is stored as a configurable map waypoint.
- Administrators can update the dock location through the dashboard.
- BROLI navigates to the current dock waypoint when battery is low.
- Supports future layout changes without major software rewriting.
- Makes the robot scalable for different library or office layouts.

### Auto-Docking and Power Management

BROLI automatically monitors its internal battery level and returns to its designated docking station when charging is needed. The robot uses autonomous navigation to reach the dock area, then performs final alignment using a combination of visual and mechanical guidance.

The docking system uses a 3D-printed guided alignment track that helps physically align the robot with the charging connector. This track assists the final docking phase and helps the custom-mated Type-C charging interface connect safely and consistently.

Key capabilities:

- Monitors battery level during operation.
- Automatically triggers return-to-dock behavior when battery is low.
- Navigates to the cloud-configured docking waypoint.
- Uses a visual fiducial marker such as an AprilTag or QR marker for final approach.
- Uses a 3D-printed guide track for mechanical alignment.
- Connects to a Type-C charging interface.
- Charges through a built-in fast-charging module.
- Reduces the need for manual charging.

### Final Docking Alignment System

For the final few centimeters of docking, BROLI uses close-range alignment support. This is important because normal indoor navigation is accurate enough to bring the robot near the dock, but charging requires more precise positioning.

The final alignment system may include:

- Webcam detection of an AprilTag or QR marker placed near the dock
- Short-range distance sensing
- Physical guide rails or alignment track
- Docking contact detection
- Charging status confirmation

This makes the docking process more reliable, especially if the robot approaches from a slightly imperfect angle.

---

## 2. Eco-Sanitation and Facility Maintenance

### Automated Floor Cleaning

BROLI includes an integrated low-profile vacuum deck for basic floor-cleaning tasks. The cleaning system operates independently from the navigation logic, meaning the robot can continue navigating while the cleaning module runs as a separate subsystem.

This feature allows BROLI to perform scheduled sweeping and debris collection while patrolling library aisles or office walkways.

Key capabilities:

- Uses a compact vacuum or blower-based cleaning mechanism.
- Performs light debris collection during patrol routines.
- Can clean under tables, along aisles, and in common walkways.
- Runs cleaning behavior separately from navigation decisions.
- Supports scheduled cleaning routines.
- Helps reduce manual maintenance workload.

### Cleaning During Patrol Mode

BROLI can combine movement and cleaning during scheduled patrols. For example, during low-traffic hours, BROLI can follow a predefined cleaning route across library aisles and common areas.

Possible cleaning behaviors:

- Scheduled cleaning during off-peak hours
- Cleaning only in selected zones
- Avoiding no-go areas or quiet zones
- Returning to dock after cleaning
- Logging completed cleaning routes

### Cleaning Efficiency Logging

BROLI can send cleaning activity logs to the administrative dashboard. These logs can help administrators understand which areas were cleaned, how often cleaning was performed, and which spaces may need more attention.

Possible logged data:

- Cleaning route completed
- Cleaning duration
- Areas covered
- Battery consumed during cleaning
- Missed or blocked cleaning zones

---

## 3. Conversational NLP and Human-Robot Interaction

### API-Based Conversational Processing

BROLI does not use a local large language model. Instead, it uses an external API-based NLP system for natural language understanding, book recommendation, and conversational responses.

This allows BROLI to process user requests without requiring heavy AI computation on the robot itself. The Raspberry Pi 5 handles the robot interface, microphone input, display, and API communication, while the language processing is performed through the selected cloud API.

Key capabilities:

- Uses API-based NLP instead of a local LLM.
- Reduces onboard computing requirements.
- Allows more flexible and accurate language understanding.
- Supports natural user questions and book descriptions.
- Enables future upgrades by changing or improving the API backend.

### Wake-Word Interaction

BROLI listens for a custom wake phrase such as:

> “Hey Broli!”

After detecting the wake phrase, BROLI responds with an interactive greeting such as:

> “How can I serve you?”

This creates a simple and friendly interaction flow for library visitors.

Key capabilities:

- Detects a custom wake phrase.
- Activates conversational mode after wake-word detection.
- Provides audio or visual feedback when activated.
- Prevents constant unnecessary interaction.
- Makes BROLI feel more approachable to users.

### Conversational Intent Recognition

Once activated, BROLI listens to the user’s request and identifies the intent behind it. The user does not need to use exact commands. They can describe what they want in natural language.

Example user requests:

- “Can you help me find a book about robotics?”
- “I need something about machine learning for beginners.”
- “Where can I find books about Philippine history?”
- “Do you have novels similar to Harry Potter?”
- “Can you guide me to the computer science section?”
- “Where is the librarian’s desk?”

Possible recognized intents:

- Book search
- Book recommendation
- Facility direction
- Shelf guidance
- Digital copy request
- Cancel interaction
- Help request

### Dynamic Service Menu Display

When BROLI enters interaction mode, its screen displays a dynamic service menu. This menu gives users clear options they can tap or select after speaking to the robot.

Example service menu options:

- Search for a book
- Recommend a book
- Guide me to a shelf
- Show digital copy
- Facility directions
- Ask librarian assistance
- Cancel

The screen helps users understand what BROLI can do, even if they are unsure how to speak their request.

### Natural Language Book Recommendation Engine

BROLI allows users to describe the type of book they want using free-form language. The system sends the request to an API-based NLP service, interprets the user’s meaning, and searches the live library database for matching books.

Example user input:

> “I want a beginner-friendly book about robots and artificial intelligence.”

BROLI may then search for:

- Robotics
- Artificial intelligence
- Beginner-level materials
- Introductory textbooks
- Available physical or digital copies

Key capabilities:

- Parses free-form book descriptions.
- Uses API-based semantic understanding.
- Queries the live library database.
- Filters results based on availability.
- Matches user intent with relevant titles.
- Displays recommended books on the touchscreen.

### Live Library Database Querying

BROLI connects to the library’s database or inventory system to retrieve real-time book information. This helps ensure that recommendations are based on actual available materials.

Possible retrieved information:

- Book title
- Author
- Category
- ISBN or accession number
- Availability status
- Shelf location
- Digital copy link
- Physical copy location
- Number of copies available

### Interactive Book Selection

After showing recommended books, BROLI prompts the user to select one title or cancel the request. The user can interact using the touchscreen or voice commands.

Possible user actions:

- Select a book from the list
- Ask for more recommendations
- Search again
- View digital copy
- Get directions to shelf
- Cancel request

### QR Code Access for Digital and Physical Books

When the user selects a book, BROLI generates a unique QR code on its screen. The user can scan the QR code using a mobile phone.

The QR code redirects the user to a web app that provides one of two options:

- An official e-library link for a digital copy
- A visual indoor map leading to the physical shelf location

Key capabilities:

- Generates a unique QR code for the selected book.
- Redirects users to the official e-library or web app.
- Supports both digital and physical book access.
- Reduces the need for manual searching.
- Helps users continue guidance on their own phone.

### Indoor Shelf Guidance

For physical books, BROLI can provide visual navigation support by showing the shelf location on its display or through the user’s phone after scanning the QR code.

Possible guidance outputs:

- Highlighted shelf location on a 2D map
- Step-by-step indoor directions
- Section name or aisle number
- Estimated walking path
- Optional robot-led guidance to the shelf

---

## 4. Cloud-Synced Administrative Dashboard and Environment Management

### Dynamic Map Editor

BROLI includes a cloud-managed web dashboard where librarians and administrators can view and edit the robot’s operating environment. The map editor displays the 2D floor plan and allows admins to update important spatial data.

Administrators can modify the environment without directly changing the robot’s source code.

Key capabilities:

- View the 2D library floor map.
- Add, edit, or remove waypoints.
- Update shelf coordinates.
- Move the docking station waypoint.
- Draw no-go zones.
- Define operational boundaries.
- Mark restricted or staff-only areas.
- Update navigation zones when the layout changes.

### Shelf Coordinate Management

Library shelves can be assigned coordinates in the map editor. Each shelf or section can be linked to categories, book ranges, or database records.

Example shelf metadata:

- Shelf ID
- Section name
- Subject category
- Aisle number
- Map coordinate
- Direction notes
- Linked book records

This allows BROLI to connect book search results with actual physical shelf locations.

### No-Go Zones and Operational Boundaries

Administrators can draw boundaries and restricted areas on the dashboard. BROLI uses these zones to avoid entering unsafe, crowded, private, or off-limits spaces.

Examples of no-go zones:

- Staff-only rooms
- Stairs
- Narrow storage areas
- Wet floor zones
- Maintenance areas
- Emergency exits
- Areas blocked for events

Key capabilities:

- Prevents BROLI from entering restricted spaces.
- Improves safety and reliability.
- Allows fast updates during temporary layout changes.
- Helps adapt BROLI to different institutions.

### Predictive Maintenance and Analytics Dashboard

BROLI logs operational data to the dashboard for monitoring and analysis. This helps administrators understand how the robot is used and when maintenance may be needed.

Possible analytics:

- Battery usage
- Docking frequency
- Cleaning duration
- Navigation routes
- Motor runtime
- Obstacle encounters
- Congestion heatmaps
- User interaction count
- Book search frequency
- Most visited shelves
- Noise-level reports

### Congestion Heatmaps

BROLI can use navigation and sensor data to identify high-traffic areas inside the library. The dashboard can display congestion heatmaps to help administrators understand movement patterns.

Possible uses:

- Identifying crowded aisles
- Improving furniture layout
- Adjusting cleaning schedules
- Planning shelf arrangement
- Monitoring study area usage

### Cleaning Efficiency Reports

BROLI can generate reports showing where and when cleaning was performed. This supports facility maintenance planning.

Possible report details:

- Cleaned zones
- Missed zones
- Cleaning duration
- Battery usage during cleaning
- Cleaning schedule completion
- Areas requiring repeated cleaning

### Multi-Profile System Mode

BROLI can switch between different operating profiles depending on the environment. The mode can be controlled through the cloud dashboard.

Main modes:

#### Library Mode

In Library Mode, BROLI focuses on quiet operation, book assistance, cleaning, and noise monitoring.

Library Mode behaviors:

- Book search and recommendation
- Shelf guidance
- Quiet movement behavior
- Ambient noise monitoring
- Cleaning patrols
- Low-volume audio responses
- Library-specific UI options

#### Office/Lobby Mode

In Office or Lobby Mode, BROLI acts more like a receptionist and visitor guide.

Office/Lobby Mode behaviors:

- Visitor greeting
- Guest direction support
- Reception assistance
- Standby greetings
- Facility navigation
- Reduced cleaning emphasis
- Lobby-friendly UI options

### Ambient Noise Enforcement

BROLI uses microphones to monitor ambient noise levels in the library. If the noise exceeds a configured threshold, BROLI can provide audio-visual feedback reminding users to keep quiet.

Possible feedback methods:

- Displaying a quiet reminder on the LCD screen
- Showing an animated facial expression
- Playing a soft alert tone
- Speaking a polite reminder
- Logging repeated high-noise events

Example message:

> “Please keep your voice low. This is a quiet study area.”

Key capabilities:

- Monitors ambient decibel levels.
- Compares sound levels against a configured threshold.
- Provides polite reminders.
- Supports quiet study environments.
- Logs noise events for dashboard reports.

---

## 5. Expressive LCD UI and Affective Computing

### Animated Digital Face

BROLI’s LCD screen displays an animated digital face that represents its current emotional or operational state. This makes the robot more approachable and easier to understand.

Possible face states:

- Standby
- Cleaning
- Searching
- Thinking
- Excited
- Bored
- Charging
- Low battery
- Error or attention needed

### Webcam-Based Face and Gesture Tracking

BROLI uses a USB webcam to detect nearby users, recognize basic gestures, and adjust its expressions or behavior accordingly.

Possible interactions:

- Detecting when a user approaches
- Looking toward a user’s face
- Waving back when a wave gesture is detected
- Showing heart animations after friendly gestures
- Activating greeting behavior
- Returning to standby when no user is nearby

### Emotional State Transitions

BROLI changes its displayed emotion depending on what it is doing.

Examples:

- **Standby:** Calm idle face while waiting for users
- **Cleaning:** Focused face while vacuum deck is active
- **Searching:** Thinking animation during book search
- **Excited:** Happy face when helping a user
- **Bored:** Idle expression during long inactivity
- **Charging:** Sleepy or charging animation at dock
- **Quiet Reminder:** Serious but polite expression during noise enforcement

### Human-Friendly Feedback

The expressive UI gives users quick visual feedback about BROLI’s current status. This helps users understand whether BROLI is ready to help, busy cleaning, charging, or responding to a command.

Key capabilities:

- Shows robot status through facial expressions.
- Makes interactions more friendly.
- Reduces confusion during operation.
- Supports visual feedback for children, students, and first-time users.

---

## 6. System Architecture and Hardware Role Separation

### Raspberry Pi 5 as Main Computer

The Raspberry Pi 5 serves as BROLI’s main onboard computer. It handles high-level robotics, interaction, display, networking, and API communication.

Main responsibilities:

- ROS 2 navigation stack
- LiDAR data processing
- SLAM and localization
- Global path planning
- Local obstacle avoidance
- Touchscreen user interface
- QR code generation
- Webcam processing
- API-based NLP communication
- Library database communication
- Dashboard/cloud synchronization
- System mode control

### ESP32 as Low-Level Microcontroller

BROLI uses one ESP32 as the low-level microcontroller for hardware control tasks. The ESP32 works alongside the Raspberry Pi 5 and handles real-time control tasks that do not need to run directly on the Pi.

Main responsibilities:

- Motor control signal handling
- Wheel encoder reading
- Battery voltage monitoring
- Basic sensor input
- Cleaning motor or blower control
- Status signal handling
- Communication with Raspberry Pi 5 through USB serial or UART

Recommended quantity:

- **1 ESP32 required inside BROLI**
- **1 extra ESP32 optional as a spare**
- **Docking station ESP32 optional only for smart dock features**

### Passive Docking Station Design

BROLI’s docking station does not require its own microcontroller if it only provides guided charging. The dock can be designed as a mostly passive mechanical and electrical station.

Passive dock components:

- 3D-printed alignment track
- Fixed Type-C charging connector
- Wall power adapter
- Charging interface
- Visual fiducial marker such as AprilTag or QR marker
- Optional LED indicator

This design keeps the docking station simpler, cheaper, and easier to maintain.

### Optional Smart Dock Features

A second ESP32 may be added to the docking station only if smart dock functionality is required.

Optional smart dock functions:

- Dock presence detection
- Charging status telemetry
- LED status indicators
- IR beacon for docking assistance
- NFC or RFID dock identification
- Servo-controlled latch
- Cloud logging of docking events
- Dock health monitoring

For the current BROLI design, the smart dock ESP32 is optional, not required.

---

## 7. Cloud and API Integration

### API-Based NLP Processing

BROLI uses an external API for conversational language processing. The robot sends the user’s text or processed speech input to the API, receives a structured response, and uses that response to search the library database or generate a reply.

This avoids the need for a local LLM and keeps the Raspberry Pi focused on robotics and interface tasks.

Key capabilities:

- Sends user requests to an NLP API.
- Receives structured intent and response data.
- Supports book recommendation and facility guidance.
- Can be upgraded by changing the API provider or model.
- Reduces local processing requirements.

### Library Database Integration

BROLI connects to the library’s live database to retrieve book and shelf information.

Possible database functions:

- Search book inventory
- Check book availability
- Retrieve digital copy links
- Retrieve physical shelf locations
- Match categories and tags
- Update book recommendation results

### Web App Integration

BROLI uses a web app to continue the user experience after scanning a QR code.

Web app functions:

- Display selected book details
- Show e-copy link
- Show indoor shelf map
- Provide walking directions
- Allow users to save or share the selected book
- Support mobile access

### Cloud Dashboard Synchronization

BROLI syncs with the cloud dashboard for configuration, map updates, analytics, and system mode control.

Dashboard-managed data:

- Robot status
- Map waypoints
- Dock waypoint
- Shelf coordinates
- No-go zones
- Cleaning schedules
- Operating mode
- Noise threshold
- Maintenance logs
- Usage analytics

---

## 8. Safety, Reliability, and Usability Features

### Obstacle Avoidance

BROLI uses sensor data and local re-planning to avoid obstacles in shared spaces.

Safety behaviors:

- Slowing down near obstacles
- Re-routing around blocked paths
- Stopping when an obstacle is too close
- Avoiding restricted areas
- Preventing collisions during patrols

### Low Battery Behavior

BROLI continuously monitors battery level and automatically returns to dock when needed.

Low battery behavior:

- Stop accepting long tasks
- Notify users on screen
- Navigate to docking waypoint
- Perform final docking alignment
- Confirm charging connection
- Enter charging mode

### Manual Override

BROLI may include an administrator override through the dashboard or direct control interface.

Possible override functions:

- Stop robot
- Pause cleaning
- Send robot to dock
- Change operating mode
- Update route or waypoint
- Disable autonomous movement temporarily

### Error and Status Feedback

BROLI displays clear status messages through the LCD UI and dashboard.

Possible status states:

- Ready
- Navigating
- Searching
- Cleaning
- Charging
- Low battery
- Docking
- Obstacle detected
- Network unavailable
- Assistance needed

---

# Updated BROLI Feature Summary

BROLI is a cloud-connected autonomous service robot designed for libraries, offices, and lobby environments. It combines ROS 2-based SLAM navigation, dynamic obstacle avoidance, automated floor cleaning, API-based conversational book querying, QR-enabled book access, cloud-managed map editing, smart facility analytics, ambient noise monitoring, expressive LCD interaction, and autonomous docking with a configurable dock waypoint.

BROLI does not rely on a local LLM. Instead, it uses API-based NLP for conversational understanding and book recommendation, while the Raspberry Pi 5 handles robotics, interface, and cloud communication. A single ESP32 is used onboard as the low-level microcontroller for motor control, encoder reading, and sensor management. The docking station can remain passive unless optional smart dock features are added.