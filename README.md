# Anti-Cheat System (ACS) - Demo Setup Guide

This guide provides quick and easy instructions to set up, deploy, and run the Anti-Cheat System for your demo. The system uses pre-compiled JAR files along with handy scripts to easily supervise and configure everything.

## 🛡️ What Does This Application Do?

The Anti-Cheat System (ACS) is designed to maintain testing integrity by monitoring a student's machine for suspicious behavior. When the client runs on a student's PC, it tracks and reports the following activities to the Server:

*   **Targeted Monitoring Control:** When you click **"Start Monitoring"** on the server, the system immediately begins tracking and actively recording all activities. When you click **"Stop Monitoring"**, the recorded session is safely finalized and automatically saved as a session log.
*   **On-Demand Live Screen Feed:** Allows proctors to simply click and start a live video stream of any connected student's screen locally over the network using FFmpeg.
*   **Active Window Tracking:** Detects if a student tabs out of the testing environment to search the web or open an unauthorized application.
*   **USB & Phone Detection:** Detects and alerts the server if new USB devices, flash drives, or mobile phones are plugged into the PC during the session.
*   **Keylogging & Keyword Flagging:** Logs keyboard input and automatically flags suspicious behavior if certain predefined "banned" keywords are typed.
*   **Fault Tolerance & Auto-Recovery:** The system features robust auto-discovery and auto-recovery methods. If a session is interrupted or network connection drops unexpectedly, the clients and server will automatically attempt to reconnect without breaking.

> 🛠️ **Status (In Development):** Because this software is currently in active development, we are exclusively releasing and referring to the **Debug Mode** scripts for this demo. Running in debug mode leaves a console terminal open on the machines, allowing developers and proctors to see live internal application logs and quickly catch any errors.

---

## ⚙️ Prerequisites
To run the server and client, you must have the **Java Runtime Environment (JRE) or JDK 25** installed on your system. 
- Please install Java 25 before proceeding.

---

## 📂 Expected Directory Structure

For everything to work smoothly, your server and client folders should look like the layout below. 

💡 **Note:** The **Server** and **Client** folders are completely independent of each other. You can set them up together on one machine for testing, or distribute the Client folder entirely on its own to other machines without issue.

### Server Folder Structure
```text
server/
├── vlc/                   # Extracted VLC media player folder (Windows only)
│   ├── libvlc.dll         # Ensure these core DLLs are visible here so the system finds VLC!
│   ├── libvlccore.dll     
│   └── ...                # Other VLC files and folders
├── server-app.jar         # The main Server JAR file
├── start-server-debug.bat # Run script for Windows
└── start-server-debug.sh  # Run script for Linux (Ubuntu)
```

### Client Folder Structure
```text
client/
├── ffmpeg/                
│   └── ffmpeg.exe         # FFmpeg executable placed inside an 'ffmpeg' folder (Windows only)
├── client-app.jar         # The main Client JAR file
├── start-client-debug.bat # Run script for Windows
└── start-client-debug.sh  # Run script for Linux (Ubuntu)
```

---

## 📦 Media Dependencies Setup (Crucial for Video Streaming)

The system heavily relies on **FFmpeg** (Client side) for capturing student screens and **VLC** (Server side) for rendering the video streams over the network. 

### Option A: Windows

**1. FFmpeg Setup (For the Client folder):**
* Go to [https://www.gyan.dev/ffmpeg/builds/](https://www.gyan.dev/ffmpeg/builds/) and download the **ffmpeg-git-full.7z** or **.zip** build.
* Extract the archive.
* Open the extracted folder and navigate into the `bin` directory.
* **Copy ONLY `ffmpeg.exe`** (you can strictly ignore `ffplay` and `ffprobe`).
* Inside your **Client folder**, create a new folder named `ffmpeg`.
* Paste `ffmpeg.exe` inside the newly created `ffmpeg` folder.

**2. VLC Setup (For the Server folder):**
* Download the VLC zip/portable version for Windows from here: [VLC Windows Download](https://images.videolan.org/vlc/download-windows.html)
* Extract the downloaded VLC folder.
* Rename the extracted folder to `vlc` and place it directly into your **Server folder**.
* **Check your work:** Open the `vlc` folder you just placed. Make sure you immediately see files like `libvlc.dll` and `libvlccore.dll` inside it. This confirms you have placed the structural root of VLC in the correct spot.

### Option B: Linux (Ubuntu)

Setting up dependencies on Ubuntu is much faster since you do not need to deal with files manually. Open your terminal and run:

```bash
sudo apt update
sudo apt install vlc ffmpeg -y
```

---

## 🚀 Running the System

Use the provided scripts to launch the application:
- **`.bat` files** are exclusively for **Windows**. 
- **`.sh` files** are exclusively for **Linux (Ubuntu)**.

**🐧 Linux (Ubuntu) Users Note:** Before running `.sh` scripts for the first time, you need to make them executable. Open a terminal in the folder and run `chmod +x *.sh`. Once executable, always run them by explicitly typing `./` before the filename (for example: `./start-server-debug.sh`).

### Step 1: Launching the Server (Admin/Monitor PC)
> 🚨 **CRITICAL NETWORK RULE:** Ensure there is **ONLY ONE** Server instance actively running on your local network at any given time. If multiple servers are active, clients might connect to the wrong server indiscriminately.

1. Navigate to your Server directory.
2. Run the server script:
   - **Windows:** Double-click `start-server-debug.bat`
   - **Linux:** Run `./start-server-debug.sh`
3. ⚠️ **IMPORTANT (Windows Users):** When the application starts, you will likely see a **Windows Defender Firewall** pop-up. You **MUST click "Allow access"** (and please ensure both private and public networks are checked). If you do not allow this, the firewall will block all incoming video streams.

### Step 2: Setting up the Client (Student PCs)
1. As mentioned, the **Client** folder is entirely independent. You can simply copy the fully structured Client folder (including the `ffmpeg/ffmpeg.exe` folder structure for Windows) and move it to any target student PC.
2. Navigate into the Client directory on the student machine.
3. Run the client script:
   - **Windows:** Double-click `start-client-debug.bat`
   - **Linux:** Run `./start-client-debug.sh`
4. ⚠️ **IMPORTANT (Windows Users):** Just like the server, if a **Windows Defender Firewall** pop-up appears, you **MUST click "Allow access"** to permit network communication.
5. *(Repeat this process for every student PC you want to monitor).*

### Step 3: Operating the Session
Once both the Server and Client(s) are running and communicating:
1. **To Begin Recording:** On the Server application UI, click the **"Start Monitoring"** button. This will start the active tracking and recording of all connected client activities.
2. **To Finish & Save:** When the demo/test is finished, click the **"Stop Monitoring"** button on the Server. This explicitly halts the tracking phase and generates the session's log file for review.

---

## 📁 Logs & Session Management

All recorded activities, system messages, and client session records are strictly logged and saved inside a root **`logs`** folder. 

**Log Structure & Flow:**
1. When you click **"Stop Monitoring"**, the session officially concludes and saves everything.
2. Inside the `logs` folder, the system will automatically create a sub-folder explicitly named after the monitored **Client's IP Address**.
3. Inside that IP folder, a `.txt` file is generated containing a compiled, time-stamped timeline of the entire session.

If there is any strange behavior with a client, navigate into their matched IP folder to check these `.txt` files for complete insights.
