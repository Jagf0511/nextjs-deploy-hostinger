# **Deploying a Next.js Application on a VPS with Hostinger**

This repository contains the documentation and scripts needed to deploy a **Next.js** application on a **VPS with Ubuntu** using **Nginx** and **PM2**, as well as setting up **SSH** for a secure connection.

## **Contents**

- [Requirements](#requirements)  
- [Connecting to the VPS](#connecting-to-the-vps)  
- [Installing Node.js and Dependencies](#installing-nodejs-and-dependencies)  
- [Running and Managing with PM2](#running-and-managing-with-pm2)  
- [Configuring Nginx](#configuring-nginx)  
- [Enabling SSL with Certbot](#enabling-ssl-with-certbot)  

## **Requirements**
- **Hostinger VPS** with **Ubuntu**  
- Domain configured to point to the VPS  
- **SSH** access with public keys  
- Git installed on the VPS  
- Node.js installed with **NVM**  

## **Connecting to the VPS**
To connect securely, we use **SSH** with key-based authentication.

1. Generate an SSH key on your local machine:  
    ```bash
    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
    ```
2. Add the public key to the VPS from the Hostinger panel.  
3. Connect to the VPS:  
    ```bash
    ssh user@SERVER_IP
    ```

## **Installing Node.js and Dependencies**

1. Update the system:  
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
2. Install **NVM** and the latest LTS version of Node.js:  
    ```bash
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
    source ~/.bashrc
    nvm install --lts
    ```
3. Clone the repository and enter the project directory:  
    ```bash
    git clone git@github.com:user/repository.git
    cd repository
    ```
4. Install dependencies and build the application:  
    ```bash
    npm install
    npm run build
    ```

## **Running and Managing with PM2**
To keep the application running in the background, we use **PM2**:  
```bash
npm install -g pm2
pm2 start npm -- start
pm2 save
pm2 startup
```

## **Configuring Nginx**

1. Install **Nginx**:  
    ```bash
    sudo apt install nginx -y
    ```
2. Configure Nginx as a reverse proxy:  
    ```bash
    sudo nano /etc/nginx/sites-available/tsm-nextapp
    ```
3. Add the following configuration:  
    ```nginx
    server {
        listen 80;
        listen [::]:80;
        server_name www.transportservicemedellin.com transportservicemedellin.com;

        location / {
            proxy_pass http://localhost:3000;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    }
    ```
4. Enable the configuration:  
    ```bash
    sudo ln -s /etc/nginx/sites-available/tsm-nextapp /etc/nginx/sites-enabled/
    sudo systemctl restart nginx
    ```

## **Enabling SSL with Certbot**

To add **HTTPS**, install **Certbot** and generate a free SSL certificate:  
```bash
sudo apt install snapd
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx -d www.transportservicemedellin.com -d transportservicemedellin.com
```

## **Conclusion**
By following these steps, we have deployed a **Next.js** application on a **VPS with Hostinger**, ensuring security and availability with **PM2**, **Nginx**, and **SSL**.

📌 **Website:** [https://transportservicemedellin.com](https://transportservicemedellin.com)
