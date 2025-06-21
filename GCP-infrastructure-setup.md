# Using the GCP infrastructure via VNC for the ICLAD 2025 Hackathon

## 🔐 Step 1: Connect to the VNC Server

- **External IP Address**: _Provided by your admin_
- **VNC Password**: _Provided by your admin_


1. Open your VNC Viewer (e.g., [RealVNC Viewer](https://www.realvnc.com/en/connect/download/viewer/)).
2. Enter the IP address in the address bar: 

3. Click **Connect** and enter the password when prompted.

---

## 💻 Step 2: Open a Terminal Window

Once connected, you'll see the XFCE desktop environment.

- Right-click anywhere on the desktop.
- Choose **Applications > Terminal Emulator**, or use the menu to launch a terminal.

---

## 📂 Step 3: Explore Directory Structure

When you open the terminal and run `ls ~`, you will see several top-level directories and files

Navigate to the "iclad_hackathon" folder

```
cd iclad_hackathon
```

The `iclad_hackathon` directory contains all files and subfolders needed for the hackathon. Its internal structure looks like this:

```plaintext
iclad_hackathon/
├── workspace/
├── hello_world/
│ └── test_gemini.py
└── ICLAD-Hackathon-2025/ (Git repo with submodules of the different problem categories)
```

## ▶️ Step 4: Run Saved Docker Image with EDA Tools

Run the saved Docker image that contains all EDA tools and mount the `iclad_hackathon` directory:

```bash
docker run -it --rm \
  -v ~/iclad_hackathon:/workspace/iclad_hackathon \
  iclad_hackathon:latest bash
```

The mounted directory will be available at:

```bash
/workspace/iclad_hackathon
```

The OpenROAD-flow-scripts can be found at:

```
/OpenROAD-flow-scripts
```

iVerilog can be run using

```bash
iverilog
```

##  ▶️ Step 5: Run Gemini Test from Docker Image

```bash
python3 /workspace/iclad_hackathon/hello_world/test_gemini.py
```



