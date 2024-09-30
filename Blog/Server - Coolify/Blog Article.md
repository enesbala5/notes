

# Key Requirements for Cloudflare Tunnels and Coolify Setup
## Hardware and Infrastructure

1. A VPS (Virtual Private Server) running Coolify
2. A home server or remote server you want to add to Coolify
3. Stable internet connection on both ends

## Software and Services

1. Coolify installed and running on your VPS
2. Cloudflare account with Zero Trust enabled
3. Cloudflare Tunnel set up for your home/remote server
4. SSH server running on your home/remote server

## Network Configuration

1. Port 22 (SSH) exposed through Cloudflare Tunnel on your home/remote server
2. Firewall rules allowing SSH connections on both servers

## SSH Configuration

1. SSH key pair for authentication
2. `.ssh/config` file on your VPS configured for Cloudflare access: