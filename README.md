
# **SSH to Server1**
Open the the terminal and then open two additional tabs (3 Total)

- ssh to server1 VM (Docker is running on this VM)
- Password: 1qazxsw2!QAZXSW@

```bash
ssh student@ip_address_of_server1
```

###  **Deploying Victim and Attacker Containers**:

- Launch a "victim" container within the network:
  ```bash
  docker run --rm -it --name victim ubuntu /bin/bash
  ```

- In the victim container, you might want to run a basic web server or service that the attacker can target. For instance, a basic Python HTTP server:
  ```bash
  echo "Hello World" > index.html
  python3 -m http.server 80
  ```

- Launch an "attacker" container within the same network:
  ```bash
  docker run --rm -it --name attacker kalilinux/kali-rolling /bin/bash
  ```

#  **Capture Traffic Using TCPDUMP**:

- In your host system or another Docker container, start capturing traffic:
  ```bash
  tcpdump -i enp0s3 -w /tmp/capture.pcap
  ```

  This will capture all the traffic on the victim's `enp0s3` interface and save it to `capture.pcap`.

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

###  **Cleanup**:

Don't forget to clean up after your experiments to free up resources:
```bash
docker stop victim attacker
```

### Note:

- Ensure you're conducting these experiments in an isolated, controlled environment to prevent unintended consequences.
- Regularly pull updates for your Docker images (especially Kali) to ensure you have the latest tools at your disposal.

By following this guide, you'll have a basic setup to simulate and capture malicious traffic. As you gain experience, you can introduce more complex scenarios, tools, and analyses into your environment.
