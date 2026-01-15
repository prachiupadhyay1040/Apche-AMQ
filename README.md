---

# 📘 Document: Install & Configure Apache ActiveMQ on Linux

## 1️⃣Prerequisites

* Linux server (RHEL/CentOS/Ubuntu)
* Root or sudo access
* Java JDK 11 or JDK 8
* Minimum 2 GB RAM (recommended)
* Open ports:

  * 61616 → AMQ broker
  * 8161 → Web console

---

## 2️⃣ Install Java

### 🔹 RHEL / CentOS

```bash
yum install -y java-11-openjdk
```

### 🔹 Ubuntu

```bash
apt update
apt install -y openjdk-11-jdk
```

### 🔹 Verify Java

```bash
java -version
```

---

## 3️⃣ Create ActiveMQ User (Best Practice)

```bash
useradd activemq
passwd activemq
```

---

## 4️⃣ Download Apache ActiveMQ

```bash
cd /opt
wget https://archive.apache.org/dist/activemq/5.18.3/apache-activemq-5.18.3-bin.tar.gz
```

> ⚠️ Use **Classic ActiveMQ (5.x)** unless Artemis is required.

---

## 5️⃣ Extract & Set Permissions

```bash
tar -xvzf apache-activemq-5.18.3-bin.tar.gz
mv apache-activemq-5.18.3 activemq
chown -R activemq:activemq /opt/activemq
```

---

## 6️⃣ Configure Environment Variables

```bash
vim /opt/activemq/bin/env
```

Add:

```bash
JAVA_HOME=/usr/lib/jvm/java-11-openjdk
ACTIVEMQ_HOME=/opt/activemq
ACTIVEMQ_USER=activemq
```

---

## 7️⃣ Start ActiveMQ

### 🔹 Switch User

```bash
su - activemq
```

### 🔹 Start Broker

```bash
/opt/activemq/bin/activemq start
```

### 🔹 Check Status

```bash
/opt/activemq/bin/activemq status
```

---

## 8️⃣ Enable Web Console Access

### 🔹 Default URL

```
http://<server-ip>:8161/admin
```

### 🔹 Default Credentials

```
Username: admin
Password: admin
```

📍 Config file:

```bash
/opt/activemq/conf/jetty-realm.properties
```

---

## 9️⃣ Configure Firewall (If Enabled)

### 🔹 RHEL / CentOS

```bash
firewall-cmd --permanent --add-port=61616/tcp
firewall-cmd --permanent --add-port=8161/tcp
firewall-cmd --reload
```

### 🔹 Ubuntu

```bash
ufw allow 61616
ufw allow 8161
```

---

## 🔟 Create systemd Service (Production Recommended)

```bash
vim /etc/systemd/system/activemq.service
```

```ini
[Unit]
Description=Apache ActiveMQ
After=network.target

[Service]
Type=forking
User=activemq
Group=activemq
ExecStart=/opt/activemq/bin/activemq start
ExecStop=/opt/activemq/bin/activemq stop
Restart=always

[Install]
WantedBy=multi-user.target
```

### 🔹 Reload & Enable

```bash
systemctl daemon-reexec
systemctl daemon-reload
systemctl enable activemq
systemctl start activemq
systemctl status activemq
```

---

## 1️⃣1️⃣ Verify Ports

```bash
netstat -tulnp | grep 61616
netstat -tulnp | grep 8161
```

---

## 1️⃣2️⃣ Important Config Files

| Purpose       | File                     |
| ------------- | ------------------------ |
| Broker config | `conf/activemq.xml`      |
| Users         | `conf/users.properties`  |
| Roles         | `conf/groups.properties` |
| JVM tuning    | `bin/env`                |

---

## 1️⃣3️⃣ Stop / Restart ActiveMQ

```bash
systemctl stop activemq
systemctl restart activemq
```

---

## ✅ Installation Completed

Apache ActiveMQ is now:

* Installed
* Running as service
* Accessible via Web Console
* Production-ready

---

