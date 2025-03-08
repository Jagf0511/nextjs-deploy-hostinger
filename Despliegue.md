# **Despliegue de Aplicación en Hostinger con Next.js (Usando una VPS)**

Este documento detalla paso a paso cómo desplegar una aplicación **Next.js** en una **VPS con Ubuntu** usando **Nginx** y **PM2** para su gestión, además de **Certbot** para agregar un certificado SSL.

## **1. Configuración interna de la VPS**

Una **VPS (Servidor Privado Virtual)** es un servidor en la nube donde podemos alojar aplicaciones web. En este caso, usamos una VPS con **Ubuntu**.

### **1.1. Actualización de repositorios y paquetes**

Antes de instalar cualquier programa, es importante actualizar el sistema para evitar problemas de compatibilidad y asegurarnos de que tenemos las versiones más recientes de los paquetes.

Ejecutamos los siguientes comandos:

```bash
sudo apt update   # Actualiza la lista de paquetes disponibles
sudo apt upgrade  # Instala las últimas versiones de los paquetes
```

### **1.2. Clonación del repositorio**

Ahora necesitamos traer el código de nuestra aplicación desde **GitHub**. Para mayor seguridad, utilizamos una clave **SSH** en lugar de **HTTPS**.

Pasos:
1. Generamos una clave **SSH** en la VPS y la agregamos a **GitHub**.
2. Clonamos el repositorio de la aplicación en la VPS:

```bash
git clone git@github.com:usuario/repositorio.git
```

3. Entramos en la carpeta del proyecto:

```bash
cd repositorio
```

## **2. Instalación de Node.js mediante NVM**

**¿Por qué necesitamos Node.js?**  
Next.js es un framework basado en **React** que necesita **Node.js** para ejecutarse. 

**¿Por qué usamos NVM?**  
En lugar de instalar Node.js directamente con **apt**, usamos **NVM** (Node Version Manager), que nos permite administrar diferentes versiones de Node.js fácilmente.

Pasos para instalar **NVM**:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc  # Recarga la configuración de la terminal
```

Ahora instalamos la versión estable de **Node.js**:

```bash
nvm install --lts
```

Verificamos que se instaló correctamente:

```bash
node --version
```

## **3. Instalación de dependencias y ejecución de la app**

Cada proyecto en **Next.js** tiene una serie de paquetes necesarios para funcionar. Para instalarlos, ejecutamos:

```bash
npm install
```

Luego, construimos la aplicación para optimizar su rendimiento:

```bash
npm run build
```

## **4. Uso de PM2 para mantener la app en ejecución**

**¿Qué es PM2 y por qué lo usamos?**  
PM2 es un administrador de procesos que mantiene la aplicación en ejecución incluso si el servidor se reinicia. Esto es clave para evitar que la aplicación se detenga inesperadamente.

Instalamos PM2:

```bash
npm install -g pm2
```

Ejecutamos la aplicación con **PM2**:

```bash
pm2 start npm -- start
```

Este comando mantiene la aplicación en ejecución en segundo plano.

## **5. Configuración de Nginx como Proxy Inverso**

**¿Qué es Nginx y por qué lo usamos?**  
Nginx es un servidor web que se encarga de recibir las peticiones de los usuarios y redirigirlas a nuestra aplicación. Sin él, los usuarios tendrían que acceder a la aplicación usando la dirección IP y el puerto manualmente.

### **5.1. Instalación de Nginx**

```bash
sudo apt install nginx
```

### **5.2. Configuración de Nginx**

Entramos en la carpeta de configuración de Nginx:

```bash
cd /etc/nginx/sites-available
```

Creamos un archivo de configuración para nuestra aplicación:

```bash
sudo nano tsm-nextapp
```

Agregamos la siguiente configuración:

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

**¿Qué hace esta configuración?**
- `listen 80;` → Escucha peticiones en el puerto **80** (HTTP).
- `server_name` → Indica los dominios permitidos.
- `proxy_pass http://localhost:3000;` → Redirige el tráfico al puerto 3000 donde se ejecuta nuestra aplicación.

Guardamos y enlazamos este archivo para que Nginx lo use:

```bash
sudo ln -s /etc/nginx/sites-available/tsm-nextapp /etc/nginx/sites-enabled/tsm-nextapp
```

Reiniciamos Nginx para aplicar los cambios:

```bash
sudo systemctl restart nginx
```

## **6. Configuración de Certificado SSL con Certbot**

**¿Por qué necesitamos un certificado SSL?**  
SSL permite que la conexión entre el usuario y el servidor sea segura, cifrando los datos y evitando ataques como **Man-in-the-Middle**.

### **6.1. Instalación de Certbot**

```bash
sudo apt install snapd
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

### **6.2. Generación del certificado SSL**

Ejecutamos el siguiente comando para obtener un certificado SSL gratuito de **Let's Encrypt**:

```bash
sudo certbot --nginx -d www.transportservicemedellin.com -d transportservicemedellin.com
```

Esto configura automáticamente **Nginx** para que use HTTPS.

---

Con esto, hemos desplegado nuestra aplicación **Next.js** en **Hostinger** usando:  
✅ Una **VPS con Ubuntu**  
✅ **Node.js** gestionado con **NVM**  
✅ **PM2** para mantener la aplicación en ejecución  
✅ **Nginx** como **proxy inverso**  
✅ **Certbot** para un **certificado SSL gratuito**  

¡Ahora nuestra aplicación está disponible en **https://transportservicemedellin.com** con seguridad y rendimiento óptimo!
