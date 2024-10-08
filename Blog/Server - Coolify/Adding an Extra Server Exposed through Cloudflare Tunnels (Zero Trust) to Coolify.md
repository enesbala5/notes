/tableof

# Introduction

If you use Coolify and also own a home server, you might find yourself in a situation like this - you rent a Remote VPS (eg. Hetzner), which is running a Coolify instance. You may have noticed the `Servers` feature in your Coolify instance, and thought about connecting your existing home server, so that it helps serve requests to your user and speeds up the build process, etc..

However, your home server may not have a public IP - in this case you can use services like `Cloudflare Tunnels` and `Access Applications`, part of `Cloudflare Zero Trust`, to expose services to the web and be able to access your server remotely through SSH.

> [!caution] Cloudflare Tunnel Requirement
> In order to use Cloudflare Tunnels you need to own a domain, which you then manage on Cloudflare.

There are many guides online for setting up `Cloudflare Tunnels` and creating a `Zero Trust Access Application` to expose SSH to a subdomain of your choosing - so I will not explain the steps in this article.

This setup works great in most cases, and latency should not be an issue either, with Cloudflare's Technology it's mostly mitigated. In our usecase, some workarounds will be needed to make everything work (specifically Coolify), but they will all be detailed in the [[#Process Walkthrough]].

Given that SSH can't be exposed like other normal HTTP services in a `Cloudflare Tunnel`, the `Zero Trust Access Application` is necessary to setup also.

> [!NOTE] SSH Zero Trust
> Please note that your `Zero Trust Access Application` must have the policy `Bypass Everyone`. 
> 
> Otherwise it will require you to login when you start a session, which will turn the automated process into a manual one. This change will make it impossible to visit it on the web however, which is a nice feature that the `Access Application` offers - so keep that in mind.

This guide assumes that you have already created a `Cloudflare Tunnel` on your home server and the `Zero Trust Access Application` which are necessary to remotely access your home server.

While there are [many methods](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/use-cases/ssh/) to then SSH into your home server, after setting up `Zero Trust`- we will be using [client-side cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/use-cases/ssh/ssh-cloudflared-authentication/). 

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
	- `cloudflared` should also be installed
2. A home server you want to add to Coolify
3. Working `Cloudflare Zero Trust Access Application` providing SSH Access to Home Server - 
	- Policy has to be  `Bypass Everyone` 
	- Firewall rules allowing SSH connections on both servers (Port 22 should not be blocked)


## 1. Initial SSH Setup / Connection - Remote VPS -> Home Server

The goal is to be able to connect to your Home Server from the Remote VPS, and complete all the setup necessary to have Coolify connect successfully as well.

### 1.1 Setup `.ssh/config` on your Remote VPS (Coolify Host) 

You have to generate an SSH key pair for authentication. To create it on your local device, it's enough to run  run `ssh-keygen` in your CLI. This will create an `RSA` key by default. In this case, we'll use an `ed25519` key.

```bash
ssh-keygen -t ed25519
```

Specify the key location and passphrase - you will need to access these files in the following steps.

### 1.2 Authorize Access to the Home Server

> Ensure that the SSH key Coolify is using is added to your home server's **root user**'s `.ssh/authorized_keys` file.

We have to append the contents of the Public Key (.pub) you generated, to `/root/.ssh/authorized_keys`  - make sure to use the Public not Private Key - you can tell by the .pub extension. The file should then look something like this:

```
sh-ed25519 AAAAC6NfaC2lFIH1NTE5AAAAIOy4xaLXOuGa4bAn/8rRF+Use/GvHWHX4pC8HPD4rkGf root@coolify
```

Copy this key and append it to the home server's `authorized_keys` file. You can also use tools like `ssh-copy-id` - if you are familiar - otherwise copy the keys manually, as this will require a password which you may unnecessarily have to generate.

```
ssh-copy-id -i ~/.ssh/mykey root@ssh.[YOUR DOMAIN].com
```

Also, you should consider disabling password authentication on your home server for better security and to avoid some issues you might otherwise face on Coolify. You can usually do this by updating the following settings through:

```bash
vim /etc/ssh/sshd_config
```

> You can use any editor you want here, not necessarily `vim`

And set the following settings to these values:

```
PermitRootLogin yes
PasswordAuthentication no
ChallengeResponseAuthentication no
```

### 1.3 Restart the `SSH Service` on your Home Server:

> This is for for **Debian / Ubuntu Servers** - if you are using another Linux distro, these instructions will not work for you.

``` bash
/etc/init.d/ssh restart
```

OR

```bash
sudo service ssh restart  
```

If you are using **systemd**, use the `systemctl` command:  

```bash
sudo systemctl restart ssh
```


### 1.4 Add the Private Key to Coolify

You can do this by following these steps,
1. Go to `Coolify Sidebar` > `Keys & Tokens`
2. Click the `Add` Button
3. Copy the contents of the Private Key you generated to the `Private Key` Input Field / Text Area
4. In the `Name` field, I  recommend you use the same name that you gave to your key *(Optional)* 
5. Finally hit `Continue`


### 1.5 Update the .ssh/config on the Remote VPS

Finally, you should update the .ssh/config on your Remote VPS to include an entry like the one pictured below. Make sure to use your own information for the domain and SSH Key.


> [!info] Make sure `cloudflared` is installed
> Please install `cloudflared` in your Remote VPS


```
Host ssh.[YOUR DOMAIN].com
	ProxyCommand cloudflared access ssh --hostname %h
	User root
	IdentityFile ~/.ssh/[INSERT YOUR SSH KEY NAME (Not .pub)]
	IdentitiesOnly yes
```

You should now be able to connect to your home server from your Remote VPS. Try to do it like this:

```bash
ssh root@ssh.[YOUR DOMAIN].com
```

## 2. Set Up SSH Tunnel

> Goal: Create a SSH proxy for the connection of VPS -> Home Server
> *-> We want to be able to use port 222 on localhost as a proxy for the main connection*

We need to set up SSH port forwarding on your VPS (where Coolify is running). The first step is to connect to your home server from the VPS like this:

Replace `ssh.[YOUR DOMAIN].com` with the hostname you use to connect to your home server through `Cloudflare Tunnels` / `Zero Trust`.

```bash
ssh -L 0.0.0.0:222:localhost:22 ssh.[YOUR DOMAIN].com
```

> [!warning] Warning!
> Make sure to keep this connection active while proceeding with the next steps.
> We will add a `Startup Script` later, which will replace the `SSH Tunnel` provided here

You should now be able to connect to your home server, if you have configured the .ssh/config correctly. If you home server is requiring a password to login, then I make sure that you have completed every step listed in [[#Setup `.ssh/config` on your Remote VPS (Coolify Host)]] - notably the `IdentityFile` should be set correctly at .ssh/config in your Remote VPS.

## 3. Configure Coolify

### Create the Server Entry

Now that we have port forwarding set up, we need to configure Coolify to use this new connection:

1. In your Coolify instance at the Remote VPS, go to **"Add Server"**
2. For the `IP Address/Domain Field`, use `host.docker.internal`
3. Set the `Port` to `222`
4. For the field `User`, use the appropriate username  `root` 
5. Select the Private Key we created and added to Coolify before
	-  More info here: [[#1.3 Add the Private Key to Coolify]]

>Note: It's possible to use alternate usernames besides root but that didn't work for me personally and created issues with the `sudoers` file

> [!info] You may get the issue "Hostname / Domain already in use"
> I went ahead and ignored it, everything worked fine. If anyone has a better solution, please let me know in the comment section. 

## 4. Verify the Connection

To verify that everything is working correctly, you can SSH into your Coolify container and try to connect to your home server:

```bash
# Try to co
docker exec -it coolify bash

ssh host.docker.internal -p 222
```

If this works, Coolify should be able to connect to your home server as well.

## 5. Startup Script

We have to insert this line in our Remote VPS's startup script.

```bash
ssh -NTC -o ServerAliveInterval=60 -o ExitOnForwardFailure=yes -L 0.0.0.0:222:localhost:22 root@ssh.[YOUR DOMAIN].com
```

I am using Ubuntu 24.04 on my Remote VPS, so I will create a `systemd service` to run the startup script.

```bash
sudo vim /etc/systemd/system/ssh-tunnel-persistent.service
```

Update the file to look like this - remember to update the `[YOUR DOMAIN]` placeholder

```bash
[Unit]
Description=Persistent SSH Tunnel (For Coolify - cloudflared Proxy)
After=network.target

[Service]
Restart=on-failure
RestartSec=5
ExecStart=ssh -NTC -o ServerAliveInterval=60 -o ExitOnForwardFailure=yes -L 0.0.0.0:222:localhost:22 root@ssh.[YOUR DOMAIN].com

[Install]
WantedBy=multi-user.target
```

Close the initial connection you created earlier at [[#2. Set Up SSH Tunnel]] - then enable and start the service by running these commands.

```bash
sudo systemctl daemon-reload
sudo systemctl enable ssh-tunnel-persistent.service
sudo systemctl start ssh-tunnel-persistent.service
```

After this setup, the script should be executed once your computer boots up. Reboot the system to test whether it is working correctly. If things went well now your Coolify should be able to connect to the server and validate it.


# Conclusion

Adding a home server exposed through `Cloudflare Tunnels` to your Coolify instance can be a bit challenging, but it's definitely achievable. This setup allows you to leverage the security benefits of Cloudflare Tunnels while still being able to connect your home server with a remote Coolify instance.

> [!info] Credits
> I want to thank **Darren** (@frostfelll) - a Community Expert from Coolify's Discord
> 
> He was very helpful and proposed the SSH tunnel solution to me. Couldn't have prepared this article without some of his tips.

If you face any issues, check the [Coolify's Docs](https://coolify.io/docs), or reach out to the Coolify community on their Discord server, they are very helpful.

- Is this a platform where I, as an individual or business, go to sell products, and the platform offers me the ability to integrate with social media (e.g., TikTok, YouTube, Instagram)?
- Will a site builder be necessary? Does the individual have to be able to personalize their page, not in terms of colors and branding, but by adding blocks to the page (e.g., different sections)?
- Does the platform allow connecting payment systems and shipping options that customers prefer?
- Does the platform have to allow the individual / business to connect their own payment systems? How many integrations have to be implemented?
- Regarding shipment, tax calculation and inventory control - are you looking to integrate with an existing solution, or build new solutions from scratch?
- Regarding social media and their dedicated sales channels - which platforms will have to be integrated to the platform? Should the individual / business have tutorials available, guiding them on integrating the two.  