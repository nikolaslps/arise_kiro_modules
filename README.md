# arise_kiro_modules

## KIRO: Key Intelligent & Interactive Robotic Operator

Welcome to the central documentation and narrative hub for the **KIRO** experiment, developed under the Horizon Europe **ARISE** project. KIRO is an HRI-enabled industrial mobile manipulation solution designed to deliver safe, intuitive, and efficient tool delivery workflows within dynamic, high-mix manufacturing environments.

---

### 🎬 System Demonstration

The end-to-end operational capabilities of the KIRO mobile manipulator—including natural language voice commanding, autonomous warehouse navigation, vision-based picking, and ergonomic handover—are highlighted in the demonstration video below.

[![KIRO demo video](https://img.youtube.com/vi/uGGcSsZhrGk/maxresdefault.jpg)](https://www.youtube.com/watch?v=uGGcSsZhrGk "Watch the KIRO on YouTube")

---

### 🧩 Architectural Breakdown & Subsystem Repositories

The KIRO framework is structured into specialized modules to enforce a clean separation of concerns, maximize reusability within the ARISE middleware catalog, and ensure real-time, deterministic performance using ROS 2 Humble (Vulcanexus) and eProsima Fast DDS. The following three modules are publicly available on GitHub.

### 1. Tool Detection Module
The Tool Detection module handles computer vision-based perception during the picking and retrieval phases of the mission execution. Implemented as a ROS 2 Action Server leveraging an Ultralytics YOLOv11n backbone, this module extracts 2D bounding boxes from RGB-D camera feeds and deprojects them into real-world metric dimensions to reliably identify and isolate targeted industrial tools inside localized storage trays. By ensuring the tool remains stable and centered across sequential frames, it guarantees high-fidelity localization parameters before triggering the arm manipulation stack.
* 📂 **Repository:** [arise-kiro-cv-tool](https://github.com/nkardaris/arise-kiro-cv-tool)

#### 2. Social Navigation Module
The Social Navigation module enables the mobile base to safely traverse cluttered, space-constrained factory settings while actively adapting to human presence and dynamic factory workflows. This subsystem splits responsibilities into two repositories: a specialized **Human Detection** codebase that fuses camera-based YOLO 3D pose estimations with omnidirectional UWB ranging data and 3D LiDAR scans via a distance-threshold strategy, and a **Social Navigation** control pipeline that wraps the Nav2 stack with the HATEB (Human-Aware Timed Elastic Band) local planner. Together, they track human agents and optimize trajectories based on proxemic and velocity constraints, yielding zero collisions and smooth obstacle circumvention.
* 📂 **Repository (Human Detection Perception):** [human_detection_fusion](https://github.com/nikolaslps/human_detection_fusion)
* 📂 **Repository (Social Planning & Execution):** [kiro_nav](https://github.com/andvatistas/kiro_nav)

#### 3. Close-Proximity HRI Module
The Close-Proximity HRI module governs the safe, direct physical exchange of assets between the manipulator arm and the human operator. This module is deployed across two repositories: **Human Detection with Ergonomics**, which hooks into ROS4HRI conventions (`hri_body_detect`) to extract skeletal joint feeds and compute live, localized ergonomic target fields based on RULA assessment criteria, and **Trajectory Path Planning**, which encapsulates MoveIt 2 interfaces and Cartesian path optimization engines. By dynamically sampling kinematically reachable, collision-free intercept zones within the calculated ergonomic volume, the subsystem orchestrates fluid, reassuring, and human-centric handovers.
* 📂 **Repository (Ergonomics Perception):** [kiro_handover_calculation](https://github.com/nikolaslps/kiro_handover_calculation)
* 📂 **Repository (Handover Execution Planner):** [kiro_handover_execution](https://github.com/nikolaslps/kiro_handover_execution)

---

### 🛠 Deployment & Middleware Interoperability

The overall orchestration is maintained by the `kiro_mission_controller` utilizing YASMIN Finite State Machines to interface between edge-robotic actions and the **FIWARE Orion-LD** context broker. Bidirectional topic synchronization across network boundaries is natively managed via a containerized eProsima DDS Router configuration.
