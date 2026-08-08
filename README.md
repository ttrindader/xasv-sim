# xasv-sim

<p align="center">
  <img src="docs/figs/logo.png" alt="xasv-sim logo" width="400">
</p>

**xasv-sim** is a clean GitHub distribution of the **xasv-sim** stack: a **ROS + ArduPilot + Gazebo** simulation environment for autonomous surface vessels (ASVs) in realistic river environments, with optional **X-in-the-Loop** workflows such as **MITL**, **SITL**, **HITL**, **RITL**, and **HuITL**.

This repository contains the reference simulation assets, ROS packages, Gazebo worlds, Blender-based modeling assets, custom plugins, and auxiliary scripts used in ASV navigation and inspection experiments.

> **Important**  
> Large binary assets are stored outside GitHub in a **public Google Drive folder** and must be restored after cloning the repository.

---

## Version

**xasv-sim 1.0.0** is the first complete and stable release of the stack.

This release freezes the core architecture, river worlds, ASV models and X-in-the-Loop workflows used in the associated **IEEE-RA-P** article, so that other groups can reproduce and extend the reported experiments.

---

## Features

xasv-sim offers a set of features aimed at realistic simulation and reproducible experiments:

- **Blender-based modeling and export**
  - ASV hulls, sensors and river infrastructures modeled in Blender.
  - Automatic export scripts to generate SDF/URDF/Xacro with simplified collision meshes for real-time simulation.

- **River-inspired Gazebo worlds**
  - Log-boom structures, inspection routes and hydroelectric plant surroundings.
  - Worlds and models organized under `xasv_sim/worlds/` and `xasv_sim/models/`.

- **Full ROS Noetic integration**
  - `xasv_sim` package with custom plugins, launch files, TF tree, topics and configuration for end-to-end experiments.

- **Control allocation utilities**
  - `migbot_allocation/` with ROS and Lua implementations for multi-thruster boats, mapping high-level commands to actuator setpoints.

- **ArduPilot SITL and HITL support**
  - Tight integration with ArduPilot Rover firmware via MAVROS and MAVLink routing.
  - Custom MAVLink messages, scripts and parameter sets for HITL operation with Pixhawk-class boards.

- **X-in-the-Loop workflows**
  - **MITL** – Model-in-the-Loop experiments for algorithm development.
  - **SITL** – Software-in-the-Loop experiments with ArduPilot SITL.
  - **HITL** – Hardware-in-the-Loop with physical flight controllers.
  - **RITL** – Render-in-the-Loop tools to vary appearance while preserving physics, for dataset generation and perception studies.
  - **HuITL** – Human-in-the-Loop tools for teleoperation, supervision and dataset collection for MLP training.

For a more detailed overview of the proposed architecture, see [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

---

## Project structure

At the top level, the repository is organised as follows:

```text
.
├── ardupilot_hitl/         # HITL bridge between ArduPilot hardware and Gazebo/ROS
├── ardupilot_sitl/         # Scripts for ArduPilot SITL setup
├── docs/                   # Documentation and figures, e.g. xitl_architecture.png
├── enviroments/            # Blender files for world modelling, e.g. SAE_HYDRO_V0, and automation scripts
├── migbot_allocation/      # Control-allocation ROS package for the ASV
├── robots/                 # Blender sources, robot descriptions and Gazebo configurations
├── scripts/                # Large-file helper scripts for the Google Drive workflow
├── teleop_wrench_keyboard/ # Keyboard teleoperation node using geometry_msgs/Wrench
└── xasv_sim/               # Main ROS package: worlds, plugins, launch files and training scripts
```

The `robots/` folder contains the ASV description and its Gazebo configuration:

```text
robots/
├── blender/               # Blender sources for the ASV hull and components
├── migbot_description/    # URDF/Xacro description and meshes for the ASV
└── migbot_gazebo/         # Gazebo models, worlds and configuration for the ASV
```

The `xasv_sim/` package groups the main simulation assets:

```text
xasv_sim/
├── data/vehicle/          # Test missions, MLP models and datasets
├── launch/                # Launch files for XITL and MAVROS
├── models/                # World-level models
├── plugins/               # Custom Gazebo world plugins
├── scripts/               # MAVROS and MLP training/evaluation utilities
└── worlds/                # River worlds, e.g. madeira_river.world
```

The `scripts/` folder contains the helper files used by the external large-file workflow:

```text
scripts/
├── drive_bigfiles.txt     # List of large files stored outside GitHub
├── download_bigfiles.sh   # Optional helper to fetch large files
└── upload_bigfiles.sh     # Optional helper for maintainers
```

---

## Compatibility and requirements

xasv-sim 1.0.0 has been developed and validated on the following stack:

- **Ubuntu 20.04**
- **ROS Noetic + Gazebo Classic 11**
- **Blender 4.x**; developed with Blender 4.0.1 and also tested with Blender 5.0
- **ArduPilot Rover**
  - **SITL**: compatible with standard ArduPilot Rover releases
  - **HITL**: custom branch derived from Rover-4.0.0

To run the examples and reproduce the experiments, you will also need:

- **MAVProxy** and **QGroundControl** for SITL/HITL control and monitoring
- **GeographicLib** geoid datasets for MAVROS, especially EGM96
- **PyTorch (torch)** for Python 3, required by the ML/DRL modules
- **rclone**, optional, only if you want to use the scripted Google Drive workflow

Installation references:

- Blender:  
  https://www.blender.org/download/

- ROS 1 Noetic and Gazebo Classic 11 on Ubuntu 20.04:  
  http://wiki.ros.org/noetic/Installation/Ubuntu

- ArduPilot Rover firmware, source and tools:  
  - SITL case: https://github.com/lmhonorio/ardupilot  
  - HITL case: https://github.com/ttrindader/ardupilot/tree/hitl

- MAVProxy:  
  https://ardupilot.org/mavproxy/

- QGroundControl:  
  https://qgroundcontrol.com/downloads/

- GeographicLib geoid datasets for MAVROS:  
  https://github.com/mavlink/mavros/tree/master/mavros#supported-gcs

- PyTorch:  
  https://pytorch.org/get-started/locally/

- rclone, optional:  
  https://rclone.org/

---

## Installation

### 1) Install main dependencies

```bash
sudo apt update
sudo apt install git curl python3-pip python3-venv
```

If you plan to use the optional rclone-based large-file workflow, install `rclone` as well:

```bash
sudo -v
curl https://rclone.org/install.sh | sudo bash
rclone version
```

### 2) Clone into a catkin workspace

```bash
mkdir -p ~/ros_ws/src
cd ~/ros_ws/src
git clone https://github.com/ttrindader/xasv-sim.git
cd xasv-sim
```

### 3) Download the large binary assets

Large files are not stored in GitHub. Download them from the public Google Drive folder below:

**Public assets folder:**  
https://drive.google.com/drive/folders/1rYY7dQA-xYNVeQeXp-KPBFIzPC7BmF31?usp=sharing

There are two supported ways to install these assets.

#### Option A — Manual download from the public Google Drive folder

Download the files from the public folder and place them in the repository **preserving the exact relative paths below**:

```text
enviroments/blender/SAE_HYDRO_V0/main.blend
enviroments/blender/SAE_HYDRO_V0/main.blend1
xasv_sim/models/SAE_HYDRO_V0/meshes/logboom.dae
```

From the repository root, the final structure must look like this:

```text
xasv-sim/
├── enviroments/
│   └── blender/
│       └── SAE_HYDRO_V0/
│           ├── main.blend
│           └── main.blend1
└── xasv_sim/
    └── models/
        └── SAE_HYDRO_V0/
            └── meshes/
                └── logboom.dae
```

Verify the files with:

```bash
cd ~/ros_ws/src/xasv-sim
ls enviroments/blender/SAE_HYDRO_V0/main.blend
ls enviroments/blender/SAE_HYDRO_V0/main.blend1
ls xasv_sim/models/SAE_HYDRO_V0/meshes/logboom.dae
```

#### Option B — Scripted installation with rclone

This option is intended mainly for maintainers or advanced users. The helper script restores the files directly to the correct locations inside the repository.

From the repository root:

```bash
cd ~/ros_ws/src/xasv-sim
bash scripts/download_bigfiles.sh
```

If your local helper script expects an explicit rclone remote/path, use the form below after configuring your remote:

```bash
bash scripts/download_bigfiles.sh mydrive:xasv-sim
```

The list of externally stored files is maintained in:

```text
scripts/drive_bigfiles.txt
```

### 4) Resolve dependencies and build

```bash
cd ~/ros_ws
./src/xasv-sim/install_deps.sh ~/ros_ws
rosdep update
rosdep install --from-paths src --ignore-src -r -y --skip-keys="mavros_msgs"
sudo apt install python3-catkin-tools python3-osrf-pycommon
catkin config --skiplist ardupilot_hitl   # initially, no HITL
catkin build                               # or: catkin_make
echo 'export GAZEBO_MODEL_PATH=${GAZEBO_MODEL_PATH}:~/ros_ws/src/xasv-sim/xasv_sim/models' >> ~/.bashrc
source ~/.bashrc
source ~/ros_ws/devel/setup.bash
```

### 5) Plugin note

Many core functionalities of xasv-sim rely on Gazebo plugins. They fall into two categories:

- Standard plugins provided by ROS/Gazebo.
- Vendored/custom plugins versioned under `xasv_sim/plugins` and built together with the workspace.

For a detailed overview of all plugins, including origin and roles, and for advanced instructions on manually building or installing them, see [docs/PLUGINS.md](docs/PLUGINS.md).

---

## Docker workflow (optional)

The native workflow above remains the reference path. The Docker workflow is optional and is intended to reproduce the same stack with:

- **Ubuntu 20.04**
- **ROS Noetic**
- **Gazebo Classic 11**
- **ArduPilot Rover SITL**
- Optional **MAVProxy** in a separate service
- Optional **GPU acceleration** for Gazebo

The Docker solution is designed so that the **original repository code remains unchanged**. Docker-specific behavior is isolated to:

- `docker/`
- `docker-compose.yml`
- `docker-compose.nvidia.yml`
- `docker-compose.dri.yml`
- `scripts/docker_prepare_sim.sh`
- `scripts/docker_run_sim.sh`
- `scripts/docker_run_sitl.sh`

### Docker prerequisites

Install Docker Engine and the Docker Compose plugin on the host.

If you plan to use **NVIDIA GPU acceleration**, configure the NVIDIA container runtime on the host **once**:

```bash
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
sudo docker run --rm --gpus all ubuntu nvidia-smi
```

You do **not** need to run the three commands above every time. They are host-side configuration and validation steps, typically needed only once per machine or after Docker/NVIDIA runtime changes.

### Files used by the Docker solution

The Docker workflow expects the following files to exist in the repository:

```text
docker/
├── sim.Dockerfile
├── sitl.Dockerfile
├── tools.Dockerfile
└── entrypoints/
    ├── sim_entrypoint.sh
    ├── sitl_entrypoint.sh
    └── run_sitl.sh

scripts/
├── docker_prepare_sim.sh
├── docker_run_sim.sh
└── docker_run_sitl.sh
```

### First-time Docker workflow

From the repository root:

```bash
cd ~/ros_ws/src/xasv-sim
sudo docker compose build sim sitl tools
bash scripts/docker_prepare_sim.sh
```

The `docker_prepare_sim.sh` helper prepares the simulation workspace inside the `sim` container and runs the initial catkin build while skipping `ardupilot_hitl`, matching the native recommendation used above.

### Start the simulator with Docker

#### Standard run

```bash
cd ~/ros_ws/src/xasv-sim
bash scripts/docker_run_sim.sh
```

This wrapper typically runs the equivalent of:

```bash
xhost +local:
sudo -E docker compose up sim
```

If you want the service in the background:

```bash
sudo docker compose up -d sim
sudo docker compose logs -f sim
```

#### NVIDIA GPU-accelerated run

If the host is already configured for NVIDIA containers, start Gazebo with the NVIDIA override:

```bash
cd ~/ros_ws/src/xasv-sim
xhost +local:
sudo -E docker compose -f docker-compose.yml -f docker-compose.nvidia.yml up -d sim
sudo docker compose logs -f sim
```

If you want to see the launch directly in the terminal instead of running detached, omit `-d`:

```bash
sudo -E docker compose -f docker-compose.yml -f docker-compose.nvidia.yml up sim
```

#### AMD / Intel render-device run

On Linux hosts using Mesa/DRI, use the DRI override:

```bash
cd ~/ros_ws/src/xasv-sim
xhost +local:
sudo -E docker compose -f docker-compose.yml -f docker-compose.dri.yml up -d sim
sudo docker compose logs -f sim
```

### Check whether Gazebo is using GPU acceleration

Inside the running `sim` container:

```bash
sudo docker compose exec sim glxinfo -B
```

Look at the renderer/vendor strings. If the renderer shows `llvmpipe`, Gazebo is still using software rendering.

### Start SITL with Docker

In another terminal:

```bash
cd ~/ros_ws/src/xasv-sim
bash scripts/docker_run_sitl.sh
```

In the Docker workflow, the SITL container should run ArduPilot in Gazebo mode while leaving MAVProxy to a separate service. This avoids conflicts between an internal MAVProxy spawned by `sim_vehicle.py` and the external MAVProxy container.

If you want the SITL container in the background:

```bash
sudo docker compose up -d sitl
sudo docker compose logs -f sitl
```

If you want a shell inside the SITL container:

```bash
sudo docker compose exec sitl bash
```

### MAVProxy via Docker

If you want MAVProxy in its own Docker service:

```bash
cd ~/ros_ws/src/xasv-sim
xhost +local:
sudo -E docker compose --profile mavproxy up -d mavproxy
sudo docker compose logs -f mavproxy
```

If you prefer to follow the logs in the foreground instead of background mode, omit `-d`.

The Docker MAVProxy container is expected to connect to the SITL endpoint, typically on TCP port `5760`, and can be configured to forward telemetry to the usual local endpoints such as `14550` and `14552`.

### Inspect running Docker services

Show active services:

```bash
sudo docker compose ps
```

Show logs:

```bash
sudo docker compose logs -f sim
sudo docker compose logs -f sitl
sudo docker compose logs -f mavproxy
```

Enter a container shell:

```bash
sudo docker compose exec sim bash
sudo docker compose exec sitl bash
sudo docker compose exec mavproxy bash
```

Stop services:

```bash
sudo docker compose down
```

### Notes about the Docker approach

- The native workflow remains the reference behavior.
- Docker-specific wrappers should not require changes to the original xasv-sim source tree.
- Large assets still need to be restored through the Google Drive workflow before running the simulator, even in Docker.
- If GPU acceleration is not active, Gazebo may start correctly but with very low FPS.

---

## Testing

### Perception test

```bash
chmod +x /home/xasvsim/ros_ws/src/ping360_gazebo/ping360_gazebo_plugin/scripts/pcl_gen.py
roslaunch xasv_sim perception_test.launch
```

In the test world, a single ASV robot navigates a river scene while streaming data from its onboard sensors. In RViz, the expected setup includes:

- robot model with grid
- RealSense RGB image
- RealSense depth image
- RealSense point cloud
- Livox lidar point cloud
- Ping360 sonar point cloud

all referenced to `base_link`.

The following result is expected:

<p align="center">
  <img src="docs/figs/perception.png" alt="xasv-sim perception test" width="600"/>
</p>

---

## 3D Modelling

xasv-sim adopts a **Blender-first workflow** for both the river environments and the ASV robot model.

For environments, the user models the scene in Blender, including riverbed, dam structures, buoys, vegetation and other elements, and then runs the Python script `sdf_exporter.py`. This script walks over all visible mesh objects, exports one COLLADA `.dae` file per object, and automatically builds a complete Gazebo model with SDF, `model.config`, and `meshes/`. By default, the output is written under `~/.gazebo/models/<MODEL_NAME>/`, but both the base directory and the model name can be customized at the top of the script.

For the ASV robot, the workflow is similar, but the output is a URDF/xacro instead of an SDF world. The hull, superstructure, collision boxes, and propellers are modeled in Blender as a single assembled scene with a visible `base_link`. A dedicated script, `migbot2_xacro_exporter.py`, adds an export entry to Blender’s menu and generates a set of COLLADA meshes plus a `migbot2.urdf.xacro` file that already includes simplified collisions, approximate inertias, and the Gazebo plugins used in xasv-sim, such as thrusters, buoyancy, USV dynamics and sensors.

For a step-by-step description of the Blender-to-Gazebo pipeline, both for worlds and for the ASV robot, see [docs/3DMODELLING.md](docs/3DMODELLING.md).

---

## X-in-the-Loop workflows

xasv-sim supports several X-in-the-Loop modes, including **MITL**, **SITL**, **HITL**, **RITL** and **HuITL**, for progressively bringing models, software and hardware into the loop. Full descriptions, diagrams and background motivation are available in `docs/XITL.md`. Below are short notes and the main entry-point commands to reproduce the reference workflows quickly.

A **low-resolution version of the demonstration video** (`XASV_Sim_Demo.mp4`)
is included directly in this repository for quick access. A **high-resolution
version** of the same video is available at:

[High-resolution XASV-Sim demonstration video](https://drive.google.com/file/d/1R5udpR7qqLipAaoThGod7nqmVP8DspQR/view?usp=drive_link)

**HIGH-RESOLUTION-VIDEO-LINK**

The video provides representative examples of the MITL, SITL, HITL, RITL and HuITL workflows described below.

### MITL — Model-in-the-Loop

MITL is used for teleoperated runs and ArduPilot waypoint mission generation for `migbot1`.

In MITL, a human operator drives the ASV in the simulated river while ROS controllers apply forces to the virtual boat. During this run, xasv-sim records the GPS track and later converts it into an ArduPilot mission, for example a `.plan` file. This is the first step in the chain: you prototype missions and control ideas in pure simulation before engaging SITL or HITL.

```bash
# Simulator with ROS controllers; no ArduPilot
roslaunch xasv_sim xasv_sim.launch   world_name:=madeira_river   robot_name:=migbot1   ardupilot:=false

# Control allocation + keyboard teleop; namespace migbot1
rosrun migbot_allocation migbot_allocation_node __ns:=/migbot1
rosrun teleop_wrench_keyboard teleop_wrench_keyboard.py __ns:=/migbot1

# Record GPS during teleop
export BAG_NAME="test.bag"
rosbag record -O "${BAG_NAME}" /fix

# Convert GPS bag to ArduPilot mission
export MISSION_POINTS="10"
export MISSION_FORMAT="plan"
export MISSION_OUT="test.plan"
export MISSION_ALT="50"

rosrun xasv_sim rosgps2mission.py   --bag "${BAG_NAME}"   --topic "/fix"   --points "${MISSION_POINTS}"   --format "${MISSION_FORMAT}"   --output "${MISSION_OUT}"   --alt "${MISSION_ALT}"

# One-shot MITL demo; logboom test mission
./src/xasv-sim/xasv_sim/tests/mitl_test.sh
```

### SITL — Software-in-the-Loop

In SITL, the full ArduPilot Rover firmware runs as a program on your machine, while xasv-sim provides the river world, sensors and actuators via ROS + Gazebo. You keep the autopilot inside the loop without any hardware, which is ideal for tuning parameters, testing missions generated in MITL, and debugging guidance and control under repeatable conditions.

```bash
# One-time SITL configuration helper; installs Lua scripts/configs
./src/xasv-sim/ardupilot_sitl/ardupilot_sitl_config.sh

# Simulator with ArduPilot connections
roslaunch xasv_sim xasv_sim.launch   world_name:=madeira_river   robot_name:=migbot1

# Start ArduPilot Rover SITL; run this from the ArduPilot Rover directory
./gzboat.sh

# One-shot SITL demo; uses the provided test mission
./src/xasv-sim/xasv_sim/tests/sitl_test.sh
```

### HITL — Hardware-in-the-Loop

HITL closes the loop with a real flight controller, such as Pixhawk 4, running a patched ArduPilot firmware, while xasv-sim still provides the virtual river, sensors and actuator loads. A dedicated ROS/MAVROS bridge sends simulated IMU/MAG/GPS data to the board and relays actuator commands back to Gazebo, so the hardware behaves as if it were driving a real boat. This mode is used to validate dynamics, I/O timing and complete guidance stacks before field trials.

> **Important**  
> Current HITL support in xasv-sim is tied to a specific ArduPilot Rover firmware version, based on the custom HITL branch. In addition, MAVLink and MAVROS must be installed from source, since HITL in xasv-sim relies on custom Gazebo message definitions and custom MAVLink/MAVROS message types that are not provided by the prebuilt binary packages. Other firmware versions are not guaranteed to work out of the box and may require porting the patches.

See the HITL section in [docs/XITL.md](docs/XITL.md) for the exact branch and commit used in the experiments and for the full step-by-step installation procedure.

```bash
# Typical HITL session; use separate terminals

# Terminal 1: Gazebo world + robot
roslaunch xasv_sim xasv_sim.launch   world_name:=madeira_river   robot_name:=migbot1

# Terminal 2: MAVProxy; USB -> Pixhawk 4
mavproxy.py --mav20 --console   --out=127.0.0.1:14550   --out=127.0.0.1:14552   --master=/dev/ttyACM0,115200

# Terminal 3: ArduPilot HITL/SITL <-> Gazebo bridge
roslaunch ardupilot_hitl apm.launch

# Terminal 4: MAVROS / link node
roslaunch ardupilot_hitl link.launch

# Terminal 5: Ground control station
./QGroundControl.AppImage

# One-shot HITL demo; same reference mission
./src/xasv-sim/xasv_sim/tests/hitl_test.sh
```

### RITL — Render-in-the-Loop

RITL experiments keep the physics running while you change the visual appearance of the scene, such as lighting, colors, textures and mesh variants. This is useful for perception studies and dataset generation. For example, the `trunk_scaler` plugin can resize mesh variants of logs without resetting the world, producing many visual conditions from a single base scenario.

```bash
# Example RITL world with trunk_scaler plugin enabled
roslaunch xasv_sim xasv_sim.launch   world_name:=madeira_river_ritl   robot_name:=migbot1
```

### HuITL — Human-in-the-Loop

HuITL explicitly involves a human operator in SITL runs. The ASV follows a predefined mission in AUTO while the operator applies local avoidance maneuvers, such as RC throttle and yaw, via a virtual joystick whenever obstacles threaten the path. xasv-sim logs these override actions and the corresponding sensor/state data, which are then used to train and evaluate learning-based avoidance policies.

```bash
# HuITL arena; SITL mode, with Livox enabled
roslaunch xasv_sim xasv_sim.launch   world_name:=Huitl_OA   robot_name:=migbot1   livox_enabled:=true   x:=0 y:=20 Y:=0

# ArduPilot SITL; same script as SITL
./gzboat.sh

# Pre-training node for HuITL dataset
roslaunch xasv_sim huitl_pre_train.launch

# Stop data collection
rostopic pub /xasv/pretrain_stop std_msgs/Bool "data: true"

# Train avoidance policy from collected HuITL dataset
python3 src/xasv-sim/xasv_sim/scripts/train_supervised.py   --csv src/xasv-sim/xasv_sim/data/xasv_huitl_pretrain/*.csv   --epochs 50   --batch_size 64   --symmetry_aug   --out trained_rc_policy.pt

# Online policy node + wrapper for validation
roslaunch xasv_sim apm.launch
./src/xasv-sim/xasv_sim/scripts/xasv_huitl_policy_wrap.sh ~/ros_ws/trained_rc_policy.pt
```

For all modes, additional details, assumptions and diagrams are available in [docs/XITL.md](docs/XITL.md).

---

## Large files and Google Drive workflow

Large binary assets such as `.blend`, `.blend1`, `.dae`, textures, maps and other heavy files must be distributed separately through the public Google Drive folder:

**Public assets folder:**  
https://drive.google.com/drive/folders/1rYY7dQA-xYNVeQeXp-KPBFIzPC7BmF31?usp=sharing

The list of externally stored files is maintained in:

```text
scripts/drive_bigfiles.txt
```

At the moment, the required large files are:

```text
enviroments/blender/SAE_HYDRO_V0/main.blend
enviroments/blender/SAE_HYDRO_V0/main.blend1
xasv_sim/models/SAE_HYDRO_V0/meshes/logboom.dae
```

### Optional helper scripts

If you use `rclone`, the repository includes helper scripts:

```bash
bash scripts/download_bigfiles.sh
bash scripts/upload_bigfiles.sh
```

- `download_bigfiles.sh` restores the large files to their correct locations in the repository.
- `upload_bigfiles.sh` uploads or refreshes the same files in the remote storage.

These scripts are mainly intended for maintainers or advanced users.

### Configuring rclone

You do **not** need access to the maintainer's Google account to clone the repository. For normal users, manual download from the public folder is enough.

If you want to use the helper scripts with your own Google Drive remote, configure `rclone` locally:

```bash
rclone config
```

Recommended choices:

```text
New remote: n
Name: mydrive
Storage: drive
client_id: press Enter
client_secret: press Enter
scope: 1
root_folder_id: press Enter
service_account_file: press Enter
Advanced config: n
Browser authentication: y
```

After configuration, use one of the forms below, depending on how your local helper script is configured:

```bash
bash scripts/download_bigfiles.sh
bash scripts/download_bigfiles.sh mydrive:xasv-sim
bash scripts/upload_bigfiles.sh mydrive:xasv-sim
```

### Maintainer rule

Do **not** add large binary files directly to GitHub. When a new large asset is required:

1. Put the file in the Google Drive distribution folder.
2. Add its relative path to `scripts/drive_bigfiles.txt`.
3. Keep only lightweight source code, metadata and helper scripts tracked by Git.
4. Verify from a fresh clone that the repository works after restoring the assets.

---

## Troubleshooting

Below are common issues seen when running xasv-sim, especially SITL/HITL, and quick fixes.

### Serial port `/dev/ttyACM0` permission denied — HITL

**Symptoms**

- `Permission denied` when opening `/dev/ttyACM0`.
- MAVProxy or QGroundControl cannot connect to the Pixhawk.

**Fix**

Check the device permissions and your groups:

```bash
ls -l /dev/ttyACM0
groups
```

Add your user to `dialout` and re-login, or apply it immediately with `newgrp`:

```bash
sudo usermod -aG dialout $USER
newgrp dialout
```

Close any application that might be using the port, such as QGroundControl, MAVProxy or `screen`, and retry.

Extra fix, common on Ubuntu, if ModemManager is grabbing the port:

```bash
sudo systemctl stop ModemManager
sudo systemctl disable ModemManager
```

---

### Sanity-check your `$PATH` — two ArduPilot trees: SITL vs HITL

xasv-sim typically uses **two ArduPilot repositories**, one for SITL and one custom tree for HITL. A random `ardurover` in your `$PATH` can silently point to the wrong build.

Quick checks:

```bash
which ardurover || true
which mavproxy.py || true
echo "$PATH" | tr ':' '
'
```

Recommendation:

- Prefer calling scripts with **absolute paths**, or `cd` into the intended ArduPilot tree before running `./waf` or `./gzboat.sh`.
- Do not mix binaries from the SITL tree with Lua/scripts/params from the HITL tree, and vice versa.

---

### `bind failed on port 5760 - Address already in use` — SITL

**Cause**

Another SITL instance, or another process, is already listening on port `5760`.

**Fix**

```bash
ss -lptn | grep ':5760' || true
# or:
lsof -i :5760 || true
```

Kill the process using that port, or reboot the SITL stack. If needed, change the port in your SITL startup script.

---

### `rosdep` cannot resolve `mavros_msgs`

**Symptoms**

- `Cannot locate rosdep definition for [mavros_msgs]`.

**Fix, recommended for first install**

Build the workspace **without** the HITL package first:

```bash
rosdep install --from-paths src --ignore-src -r -y --skip-keys="mavros_msgs"
catkin config --skiplist ardupilot_hitl
catkin build
```

Later, when you actually need HITL, install/build MAVROS and custom messages if required by your HITL branch, and then remove the skiplist.

---

### Submodules failing: `Permission denied (publickey)` or `git://...` blocked

**Symptoms**

- `git@github.com: Permission denied (publickey).`
- Submodules try to clone with `git@...` or `git://...`.

**Fix**

Force HTTPS for GitHub URLs:

```bash
git config --global url."https://github.com/".insteadOf git@github.com:
git config --global url."https://github.com/".insteadOf git://github.com/
```

Then resync and retry:

```bash
git submodule sync --recursive
git submodule update --init --recursive
```

---

### Python toolchain issues — pip, setuptools, empy, importlib_metadata

**Symptoms**

- `empy==3.3.4` fails to build.
- `AttributeError: module 'importlib_metadata' has no attribute '__version__'`.
- Random breakage after mixing system Python, `pip --user` and conda.

**Fix, most stable**

Use an isolated environment, such as venv or conda, and avoid mixing with `~/.local`:

```bash
python3 -m venv .venv_xasv
source .venv_xasv/bin/activate
python -m pip install -U pip wheel setuptools
```

If you already mixed environments, inspect the user site-packages that may be shadowing system packages:

```bash
python3 -m site --user-site
# then inspect/remove conflicting packages under that path, if necessary
```

---

### MAVProxy FTP / logs: `Unable to fetch Log File Size: ENOENT`

This can happen after deleting logs via FTP and then trying to query a now-missing index or file.

**Fix**

Prefer the autopilot-side erase command, which is cleaner than removing files manually. From MAVProxy, try:

```text
log erase
```

Then reboot the autopilot. If using FTP, refresh listings and paths:

```bash
ftp list /
ftp list /APM/log
```

---

### Lua scripting errors — API mismatch

**Symptoms**

- `attempt to call a nil value (global 'Parameter')`.
- A script runs on one firmware branch but not on another.

**Cause**

Lua scripting APIs can differ between ArduPilot versions and branches.

**Fix**

- Ensure you are using the **Lua scripts shipped for that exact firmware branch**, especially in HITL.
- Do not reuse Lua scripts across unrelated ArduPilot versions without porting.

---

### Gazebo models/plugins not found

**Symptoms**

- Missing models at runtime.
- Plugin load failures after build.

**Fix**

Ensure model paths are exported, and that you opened a new shell or sourced `~/.bashrc`:

```bash
echo "$GAZEBO_MODEL_PATH"
```

If you updated plugins or messages, do a clean rebuild:

```bash
catkin clean -y
catkin build
source ~/ros_ws/devel/setup.bash
```

Also verify that the large files were restored:

```bash
cd ~/ros_ws/src/xasv-sim
ls enviroments/blender/SAE_HYDRO_V0/main.blend
ls enviroments/blender/SAE_HYDRO_V0/main.blend1
ls xasv_sim/models/SAE_HYDRO_V0/meshes/logboom.dae
```

---

### Large files are missing after clone

**Symptoms**

- The repository cloned successfully, but Gazebo or Blender cannot find `.blend`, `.blend1`, `.dae`, textures or meshes.

**Cause**

Large assets are stored externally in Google Drive.

**Fix**

Use the manual Google Drive download or the rclone helper script described in [Large files and Google Drive workflow](#large-files-and-google-drive-workflow).

---

### Docker SITL launches but MAVProxy console fails or blocks the terminal

**Symptoms**

- The `sitl` container starts, but MAVProxy reports:
  - `Failed to load module: No module named 'console'`
  - `Waiting for heartbeat from tcp:127.0.0.1:5760`
- The SITL container occupies the terminal or launches an internal MAVProxy that conflicts with a separate Docker MAVProxy service.

**Cause**

If the Docker SITL service starts `sim_vehicle.py` with the default MAVProxy behavior, it may try to open its own `--console` session. In a Docker workflow where MAVProxy is intentionally separated into another service, this causes duplication and console-module issues.

**Fix**

Prefer a Docker-side SITL command that runs ArduPilot with **`--no-mavproxy`**, while keeping MAVProxy in the dedicated `mavproxy` service.

Also, if you want the SITL or MAVProxy container to keep running **without locking your terminal**, use detached mode:

```bash
sudo docker compose up -d sitl
sudo docker compose --profile mavproxy up -d mavproxy
```

Then inspect logs with:

```bash
sudo docker compose logs -f sitl
sudo docker compose logs -f mavproxy
```

To enter the container later:

```bash
sudo docker compose exec sitl bash
sudo docker compose exec mavproxy bash
```

---

### Docker Gazebo starts but runs without GPU acceleration

**Symptoms**

- Gazebo launches successfully in Docker, but FPS is very low.
- `glxinfo -B` inside the container shows a software renderer such as `llvmpipe`.

**Cause**

The simulator container is running without access to the host GPU, or the NVIDIA/DRI Docker runtime path is not configured correctly.

**Fix, NVIDIA**

Configure the NVIDIA runtime on the host once:

```bash
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
sudo docker run --rm --gpus all ubuntu nvidia-smi
```

Then launch the simulator with the NVIDIA Compose override:

```bash
xhost +local:
sudo -E docker compose -f docker-compose.yml -f docker-compose.nvidia.yml up -d sim
sudo docker compose logs -f sim
```

To verify GPU usage inside the container:

```bash
sudo docker compose exec sim glxinfo -B
```

**Fix, AMD / Intel**

Use the DRI override:

```bash
xhost +local:
sudo -E docker compose -f docker-compose.yml -f docker-compose.dri.yml up -d sim
sudo docker compose exec sim glxinfo -B
```

If the renderer is still software-based, compare host and container OpenGL information.

---

### Docker `sim` workspace reinitializes or tries to build `ardupilot_hitl`

**Symptoms**

- The container repeatedly prints `Initialized new catkin workspace in /ws`.
- `catkin config` does not preserve the skiplist.
- The build tries to compile `ardupilot_hitl` and fails at:
  - `mavros_msgs/GazeboMavlink.h: No such file or directory`

**Cause**

The Docker workspace state is not being persisted consistently, or the preparation step did not complete successfully before starting the simulator.

**Fix**

Use the dedicated preparation script before starting `sim`:

```bash
bash scripts/docker_prepare_sim.sh
```

This step must complete successfully before running:

```bash
bash scripts/docker_run_sim.sh
```

If needed, inspect the workspace contents under the Docker-side workspace directory, depending on your Docker setup, and rerun the preparation step.

---

## Still stuck?

When opening an issue, include:

- Ubuntu / ROS / Gazebo versions.
- Whether you are running **MITL**, **SITL**, **HITL**, **RITL** or **HuITL**.
- The exact command you ran.
- Full terminal output from the failing step.
- For SITL/HITL problems, also include:

```bash
which ardurover || true
which mavproxy.py || true
echo "$PATH" | tr ':' '
'
```

---

## Contributing

Contributions, issue reports and feature requests are welcome.

- Create feature branches: `git switch -c feature/<short-name>`.
- Use clear, descriptive commit messages.
- Open pull requests against `main`.
- Do **not** add large binary files directly to GitHub.
- Keep heavy assets in the public Google Drive workflow and update `scripts/drive_bigfiles.txt` when needed.

---

## License

The license for xasv-sim is being finalised.

Until a `LICENSE` file is added at the repository root, please treat this code as **research-only** and contact the maintainers before redistributing or relicensing it.

---

## Citation

If xasv-sim is useful in your research, please cite the associated project and article. A formal citation entry, such as BibTeX, will be added here once the corresponding paper is published.

For now, a generic reference such as the following can be used/adapted:

> T. T. Ribeiro et al., "xasv-sim: A ROS + Gazebo X-in-the-Loop stack for autonomous surface vessels in realistic river environments", GitHub repository, accessed YYYY-MM-DD.
