# Introduction

If you have a home server, which doesn't have a public IP, you can use services like `Cloudflare Tunnels` and `Access Applications`, part of `Cloudflare Zero Trust`, to expose services to the web and access your server remotely through SSH.

There are many guides online for setting up `Cloudflare Tunnels` and creating a `Zero Trust Access Application` to expose SSH to a subdomain of your choosing.

> [!NOTE] Note
> In order to use Cloudflare Tunnels you need to own a domain, which you then manage on Cloudflare.

 Given that SSH can't be exposed like other normal HTTP services in a `Cloudflare Tunnel`, the `Zero Trust Access Application` is necessary to our setup.

This guide assumes that you have already created a `Cloudflare Tunnel` on your home server and the `Zero Trust Access Application` which are necessary, if you are to remotely access your home server.

While there are [many methods](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/use-cases/ssh/) to then SSH into that server, we will be using [client-side cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/use-cases/ssh/ssh-cloudflared-authentication/) in this case. 

This method requires the usage of a `ProxyCommand` in your `.ssh/config` file. You can find the exact configuration I use at the section [[#SSH Configuration]]

> [!NOTE] SSH Zero Trust
> Please note that your `Zero Trust Access Application` must have the policy `Bypass Everyone`. 
> 
> Otherwise it will require you to login when you start a session, which will turn the automated process into a manual one. This change will make it impossible to visit it on the web however, which is a nice feature that the `Access Application` offers - so keep that in mind.


## The Main Challenge

It's easy to connect to your home server, behind the `Cloudflare Zero Trust` network, when you have full access to the `.ssh/config` - however that is not always the case. 

In this article we'll explore how to proxy into a `cloudflared` SSH session - in order to be able to access your home server within Coolify.

Given that Coolify runs inside a Docker container, it doesn't have access to your local SSH config or the `cloudflared` `ProxyCommand`. This can make it difficult to establish a connection between your Coolify instance and your home server.

## The Solution

We'll use a combination of SSH port forwarding and Docker networking to overcome this challenge. In this guide, I'll walk you through all the steps required to setup the connection between Coolify and your home server exposed through Cloudflare.


# Process Walkthrough

## Key Requirements for Cloudflare Tunnels and Coolify Setup

### Hardware and Infrastructure

1. A VPS (Virtual Private Server) running Coolify
2. A home server or remote server you want to add to Coolify
3. Stable internet connection on both ends

### Software and Services

1. Coolify installed and running on your Remove VPS
2. Working Cloudflare Zero Trust Setup - with  `Bypass Everyone` policy 
	-  You should be able to access your Home Server through SSH after having configured it with `Zero Trust Access Application` and having added 
	- SSH server running on your home server

### Network Configuration

1. Port 22 (SSH) exposed through Cloudflare Tunnel on your home/remote server
2. Firewall rules allowing SSH connections on both servers

### SSH Configuration

1. SSH key pair for authentication
2. `.ssh/config` file on your VPS configured for Cloudflare access:

## Steps:
### 1. Setup `.ssh/config` on your Remote VPS (Coolify Host) 


### 5. Startup Script
We have to insert this line in our Remote VPS's startup script.

```bash
ssh -L 222:localhost:22 ssh.enesbala.com
```

I am using Debian on my system, so I initially ran:
\
```bash
sudo nano /etc/rc.local_
```

### 1. Set Up SSH Port Forwarding

First, we need to set up SSH port forwarding on your VPS (where Coolify is running). This will create a bridge between your VPS and your home server.

```bash
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

```bash
PermitRootLogin yes
PasswordAuthentication no
ChallengeResponseAuthentication no
```

### 4. Verify the Connection

To verify that everything is working correctly, you can SSH into your Coolify container and try to connect to your home server:

```bash
# Try to co
docker exec -it coolify bash

ssh host.docker.internal -p 222
```

If this works, Coolify should be able to connect to your home server as well.

## Key Takeaways

1. Use SSH port forwarding to create a bridge between your VPS and home server.
2. Configure Coolify to use `host.docker.internal` as the hostname and `222` as the port.
3. Ensure SSH keys are properly set up on both ends.
4. Disable password authentication for better security.

## Maintaining the Connection

Remember that this setup will need to be re-established every time your VPS reboots. To make this process automatic, consider creating a startup script that runs the SSH port forwarding command.

## Conclusion

Adding a home server exposed through Cloudflare Tunnels to your Coolify setup can be a bit challenging, but it's definitely achievable. This setup allows you to leverage the security benefits of Cloudflare Tunnels while still being able to manage your home server through Coolify.

If you're having trouble, check the official documentation, or reach out to the Coolify community on Discord, they have great support.