
# â˜ï¸ GCP VM Setup with VNC and Docker for ICLAD Hackathon

This guide provides detailed instructions to launch a GCP virtual machine, install a VNC server, configure the environment, and prepare for the ICLAD Hackathon.

---

## ðŸ”‘ Step 1: Create a VM Instance on GCP

### 1. Login and Create VM
- Log in to [Google Cloud Console](https://console.cloud.google.com/) with your username and password.
- Click on **Console**.
- Click **Select a project** and choose the project matching your account name.
- Enable **Compute Engine API** (search for "Compute Engine" and click **Enable**).
- Click **VM Instances > Create Instance**:
  - **Name**: `iclad-hackathon-default-vm`
  - **Machine type**: Default is fine (`e2-medium`) or upgrade if more power is needed.
  - **Boot disk**: Click **Change**, select **Ubuntu 22.04 LTS x86/64 jammy**.
  - **Disk size**: Set to **128 GB or larger**.
  - **Firewall**: Check `Allow HTTP traffic` and/or `Allow HTTPS traffic` as needed.
  - **Network Tags**: Type `vnc-server`.
  - **Security > Access scopes**: Select `Allow full access to all Cloud APIs`.

### 2. Create a Firewall Rule for VNC
- Go to **VPC network > Firewall > Create Firewall Rule**:
  - **Name**: `default-allow-vnc`
  - **Description**: Rule to allow ingress VNC traffic.
  - **Network**: `default`
  - **Priority**: `1000`
  - **Direction**: `Ingress`
  - **Action**: `Allow`
  - **Targets**: `Specified target tags`
  - **Target Tags**: `vnc-server`
  - **Source Filter**: `IP Ranges`
  - **Source IP Ranges**: `0.0.0.0/0`
  - **Protocols and Ports**: `tcp:5900-5910`

---

## ðŸ’» Step 2: Connect to the VM

- Go to **VM instances** and click the **SSH** button next to your VM.
- Ensure **Vertex AI API** is enabled (search "Vertex AI API" in the search bar and click **Enable**).

---

## âš™ï¸ Step 3: Create Setup Scripts

### 1. `setup_iclad_env.sh`

```bash
#!/bin/bash

set -e

# Basic packages
sudo apt-get update
sudo apt-get install -y git vim-gtk3

# Docker installation
sudo apt update
sudo apt install -y ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

sudo usermod -aG docker "$USER"

# Create directories
mkdir -p iclad_hackathon/workspace
mkdir -p iclad_hackathon/hello_world

# Clone repo
cd iclad_hackathon
git clone --recurse-submodules https://github.com/ICLAD-Hackathon/ICLAD-Hackathon-2025.git
cd ..

# Create Gemini test
cat <<EOF > iclad_hackathon/hello_world/test_gemini.py
from vertexai.preview.generative_models import GenerativeModel
import vertexai
vertexai.init(location="us-central1")
model = GenerativeModel("gemini-2.0-flash-001")
response = model.generate_content("Say hi from inside the ICLAD Hackathon container!")
print(response.candidates[0].text)
EOF

echo "Environment setup complete. Please log out and log back in for Docker changes to apply."
```

### 2. `install_vnc_xfce.sh`

```bash
#!/bin/bash

echo "=== Updating packages ==="
sudo apt update

echo "=== Installing TightVNC and XFCE ==="
sudo apt install -y tightvncserver xfce4 xfce4-goodies autocutsel

echo "=== Setting VNC password ==="
vncpasswd

echo "=== Starting and stopping VNC to initialize files ==="
vncserver -kill :1
vncserver :1
vncserver -kill :1

echo "=== Writing XFCE startup script to ~/.vnc/xstartup ==="
cat > ~/.vnc/xstartup << 'EOF'
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
xrdb $HOME/.Xresources
startxfce4 &
autocutsel -fork
EOF

chmod +x ~/.vnc/xstartup

echo "=== Starting VNC server on display :1 ==="
vncserver :1
echo "=== Done! Connect using a VNC client to <your-external-ip>:5901 ==="
```

---

## ðŸ› ï¸ Step 4: Run Setup

```bash
chmod +x setup_iclad_env.sh install_vnc_xfce.sh
./setup_iclad_env.sh
```

- ðŸ” **Close the SSH session and re-open it.**
- Then run:

```bash
./install_vnc_xfce.sh
```

- When prompted for the VNC password: use `vnc123`
- For **view-only password**: type `n`

---

## ðŸ³ Step 5: Docker Image Setup

### 1. Pull the Docker Image

```bash
docker pull openroad/orfs:latest
```

### 2. Run and Modify the Container

```bash
docker run -it --name iclad_hackathon \
  -v ~/iclad_hackathon:/workspace/iclad_hackathon \
  openroad/orfs:latest bash
```

### 3. Install Required Packages Inside Docker

```bash
apt-get update && apt-get install -y git iverilog gtkwave vim-gtk3
pip install --no-cache-dir google-cloud-aiplatform
```

### 4. Run Gemini Test

```bash
python3 /workspace/iclad_hackathon/hello_world/test_gemini.py
```


## ðŸ’¾ Step 6: Save Docker Image

```bash
exit
docker commit iclad_hackathon iclad_hackathon:latest
# Optional: Save to file
docker save -o iclad_hackathon.tar iclad_hackathon
```


## â–¶ï¸ Step 7: Run Saved Docker Image

```bash
docker run -it --rm \
  -v ~/iclad_hackathon:/workspace/iclad_hackathon \
  iclad_hackathon:latest bash
```


## ðŸŽ‰ Done!

You can now develop and run your ICLAD Hackathon code inside a fully configured VM with GUI access via VNC and development support via Docker.

