# Introduction

If you have a home server, which doesn't have a public IP, you can use services like Cloudflare Zero Trust & Tunnels to expose services and SSH to the web. 

Here you can find a great guide on setting up Cloudflare Zero Trust to expose your home server's SSH. 

You might find yourself wanting to add a home server, exposed through Cloudflare Tunnels (Zero Trust) to your existing Coolify setup. This can be a bit tricky, especially when dealing with SSH configurations and Docker containers. In this guide, I'll walk you through the process of connecting a server exposed through Cloudflare Tunnels to your Coolify instance.

## The Challenge

The main challenge here is that Coolify runs inside a Docker container, which means it doesn't have access to your local SSH config or the Cloudflare proxy command. This can make it difficult to establish a connection between your Coolify instance and your home server.

## The Solution

We'll use a combination of SSH port forwarding and Docker networking to overcome this challenge. Here's a step-by-step guide to get you up and running.


# Process Walkthrough

## Key Requirements for Cloudflare Tunnels and Coolify Setup

### Hardware and Infrastructure

1. A VPS (Virtual Private Server) running Coolify
2. A home server or remote server you want to add to Coolify
3. Stable internet connection on both ends

### Software and Services

1. Coolify installed and running on your VPS
2. Cloudflare account with Zero Trust enabled
3. Cloudflare Tunnel set up for your home/remote server
4. SSH server running on your home/remote server

### Network Configuration

1. Port 22 (SSH) exposed through Cloudflare Tunnel on your home/remote server
2. Firewall rules allowing SSH connections on both servers

### SSH Configuration

1. SSH key pair for authentication
2. `.ssh/config` file on your VPS configured for Cloudflare access:

## Steps:
### 1. Set Up SSH Port Forwarding

First, we need to set up SSH port forwarding on your VPS (where Coolify is running). This will create a bridge between your VPS and your home server.

```
ssh -L 0.0.0.0:222:localhost:22 ssh.yourdomain.com
```

Replace `ssh.yourdomain.com` with the hostname you use to connect to your home server through Cloudflare Tunnels.

### 2. Configure Coolify

Now that we have port forwarding set up, we need to configure Coolify to use this new connection:

1. In Coolify, go to "Add Server"
2. For the hostname, enter `host.docker.internal`
3. Set the port to `222`
4. Use the appropriate username  `root` 
	1. Note: It's possible to use alternate usernames but that didn't work for me personally
5. Make sure you've added the correct SSH key

### 3. Ensure the SSH key Coolify is using is added to your home server's `authorized_keys` file.

Make sure that the public key of the key you are using in your Coolify Server Connection is also in the `/root/.ssh/authorized_keys` file.

You can do this by going to 

Consider disabling password authentication on your home server for better security and to avoid some issues you might face otherwise on Coolify. You can usually do this by setting the following:

```
PermitRootLogin yes
PasswordAuthentication no
ChallengeResponseAuthentication no
```

