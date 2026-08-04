# 🚀 Full Server Hosting Guide

### Host AdonisJS · Laravel · Vite Apps from Git with SSL, Cron, Queue & Firewall

---

> **Assumptions:** Ubuntu 22.04/24.04 LTS, root or sudo access, a domain name pointed to your server's IP.

---

## Table of Contents

1. [Initial Server Setup](#1-initial-server-setup)
2. [Install Core Dependencies](#2-install-core-dependencies)
3. [Firewall Configuration (UFW)](#3-firewall-configuration-ufw)
4. [Clone Your App from Git](#4-clone-your-app-from-git)
5. [App Setup by Framework](#5-app-setup-by-framework)
  - [Laravel](#51-laravel)
  - [AdonisJS](#52-adonisjs)
  - [Vite (Static / SSR)](#53-vite-static--ssr)
6. [Web Server Configuration](#6-web-server-configuration)
  - [Nginx](#61-nginx)
  - [Apache](#62-apache)
  - [Caddy](#63-caddy)
7. [SSL with Let's Encrypt](#7-ssl-with-lets-encrypt)
8. [Process Management with PM2](#8-process-management-with-pm2)
9. [Queue Workers](#9-queue-workers)
10. [Cron Jobs](#10-cron-jobs)
11. [Deploy Script & Git-Based Updates](#11-deploy-script--git-based-updates)
12. [Security Hardening Checklist](#12-security-hardening-checklist)

---

## 1. Initial Server Setup

### Create a deploy user (never run your app as root)

```bash
adduser deployer
usermod -aG sudo deployer
# Switch to the new user for all subsequent steps
su - deployer
```

> ⚠️**WARNING:** Running your application as `root` is dangerous. A compromised app could give an attacker full control over your server. Always use a dedicated, unprivileged user.

### Set up SSH key authentication

On your **local machine**:

```bash
ssh-keygen -t ed25519 -C "your@email.com"
ssh-copy-id deployer@YOUR_SERVER_IP
```

Then disable password auth on the server:

```bash
sudo nano /etc/ssh/sshd_config
# Set:
#   PasswordAuthentication no
#   PermitRootLogin no
sudo systemctl restart sshd
```

> ⚠️ **WARNING:** Do not disable password authentication until you have verified your SSH key works in a separate terminal session. Locking yourself out requires console/rescue mode access.

---

## 2. Install Core Dependencies

### System update

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl wget unzip build-essential
```

### Node.js (via NVM — recommended)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install --lts
nvm use --lts
node -v && npm -v
```

### PHP 8.3 (for Laravel)

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install -y php8.3 php8.3-fpm php8.3-cli php8.3-mbstring \
  php8.3-xml php8.3-curl php8.3-zip php8.3-bcmath php8.3-pgsql \
  php8.3-mysql php8.3-redis php8.3-gd php8.3-intl
```

### Composer (for Laravel)

```bash
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
composer --version
```

### Database (choose one)

**MySQL:**

```bash
sudo apt install -y mysql-server
sudo mysql_secure_installation
```

**PostgreSQL:**

```bash
sudo apt install -y postgresql postgresql-contrib
sudo -u postgres psql -c "CREATE USER appuser WITH PASSWORD 'strongpassword';"
sudo -u postgres psql -c "CREATE DATABASE appdb OWNER appuser;"
```

**Redis (for queues/cache):**

```bash
sudo apt install -y redis-server
sudo systemctl enable redis-server
```

---

## 3. Firewall Configuration (UFW)

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (critical — do this FIRST)
sudo ufw allow OpenSSH

# Allow web traffic
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Enable the firewall
sudo ufw enable

# Verify
sudo ufw status verbose
```

> ⚠️ **WARNING:** Always run `sudo ufw allow OpenSSH` **before** `sudo ufw enable`. If you enable the firewall without allowing SSH, you will permanently lock yourself out of the server over SSH.

### Optional: Restrict database ports

```bash
# Only allow DB access from localhost (never expose DB to the internet)
sudo ufw deny 3306   # MySQL
sudo ufw deny 5432   # PostgreSQL
sudo ufw deny 6379   # Redis
```

> ⚠️ **WARNING:** Exposing `3306`, `5432`, or `6379` to the public internet with a weak password is one of the most common causes of server compromise. Always keep these ports firewalled.

---

## 4. Clone Your App from Git

### Set up a deploy directory

```bash
sudo mkdir -p /var/www
sudo chown deployer:deployer /var/www
```

### Using HTTPS (simple)

```bash
cd /var/www
git clone https://github.com/youruser/yourapp.git myapp
cd myapp
```

### Using SSH Deploy Keys (recommended for private repos)

```bash
# Generate a deploy key on the server
ssh-keygen -t ed25519 -f ~/.ssh/deploy_key -N ""
cat ~/.ssh/deploy_key.pub
# → Add this public key to your GitHub/GitLab repo under Settings > Deploy Keys
```

Configure SSH to use this key:

```bash
nano ~/.ssh/config
```

```
Host github.com
  IdentityFile ~/.ssh/deploy_key
  StrictHostKeyChecking no
```

Then clone with SSH:

```bash
git clone git@github.com:youruser/yourapp.git /var/www/myapp
```

---

## 5. App Setup by Framework

### 5.1 Laravel

```bash
cd /var/www/myapp

# Install dependencies
composer install --no-dev --optimize-autoloader

# Set up environment
cp .env.example .env
nano .env   # Fill in DB_*, APP_URL, MAIL_*, QUEUE_CONNECTION=redis, etc.

# Generate application key
php artisan key:generate

# Run migrations
php artisan migrate --force

# Seed database (if needed)
php artisan db:seed --force

# Cache configuration for production
php artisan config:cache
php artisan route:cache
php artisan view:cache

# Storage symlink
php artisan storage:link

# Set correct permissions
sudo chown -R deployer:www-data /var/www/myapp
sudo chmod -R 775 /var/www/myapp/storage
sudo chmod -R 775 /var/www/myapp/bootstrap/cache
```

> ⚠️ **WARNING:** Never commit your `.env` file to Git. It contains secrets (APP_KEY, database passwords, API keys). Add `.env` to your `.gitignore`.

---

### 5.2 AdonisJS

```bash
cd /var/www/myapp

# Install dependencies
npm ci --omit=dev

# Or if you need dev deps for the build step:
npm ci
npm run build
# Then prune dev deps
npm prune --omit=dev

# Set up environment
cp .env.example .env
nano .env   # Fill in APP_KEY, DB_*, PORT, HOST=0.0.0.0, NODE_ENV=production

# Generate app key (if not set)
node ace generate:key

# Run migrations
node ace migration:run --force
```

The built output will be in `build/`. When running in production, point your process manager to `build/bin/server.js`.

---

### 5.3 Vite (Static / SSR)

**Static build (pure frontend):**

```bash
cd /var/www/myapp
npm ci
npm run build
# Output is in dist/ — serve this folder as a static site
```

**SSR / Node server (e.g., Vite + Express, SvelteKit, Nuxt):**

```bash
npm ci
npm run build
# Point PM2 to your server entry, e.g. build/index.js or server/index.js
```

---

## 6. Web Server Configuration

### 6.1 Nginx

**Install:**

```bash
sudo apt install -y nginx
sudo systemctl enable nginx
```

#### Laravel / Static Vite — Nginx config

```bash
sudo nano /etc/nginx/sites-available/myapp
```

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    root /var/www/myapp/public;   # Laravel: /public | Vite static: /dist
    index index.php index.html;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
    add_header X-XSS-Protection "1; mode=block";

    location / {
        try_files $uri $uri/ /index.php?$query_string;  # Remove for static Vite
    }

    # PHP-FPM (Laravel only)
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Block access to dot files
    location ~ /\.(?!well-known).* {
        deny all;
    }

    client_max_body_size 50M;
}
```

#### AdonisJS / Vite SSR (Node proxy) — Nginx config

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:3333;   # Your Node app port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

**Enable the site:**

```bash
sudo ln -s /etc/nginx/sites-available/myapp /etc/nginx/sites-enabled/
sudo nginx -t    # Test config before reloading!
sudo systemctl reload nginx
```

> ⚠️ **WARNING:** Always run `sudo nginx -t` before `sudo systemctl reload nginx`. A syntax error in your config will take down Nginx entirely for all sites on the server.

---

### 6.2 Apache

**Install:**

```bash
sudo apt install -y apache2
sudo a2enmod rewrite proxy proxy_http headers
sudo systemctl enable apache2
```

#### Laravel — Apache VirtualHost

```bash
sudo nano /etc/apache2/sites-available/myapp.conf
```

```apache
<VirtualHost *:80>
    ServerName yourdomain.com
    ServerAlias www.yourdomain.com
    DocumentRoot /var/www/myapp/public

    <Directory /var/www/myapp/public>
        AllowOverride All
        Require all granted
        Options -Indexes
    </Directory>

    # Security headers
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"

    ErrorLog ${APACHE_LOG_DIR}/myapp_error.log
    CustomLog ${APACHE_LOG_DIR}/myapp_access.log combined
</VirtualHost>
```

#### AdonisJS / Node Proxy — Apache VirtualHost

```apache
<VirtualHost *:80>
    ServerName yourdomain.com

    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:3333/
    ProxyPassReverse / http://127.0.0.1:3333/

    RequestHeader set X-Forwarded-Proto "http"
</VirtualHost>
```

**Enable the site:**

```bash
sudo a2ensite myapp.conf
sudo apachectl configtest   # Always test first!
sudo systemctl reload apache2
```

> ⚠️ **WARNING:** Ensure your Laravel `.htaccess` in the `public/` directory exists and `AllowOverride All` is set. Without this, all routes except `/` will return 404.

---

### 6.3 Caddy

Caddy is the simplest option — it handles SSL automatically with zero configuration.

**Install:**

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update && sudo apt install caddy
```

**Configure `/etc/caddy/Caddyfile`:**

#### Laravel (PHP-FPM):

```caddyfile
yourdomain.com {
    root * /var/www/myapp/public
    php_fastcgi unix//var/run/php/php8.3-fpm.sock
    file_server
    encode gzip

    @dotfiles {
        path */.*
    }
    respond @dotfiles 403
}
```

#### AdonisJS / Vite SSR (Node Proxy):

```caddyfile
yourdomain.com {
    reverse_proxy 127.0.0.1:3333
    encode gzip
}
```

#### Vite Static:

```caddyfile
yourdomain.com {
    root * /var/www/myapp/dist
    file_server
    encode gzip
    try_files {path} /index.html   # for SPA client-side routing
}
```

**Start Caddy:**

```bash
sudo systemctl enable --now caddy
sudo caddy reload --config /etc/caddy/Caddyfile
```

> ✅ **Caddy automatically obtains and renews Let's Encrypt SSL certificates.** No extra step needed. Skip Section 7 if using Caddy.

---

## 7. SSL with Let's Encrypt

> Skip this section if you are using **Caddy** (it handles SSL automatically).

### Nginx SSL

```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

Certbot will automatically edit your Nginx config to add SSL. Test renewal:

```bash
sudo certbot renew --dry-run
```

### Apache SSL

```bash
sudo apt install -y certbot python3-certbot-apache
sudo a2enmod ssl
sudo certbot --apache -d yourdomain.com -d www.yourdomain.com
sudo certbot renew --dry-run
```

### Auto-renewal via systemd (already included with certbot)

```bash
sudo systemctl status certbot.timer
# Should show "active (waiting)" — renewals happen automatically
```

> ⚠️ **WARNING:** Let's Encrypt certificates expire every 90 days. Ensure your auto-renewal timer is active. A lapsed certificate will show scary browser warnings to all visitors and may break API integrations.

---

## 8. Process Management with PM2

PM2 keeps your Node app (AdonisJS, Vite SSR) alive, restarts it on crash, and manages logs.

```bash
npm install -g pm2
```

### Start your app

**AdonisJS:**

```bash
cd /var/www/myapp
pm2 start build/bin/server.js --name "myapp" --env production
```

**Vite SSR / Custom Node server:**

```bash
pm2 start build/index.js --name "myapp"
```

### Ecosystem file (recommended — commit to repo)

Create `ecosystem.config.js` in your project root:

```javascript
module.exports = {
  apps: [{
    name: 'myapp',
    script: './build/bin/server.js',
    instances: 'max',          // Use all CPU cores
    exec_mode: 'cluster',      // Enable cluster mode
    env_production: {
      NODE_ENV: 'production',
      PORT: 3333,
    },
    error_file: '/var/log/myapp/error.log',
    out_file: '/var/log/myapp/out.log',
    merge_logs: true,
    max_memory_restart: '512M',
  }]
}
```

```bash
pm2 start ecosystem.config.js --env production
pm2 save           # Save process list
pm2 startup        # Generate systemd unit to auto-start PM2 on reboot
# Run the command it outputs, e.g.:
# sudo env PATH=$PATH:/home/deployer/.nvm/versions/node/v20.x.x/bin pm2 startup systemd -u deployer --hp /home/deployer
```

### Useful PM2 commands

```bash
pm2 list                  # Show running apps
pm2 logs myapp            # Stream logs
pm2 logs myapp --lines 100
pm2 restart myapp         # Restart app
pm2 reload myapp          # Zero-downtime reload (cluster mode)
pm2 stop myapp
pm2 delete myapp
pm2 monit                 # Live CPU/memory dashboard
```

---

## 9. Queue Workers

### Laravel Queue (Redis)

Create a systemd service for the queue worker so it survives reboots:

```bash
sudo nano /etc/systemd/system/laravel-queue.service
```

```ini
[Unit]
Description=Laravel Queue Worker
After=network.target redis.service

[Service]
User=deployer
Group=www-data
WorkingDirectory=/var/www/myapp
ExecStart=/usr/bin/php artisan queue:work redis \
  --sleep=3 \
  --tries=3 \
  --max-time=3600 \
  --timeout=90
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable laravel-queue
sudo systemctl start laravel-queue
sudo systemctl status laravel-queue
```

**Laravel Horizon (advanced queue dashboard):**

```bash
composer require laravel/horizon
php artisan horizon:install
```

Add to your systemd service:

```ini
ExecStart=/usr/bin/php artisan horizon
```

### AdonisJS Queue (Bull / BullMQ)

AdonisJS queues (via `@adonisjs/bull-mq` or similar) run as part of your app process or as a separate worker. Start a dedicated worker process via PM2:

```bash
pm2 start "node ace queue:listen" --name "myapp-worker" --cwd /var/www/myapp
pm2 save
```

---

## 10. Cron Jobs

### Laravel Scheduler

Laravel's scheduler only needs one cron entry — it handles all your `schedule()` definitions internally.

```bash
crontab -e -u deployer
```

Add:

```cron
* * * * * cd /var/www/myapp && php artisan schedule:run >> /dev/null 2>&1
```

**Verify the scheduler is running:**

```bash
php artisan schedule:list
php artisan schedule:work   # Useful for local testing
```

### AdonisJS / Custom Cron Jobs

For Node-based apps, you can use `node-cron` inside your app, or define OS-level cron jobs:

```bash
crontab -e -u deployer
```

Examples:

```cron
# Run a custom Node script every hour
0 * * * * node /var/www/myapp/scripts/cleanup.js >> /var/log/myapp/cron.log 2>&1

# Database backup every night at 2am
0 2 * * * /usr/bin/pg_dump appdb > /backups/db_$(date +\%F).sql 2>&1

# Clear temp files every Sunday at midnight
0 0 * * 0 find /var/www/myapp/tmp -type f -mtime +7 -delete
```

**View current cron jobs:**

```bash
crontab -l -u deployer
```

> ⚠️ **WARNING:** Cron jobs run with limited PATH. Always use full paths to binaries (e.g., `/usr/bin/php`, not `php`). Use `which php` to find the correct path.

---

## 11. Deploy Script & Git-Based Updates

Create a reusable deploy script at `/var/www/myapp/deploy.sh`:

### Laravel Deploy Script

```bash
#!/usr/bin/env bash
set -e  # Exit on any error

APP_DIR="/var/www/myapp"
echo "▶ Deploying Laravel app..."
cd "$APP_DIR"

echo "▶ Pulling latest code..."
git pull origin main

echo "▶ Installing/updating dependencies..."
composer install --no-dev --optimize-autoloader

echo "▶ Running migrations..."
php artisan migrate --force

echo "▶ Clearing & re-caching config..."
php artisan down --render="errors::503"   # Enable maintenance mode
php artisan config:clear
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan event:cache

echo "▶ Restarting queue workers..."
php artisan queue:restart

php artisan up
echo "✅ Deployment complete!"
```

### AdonisJS Deploy Script

```bash
#!/usr/bin/env bash
set -e

APP_DIR="/var/www/myapp"
echo "▶ Deploying AdonisJS app..."
cd "$APP_DIR"

echo "▶ Pulling latest code..."
git pull origin main

echo "▶ Installing dependencies..."
npm ci

echo "▶ Building app..."
npm run build

echo "▶ Running migrations..."
node ace migration:run --force

echo "▶ Reloading PM2 process (zero downtime)..."
pm2 reload myapp

echo "✅ Deployment complete!"
```

### Vite Static Deploy Script

```bash
#!/usr/bin/env bash
set -e

APP_DIR="/var/www/myapp"
echo "▶ Deploying Vite app..."
cd "$APP_DIR"

git pull origin main
npm ci
npm run build

echo "✅ Build complete. Files available at $APP_DIR/dist"
```

**Make the script executable:**

```bash
chmod +x /var/www/myapp/deploy.sh
```

**Run a deployment:**

```bash
/var/www/myapp/deploy.sh
```

> ⚠️ **WARNING:** The `php artisan migrate --force` flag bypasses the confirmation prompt. In production this is necessary for automation, but it means migrations run immediately and irreversibly. Always back up your database before deploying schema changes.

### Optional: GitHub Actions / GitLab CI Auto-Deploy

Add a `.github/workflows/deploy.yml`:

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to server
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: deployer
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: /var/www/myapp/deploy.sh
```

---

## 12. Security Hardening Checklist

### File Permissions

```bash
# Laravel
find /var/www/myapp -type f -exec chmod 644 {} \;
find /var/www/myapp -type d -exec chmod 755 {} \;
chmod -R 775 /var/www/myapp/storage /var/www/myapp/bootstrap/cache
chown -R deployer:www-data /var/www/myapp

# Never do this (insecure):
# chmod -R 777 /var/www/myapp   ← DO NOT DO THIS
```

> ⚠️ **WARNING:** Never set `chmod 777` on your web directory. This grants read, write, and execute permission to every user on the system, including the web server process and any other compromised service. Use `775` for directories that need write access and `664` for files.

### Environment & Secrets

```bash
# Ensure .env is not web-accessible (Nginx: blocked by default, Apache: add to .htaccess)
# In Apache public/.htaccess:
# <Files ".env">
#     Require all denied
# </Files>

# Verify .env permissions
chmod 600 /var/www/myapp/.env
```

### Database Security

```bash
# Create a dedicated DB user with minimal permissions
# MySQL:
mysql -u root -p
CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'strongrandompassword';
GRANT SELECT, INSERT, UPDATE, DELETE ON appdb.* TO 'appuser'@'localhost';
FLUSH PRIVILEGES;
```

> ⚠️ **WARNING:** Never use `root` as your application database user. If your app is compromised, an attacker would have full database access including the ability to drop all tables.

### Fail2Ban (block brute force attacks)

```bash
sudo apt install -y fail2ban
sudo systemctl enable fail2ban

# Protect SSH, Nginx, and Laravel login
sudo nano /etc/fail2ban/jail.local
```

```ini
[sshd]
enabled = true
maxretry = 5
bantime = 3600

[nginx-http-auth]
enabled = true
```

```bash
sudo systemctl restart fail2ban
sudo fail2ban-client status
```

### Log Rotation

```bash
sudo nano /etc/logrotate.d/myapp
```

```
/var/log/myapp/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    sharedscripts
    postrotate
        pm2 reloadLogs
    endscript
}
```

### Quick Reference: Service Commands


| Action               | Nginx                              | Apache                               | Caddy                    | PHP-FPM                           |
| -------------------- | ---------------------------------- | ------------------------------------ | ------------------------ | --------------------------------- |
| Start                | `systemctl start nginx`            | `systemctl start apache2`            | `systemctl start caddy`  | `systemctl start php8.3-fpm`      |
| Reload (no downtime) | `systemctl reload nginx`           | `systemctl reload apache2`           | `caddy reload`           | `systemctl reload php8.3-fpm`     |
| Test config          | `nginx -t`                         | `apachectl configtest`               | `caddy validate`         | —                                 |
| View logs            | `tail -f /var/log/nginx/error.log` | `tail -f /var/log/apache2/error.log` | `journalctl -u caddy -f` | `tail -f /var/log/php8.3-fpm.log` |


---

## Summary


| Component       | Laravel                              | AdonisJS                            | Vite Static                      |
| --------------- | ------------------------------------ | ----------------------------------- | -------------------------------- |
| **Web Server**  | Nginx/Apache/Caddy + PHP-FPM         | Nginx/Apache/Caddy (reverse proxy)  | Nginx/Apache/Caddy (file server) |
| **Process Mgr** | PHP-FPM (managed by systemd)         | PM2                                 | N/A                              |
| **SSL**         | Certbot (Nginx/Apache) or Caddy auto | Certbot or Caddy auto               | Certbot or Caddy auto            |
| **Queue**       | `php artisan queue:work` via systemd | PM2 worker process                  | N/A                              |
| **Cron**        | One crontab entry → `schedule:run`   | OS crontab or `node-cron`           | N/A                              |
| **Deploy**      | `git pull` + composer + migrate      | `git pull` + npm build + pm2 reload | `git pull` + npm build           |


---

*Guide written for Ubuntu 22.04/24.04. Commands may differ slightly on other distributions.*