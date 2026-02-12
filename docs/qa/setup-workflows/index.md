# Server Setup & DevOps Workflows

Common server setup procedures, application deployment, and scaling workflows.

---

## New Server Setup Checklist

### Question: What are the steps to set up a new Linux server?

**Initial Setup:**

```bash
# 1. Update system
sudo dnf update -y
sudo dnf upgrade -y

# 2. Set hostname
sudo hostnamectl set-hostname myserver
echo "127.0.0.1 myserver" | sudo tee -a /etc/hosts

# 3. Configure network
sudo nmcli connection modify ens33 ipv4.method manual
sudo nmcli connection modify ens33 ipv4.addresses "192.168.1.100/24"
sudo nmcli connection modify ens33 ipv4.gateway "192.168.1.1"
sudo nmcli connection modify ens33 ipv4.dns "8.8.8.8"
sudo nmcli connection up ens33

# 4. Configure timezone
sudo timedatectl set-timezone America/New_York

# 5. Configure firewall
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload

# 6. Create admin user
sudo useradd -m -s /bin/bash admin
sudo usermod -aG wheel admin
sudo passwd admin

# 7. Configure sudo access
sudo visudo
# Add: admin ALL=(ALL) NOPASSWD:ALL

# 8. Setup SSH keys
sudo -u admin ssh-keygen -t rsa -b 4096
# Copy public key to authorized_keys

# 9. Disable root login (optional)
sudo sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# 10. Install essential tools
sudo dnf install -y curl wget git vim htop
```

**Tags:** `#setup` `#server` `#infrastructure`

---

## Application Deployment

### Question: How do I deploy an application to production?

**Pre-deployment:**

```bash
# 1. Set up application directory
sudo mkdir -p /opt/myapp
sudo chown $USER:$USER /opt/myapp

# 2. Clone repository
cd /opt/myapp
git clone https://github.com/user/repo.git .

# 3. Create virtual environment (Python)
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Or Node.js
npm install
npm run build

# 4. Configure environment
cp .env.example .env
vim .env  # Set production values

# 5. Create systemd service
sudo tee /etc/systemd/system/myapp.service > /dev/null <<EOF
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=appuser
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/venv/bin/python app.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# 6. Start and enable service
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
```

**Verification:**

```bash
# Check status
sudo systemctl status myapp

# View logs
sudo journalctl -u myapp -f

# Test endpoint
curl http://localhost:5000/health
```

**Tags:** `#deployment` `#application` `#production`

---

## Scaling Infrastructure

### Question: How do I scale an application horizontally?

**Load Balancer Setup (HAProxy):**

```bash
# Install HAProxy
sudo dnf install -y haproxy

# Configure
sudo tee /etc/haproxy/haproxy.cfg > /dev/null <<EOF
global
    log /dev/log local0

defaults
    log     global
    mode    http

frontend web
    bind *:80
    default_backend backend

backend backend
    balance roundrobin
    server app1 192.168.1.101:5000 check
    server app2 192.168.1.102:5000 check
    server app3 192.168.1.103:5000 check
EOF

# Start HAProxy
sudo systemctl enable haproxy
sudo systemctl restart haproxy
```

**Database Scaling:**

```bash
# Set up replication (MySQL)
# Master configuration
# Enable binary logging in master

# Slave configuration
CHANGE MASTER TO
  MASTER_HOST='master_ip',
  MASTER_USER='repl_user',
  MASTER_PASSWORD='password',
  MASTER_LOG_FILE='mysql-bin.000001',
  MASTER_LOG_POS=154;

START SLAVE;
SHOW SLAVE STATUS\G
```

**Tags:** `#scaling` `#infrastructure` `#highavailability`

---

## Blue-Green Deployment

### Question: How do I perform a blue-green deployment?

**Process:**

```bash
# 1. Run "green" (new) environment alongside "blue" (current)
# Blue: Running production
# Green: New version ready

# 2. Direct traffic to green once verified
sudo firewall-cmd --remove-forward-port=port=80:proto=tcp:toaddr=192.168.1.101
sudo firewall-cmd --add-forward-port=port=80:proto=tcp:toaddr=192.168.1.102
sudo firewall-cmd --reload

# 3. Monitor green environment
watch curl http://app-green/health

# 4. If successful, make green the new blue
# If failed, traffic still routes to blue, no downtime

# 5. Update blue from green for next deployment
docker pull app:latest
docker tag app:latest app:blue
```

**Tags:** `#deployment` `#bluegreen` `#zerodowntime`

---

## Kubernetes Deployment

### Question: How do I deploy to Kubernetes?

**Basic Deployment:**

```bash
# 1. Create deployment manifest
kubectl create deployment app --image=myapp:1.0 --port=8080

# 2. Expose service
kubectl expose deployment app --type=LoadBalancer --port=80 --target-port=8080

# 3. Scale deployment
kubectl scale deployment app --replicas=3

# 4. Update image
kubectl set image deployment/app app=myapp:2.0

# 5. Monitor rollout
kubectl rollout status deployment/app
kubectl get pods
kubectl logs pod_name
```

**Using Manifests:**

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:
      - name: app
        image: myapp:1.0
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
---
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: app
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
```

**Tags:** `#kubernetes` `#deployment` `#k8s`

---

## Zero-Downtime Updates

### Question: How do I update services without downtime?

**Process:**

```bash
# 1. Prepare new version
docker build -t myapp:2.0 .
docker push myapp:2.0

# 2. Create new instance with new version
docker run -d -p 5001:5000 myapp:2.0

# 3. Health check
curl http://localhost:5001/health

# 4. Add to load balancer
echo "server app2 localhost:5001 check" >> /etc/haproxy/haproxy.cfg
sudo systemctl reload haproxy

# 5. Drain connections from old version
# Wait for existing connections to close

# 6. Remove old version from load balancer
vim /etc/haproxy/haproxy.cfg  # Comment out old server
sudo systemctl reload haproxy

# 7. Stop old version
docker stop old_container
```

**Tags:** `#deployment` `#zerodowntime` `#updates`

---

**Tags:** `#setup` `#deployment` `#infrastructure` `#workflows`
