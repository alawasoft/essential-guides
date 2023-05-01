Here's a `README.md` version of the CVAT installation and configuration guide:

```
# CVAT Installation and Configuration Guide

Follow these steps to install and run CVAT:

1. **Update repositories and install required packages:**
```bash
sudo apt-get update
sudo apt-get --no-install-recommends install -y \
  apt-transport-https \
  ca-certificates \
  curl \
  gnupg-agent \
  software-properties-common
```

2. **Add Docker repository:**
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) \
  stable"
sudo apt-get update
```

3. **Install Docker:**
```bash
apt-get --no-install-recommends install -y docker-ce docker-ce-cli containerd.io
sudo groupadd docker
sudo usermod -aG docker $USER
sudo reboot
```

4. **Install Python3 and related packages:**
```bash
sudo apt-get --no-install-recommends install -y python3-pip python3-setuptools
sudo python3 -m pip install setuptools docker-compose
```

5. **Install Git:**
```bash
sudo apt-get --no-install-recommends install -y git
```

6. **Clone the CVAT repository and enter the directory:**
```bash
git clone https://github.com/opencv/cvat.git -b develop --recursive
cd cvat
```

7. **Set CVAT_HOST environment variable:**
```bash
export CVAT_HOST=your-ip-address
```

8. **Build Docker images:**
```bash
docker-compose -f docker-compose.yml -f components/analytics/docker-compose.analytics.yml -f components/serverless/docker-compose.serverless.yml -f docker-compose.override.yml build
```

9. **Start CVAT services:**
```bash
docker-compose up -d
```

10. **Remove all Docker images and containers (optional):**
```bash
docker kill $(docker ps -q)
docker rm $(docker ps -a -q)
docker rmi $(docker images -q)
docker container prune -a -f
docker image prune -a -f
docker system prune -a -f
```

11. **Install Nuclio:**
```bash
wget https://github.com/nuclio/nuclio/releases/download/1.5.16/nuctl-1.5.16-linux-amd64
sudo chmod +x nuctl-1.5.16-linux-amd64
sudo ln -sf $(pwd)/nuctl-1.5.16-linux-amd64 /usr/local/bin/nuctl
```

12. **Create Nuclio project and deploy functions:**
```bash
nuctl create project cvat
nuctl deploy --project-name cvat \
  --path serverless/openvino/dextr/nuclio \
  --volume `pwd`/serverless/common:/opt/nuclio/common \
  --platform local
nuctl deploy --project-name cvat \
  --path serverless/pytorch/ultralytics/yolov5/nuclio \
  --volume `pwd`/serverless/common:/opt/nuclio/common \
  --platform local
nuctl deploy --project-name cvat \
  --path serverless/pytorch/ultralytics/yolov5/ocr \
  --volume `pwd`/serverless/common:/opt/nuclio/common \
  --platform local
```
13. **Start CVAT services:**
```bash
docker-compose up -d
```

14. **Allow required ports through the firewall:**
```bash
sudo ufw allow 8080
sudo ufw allow 8070
```

15. **Create a superuser for CVAT:**
```bash
docker exec -it cvat bash -ic 'python3 ~/manage.py createsuperuser'
```

16. **(Optional) Enable email verification for newly registered users:**

In the CVAT settings file, configure Django all-auth to enable email verification (ACCOUNT_EMAIL_VERIFICATION = 'mandatory'). Access is denied until the user's email address is verified.
```
ACCOUNT_AUTHENTICATION_METHOD = 'username'
ACCOUNT_CONFIRM_EMAIL_ON_GET = True
ACCOUNT_EMAIL_REQUIRED = True
ACCOUNT_EMAIL_VERIFICATION = 'mandatory'
```

17. **(Optional) Deploy a secure CVAT instance with HTTPS using Traefik:**

To enable this, first set the CVAT_HOST (the domain of your website) and ACME_EMAIL (contact email for Let's Encrypt) environment variables:
```bash
export CVAT_HOST=<YOUR_DOMAIN>
export ACME_EMAIL=<YOUR_EMAIL>
```

That's it! You have now installed and configured CVAT. Visit `http://your-ip-address:8080` or `https://your-domain` (if you enabled HTTPS) to access the CVAT web interface.
```
