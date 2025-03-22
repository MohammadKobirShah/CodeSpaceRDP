

---

# 🐳 **Docker Setup in Codespace** 🚀  

> A **step-by-step guide** to setting up **Docker** in **GitHub Codespaces** and running a **Windows 10 container** effortlessly. 💻  

---

## 📌 **1. Check Available Storage**  

Before setting up Docker, check your **available storage** with:  

```bash
df -h
```  

👉 Choose the partition with the **largest free space**.  

---

## 📂 **2. Create a Directory for Docker Data**  

Run the following command to create the directory where **Docker will store its data**:  

```bash
sudo mkdir -p /tmp/docker-data
```  

✅ This ensures Docker has a dedicated location for storing container files.  

---

## ⚙️ **3. Configure Docker**  

Now, let's **update the Docker configuration** by creating the `/etc/docker/daemon.json` file. You can use **any of the methods below**:  

### 📝 **Method 1 (Using `echo` & `tee`)**  
```bash
echo '{ "data-root": "/tmp/docker-data" }' | sudo tee /etc/docker/daemon.json > /dev/null
```

### 📝 **Method 2 (Using `cat`)**  
```bash
sudo cat <<EOF | sudo tee /etc/docker/daemon.json > /dev/null
{
  "data-root": "/tmp/docker-data"
}
EOF
```

### 📝 **Method 3 (Using `bash -c`)**  
```bash
sudo bash -c 'echo "{ \"data-root\": \"/tmp/docker-data\" }" > /etc/docker/daemon.json'
```

---

## 🔄 **4. Restart Docker**  

After updating the configuration, restart the Docker service:  

```bash
sudo systemctl restart docker
```

---

## ✅ **5. Verify Docker Configuration**  

Check if Docker is correctly configured by running:  

```bash
docker info
```

If the **`data-root`** path is correctly set to `/tmp/docker-data`, you're good to go! 🎉  

---

# 🖥️ **Setting Up the Windows 10 Container**  

## 📌 **1. Create `windows10.yml` File**  

Create a file named `windows10.yml` with the following content:  

```yaml
services:
  windows:
    image: dockurr/windows
    container_name: windows
    environment:
      VERSION: "10"
      USERNAME: ${WINDOWS_USERNAME}   # Use a .env file for sensitive variables
      PASSWORD: ${WINDOWS_PASSWORD}   # Use a .env file for sensitive variables
      RAM_SIZE: "4G"
      CPU_CORES: "4"
    cap_add:
      - NET_ADMIN
    ports:
      - "8006:8006"
      - "3389:3389/tcp"  # Expose only TCP for RDP
    volumes:
      - /tmp/docker-data:/mnt/disk1   # Ensure this directory exists
      - windows-data:/mnt/windows-data # Additional mount if needed
    devices:
      - "/dev/kvm:/dev/kvm"  # Only if you need KVM access
      - "/dev/net/tun:/dev/net/tun"  # Only if you need virtual network interfaces
    stop_grace_period: 2m
    restart: always

volumes:
  windows-data:  # Define the volume if it doesn't exist
```

---

## 🔑 **2. Secure Credentials with a `.env` File**  

Create a **`.env` file** in the same folder as `windows10.yml` to store **sensitive environment variables**:  

```ini
WINDOWS_USERNAME=YourUsername
WINDOWS_PASSWORD=YourPassword
```

🚨 **Important:**  
- **Do not** upload the `.env` file to **public repositories**.  
- Add it to your `.gitignore` file to **keep it secure**.  

---

## 🚀 **3. Start the Windows 10 Container**  

### 🔹 **Run the container**  
```bash
docker-compose -f windows10.yml up -d
```

### 🔹 **Start the container manually (if needed):**  
```bash
docker start windows
```

---

# 📌 **Additional Notes**  

### 🔍 **Check if Your Codespace Supports Virtualization**  
Run this command:  

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

- **`0`** → Your environment **does not support** hardware virtualization.  
- **`1 or more`** → KVM should work correctly.  

### 🛠️ **Alternative: Run Windows with QEMU**  
If **KVM is unavailable**, you may need to use **QEMU** instead of Docker’s built-in virtualization.  

---

# 🎉 **You're All Set!**  

🚀 With these steps, you've successfully configured **Docker** and created a **Windows 10 container** in your **Codespace**!  

💡 If you run into any issues or have questions, feel free to ask! 😃
