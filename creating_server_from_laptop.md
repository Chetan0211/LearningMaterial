# How to create a remote server from your old computer.

In here we are going to learn how to make your old computer act as a server and what are the process you can do to connect it from remotely anywhere.

Normally we need to be having a public ip address to to connect to our system remotely(Here what i meant is to use our system as an server).

If you have that well and good, we can just use that public IP and we can do port forward in router to make server listen from outside of our local network.

But the problem is in most of the houses we are given **CGNAT(Carrier Grage NAT)** which means multiple users sharing same public IP address. Because of that from that public IP we were given a private IP which we can't use for communicating withpublic network.

To solve this issue we have few ways. One of the straight forward way is to ask your ISP for  an public IP. But in most of the cases it is not possible.

So we can try accessing lifetime free servers from a cloud provider and we can able to use those virtual machines which has public ip address to do reverse SSH to our systems. Now because of that the  network can carry from that Virtual Machine to your personal system which acts as remote server. Yes, it has some latency compared to directly hosted from virtual server. But we don't need to spend money for all the infro as we already have our old system which can do our needs like creating our own cloud.

So, in this we will learn how to do reverse SSH to connect with the cloud VM and how can i connect automatically by just spinning up my system.

Here I'll be using terms remote server(Old system which acts as server), cloud machine(Our cloud server).

### Set up Cloud Machine
For this experiment I'm using Oracle cloud platform because it five me lifetime free compute server which is enough for my purpose. If you fin any alternative you can use those cloud platforms for the setup. Mostly the setup will be same for all the cloud platforms.

So now you need to create a new compute instance which has a free tier. Once you create it, it will provide you with the public IP address. 

Now you need to open ports for cloud machine. You can do that by going inside the instance. Here you will find Networking, go inside and click on the subnet link(it will be against subnet row. Ex: subnet-20241122-1529). Once you are inside the subnet you will find Security taband inside click on Default Security List for vnc.

Once you are inside of that you can find security rules where you can open ports in Ingress Rules.
In here consider you need to open ports ranging 54321-65500 (which is huge, which we don't normally do but just foe this example), you need to click on `Add Ingress Rules` button and in there you can add the destination port range `54321-65500` and you can also change the IP protocal if you need and then click on `Add Ingress Rule`. That's it now those ports are open and will listen from outside.

Now connect to the cloud machine using ssh(explaining connection to ssh is not the scope of this file).

Once connected to ssh you need to make a change in `/etc/ssh/sshd_config`

Open it in an editor like

```shell
nano /etc/ssh/sshd_config
```

Add the below line to the file
```bash
.
.
GatewayPorts yes
.
.
```
and save it.

### Set up Remote Machine

Now get into your remote server and you need to do the perform reverse ssh over here.

access `/etc/ssh/sshd_config`

inside this you have option to give the ssh user rootlogin. You can do that by adding

```bash
PermitRootLogin yes
```
and also change or add 
```bash 
port 22
AllowTcpForwarding yes
GatewayPorts yes
ClientAliveInterval 30
ClientAliveCountMax 2
PasswordAuthentication yes #This is set to No  if we need only ssh authentication
```

To run the reverse SSH open the terminal and type

```bash
ssh -N -R 54321:localhost:22 -R 54322:localhost:80 -i /home/chetan/.ssh/ssh-key-2024-11-22.key {USER_NAME}@{PUBLIC_IP_ADDRESS}
```

Let's explain the code
`-N`: Do not execute a remote command. This is correct and typically desired when you're only using SSH for port forwarding. It keeps the connection open just for the tunnels without trying to run a shell on the remote machine.

`-R 54321:localhost:22`: Sets up Remote Port Forwarding. This means:
Connections made to port 54321 on the cloud machine
will be forwarded through the SSH tunnel
to port 22 (SSH) on localhost relative to your remote server(local machine where you are running the ssh command).
Essentially, this allows the cloud machine to access the SSH server running on your remote server(local machine) via localhost:54321 on the remote end.

`-R 54322:localhost:80`: Sets up another Remote Port Forwarding. Similar to above:
Connections made to port 54322 on the remote server (129.154.46.176)
will be forwarded
to port 80 (HTTP) on localhost relative to your remote server(local machine).
This allows the cloud machine to access a web server running on port 80 on your local machine via localhost:54322 on the remote end.

`-i /home/chetan/.ssh/ssh-key-2024-11-22.key`: Specifies the private key file to use for authentication. (Correct syntax - ensure the file exists and has the correct permissions: chmod 600 /home/chetan/.ssh/ssh-key-2024-11-22.key).

But the problem with this is once the system is turned off and back on or some issue happened and your ssh is disconnected you need to run the ssh command again.

To avoid this problem you can use autossh.

install autossh in your system

```shell
sudo apt install autossh -y
```

After installing you need to add the service file in your system folder to run the code automatically when your system is turned on.

create `reverse-tunnel.service` inside `/etc/systemd/system`

```bash
[Unit]
# --- This section provides metadata and dependency information ---
Description=Persistent Reverse SSH Tunnel
# A human-readable name for the service, shown in status commands.

After=network.target
# Specifies that this service should start *after* the basic networking functionality (`network.target`) is initialized during boot.
# This helps ensure the network is likely available before trying to SSH.
# Note: For services that absolutely require full network connectivity (like external access), `network-online.target` is sometimes preferred, but `network.target` is often sufficient.

[Service]
# --- This section defines how the service is executed ---
User=chetan
# Specifies that the command defined in `ExecStart` should be run as the system user named 'chetan'.
# This is important for security (avoiding root) and accessing the correct user's home directory and SSH keys.

Environment="AUTOSSH_GATETIME=0"
# Sets an environment variable for the autossh process. `AUTOSSH_GATETIME=0` tells autossh not to exit immediately if the very first SSH connection attempt fails. This helps make the tunnel more resilient if the network or remote server is slow to come up initially.

Environment="AUTOSSH_DEBUG=1"
Environment="AUTOSSH_LOGLEVEL=7"
# These lines enable verbose debugging output from autossh (and the underlying ssh command).
# `AUTOSSH_DEBUG=1` enables general debug messages.
# `AUTOSSH_LOGLEVEL=7` sets the autossh logging level to the most verbose (DEBUG).
# This is very useful for troubleshooting connection problems, but you would normally remove or comment these out for regular operation to avoid filling up logs.

Environment="HOME=/home/chetan"
# Explicitly sets the HOME directory environment variable. Ensures SSH and other tools look in the correct place (`/home/chetan/.ssh/`) for configuration files or default keys, even though running as this user usually sets it automatically.

Environment="PATH=/usr/local/bin:/usr/bin:/bin"
# Explicitly sets the system PATH for the executed command. This ensures that executables like `autossh` and `ssh` are found reliably.

ExecStart=/usr/bin/autossh -M 6000 -N -R 54321:localhost:22 -R 54322:localhost:80 -i /home/chetan/.ssh/ssh-key-2024-11-22.key ubuntu@{IP_ADDRESS}
# This is the main command that systemd will execute to start the service.
# `/usr/bin/autossh`: The full path to the autossh executable.
# `-M 6000`: Tells autossh to use local port 6000 (and 6001) for its monitoring connection loop to check if the main SSH session is alive. Ensure port 6000 is not used by other services on chetan's machine.
# `-N`: Tells the underlying SSH process not to execute any remote command, just set up the tunnels.
# `-R 54321:localhost:22`: Sets up a *Remote* tunnel. Connections to port 54321 on the *remote* server (`{IP_ADDRESS}`) will be forwarded back to port 22 (SSH) on the *local* machine (localhost relative to where autossh is running - chetan's machine).
# `-R 54322:localhost:80`: Sets up another *Remote* tunnel. Connections to port 54322 on the *remote* server will be forwarded back to port 80 (HTTP) on the *local* machine.
# `-i /home/chetan/.ssh/ssh-key-2024-11-22.key`: Specifies the exact private SSH key file to use for authentication.
# `ubuntu@{IP_ADDRESS}`: The user (`ubuntu`) and hostname/IP address (`{IP_ADDRESS}`) of the remote server to connect to. You need to replace `{IP_ADDRESS}` with the actual IP address or DNS name.

Restart=always
# Tells systemd to automatically restart the service if the `ExecStart` process ever stops, regardless of the exit code (e.g., if it crashes or the connection is forcibly closed).

RestartSec=10
# Specifies a 10-second delay before systemd attempts to restart the service after it stops. Prevents systemd from trying to restart too rapidly if there's a persistent problem.

StandardOutput=journal
StandardError=journal
# Redirects the standard output (stdout) and standard error (stderr) streams from the `ExecStart` command directly into the systemd journal. You can view these logs using `journalctl -u your-service-name.service`. This is the standard way to handle logging for systemd services.

TimeoutStartSec=0
# Configures how long systemd should wait for the service to signal that it has successfully started up. Setting it to `0` disables the startup timeout. This can be useful for services like autossh that might take a variable amount of time to establish the initial connection and don't necessarily signal readiness in a way systemd expects.

ExecStartPre=/bin/sleep 10
# Specifies a command to run *before* the main `ExecStart` command is executed. Here, it simply pauses the service startup process for 10 seconds. This can sometimes be used as an extra delay to wait for network interfaces or other dependencies to become fully ready, even after `network.target` is reached.

[Install]
# --- This section defines how the service integrates with systemd targets ---
WantedBy=multi-user.target
# Specifies that when this service is "enabled" using `systemctl enable`, a symbolic link should be created that makes it part of the `multi-user.target`. This target is typically reached during a normal boot process (for non-graphical or pre-graphical multi-user operation), ensuring the service starts automatically on boot.
```

Once you create file you need to enable this service so that next time when you turn on the system it automatically runs the command.

```bash
sudo systemctl enable reverse-tunnel.service
```

That's it, now you can access the remote server whereever you want.
