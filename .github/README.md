# GitHub Actions PR Deploy Previews for Serverside Apps

## Overview
This repository enables automatic deployment of preview environments for pull requests using GitHub Actions, Docker Compose, and Traefik.
via [FRP tunnel](https://github.com/cirunlabs/frp-tunnel-action)

## Features
- **Automatic Deployment**: Deploys a preview environment for each pull request labeled with `deploy`.
- **Traefik for Reverse Proxy**: Uses Traefik for HTTPS and automatic Let's Encrypt SSL certificate management.
- **Cloudflare DNS Integration**: Automatically adds DNS records to Cloudflare for preview environments.
- **SSH Access to Runner**: Provides SSH access to the GitHub Actions runner using FRP tunnels.
- **Automatic Cleanup**: Removes deployment when the `deploy` label is removed from the pull request.

## Deployment Workflow
1. When a pull request is labeled `deploy`, GitHub Actions triggers the deployment workflow.
2. The app is deployed using Docker Compose.
3. A DNS record is added via Cloudflare.
4. FRP tunnels are established to enable SSH and https access to the server and app respectively.
5. A comment with the preview URL and SSH instructions is added to the pull request.
6. When the `deploy` label is removed, the deployment is cleaned up automatically.

## How to Use
### Deploy a Preview
1. Create a pull request.
2. Label the PR with `deploy`.
3. GitHub Actions will deploy a preview environment and post a comment with access details.

### Access the Preview
- Visit `https://app-pr-<PR_NUMBER>.iakte.ch`
- SSH into the runner:
  ```bash
  ssh -o 'proxycommand socat - PROXY:ssh-pr-<PR_NUMBER>.iakte.ch:%h:%p,proxyport=5002' runner@ssh-pr-<PR_NUMBER>.iakte.ch
  ```

### Remove a Preview
- Remove the `deploy` label from the pull request.
- The preview environment will be automatically stopped and cleaned up.

## Docker Compose Configuration
The repository uses Docker Compose to manage the services:
- **Traefik**: Handles reverse proxying and SSL termination.
- **httpbin**: A simple HTTP server to verify deployments.

## Environment Variables
| Variable | Description |
|----------|-------------|
| `APP_DNS` | Subdomain for the preview app |
| `SSH_DNS` | Subdomain for SSH access |
| `CLOUDFLARE_DNS_API_TOKEN` | API token for Cloudflare DNS updates |
| `CLOUDFLARE_ZONE_ID` | Cloudflare Zone ID for DNS records |
| `FRP_SERVER` | FRP server address |
| `FRP_TOKEN` | Authentication token for FRP |


## Troubleshooting
- Ensure the `deploy` label is applied to the pull request.
- Check GitHub Actions logs for errors.
- Verify Cloudflare API credentials are correctly set in repository secrets.
- Check FRP server status if SSH access fails.

## License
This project is licensed under the MIT License.

