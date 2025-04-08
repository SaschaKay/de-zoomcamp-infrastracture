# 🛠️ Setting up a VM and Docker Containers for the DE Zoomcamp course

**_Readme file is in process..._**
<em>
 TODO:
* terraform/terraform_install_docker.bash - fixate versions instead of comments
* add comment about stable IP
</em>

## 📚 Table of Contents
<!-- GitHub-style manual TOC -->
- [Before starting](#before-starting)
- [What if I don’t want to use Google Cloud?](#what-if-i-dont-want-to-use-google-cloud_)
- [How to create a VM in Google Cloud?](#how-to-create-a-vm-in-google-cloud_)
- [How to set up GCP OS Login?](#how-to-set-up-gcp-os-login-optional-but-recommended_)
- [How to build and run containers?](#how-to-build-and-run-containers_)

> 📝 Check <mark >de-zoomcamp-infrastracture/docker/README.md</mark> for containers descriptions and SSH tunneling how-to.

## 🚦 Before starting

You should already have:

- An **active Google Cloud account**.
- A **configured SSH connection** to your GC project with permissions to create instances(service account with Compute Admin and Storage Admin roles).
- **Terraform(v1.11.3) installed** on your local machine.

> If you're missing any of this, check the official [Zoomcamp repository](https://github.com/DataTalksClub/data-engineering-zoomcamp/tree/main/01-docker-terraform#movie_camera-gcp-cloud-vm)


### ❓ What if I don`t want to use Google Cloud? 

You can still follow along, but you will need to adjust things manually:

- Configure a VM on another cloud manually.  
  For compatibility check the same VM image shown in <mark>terraform/terraform_gcs/variables.tf</mark> and find matching images via [Ubuntu Cloud Image Finder](https://cloud-images.ubuntu.com/locator/)

- Or run everything on any other image and _deal with the consequences_   
  (Check docker version used in `<mark>terraform/terraform_install_docker.bash</mark>`)


## 🛠️ How to create a VM in Google Cloud?

1. Copy this repository to a machine with a configured SSH connection to your Google Cloud
2. Change the key file in `de-zoomcamp-infrastracture/terraform/terraform_gcs/keys/` (in this folder, you can see an _example_ of how this file looks).
3. Create a Google Access Token with `/terraform/terraform_gcs/keys/get_access_key.bash`
   
   > :warning: DO NOT PUSH YOUR ACTUAL KEYS TO YOUR GIT REPO
2. Apply it in `de-zoomcamp-infrastracture/terraform/terraform_gcs/` to create a VM

````bash
   terraform init
   terraform apply
 ````


  
## 🔐 How to set up GCP OS Login? (optional, but recommended)

OS Login allows secure, reusable SSH access without rotating temporary keys.
1. Create SSH key on your local machine (locally, e.g., in WSL)
   
````bash
   ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "your_email@example.com"
   ssh-keygen -f ~/.ssh/known_hosts -R "YOUR_VM_IP"
````

3. Enabled OS Login in Google Cloud Console
  Via GCP UI, go to Compute Engine → Metadata -> add Custom metadata
     Key: enable-oslogin
     Value: TRUE
2. Upload public key to OS Login:
    1. Go to the OS Login SSH Keys page
    2. Paste contents of id_ed25519.pub
    3. Save


   >  :memo: This will create a new user with the name being your email address with all special symbols replaced by '_' _(underscore)_ (for example, your_email_gmail_com)
   
   
### Some optional steps for different situations

#### 🔐 If you want to access files created under a previous user:
    
````bash
   sudo chown -R $USER:YOUR_OLD_USER_NAME /home/YOUR_OLD_USER_NAME
``````
    
#### 🔐 If you want to have the same SSH connections as a previous user:

````bash
   sudo cp -r /home/PRECIOUS_USER/.ssh ~
   sudo chown -R $USER:$USER ~/.ssh
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/*
``````

#### 🔐 If you want to have full sudo access to your new user

1. In GCP UI go IAM & Admin → IAM
2. Find your email
2. Assign role: Compute OS Admin Login (grants sudo rights via OS Login)

    
#### 💻 If you work from WSL, but want to use VS Code or another IDE

1. In WSL:
   
````bash
   cp ~/.ssh/id_ed25519* /mnt/c/Users/YOUR_WINDOWS_USER/.ssh/
````
3. In PowerShell:
   
````powershell
   icacls "C:\Users\YOUR_WINDOWS_USER\.ssh\id_ed25519" /inheritance:r /grant:r "%USERNAME%:R"
   (Get-Content "$env:USERPROFILE\.ssh\known_hosts") -notmatch "YOUR\.IP\.WITH\.BACKSLASHES" | Set-Content "$env:USERPROFILE\.ssh\known_hosts"
````
      
#### 🔁 If you want to create a VM name alias for easier use

Add in `~/.ssh/config`

````bash
   Host YOUR_VM_ALIAS
      HostName YOUR_VM_PUBLUC_IP
      User YOUR_OS_LOGIN_USER
      IdentityFile ~/.ssh/id_ed25519
````

        
#### 🐳 If you want to use Docker without sudo

On your GCP VM:

````bash
   sudo usermod -aG docker $USER
   newgrp docker  # or logout/login
````


## 🧱 How to build and run containers?

1. Install Docker and Docker-Compose via `de-zoomcamp-infrastracture/terraform/terraform_install_docker.bash`
2. Create `.env` file in `de-zoomcamp-infrastracture/docker/`, copy `.env.txt` contents in your `.env` file
3. Follow instructions in the comments in `.env.txt`
2. In `de-zoomcamp-infrastracture/docker/` run
   
````bash
   sudo docker compose build
   sudo docker compose up
````
> 📝 Check `de-zoomcamp-infrastracture/docker/README.md` for
>  - containers descriptions
>  - dealing with Airflow dependencies how-to
>  - SSH tunneling how-to

