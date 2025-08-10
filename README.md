# Website Setup Guide

This README explains how to setup a simple HTML and CSS website using an Ubuntu server running NGINX.
I used these steps to create [freesidejockey.com](https://freesidejockey.com/).

## Table of Contents

- [Creating a Bare-bones HTML Document](#creating-a-bare-bones-html-document)
- [Adding Minimal Styling](#adding-minimal-styling)
- [Creating a DigitalOcean Droplet](#creating-a-digitalocean-droplet)
- [Purchasing a Domain Name](#purchasing-a-domain-name)
- [Writing an NGINX Config](#writing-an-nginx-config)
- [Server Setup and Deployment](#server-setup-and-deployment)

## Creating a Bare-bones HTML Document

The foundation of any website is an HTML document. Start with the minimal structure required for a valid HTML page.

### The Artifact

Create a file named `index.html` with the following content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Page Title</title>
</head>
<body>
	<h1>Page Header</h1>
	<p>Page Text</p>
</body>
</html>
```

### The Explanation

- `<!DOCTYPE html>`: Declares this as an HTML5 document, preventing browser quirks mode
- `<html lang="en">`: Root element with language specification for accessibility and SEO
- `<head>`: Contains metadata not displayed on the page
    - `<meta charset="UTF-8">`: Ensures proper character encoding for all text
    - `<meta name="viewport"...>`: Prevents mobile browsers from zooming out to fit desktop layouts
    - `<title>`: Text shown in browser tab and search results
- `<body>`: Contains all visible content

### Testing

Open `index.html` in a web browser. A heading and paragraph should appear with basic browser default styling - black text on white background.

### Code Reference

Code available on [GitHub](https://github.com/freesidejockey/freeside-jockey-personal-site/commit/a46c1656c8dcfce5bb28745a6726167522ea3988).

## Adding Minimal Styling

CSS provides visual styling for web pages. This example uses a minimal cyberpunk theme with washed-out colors for better readability.

### The Artifact

Replace the `index.html` content with:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Website</title>
  <style>
    *,
    *::before,
    *::after {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
      max-width: 800px;
      margin: 0 auto;
      padding: 20px;
      line-height: 1.6;
      background-color: #1a1a1f;
      color: #d4d4d8;
    }

    h1 {
      color: #4ade80;
      text-align: center;
      text-shadow: 0 0 8px rgba(74, 222, 128, 0.15);
      margin-bottom: 1.5em;
      font-weight: 600;
    }

    p {
      font-size: 16px;
      margin-bottom: 1em;
      color: #a1a1aa;
    }
  </style>
</head>

<body>
  <h1>How This Website Was Built</h1>
  <p>The art of building something so simple, even I (and you) can do it.</p>

  <h2>About This Project</h2>
  <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna
    aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
  </p>

  <p>Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur
    sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>

  <h2>Technical Details</h2>
  <p>Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem
    aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo.</p>

  <p>Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos
    qui ratione voluptatem sequi nesciunt. Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet, consectetur,
    adipisci velit.</p>

  <p>At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium voluptatum deleniti atque
    corrupti quos dolores et quas molestias excepturi sint occaecati cupiditate non provident, similique sunt in culpa
    qui officia deserunt mollitia animi.</p>

  <h2>Future Plans</h2>
  <p>Ut enim ad minima veniam, quis nostrum exercitationem ullam corporis suscipit laboriosam, nisi ut aliquid ex ea
    commodi consequatur. Quis autem vel eum iure reprehenderit qui in ea voluptate velit esse quam nihil molestiae
    consequatur, vel illum qui dolorem eum fugiat quo voluptas nulla pariatur.</p>
</body>
</html>
```

### Testing

Open `index.html` in a web browser. A well-formatted web page should display.

### Code Reference

[GitHub commit](https://github.com/freesidejockey/freeside-jockey-personal-site/commit/08343bc85573d57295ef35c96613dd41dafc5398)

## Creating a DigitalOcean Droplet

A server is required to deploy the website. These instructions use a DigitalOcean droplet running Ubuntu. Skip this section if SSH access to an Ubuntu server is already available.

### Prerequisites

1. Sign up for [DigitalOcean](https://www.digitalocean.com)
2. Generate an API Token at [https://cloud.digitalocean.com/account/api/tokens](https://cloud.digitalocean.com/account/api/tokens)
3. Save the token securely (e.g., in a password manager)

### Install and Configure DigitalOcean CLI

#### macOS with Homebrew

```bash
# Install the CLI
brew install doctl

# Authenticate to account
doctl auth init
# Paste the token string when prompted
```

For other operating systems, follow the [official installation guide](https://docs.digitalocean.com/reference/doctl/how-to/install/).

### Setup SSH Key

```bash
# Generate SSH key pair (if one doesn't exist)
ssh-keygen -t ed25519 -f ~/.ssh/{local-key-name} -C "{identifying-comment}"

# Add the public key to DigitalOcean
doctl compute ssh-key import {remote-key-name} --public-key-file ~/.ssh/{local-key-name}.pub

# Get the SSH key ID (note the ID number)
doctl compute ssh-key list
```

### Create the Droplet

```bash
doctl compute droplet create {droplet-name} \
  --region nyc3 \
  --image ubuntu-22-04-x64 \
  --size s-1vcpu-1gb \
  --ssh-keys {YOUR_SSH_KEY_ID} \
  --wait
```

## Purchasing a Domain Name

1. Purchase a domain from a domain provider (e.g., Namecheap, GoDaddy, etc.)
2. Obtain the public IP address of the DigitalOcean droplet
3. Configure DNS by adding A Records:
    - Create an A Record for the root domain pointing to the droplet's IP
    - Create an A Record for www subdomain pointing to the droplet's IP

## Writing an NGINX Config

Create an NGINX configuration file locally. This will be automatically modified by Certbot later.

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/example.com;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

## Server Setup and Deployment

### Initial Server Configuration

```bash
# SSH into the server
ssh root@{server-ip}

# Update system packages
sudo apt update

# Install NGINX
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx

# Verify NGINX status
sudo systemctl status nginx
```

### Configure Firewall

```bash
# Enable UFW firewall
sudo ufw enable

# Allow SSH (port 22)
sudo ufw allow ssh

# Allow HTTP and HTTPS
sudo ufw allow 'Nginx Full'

# Check firewall status
sudo ufw status
```

### Create Website Directory

```bash
# Create site directory
sudo mkdir -p /var/www/{domain.com}
sudo chown -R $USER:$USER /var/www/{domain.com}
sudo chmod -R 755 /var/www/{domain.com}
```

### Upload Files

```bash
# Exit server
exit

# From local machine, upload files
scp index.html root@{server-ip}:/var/www/{domain.com}/
scp nginx root@{server-ip}:/etc/nginx/sites-available/{domain.com}
```

### Enable Site Configuration

```bash
# SSH back into server
ssh root@{server-ip}

# Enable the site
sudo ln -s /etc/nginx/sites-available/{domain.com} /etc/nginx/sites-enabled/

# Remove default site
sudo rm /etc/nginx/sites-enabled/default

# Test configuration
sudo nginx -t

# Reload NGINX
sudo systemctl reload nginx
```

### Setup SSL Certificate

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Obtain SSL certificate
sudo certbot --nginx -d {domain.com} -d www.{domain.com}

# Test auto-renewal
sudo certbot renew --dry-run
```

## Completion

The website is now live and accessible via HTTPS at the configured domain. The server will automatically renew SSL certificates and serve the HTML content with the configured styling.
