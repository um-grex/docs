---
weight: 1500
linkTitle: "Openstack Cloud - Arbutus"
Title: "Web service example - Forgejo"
description: "Workshops and Training Material - 2026 - VM Creation"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
#draft: true
#build:
# list: false
# render: false
---

## Introduction
---

Walkthrough to install a code repository service based on [Forgejo](https://forgejo.org/).

This tutorial assumes you already have a working [Debian 12](https://www.debian.org/releases/bookworm/) virtual machine with a public IP.

Some useful links:
 - Arbutus (legacy) - [https://arbutus.cloud.computecanada.ca/](https://arbutus.cloud.computecanada.ca/)
 - Cloud on DRAC Wiki - [https://docs.alliancecan.ca/wiki/Cloud](https://docs.alliancecan.ca/wiki/Cloud)
 - Cloud Quick Start on DRAC Wiki - [https://docs.alliancecan.ca/wiki/Cloud_Quick_Start](https://docs.alliancecan.ca/wiki/Cloud_Quick_Start)

## Install Forgejo
---

1. Update the system

{{< highlight bash >}}
sudo apt update
sudo apt -y upgrade
{{< /highlight >}}

2. Install dependencies (and some utilities)

{{< highlight bash >}}
sudo apt -y install vim tmux net-tools dnsutils git git-lfs postgresql postgresql-client nginx certbot
{{< /highlight >}}

3. Create a `git` user

{{< highlight bash >}}
sudo adduser --system --group --disabled-password --shell /bin/bash --home /opt/git git
{{< /highlight >}}

4. Download [Forgejo](https://forgejo.org/) from the [release page](https://codeberg.org/forgejo/forgejo/releases)

{{< highlight bash >}}
sudo wget -O /opt/git/forgejo https://codeberg.org/forgejo/forgejo/releases/download/v15.0.2/forgejo-15.0.2-linux-amd64
sudo chown git:git /opt/git/forgejo
sudo chmod +x /opt/git/forgejo
{{< /highlight >}}

5. Create required directories

{{< highlight bash >}}
sudo mkdir -p /etc/forgejo /var/lib/forgejo
sudo chown git:git /etc/forgejo /var/lib/forgejo
sudo chmod 750 /etc/forgejo /var/lib/forgejo
{{< /highlight >}}

6. Create the database

{{< highlight bash >}}
sudo -iu postgres -- psql -c "CREATE ROLE forgejo WITH LOGIN PASSWORD 'forgejo'"
sudo -iu postgres -- psql -c "CREATE DATABASE forgejo WITH OWNER forgejo"
{{< /highlight >}}

7. Download the Systemd service file

{{< highlight bash >}}
sudo wget -O /etc/systemd/system/forgejo.service https://codeberg.org/forgejo/forgejo/raw/branch/forgejo/contrib/systemd/forgejo.service
sudo sed -i -e 's|=/usr/local/bin/forgejo|=/opt/git/forgejo|' /etc/systemd/system/forgejo.service
sudo sed -i -e 's|HOME=/home/git|HOME=/opt/git|' /etc/systemd/system/forgejo.service
sudo systemctl daemon-reload
sudo systemctl enable forgejo.service
{{< /highlight >}}

8. Create the TLS certificate

{{< highlight bash >}}
sudo systemctl stop nginx.service
sudo certbot certonly --standalone -n --agree-tos --register-unsafely-without-email -d CHANGE_THIS.cloud.computecanada.ca
{{< /highlight >}}

9. Configure Nginx

{{< highlight bash >}}
sudo rm /etc/nginx/sites-enabled/default
sudo touch /etc/nginx/sites-available/forgejo
sudo ln -s /etc/nginx/sites-available/forgejo /etc/nginx/sites-enabled/forgejo
sudo vim /etc/nginx/sites-available/forgejo
{{< /highlight >}}

Copy the following configuration inside `/etc/nginx/sites-available/forgejo`:

{{< highlight nginx >}}
server {
    listen 80;

    return 308 https://$host$request_uri;
}

server {
    listen 443 ssl;

    ssl_certificate     /etc/letsencrypt/live/CHANGE_THIS.cloud.computecanada.ca/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/CHANGE_THIS.cloud.computecanada.ca/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305;
    ssl_prefer_server_ciphers off;

    ssl_session_timeout 1d;
    ssl_session_cache shared:MozSSL:10m;

    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;

    merge_slashes off;

    location / {
        client_max_body_size 0;
        proxy_read_timeout 2h;

        proxy_set_header Connection        $http_connection;
        proxy_set_header Upgrade           $http_upgrade;
        proxy_set_header Host              $host;
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_pass http://127.0.0.1:3000;
    }
}
{{< /highlight >}}

10. Reboot the virtual machine

{{< highlight bash >}}
sudo reboot
{{< /highlight >}}

11. Navigate to [https://CHANGE_THIS.cloud.computecanada.ca](https://CHANGE_THIS.cloud.computecanada.ca) to finish configuration

<!-- Changes and update:
* Last revision: May 21, 2026.
-->

