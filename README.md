# Setting up the enviroment - WSL


### 1. Install WSL
Installing WSL 2 (Windows Subsystem for Linux version 2) requires a few specific steps. Before starting, ensure you're running Windows 10 Version 1903 or higher, with Build 18362 or higher. 


#### Using PowerShell:

1. Open PowerShell as Administrator.
2. Run the following command to enable the WSL feature:
   ```powershell
   wsl --install
   ```

   OR if you are on an older version:

   ```powershell
   Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
   ```



### 2. Set WSL 2 as the default version

Open PowerShell and set WSL 2 as the default:
```powershell
wsl --set-default-version 2
```

### 3. Install a Linux distribution

1. Open the Microsoft Store.
2. Search for "Linux" to see a list of available Linux distributions.
3. Select your preferred distribution (e.g., Ubuntu. ).
4. Click "Install".

### 4. Set up your Linux distribution

1. Once the installation completes, open the Start Menu.
2. Search for your installed Linux distribution (e.g., "Ubuntu").
3. Launch the application. This will open a terminal window.
4. On the first run, it will finalize the installation and prompt you to create a user and password. This user will be the default superuser and will have sudo access.

### 5. (Optional) Confirm WSL Version

To ensure that your Linux distribution is running with WSL 2, open PowerShell and run:

```powershell
wsl --list --verbose
```

You should see a "2" under the "VERSION" column for your distribution.

That's it! You should now have WSL 2 up and running on your Windows machine. Remember, you can always switch between WSL 1 and WSL 2 for any given distribution if needed.




# Setting up the enviroment - Docker

Creating a simulated environment to generate and capture malicious traffic can be an invaluable tool for training and testing. Docker provides an isolated environment for such experiments. Here's a step-by-step guide to build this setup:

### 1. **Setup Docker Environment**:

- Install Docker:
  ```bash
  sudo apt update
  sudo apt install docker.io
  sudo systemctl enable --now docker
  sudo usermod -aG docker $USER
  ```

- Ensure Docker is up and running:
  ```bash
  sudo systemctl status docker
  ```

### 2. **Create a Docker Network**:

- Create an isolated bridge network for your containers:
  ```bash
  docker network create --driver=bridge isolated_nw
  ```

### 3. **Deploying Victim and Attacker Containers**:

- Launch a "victim" container within the network:
  ```bash
  docker run --rm -it --network=isolated_nw --name victim ubuntu /bin/bash
  ```

- In the victim container, you might want to run a basic web server or service that the attacker can target. For instance, a basic Python HTTP server:
  ```bash
  apt update && apt install -y python3
  echo "Hello World" > index.html
  python3 -m http.server 80
  ```

- Launch an "attacker" container within the same network:
  ```bash
  docker run --rm -it --network=isolated_nw --name attacker kali /bin/bash
  ```



#  **Capture Traffic Using TCPDUMP**:

- In your host system or another Docker container, start capturing traffic:
  ```bash
  docker exec -it victim tcpdump -i eth0 -w /tmp/capture.pcap
  ```

  This will capture all the traffic on the victim's `eth0` interface and save it to `capture.pcap`.

###  **Simulate Malicious Traffic**:

From the attacker container (Kali Linux is recommended due to its suite of penetration testing tools), you can generate malicious traffic:

- **Port Scanning** with Nmap:
  ```bash
  nmap victim
  ```

- **HTTP-based Attacks** using tools like `curl` or `sqlmap`.

- **Brute Force Attacks** using tools like `hydra`.

- Other tools in Kali Linux such as `metasploit`, `armitage`, etc., can also be used to simulate different kinds of malicious activities.

###  **Querying the Captured Traffic**:

After generating malicious traffic, analyze the `capture.pcap` file:

- Copy the pcap file from the Docker container to the host:
  ```bash
  docker cp victim:/tmp/capture.pcap .
  ```

- Use tools like `tcpdump`, `wireshark`, or `tshark` to analyze the pcap file. 

For example, using `tcpdump` to see HTTP requests:
```bash
tcpdump -r capture.pcap 'tcp port 80'
```

To identify potential malicious activities, you can search for patterns typical of known attacks (e.g., numerous SYN requests, HTTP requests with SQLi patterns, etc.).

### 7. **Cleanup**:

Don't forget to clean up after your experiments to free up resources:
```bash
docker stop victim attacker
docker network rm isolated_nw
```

### Note:

- Ensure you're conducting these experiments in an isolated, controlled environment to prevent unintended consequences.
- Regularly pull updates for your Docker images (especially Kali) to ensure you have the latest tools at your disposal.

By following this guide, you'll have a basic setup to simulate and capture malicious traffic. As you gain experience, you can introduce more complex scenarios, tools, and analyses into your environment.
