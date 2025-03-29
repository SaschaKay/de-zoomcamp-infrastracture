**_In process..._**
<em>
 TODO:
* review git ignore
* push excamples of missing git ignore files with comments
* terraform/terraform_install_docker.bash - fixate versions
* add links to zoomcamp repo with tutorials
* add commands (or better bash files with comments???)
* add descriptions of ports and how to create tunnel
* add descriptions of containers
* add comments about stable ip
  
* add anchors and links
* formatting
</em>

## Before starting
You should already have:
* active Google Cloud account
* configured SSH connection to your Google Cloud account with permissions to create instances **_specify permissions_**
* installed Terraform library on your machine with connection
If you're missing any of this, check **_add link to zoomcamp repo_**


#### What if I don`t want to use Google Cloud? 
Using Terraform configuration for GC from this repo guarantees that the rest of the code will work without any adjustments. But if you don't want to use it, you have several alternatives: 
- Configure a VM in any other cloud yourself. For better comparability, check vm_image in terraform/terraform_gcs/variables.tf and use https://cloud-images.ubuntu.com/locator/ to find the image with the same Ubuntu environment for your cloud.
- Miss this step and use any of your existing machines and _deal with the consequences_. You can check Docker and Docker Compose versions in terraform/terraform_install_docker.bash.

## How to create a VM in GS?
* Copy this repository to your machine with a configured SSH connection to your Google Cloud
* Change keys files in de-zoomcamp-infrastracture/terraform/terraform_gcs/keys/ (in this folder you can see an _example_ of how these files look).
!!!DO NOT PUSH YOUR ACTUAL KAYS TO YOUR GIT REPO!!!
* Apply it in de-zoomcamp-infrastracture/terraform/terraform_gcs/ to create a VM _(specify later)_
  
## How to set up GCP OS Login? (optional)
OS Login allows you to connect to your VM without rotating temporary SSH keys.
- Create SSH key on your local machine (for example, WSL)

      ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "your_email@example.com"
      ssh-keygen -f ~/.ssh/known_hosts -R "your-vm-ip"
  As result you will have 
  Public key: ~/.ssh/id_ed25519.pub
  Private key: ~/.ssh/id_ed25519
- Enabled OS Login in Google Cloud Console
  Via UI:
   Go to Metadata (project level)
   Add custom metadata:
   Key: enable-oslogin
   Value: TRUE
- Upload public key to OS Login
  Go to OS Login SSH Keys page
  Paste contents of id_ed25519.pub
  Save
Note: this will create a new user named by your email with all special symbols replaced by _ (for example, your_email_gmail_com)

#### Optional steps:
- If you want to have full sudo access to your new user
  Via GCP UI:
    Go to IAM & Admin → IAM
    Find your email
    Assign role: Compute OS Admin Login (grants sudo rights via OS Login)

- If you work from WSL, but want to use VS Code or another IDE
  On WSL:

      cp ~/.ssh/id_ed25519* /mnt/c/Users/YOUR_WINDOWS_USER/.ssh/
  Then in PowerShell:
  
      icacls "C:\Users\YOUR_WINDOWS_USER\.ssh\id_ed25519" /inheritance:r /grant:r "%USERNAME%:R"

- If you want to create VM name alias for easier use
  Add in ~/.ssh/config
  
      Host YOUR_VM_DESIERED_NAME
        HostName YOUR_VM_PUBLUC_IP
        User YOUR_OS_LOGIN_USER
        IdentityFile ~/.ssh/id_ed25519
- If you want to use Docker without sudo
  On your GCP VM:

      sudo usermod -aG docker $USER
      newgrp docker  # or logout/login
  
## How to create containers on your host machine?
* install Docker and Docker-Compose with
  bash ./de-zoomcamp-infrastracture/terraform/terraform_install_docker.bash
* In ./de-zoomcamp-infrastracture/docker/ run
        docker compose build

  
