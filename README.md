
---

# 🔒 **Deploying a Django Application on AWS EC2 with Gunicorn and Nginx (Ubuntu 22.04 LTS)**

This guide provides a detailed walkthrough for deploying a Django application on an **AWS EC2 instance** running **Ubuntu 22.04 LTS**, using **Gunicorn** as the application server and **Nginx** as the reverse proxy.

---

## 🎯 **Technologies Used**  
- **Cloud Provider:** AWS EC2  
- **Instance Type:** t2.medium (2 vCPUs, 4 GB RAM)  
- **Operating System:** Ubuntu 22.04 LTS  
- **Programming Language:** Python (Django Framework)  
- **Web Server:** Nginx  
- **Application Server:** Gunicorn  
- **Database:** SQLite (can be replaced with PostgreSQL/MySQL in production)  
- **Process Manager:** systemd  
- **Firewall Management:** UFW  
- **Version Control:** Git  

---

## 📚 **Table of Contents**  
- [🚀 Launch EC2 Instance](#-launch-ec2-instance)  
- [🛠️ Install Required Packages](#️-install-required-packages)  
- [📂 Clone Project & Set Up Virtual Environment](#-clone-project--set-up-virtual-environment)  
- [🔗 Configure Gunicorn](#-configure-gunicorn)  
- [⚡ Set Up Nginx as Reverse Proxy](#-set-up-nginx-as-reverse-proxy)  
- [🔒 Secure the Application with SSL](#-secure-the-application-with-ssl)  
- [🚀 Final Checks & Production Tips](#-final-checks--production-tips)  

---

## 🚀 **1. Launch EC2 Instance**  
1. Log in to your **AWS Management Console**.  
2. Navigate to **EC2 Dashboard** and click on **Launch Instance**.  
3. Choose the **Ubuntu 22.04 LTS** AMI (Amazon Machine Image).  
4. Select **t2.medium** as the instance type (2 vCPUs, 4 GB RAM).  
5. Configure security groups to allow inbound traffic on:  
   - Port `22` (SSH)  
   - Port `80` (HTTP)  
   - Port `443` (HTTPS)  
6. Launch the instance and download the **.pem key file** for SSH access.  

---

## 🔗 **2. Connect to Your EC2 Instance**  
Open your terminal and SSH into the instance:  
```bash
ssh -i /path/to/your-key.pem ubuntu@your-ec2-public-ip
```

---

## 🛠️ **3. Install Required Packages**  
1. Update system packages and install required dependencies:  
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y python3 python3-pip python3-venv nginx gunicorn git
```

2. Allow firewall rules for SSH, HTTP, and HTTPS:  
```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

---

## 📂 **4. Clone Project & Set Up Virtual Environment**  

1. Create a project directory:  
```bash
sudo mkdir /dj
cd /dj
```
2. Clone your Django project repository:  
```bash
git clone https://github.com/sunilkumar0633/social_media.git
cd social_media
```
3. Set up a virtual environment and install dependencies:  
```bash
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

---

## ⚙️ **5. Run Migrations & Collect Static Files**  
1. Run database migrations:  
```bash
python manage.py migrate
```
2. Collect static files for production:  
```bash
python manage.py collectstatic --noinput
```
3. Test the application:  
```bash
python manage.py runserver 0.0.0.0:8000
```
Check by visiting:  
```
http://your-ec2-public-ip:8000
```

---

## 🔗 **6. Configure Gunicorn**  

1. Install Gunicorn:  
```bash
pip install gunicorn
```
2. Test Gunicorn by running:  
```bash
gunicorn --bind 0.0.0.0:8000 social.wsgi
```
3. Create a systemd service for Gunicorn:  
```bash
sudo vim /etc/systemd/system/gunicorn.service
```
4. Add the following configuration:  
```ini
[Unit]
Description=Gunicorn daemon for Django app
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/dj/social_media
ExecStart=/dj/social_media/venv/bin/gunicorn --workers 3 --bind unix:/dj/social_media/social.sock social.wsgi:application

[Install]
WantedBy=multi-user.target
```
5. Start and enable Gunicorn:  
```bash
sudo systemctl daemon-reload
sudo systemctl enable gunicorn
sudo systemctl start gunicorn
sudo systemctl status gunicorn
```

---

## ⚡ **7. Set Up Nginx as Reverse Proxy**  

1. Create a new Nginx configuration:  
```bash
sudo vim /etc/nginx/sites-available/social_media
```
2. Add the following configuration:  
```nginx
server {
    listen 80;
    server_name your-domain.com;  # Replace with your domain or public IP

    location / {
        include proxy_params;
        proxy_pass http://unix:/dj/social_media/social.sock;
    }

    location /static/ {
        root /dj/social_media;
    }

    location /media/ {
        root /dj/social_media;
    }
}
```
3. Create a symbolic link to enable the configuration:  
```bash
sudo ln -s /etc/nginx/sites-available/social_media /etc/nginx/sites-enabled/
```
4. Test and restart Nginx:  
```bash
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl enable nginx
```

---

## 🔒 **8. Secure the Application with SSL**  

1. Install Certbot for SSL configuration:  
```bash
sudo apt install certbot python3-certbot-nginx
```
2. Obtain and configure an SSL certificate:  
```bash
sudo certbot --nginx -d your-domain.com
```
3. Verify SSL and reload services:  
```bash
sudo systemctl restart gunicorn
sudo systemctl restart nginx
```

---

## 🚀 **9. Final Checks & Production Tips**  

✅ Check application status using:  
```bash
sudo systemctl status gunicorn
sudo systemctl status nginx
```
✅ Visit your domain or public IP:  
```
https://your-domain.com
```

---
 

## 🎯 **Expected Outcomes**

- ✅ Successful deployment of the Django application on AWS EC2 with Gunicorn and Nginx.  
- ✅ Properly configured SSL for secure HTTPS communication using Certbot.  
- ✅ Application accessible via the domain name or public IP with HTTP and HTTPS.  
- ✅ Hands-on experience in managing and deploying production-ready applications.  
- ✅ **Done! Your Django application is now successfully deployed on AWS EC2 with Gunicorn and Nginx!** 
![Image2](./Assests/Live%20Demo%201.png) 
![Image2](./Assests/Live%20Demo%202.png) 

---
## 📢 Let's Connect!
- Stay updated on [LinkedIn](https://www.linkedin.com/in/-kartikjain/) for more DevOps projects and insights.
- Follow along as I explore **Cloud Infrastructure, Ansible Automation, and DevOps practices**.
- Let's collaborate and build scalable solutions together!

---