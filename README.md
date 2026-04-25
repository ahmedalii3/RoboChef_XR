Three.js Kitchen, Robot, and WebXR Experiments

This repository contains a collection of Three.js exercises that build progressively from a simple kitchen scene to a more advanced WebXR robot tele-operation demo.

The project explores:

* Basic Three.js scene setup
* Lighting and shadows
* Kitchen/table modeling with simple geometry
* Hierarchical robot modeling
* Robot joint animation
* WebXR / VR / AR integration
* XR controller interaction
* Passthrough-style AR visualization
* Controller-driven robot arm tele-operation using inverse kinematics

Project Overview

The main idea is to create an interactive 3D kitchen environment and then add a robot that can be animated or controlled in XR.

The early exercises focus on building the room, furniture, table, cup, plate, and chairs. Later versions add a robot with a proper scene graph hierarchy. The final XR version adds AR/VR buttons, controller models, a passthrough toggle, and a two-bone IK system that maps controller movement to the robot arms.

Features

* Three.js WebGL rendering
* OrbitControls for desktop navigation
* Procedural room, floor, walls, table, cup, plate, and chairs
* Metallic and transparent materials
* Ambient, directional, point, and spot lighting
* Shadow-map support
* Hierarchical robot model
* Shoulder, elbow, wrist/hand joint groups
* Debug AxesHelper objects for robot joints
* lil-gui controls for lights and materials
* WebXR support using VRButton and ARButton
* XR controller models using XRControllerModelFactory
* Controller rays for pointing interaction
* 3D HTML-based XR HUD using HTMLMesh
* Passthrough-style AR mode
* XR world offset controls
* Controller-to-robot tele-operation
* Two-bone IK solver for robot arms

Exercises / Files

1. Basic Kitchen Scene

This version creates a simple kitchen/dining environment.

It includes:

* A floor
* A back wall
* A left wall
* A metallic table/workplan
* Four cylindrical table legs
* A transparent cup
* A plate
* Optional chair creation code
* OrbitControls

Main goal:

Learn how to create a basic Three.js scene using primitive geometries and simple materials.

2. Kitchen Scene with Robot

This version extends the kitchen scene by adding a robot beside the table.

The robot is built using a hierarchical scene graph:

robotRoot
└── torsoGroup
    ├── headGroup
    ├── left shoulderGroup
    │   └── elbowGroup
    │       └── handGroup
    └── right shoulderGroup
        └── elbowGroup
            └── handGroup

This hierarchy allows each joint to rotate relative to its parent, which is the basic idea behind robot animation.

It includes:

* Robot legs
* Hips
* Torso
* Head
* Eyes
* Antennas
* Left and right articulated arms
* Shoulder, elbow, and hand pivots
* Optional idle animation examples

Main goal:

Understand how parent-child relationships in Three.js can be used to build articulated models.

3. XR Lighting and Robot Tele-operation Scene

This is the most advanced version of the project.

It includes:

* A smaller XR-scaled kitchen/table scene
* Spotlight with shadows
* GUI controls using lil-gui
* VR and AR buttons
* Passthrough-style AR mode
* XR controller models
* XR controller rays
* 3D HTML HUD using HTMLMesh
* Controller-to-robot tele-operation
* Two-bone inverse kinematics for the robot arms

Main goal:

Use WebXR controllers to drive a robot arm rig interactively in VR/AR.

Robot Hierarchy

The robot is built using nested THREE.Group objects. Each group acts as a pivot point.

robotRoot

Controls the entire robot.

Changing robotRoot.position moves the robot. Changing robotRoot.rotation rotates the whole robot.

torsoGroup

Acts as the pivot for the upper body.

The torso, head, and arms are attached to this group.

headGroup

Acts as the neck joint.

Rotating headGroup.rotation.y makes the robot look left and right.

Arm Structure

Each arm is built using nested groups:

shoulderAbdGroup
└── shoulderGroup
    └── elbowGroup
        └── handGroup

The purpose of each group:

* shoulderAbdGroup: moves the arm sideways away from or toward the body
* shoulderGroup: moves the upper arm forward/backward
* elbowGroup: bends the forearm
* handGroup: controls the wrist/hand

This makes the arm easier to animate and easier to control with IK.

Inverse Kinematics

The XR version uses a simple two-bone IK system for each robot arm.

The two bones are:

* Upper arm
* Forearm

The controller position becomes the target. The code converts the controller position from world coordinates into the robot torso’s local coordinate system, then computes the shoulder and elbow angles needed to reach the target.

Important constants:

const UPPER_ARM_LEN = 0.30;
const FOREARM_LEN = 0.28;

Important tele-operation settings:

const teleop = {
  enabled: true,
  headTrackXR: false,
  ikReachScale: 1.0,
  ikInputGain: 1.4,
  shoulderLiftGain: 1.9,
  controllerHeightOffset: 0.0,
  controllerReachOffset: -0.08,
  shoulderAbductionGain: 0.55,
  showXRControllers: true
};

These values control how sensitive the robot is to controller movement.

XR World Offset

In the XR version, the virtual scene is placed inside a group called world.

const world = new THREE.Group();
scene.add(world);

This makes it possible to move the whole virtual world relative to the headset.

The XR spawn settings are:

const xrSpawn = {
  x: 0,
  y: -1.65,
  z: -1.25,
  yaw: -Math.PI / 2,
  pitch: -0.12
};

Changing these values changes where the virtual table and robot appear when entering XR.

Passthrough-style AR Mode

The XR version includes a passthrough-style mode.

When AR is active and passthrough style is enabled:

* The scene background becomes transparent.
* The virtual room is hidden.
* The table, robot, cup, and plate remain visible.

This is handled by:

function applyPassthroughMode(isAR) {
  const usePassthroughStyle = isAR && xrView.passthroughStyle;
  scene.background = usePassthroughStyle
    ? null
    : new THREE.Color(0x888888);
  roomGroup.visible = !usePassthroughStyle;
}

This gives the impression that the virtual robot and table are placed inside the real world.

XR UI

The passthrough toggle button is written as normal HTML:

<div id="xrHudSource">
  <button id="togglePassthrough">Passthrough: ON</button>
</div>

Then it is converted into a 3D panel using HTMLMesh:

const xrUiMesh = new HTMLMesh(document.getElementById('xrHudSource'));

The XR controllers can interact with this UI using:

* InteractiveGroup
* Controller rays
* A fallback raycaster click system

Technologies Used

* HTML
* CSS
* JavaScript
* Three.js
* WebGL
* WebXR
* OrbitControls
* lil-gui
* VRButton
* ARButton
* XRControllerModelFactory
* InteractiveGroup
* HTMLMesh

How to Run Locally

Because this project uses JavaScript modules, it should be opened through a local server instead of directly opening the HTML file.

Option 1: VS Code Live Server

1. Open the project folder in VS Code.
2. Install the Live Server extension.
3. Right-click the HTML file.
4. Choose Open with Live Server.
5. Open the generated local URL in your browser.

Example:

http://127.0.0.1:5500/index.html

Option 2: Python Local Server

From the project folder, run:

python3 -m http.server 8000

Then open:

http://localhost:8000/

Running on Meta Quest / WebXR Headset

For WebXR testing, open the page in a compatible headset browser such as the Meta Quest Browser.

Important notes:

* WebXR usually requires HTTPS or a secure context.
* localhost only works on the same device.
* If the server runs on your laptop, the headset must use your laptop’s local network IP address.

Example:

http://192.168.1.20:5500/

Both the laptop and headset must be connected to the same network.

For easier headset testing, deploy the project using a service such as:

* GitHub Pages
* Vercel
* Netlify

Common Issues

Import map error

If you see an error like:

Failed to parse import map

check that the import map is valid JSON and does not contain trailing commas or missing quotes.

Module specifier error

If you see:

Failed to resolve module specifier "three"

then the import map is not being loaded correctly, or the file is not being served from a local server.

WebXR not starting

WebXR may fail if:

* The browser does not support WebXR.
* The page is not served from a secure context.
* The headset browser blocks the session.
* AR is requested on a device/browser that only supports VR.

Passthrough not visible

Passthrough depends on AR support and environment blend mode.

The code checks:

const blendMode = renderer.xr.getEnvironmentBlendMode?.();
const isAR = isXR && blendMode === 'alpha-blend';

If the session is not AR with alpha blending, the background will not behave like passthrough.

Suggested Folder Structure

project-folder/
│
├── index.html
├── kitchen-scene.html
├── kitchen-robot.html
├── xr-robot-teleop.html
│
└── README.md

File names can be different, but update the README and links accordingly.

Possible Improvements

Future improvements could include:

* Add real texture images for the floor, walls, and table
* Enable full object shadow casting and receiving for every mesh
* Add GUI controls for robot joint angles
* Add smoother IK interpolation
* Add wrist orientation control from controller rotation
* Add calibration instructions inside the XR UI
* Add buttons to reset world position in XR
* Add AR-only and VR-only versions
* Add screenshots or GIF previews
* Organize repeated code into reusable modules
* Improve robot proportions and joint constraints

Author

Ahmed Hassan

Master’s student in Computer Science, interested in computer vision, AI, 3D graphics, robotics-style interaction, and WebXR.