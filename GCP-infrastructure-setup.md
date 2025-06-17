
# ☁️ GCP Setup Instructions for ICLAD Hackathon

Follow these steps to create and configure a GCP virtual machine (VM) for the hackathon environment.

---

## 🔐 Login & Project Setup

1. Go to [https://console.cloud.google.com](https://console.cloud.google.com).
2. Log in using the provided **username and password**.
3. Click on **Console** in the top-right.
4. Click **Select a project**, then choose the project that matches your account name.

---

## ⚙️ Enable Required APIs

Use the search bar at the top of the console to find and enable the following APIs:

- **Compute Engine API**
- **Vertex AI API**

> Click **Enable** if not already activated.

---

## 🖥️ Create a Virtual Machine (VM)

1. Navigate to **Compute Engine > VM Instances**.
2. Click **Create Instance** and configure the following:
   - **OS and Storage**: Set disk size to 128 GB or larger if required
   - **Machine Type**: Use the default (`e2-medium`), or select a larger type for more power.
   - **Boot Disk**: Click **Change** → Select **Ubuntu 22.04 LTS**.
   - **Firewall**: Check **Allow HTTP traffic** and/or **Allow HTTPS traffic** if needed.
   - **Security**: Under **Access Scopes**, choose **Allow full access to all Cloud APIs**.
4. Click **Create**.

---

## 🔧 Configure Your VM (After Creation)

1. On the **VM Instances** page, click the **SSH** button next to your instance to open a terminal.
2. Install Git and Docker with:

    ```bash
    sudo apt-get update && sudo apt-get install -y git
    sudo apt-get update && sudo apt-get install -y docker.io
    sudo usermod -aG docker $USER
    ```

3. **Close the SSH session**, then **reconnect** by clicking the SSH button again to activate Docker group membership.

---

# 🧪 Example: Running a Docker Image (Example: Spec2Tapeout Problems)

> This is just **one example** Docker image for the Spec2Tapeout problems. Other categories will have other Docker images that can be pulled in the same way as documented in the problem category readmes. 

---

## 🐳 Pull the Docker Image

```bash
docker pull openroad/orfs:latest
```
## 🖥️ Run the Container

Launch a terminal inside the Docker container with OpenROAD-flow-scripts and Verilator pre-installed:

```bash
docker run -it --rm openroad/orfs:latest bash
```

## 📁 (Optional) Mount a Local Design Directory

If you'd like to access your local design files inside the container, use:

```bash
docker run -it --rm \
  -v /path/to/your/local/design:/workspace/design \
  openroad/orfs:latest bash
```


# 🤖 Run Gemini from Docker 

You may use Gemini APIs either inside the Docker image or directly on the VM.

## ❗️ If running Gemini inside the Docker image  
Install the Vertex AI SDK:

```bash
pip install --no-cache-dir google-cloud-aiplatform
```

## ✅If running Gemini on the VM (outside Docker)  
No installation is needed â€” the SDK is already available.

---

## 🧪 Create and Run a Gemini Test Script

### Step 1: Create a test script  
Create a Python file named `test_gemini.py` with the following content:

```python
from vertexai.preview.generative_models import GenerativeModel
import vertexai

vertexai.init(location="us-central1")
model = GenerativeModel("gemini-2.0-flash-001")
response = model.generate_content("Say hi from inside the OpenROAD container!")
print(response.candidates[0].text)
```

### Step 2: Run the script  
Execute the script using:

```bash
python3 test_gemini.py
```

If everything is set up properly, you should see a response from the Gemini API printed in the terminal  ✅
