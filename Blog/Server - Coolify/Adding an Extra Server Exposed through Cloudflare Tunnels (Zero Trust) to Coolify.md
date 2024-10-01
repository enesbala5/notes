# Introduction

If you use Coolify and also own a home server, you might find yourself in a similar situation to this - you rent a Remote VPS (from Hetzner in my case), which is running a Coolify instance. You may have noticed the `Servers` feature in your Coolify instance, and thought about connecting your existing home server, so that it helps serve requests to your user. After all, what's the point of having a home server if you're not going to use it for such cases.

However, as is the case for many, your home server may not have a public IP - in this case you can use services like `Cloudflare Tunnels` and `Access Applications`, part of `Cloudflare Zero Trust`, to expose services to the web and be able to access your server remotely through SSH.

> [!NOTE] Note
> In order to use Cloudflare Tunnels you need to own a domain, which you then manage on Cloudflare.

There are many guides online for setting up `Cloudflare Tunnels` and creating a `Zero Trust Access Application` to expose SSH to a subdomain of your choosing - so I will not explain the steps in this article.

This setup works great in most cases, and latency should not be an issue either, with Cloudflare's Technology it's mostly mitigated. In our usecase, some workarounds will be needed to make everything work, but they will all be detailed in the [[#Process Walkthrough]].

Given that SSH can't be exposed like other normal HTTP services in a `Cloudflare Tunnel`, the `Zero Trust Access Application` is necessary to setup also.

> [!NOTE] SSH Zero Trust
> Please note that your `Zero Trust Access Application` must have the policy `Bypass Everyone`. 
> 
> Otherwise it will require you to login when you start a session, which will turn the automated process into a manual one. This change will make it impossible to visit it on the web however, which is a nice feature that the `Access Application` offers - so keep that in mind.

This guide assumes that you have already created a `Cloudflare Tunnel` on your home server and the `Zero Trust Access Application` which are necessary to remotely access your home server.

While there are [many methods](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/use-cases/ssh/) to then SSH into your home server, after setting up `Zero Trust`- we will be using [client-side cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/use-cases/ssh/ssh-cloudflared-authentication/) in this case. 

This method requires the usage of a `ProxyCommand` in your `.ssh/config` file. You can find the exact configuration I use at the section [[#SSH Configuration]]. 


# The Main Challenge

It's easy to connect to your home server, behind the `Cloudflare Zero Trust` network, when you have full access to the `.ssh/config` - however that is not always the case. 

In this article we'll explore how to proxy into a `cloudflared` SSH session - in order to be able to access your home server within Coolify.

Given that Coolify runs inside a Docker container, it doesn't have access to your local SSH config or the `cloudflared` `ProxyCommand`. This can make it difficult to establish a connection between your Coolify instance and your home server.

## The Solution

We'll use a combination of SSH port forwarding and Docker networking to overcome this challenge. In this guide, I'll walk you through all the steps required to setup the connection between Coolify and your home server exposed through Cloudflare.


# Process Walkthrough

## Key Requirements

1. Coolify installed and running on your Remote VPS (Virtual Private Server)
2. A home server you want to add to Coolify
3. Working Cloudflare Zero Trust Setup on Home Server - with  `Bypass Everyone` policy 
	- Port 22 (SSH) exposed through Cloudflare Tunnel on your home/remote server
	- Firewall rules allowing SSH connections on both servers

> [!NOTE] Important Note
> You should be able to access your Home Server through SSH after having configured it with `Zero Trust Access Application` and having added SSH server running on your home server

## Steps:

2. Configure Coolify to use `host.docker.internal` as the hostname and `222` as the port.
3. Ensure SSH keys are properly set up on both ends.
4. Disable password authentication for better security.


### SSH Port Forwarding Setup
We will use SSH port forwarding to create a bridge between your VPS and home server.
#### 1. Setup `.ssh/config` on your Remote VPS (Coolify Host) 

Firstly you have to generate an SSH key pair for authentication. To create it on your local device, it's enough to run  run `ssh-keygen` in your CLI. This will create an `RSA` key by default. In this case, we'll use an `ed25519` key.


```bash
ssh-keygen -t ed25519
```

Specify the key location and passphrase (recommended). Then, you should connect to your home server, and update `/root/.ssh/authorized_keys` to include your new key - make sure to use the Public Key (.pub) key.

You can copy the contents of `[SSH_KEY].pub` and append them to the `authorized_keys` file. The file should then look something like this:

```
sh-ed25519 AAAAC6NfaC2lFIH1NTE5AAAAIOy4xaLXOuGa4bAn/8rRF+Use/GvHWHX4pC8HPD4rkGf root@coolify
```

Finally, update the .ssh/config on your Remote VPS to include an entry like the one pictured below. Make sure to use your own information for the domain and SSH Key.

```
Host ssh.[YOUR DOMAIN].com
	ProxyCommand cloudflared access ssh --hostname %h
	User e
	IdentityFile ~/.ssh/[INSERT YOUR SSH KEY NAME (Not .pub)]
	IdentitiesOnly yes
```

You should now be able to connect to your home server from your Remo

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


### 5. Startup Script
We have to insert this line in our Remote VPS's startup script.

```bash
ssh -L 222:localhost:22 ssh.enesbala.com
```

I am using Debian on my system, so I initially ran:

```bash
sudo nano /etc/rc.local
```

And updated the file to look like this - remember to place the startup script before `exit 0`.

```bash
!/bin/sh -e

# Start Script
echo date -R >> /var/log/sys-start.log
ssh -L 222:localhost:22 ssh.enesbala.com

exit 0

```

Then you have to run to grant it execution permissions.

```bash
chmod +x /etc/rc.local
```

Start rc.local service

```bash
systemctl enable rc-local  
systemctl start rc-local.service
```

Finally, you can reboot the system to test whether it is working.


## Maintaining the Connection

Remember that this setup will need to be re-established every time your VPS reboots. To make this process automatic, consider creating a startup script that runs the SSH port forwarding command.

## Conclusion

Adding a home server exposed through Cloudflare Tunnels to your Coolify setup can be a bit challenging, but it's definitely achievable. This setup allows you to leverage the security benefits of Cloudflare Tunnels while still being able to manage your home server through Coolify.

If you're having trouble, check the official documentation, or reach out to the Coolify community on Discord, they have great support.
