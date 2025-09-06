🚀 CI/CD Deployment to AWS EC2 using GitHub Actions
===================================================

This repository is configured to **automatically deploy code from GitHub to an AWS EC2 instance** using **GitHub Actions** and **SSH (RSA key authentication)**.

📂 Project Setup
----------------

1.  **Code Repository**
    
    *   All source code is stored in this GitHub repository.
        
2.  **Deployment Target**
    
    *   An **Amazon EC2 instance** running Ubuntu (or another Linux distribution).
        
    *   The web server (e.g., **Nginx**, **Apache**, or Node.js) must be installed and configured to serve the application.
  
    *   I am using Apache to host this.
        

🔑 Prerequisites
----------------

1.  Example (Ubuntu):sudo apt update && sudo apt upgrade -ysudo apt install git nginx -y
    
    *   Create an EC2 instance with SSH access.
        
    *   Install necessary packages (Git, Node.js, Apache, etc. depending on your stack).
        
2.  ssh-keygen -t rsa -b 4096 -C "your\_email@example.com"
    
    *   ssh-copy-id -i ~/.ssh/id\_rsa.pub ubuntu@
        
    *   Keep the **private key (id\_rsa)** safe.
        
3.  **Add Secrets to GitHub**Go to **GitHub → Settings → Secrets and variables → Actions → New repository secret**:
    
    *   EC2\_HOST → Public IP or domain of your EC2 instance
        
    *   EC2\_USER → SSH username (e.g., ubuntu)
        
    *   EC2\_KEY → Contents of your private RSA key (id\_rsa)
        

⚙️ GitHub Actions Workflow
--------------------------

The workflow file is located in:
```
 .github/workflows/deploy.yml
```

### Example Workflow

```
name: Deploy to EC2

on:
  push:
    branches:
      - main   # Change if deploying from another branch

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Setup SSH
        uses: webfactory/ssh-agent@v0.7.0
        with:
          ssh-private-key: ${{ secrets.EC2_KEY }}

      - name: Deploy to EC2
        run: |
          ssh -o StrictHostKeyChecking=no ${{ secrets.EC2_USER }}@${{ secrets.EC2_HOST }} << 'EOF'
            cd /var/www/myapp || exit
            git pull origin main
            npm install --production   # or pip install -r requirements.txt
            pm2 restart all            # or systemctl restart nginx
          EOF
```
NOTE: The above workflow is a example it may change on the scenario basis

🚀 Deployment Flow
------------------

1.  Push code to the main branch of GitHub.
    
2.  GitHub Actions triggers the workflow.
    
3.  The workflow establishes an **SSH connection** to the EC2 instance using the stored RSA private key.
    
4.  Code is pulled, dependencies are installed, and services are restarted.
    

🛠 Troubleshooting
------------------

*   Ensure correct file permissions for your private key (chmod 600 id\_rsa).
    
*   Make sure the EC2 security group allows inbound **port 22 (SSH)** and **HTTP/HTTPS**.
    
*   Check GitHub Actions logs for deployment errors.
