# Flask Application Deployment to EC2 with GitHub Actions

This repository contains a Flask application with an automated CI/CD pipeline that deploys to an AWS EC2 instance using GitHub Actions.

## Overview

The pipeline automates the following steps:
1. Installs dependencies on the EC2 instance
2. Deploys the Flask application
3. Configures Nginx as a reverse proxy
4. Sets up Gunicorn as the application server
5. Creates a systemd service for process management

## Prerequisites

Before using this pipeline, ensure you have:

1. An AWS EC2 instance running Ubuntu
2. SSH access configured with a key pair
3. The following GitHub Secrets configured in your repository:
   - `SSH_PRIVATE_KEY`: Private SSH key for EC2 access
   - `KNOWN_HOSTS`: EC2 instance's known hosts entry

## Pipeline Configuration

The workflow is defined in `.github/workflows/deploy.yml` and triggers on:
- Pushes to the `main` branch
- Manual triggers via GitHub Actions UI

### Environment Variables

The pipeline uses these environment variables:
- `EC2_IP`: Your EC2 instance's public IP address
- `SSH_USER`: SSH username (typically 'ubuntu' for AWS Ubuntu instances)
- `APP_DIR`: Deployment directory on EC2 (default: `/var/www/flaskapp`)

## Deployment Steps

1. **Checkout Code**: Checks out your repository
2. **Install SSH Key**: Sets up SSH access to EC2
3. **Install Dependencies**: Installs Python, Nginx, Gunicorn, and other required packages
4. **Deploy Flask App**:
   - Creates application directory
   - Clones/pulls the repository
   - Sets up Python virtual environment
   - Installs Python dependencies
5. **Configure Nginx**:
   - Sets up Nginx as reverse proxy
   - Configures port 80 to forward to Gunicorn
6. **Start Gunicorn Service**:
   - Creates systemd service file
   - Enables and starts the service

## Post-Deployment Verification

After successful deployment:
1. Your application should be accessible at `http://<EC2_IP>`
2. Check service status with:
   ```bash
   sudo systemctl status flaskapp
   sudo systemctl status nginx
