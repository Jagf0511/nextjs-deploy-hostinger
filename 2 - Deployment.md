# **Deploying a Next.js Application on Hostinger with a VPS**

This document provides a step-by-step guide on how to deploy a **Next.js** application on a **VPS with Ubuntu** using **Nginx** and **PM2** for management, along with **Certbot** for adding an SSL certificate.

## **1. Internal VPS Configuration**

A **VPS (Virtual Private Server)** is a cloud server where we can host web applications. In this case, we are using a VPS with **Ubuntu**.

### **1.1. Updating Repositories and Packages**

Before installing any program, it is important to update the system to avoid compatibility issues and ensure we have the latest package versions.

Run the following commands:

```bash
sudo apt update   # Updates the list of available packages
sudo apt upgrade  # Installs the latest package versions
```

### **1.2. Cloning the Repository**

Now we need to fetch our application code from **GitHub**. For security reasons, we use an **SSH** key instead of **HTTPS**.

Steps:
1. Generate an **SSH** key on the VPS and add it to **GitHub**.
2. Clone the application repository onto the VPS:

```bash
git clone git@github.com:user/repository.git
```

3. Navigate into the project folder:

```bash
cd repository
```

## **2. Installing Node.js with NVM**

**Why do we need Node.js?**  
Next.js is a **React-based framework** that requires **Node.js** to run.  

**Why use NVM?**  
Instead of installing Node.js directly with **apt**, we use **NVM** (Node Version Manager), which allows us to manage different versions of Node.js easily.

Steps to install **NVM**:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc  # Reload terminal configuration
```

Now install the stable version of **Node.js**:

```bash
nvm install --lts
```

Verify the installation:

```bash
node --version
```

## **3. Installing Dependencies and Running the App**

Every **Next.js** project has a set of required packages. To install them, run:

```bash
npm install
```

Then, build the application to optimize performance:

```bash
npm run build
```

## **4. Using PM2 to Keep the App Running**

**What is PM2 and why use it?**  
PM2 is a process manager that keeps the application running even if the server restarts. This is crucial to prevent unexpected app shutdowns.

Install PM2:

```bash
npm install -g pm2
```

Run the application with **PM2**:

```bash
pm2 start npm -- start
```

This command keeps the application running in the background.

## **5. Configuring Nginx as a Reverse Proxy**

**What is Nginx and why use it?**  
Nginx is a web server that handles user requests and redirects them to our application. Without it, users would have to manually enter the server's IP address and port.

### **5.1. Installing Nginx**

```bash
sudo apt install nginx
```

### **5.2. Configuring Nginx**

Navigate to the Nginx configuration folder:

```bash
cd /etc/nginx/sites-available
```

Create a configuration file for our application:

```bash
sudo nano tsm-nextapp
```

Add the following configuration:

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

**What does this configuration do?**
- `listen 80;` → Listens for requests on port **80** (HTTP).
- `server_name` → Specifies the allowed domains.
- `proxy_pass http://localhost:3000;` → Redirects traffic to port 3000, where our app runs.

Save the file and create a symbolic link to enable it:

```bash
sudo ln -s /etc/nginx/sites-available/tsm-nextapp /etc/nginx/sites-enabled/tsm-nextapp
```

Restart Nginx to apply the changes:

```bash
sudo systemctl restart nginx
```

## **6. Configuring an SSL Certificate with Certbot**

**Why do we need an SSL certificate?**  
SSL ensures that the connection between the user and the server is secure by encrypting data, preventing attacks like **Man-in-the-Middle**.

### **6.1. Installing Certbot**

```bash
sudo apt install snapd
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

### **6.2. Generating the SSL Certificate**

Run the following command to obtain a free SSL certificate from **Let's Encrypt**:

```bash
sudo certbot --nginx -d www.transportservicemedellin.com -d transportservicemedellin.com
```

This automatically configures **Nginx** to use HTTPS.

---

With this setup, we have successfully deployed our **Next.js** application on **Hostinger** using:  
✅ A **VPS with Ubuntu**  
✅ **Node.js** managed with **NVM**  
✅ **PM2** to keep the app running  
✅ **Nginx** as a **reverse proxy**  
✅ **Certbot** for a **free SSL certificate**  

Our application is now available at **https://transportservicemedellin.com** with enhanced security and performance!


