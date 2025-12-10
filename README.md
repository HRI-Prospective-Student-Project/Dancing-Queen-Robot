# Dancing-Queen-Robot

Project Overview
- This repository contains the Dancing-Queen-Robot codebase — a Flask-based web dashboard paired with example scripts that interact with a Misty Robotics robot via the `mistyPy` SDK. The project includes web pages (in `templates/`) designed as interactive demo pages and Python scripts that demonstrate robot behaviors such as dancing, greeting, and object/person tracking.

Quick Notes
- **Robot hardware**: This code expects a Misty Robotics robot reachable on your local network. Several scripts set the robot IP via a `MISTY_IP` (for example `192.168.1.9` or `192.168.1.4`) — update these values to match your robot.
- **SDK wrapper**: The `mistyPy/` folder contains the SDK helpers used by the examples.

Prerequisites
- **Python 3.8+**: A Python 3 runtime is required.
- **Python packages**: Install Python dependencies with:

```powershell
pip install -r requirements.txt
```

Web / Frontend
- The frontend uses static assets in `templates/` and `static/assets`.
- The repo includes a `package.json` for front-end dependencies (Tailwind, feather-icons); run the usual Node workflows if you want to rebuild frontend assets.
- Tailwind, and the feather-icons require internet connection or for them to be cached before use. 
    - One way to cache is to run test.py with internet (make sure each route is routed to the correct html page)

**Misty Desktop Environment (Windows) — Robot Setup**
This project can be used with a local Python development environment. The Misty Lessons include a recommended workflow for setting up a Windows desktop environment for Python development — the steps below summarize that lesson and add a few project-specific tips.

1) Power on Misty and connect it to your Wi‑Fi network.

  - Make sure the robot is reachable from your development machine (same LAN).

2) Install a code editor, Python, and the Misty Python SDK

```powershell
# Editor
Download Visual Studio Code: https://code.visualstudio.com/download

# Python
Download Python: https://www.python.org/downloads/

# Misty Python SDK (clone or download zip)
https://github.com/MistyCommunity/Python-SDK
```

3) Create a working directory and set PATHs (Windows)

  - Choose a folder for your Misty SDK and projects (for example `C:\Users\YourName\Desktop\MistyPythonSDK`).
  - Open `Edit the system environment variables` → `Environment Variables` → edit `Path` and add the folders that contain your Python executables, editor binaries, and your working directory so that the terminal inside your editor can access them.

4) Configure VS Code terminal working directory (optional but recommended)

  - In VS Code settings set `terminal.integrated.cwd` to your working folder so the integrated terminal starts in the correct location.

5) Install required Python dependencies for the Misty SDK (run from your project folder / virtual environment)

```powershell
pip install 'requests>=2.25.1'
pip install 'websocket-client<=0.57.0'
pip install 'yapf>=0.30.0'
```

  - For this repository also install Flask (already listed in `requirements.txt`):

```powershell
pip install -r requirements.txt
```

6) Generate Robot wrappers (Robot Generator)

  - Create a small script (for example `update.py`) in your working directory to regenerate the SDK command wrappers for your specific robot model and firmware. Example contents:

```python
from mistyPy.GenerateRobot import RobotGenerator
RobotGenerator("ROBOT-IP_ADDRESS-GOES-HERE")
```

  - Run it from the terminal:

```powershell
python update.py
```

  - This step will update `mistyPy` wrappers (e.g., `RobotCommands.py` / `Websocket.py`) to match your robot's available commands and websockets.

7) Create and or run your Python project

  - Keep your project files in or next to the Misty SDK folder so imports like `from mistyPy.Robot import Robot` resolve. Alternatively, install the SDK into your virtualenv.
  - Update the robot IP in scripts (e.g., `MISTY_IP = "192.168.1.X"` or `Robot("192.168.1.X")`) to match your robot.

  - Run an example script to verify connectivity, for example:

```powershell
python Examples\examples_first_skill.py
```

  - For the Flask web dashboard (if using live robot features):

```powershell
python app.py
```

8) Troubleshooting tips

  - Verify the robot and development machine are on the same network and that the robot IP is correct.
  - If you have firewall restrictions, allow the developer tools/terminal access or temporarily disable the blocking rules while testing.
  - Make sure assets referenced by the code (audio/images) exist on the robot or change the filenames in code.
  - If a command is missing, re-run `update.py` to refresh the generated SDK wrappers.

For full details and images, consult the original Misty lesson: https://lessons.mistyrobotics.com/misty-lessons/desktop-environment

How to run the web app (local)

```powershell
python app.py
```

The Flask apps run on `0.0.0.0:5001` by default (see file top-level `if __name__ == '__main__'` blocks).
Link to page is in the terminal
Running the robot and dashboard together
- The web dashboard (`app.py`) and the robot behavior script (`DancingQueen.py`) are intended to run at the same time so the dashboard can send commands/events and the robot-side event loop can respond. Start them in separate terminals (or separate machines) on the same network. Example (two terminals):

```powershell
# Terminal A: serve the dashboard
python app.py

# Terminal B: run robot behavior/event loop
python DancingQueen.py
```

- If you only want the frontend without connecting to the robot, `test.py`.


Key Scripts & Purpose
- `app.py`: Main Flask app that interacts with Misty via `mistyPy.Robot`. Provides routes used by the web dashboard and several JSON endpoints (`/speak`, `/mistyStart`, `/directSpeak`).
- `DancingQueen.py`: A robot script that contains behavior for dancing, touch-based interactions, and event-driven behavior (pose estimation and object/person tracking).
- `Examples/generate_robot.py`: Script that uses `mistyPy.GenerateRobot.RobotGenerator` to regenerate command wrappers for a robot at a given IP (used when customizing SDK wrappers for a specific robot).
- `Examples/examples_first_skill.py`: Example robot behavior demonstrating speech and event subscription. In order to add new events, you also need to add it to Events.py under the mistyPy folder. add the name to the available_events list as a string and then create a variable that is equal to that name.

Configuring the Robot IP
- Several files hardcode a robot IP (e.g., `192.168.1.9`, `192.168.1.4`, `192.168.1.1`). Update the `MISTY_IP` or `Robot(...)` lines to the actual IP of your Misty robot before running the scripts.

Running Examples
- To run an example that controls the robot (ensure robot is reachable and you have permission to control it):

```powershell
python Examples\examples_first_skill.py
```

Testing
- There are no automated unit tests included. Manual testing is via the Flask apps and running the example robot scripts.

Folder Structure (high level)
- `app.py` — Flask app (live robot integration)
- `DancingQueen.py` — robot behavior / event-driven logic
- `Examples/` — sample scripts and SDK generator
- `mistyPy/` — local SDK wrapper used by examples
- `templates/` and `static/` — frontend pages and assets

Troubleshooting & Tips
- If you see network or connection errors, verify the robot and your development machine are on the same network and confirm the robot IP.
- Many Misty commands assume audio assets and images are available on the robot (e.g., `s_Success3.wav`, `e_Joy3.jpg`). If a command fails to find an asset, either upload the asset to the robot via the Misty tools or change the filename used in code.

Contributing / Next Steps
- If you want a more portable configuration, consider adding a single config file (e.g., `config.ini` or environment variables) to centralize the `MISTY_IP` and other runtime options.
- If you want, I can:
  - Add a `README` section with example screenshots and a quick demo GIF.
  - Create a `config.py` to centralize IP settings and update scripts to use it.

License
- This repository contains code derived from Misty Robotics samples. The `Examples/generate_robot.py` file includes a Misty Robotics Apache 2.0 header; respect the respective licenses when re-using code.

---