# **Despliegue de Aplicación Next.js en VPS con Hostinger**

Este repositorio contiene la documentación y los scripts necesarios para desplegar una aplicación **Next.js** en una **VPS con Ubuntu** usando **Nginx** y **PM2**, además de configurar **SSH** para una conexión segura.

## **Contenido**

- [Requisitos](#requisitos)
- [Conexión a la VPS](#conexión-a-la-vps)
- [Instalación de Node.js y Dependencias](#instalación-de-nodejs-y-dependencias)
- [Ejecución y Gestión con PM2](#ejecución-y-gestión-con-pm2)
- [Configuración de Nginx](#configuración-de-nginx)
- [Habilitar SSL con Certbot](#habilitar-ssl-con-certbot)

## **Requisitos**
- VPS en **Hostinger** con **Ubuntu**
- Dominio configurado para apuntar a la VPS
- Acceso **SSH** con claves públicas
- Git instalado en la VPS
- Node.js instalado con **NVM**

## **Conexión a la VPS**
Para conectarnos de forma segura, utilizamos **SSH** con autenticación basada en claves.

1. Generar una clave SSH en tu máquina local:
    ```bash
    ssh-keygen -t rsa -b 4096 -C "tu_email@example.com"
    ```
2. Agregar la clave pública a la VPS desde el panel de Hostinger.
3. Conectar a la VPS:
    ```bash
    ssh usuario@IP_DEL_SERVIDOR
    ```

## **Instalación de Node.js y Dependencias**

1. Actualizar el sistema:
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
2. Instalar **NVM** y la última versión LTS de Node.js:
    ```bash
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
    source ~/.bashrc
    nvm install --lts
    ```
3. Clonar el repositorio y entrar en el proyecto:
    ```bash
    git clone git@github.com:usuario/repositorio.git
    cd repositorio
    ```
4. Instalar dependencias y construir la aplicación:
    ```bash
    npm install
    npm run build
    ```

## **Ejecución y Gestión con PM2**
Para mantener la aplicación corriendo en segundo plano, utilizamos **PM2**:
```bash
npm install -g pm2
pm2 start npm -- start
pm2 save
pm2 startup
```

## **Configuración de Nginx**

1. Instalar **Nginx**:
    ```bash
    sudo apt install nginx -y
    ```
2. Configurar Nginx como proxy inverso:
    ```bash
    sudo nano /etc/nginx/sites-available/tsm-nextapp
    ```
3. Agregar la siguiente configuración:
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
4. Activar la configuración:
    ```bash
    sudo ln -s /etc/nginx/sites-available/tsm-nextapp /etc/nginx/sites-enabled/
    sudo systemctl restart nginx
    ```

## **Habilitar SSL con Certbot**

Para agregar **HTTPS**, instalamos **Certbot** y generamos un certificado SSL gratuito:
```bash
sudo apt install snapd
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx -d www.transportservicemedellin.com -d transportservicemedellin.com
```

## **Conclusión**
Siguiendo estos pasos, hemos desplegado una aplicación **Next.js** en una **VPS con Hostinger**, garantizando seguridad y disponibilidad con **PM2**, **Nginx** y **SSL**.

📌 **Sitio web:** [https://transportservicemedellin.com](https://transportservicemedellin.com)
