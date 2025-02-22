# Nginx Configuration Guide

This document provides basic Nginx configurations for load balancing, serving static files, and handling redirects.

## Nginx Configuration Location

By default, the main Nginx configuration file (`nginx.conf`) is placed in:

```sh
cd /usr/local/etc/nginx
```

---

## 1. Load Balancing Configuration

Nginx uses the **round-robin** algorithm for load balancing by default. Below is a basic configuration for setting up load balancing across three backend servers:

```nginx
http {
    include mime.types;
    
    # Define an upstream group named 'backendserver' with three backend servers
    upstream backendserver {
        server 127.0.0.1:1111;
        server 127.0.0.1:2222;
        server 127.0.0.1:3333;
    }

    server {
        listen 8080; # The server listens on port 8080

        location / {
            proxy_pass http://backendserver/; # Forward requests to the backend server group
        }
    }
}

# Required events block
events {}
```

---

## 2. Serving Static Files and Redirects

This configuration is used for serving static files and implementing basic URL rewrites and redirects.

```nginx
http {
    include mime.types;
    
    server {
        listen 8080; # The server listens on port 8080
        root /Users/ajeeshrs/Desktop/learn/nginx/fe; # Set the root directory for static files

        # Serve static files from the root directory
        location /bikes {
            root /Users/ajeeshrs/Desktop/learn/nginx/fe;
        }

        # Redirect /cars to the /bikes directory
        location /cars {
            alias /Users/ajeeshrs/Desktop/learn/nginx/fe/bikes;
        }

        # Serve /supercars with a fallback to index.html if the file is missing
        location /supercars {
            root /Users/ajeeshrs/Desktop/learn/nginx/fe;
            try_files /supercars/sup.html /index.html =404;
        }

        # Rewrite URLs from /number/{value} to /speed/{value}
        rewrite ^/number/(\w+) /speed/$1;

        # Serve /speed/[0-299] from the root and fallback to index.html
        location ~* /speed/[0-299] {
            root /Users/ajeeshrs/Desktop/learn/nginx/fe;
            try_files /index.html =404;
        }

        # Redirect /supspeed to /supercars with a 307 Temporary Redirect
        location /supspeed {
            return 307 /supercars;
        }
    }
}

# Required events block
events {}
```

---

## Explanation of Key Features

- **Load Balancing:**

  - Uses `upstream backendserver` to define multiple backend servers.
  - Requests are forwarded using `proxy_pass`.
  - Default round-robin algorithm distributes traffic evenly.

- **Serving Static Files:**

  - The `root` directive defines the directory where static files are served from.
  - `alias` can be used for mapping one path to another.

- **Rewrites & Redirects:**

  - `rewrite` modifies the requested URL before processing it.
  - `try_files` checks for files in order and falls back to `index.html` or returns a 404.
  - `return 307` provides a temporary redirect.

This guide should help you configure Nginx for basic load balancing and static file serving. 🚀

