Below is your entire **NGINX + Apache Reverse Proxy & Load Balancer LAB** rewritten in **clean, professional, perfectly formatted Markdown (MD)** — ready to paste into GitHub, Notion, or README.md.

---

# 🌀 Reverse Proxy & Load Balancer Lab

## **NGINX + 2 Apache Servers (1 Server, 2 Clients)**

---

## 🖥 **LAB SETUP – 3 VMs (1 SERVER + 2 CLIENTS)**

All VMs must be:

* **NAT Network Mode**
* **SELinux:** disabled or permissive

| VM      | Role            | Service                               |
| ------- | --------------- | ------------------------------------- |
| **VM1** | Apache Server 1 | httpd                                 |
| **VM2** | Apache Server 2 | httpd                                 |
| **VM3** | Nginx Server    | nginx (Reverse Proxy + Load Balancer) |

**Update all VMs:**

```bash
sudo yum update -y
```

---

# 🚀 Apache Setup (Client 1 & Client 2)

## **1️⃣ Install Apache**

```bash
sudo yum install httpd -y
# Ubuntu:
sudo apt install apache2 -y
```

---

## **2️⃣ Create Webpage**

```bash
sudo vi /var/www/html/index.html
```

### **Client 1 Example**

```html
<h1>Apache Server 1</h1> Served by: <IP-of-Apache-1>
```

### **Client 2 Example**

```html
<h1>Apache Server 2</h1> Served by: <IP-of-Apache-2>
```

---

## **3️⃣ Start Apache**

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

---

## **4️⃣ Open Firewall**

```bash
sudo firewall-cmd --add-service=http
sudo firewall-cmd --add-service=http --permanent
```

---

# 🟩 NGINX Setup (Main Front-End Server)

## **1️⃣ Install Nginx**

```bash
sudo yum install nginx -y
```

## **2️⃣ Start Nginx**

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

## **3️⃣ Open Firewall**

```bash
sudo firewall-cmd --add-service=http
sudo firewall-cmd --add-service=http --permanent
```

---

## **4️⃣ Test Nginx Default Page**

Open browser on Windows:

```
http://<nginx-server-IP>
```

You should see the **default Nginx welcome page**.

---

# 🔁 NGINX as Reverse Proxy

---

## **1️⃣ Backup original config**

```bash
sudo cp /etc/nginx/nginx.conf ~
```

---

## **2️⃣ Edit Nginx Config**

```bash
sudo vi /etc/nginx/nginx.conf
```

### Comment these lines inside the `server {}` block:

```nginx
#listen [::]:80;
#server_name _;
#root /usr/share/nginx/html;
```

Ensure:

```nginx
include /etc/nginx/default.d/*.conf;
```

---

## **3️⃣ Add Reverse Proxy Configuration**

Inside the `server {}` block, add:

### **Route `/` → Apache Server 1**

```nginx
location / {
    proxy_pass http://<Apache-1-IP>/;
    proxy_buffering off;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

### **Route `/courses` → Apache Server 2**

```nginx
location /courses {
    proxy_pass http://<Apache-2-IP>/;
    proxy_buffering off;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

---

## **4️⃣ Restart Nginx**

```bash
sudo systemctl restart nginx
```

---

## **5️⃣ Test Reverse Proxy**

| URL                         | Expected Result      |
| --------------------------- | -------------------- |
| `http://<nginx-IP>/`        | Apache Server 1 page |
| `http://<nginx-IP>/courses` | Apache Server 2 page |

✔️ Reverse Proxy Working

---

# ⚖️ NGINX as Load Balancer

---

## **1️⃣ Restore Original Config**

```bash
sudo cp -f ~/nginx.conf /etc/nginx/
```

---

## **2️⃣ Edit Nginx for Load Balancing**

```bash
sudo vi /etc/nginx/nginx.conf
```

Comment again:

```nginx
#listen [::]:80;
#server_name _;
#root /usr/share/nginx/html;
```

Ensure:

```nginx
include /etc/nginx/default.d/*.conf;
```

---

## **3️⃣ Add Upstream + Proxy Block**

### **Upstream block**

```nginx
upstream backend {
    server <Apache-1-IP>;
    server <Apache-2-IP>;
}
```

### **Server block**

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://backend/;
    }
}
```

---

## **4️⃣ Restart Nginx**

```bash
sudo systemctl restart nginx
```

---

## **5️⃣ Test Load Balancer**

Open:

```
http://<nginx-IP>/
```

Refresh multiple times:

* 1st → Apache Server 1
* 2nd → Apache Server 2
* 3rd → Apache Server 1
* 4th → Apache Server 2

✔️ **Default Round Robin load balancing** confirmed

---

# 🎉 LAB COMPLETED

You now have:

✔️ **Reverse Proxy**
✔️ **Route-based Proxying**
✔️ **Load Balancer (Round Robin)**
✔️ **3-VM working architecture**

---

If you want, I can also generate:

✅ **Diagram in PNG**
✅ **Folder structure + config files**
✅ **Automated deployment script (.sh)**
✅ **Markdown with color themes, emojis, TOC**

Just say **"make diagram"**, **"make script"**, etc.
