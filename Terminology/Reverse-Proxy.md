A **Reverse Proxy** is a server or service that sits between client devices and a web server, forwarding client requests to the web server and returning the server's responses back to the clients. Unlike a forward proxy, which handles requests on behalf of clients seeking resources from the internet, a reverse proxy manages requests on behalf of servers.

# Creating a Reverse-Proxy Using Nginx

Creating a **Reverse-Proxy** in Nginx involves configuring Nginx to forward client requests to backend servers. Here's a basic example of setting up a reverse proxy using Nginx:

1. Ensure that Nginx is installed on your server. You can typically install it using your system's package manager. For example, on Ubuntu:

``` bash
sudo apt-get update 
sudo apt-get install nginx
```

2. Open the Nginx configuration file. This is often located at `/etc/nginx/conf.d/`.

```nginx
server {
    listen 80;

    server_name example.com;

    location / {
        proxy_pass http://localhost:8001;
    }
}
```

Replace `example.com` with your domain or server IP, and set the `proxy_pass` directive to the address of your backend server.

3. After making changes, restart Nginx to apply the configuration:

``` bash
sudo systemctl restart nginx
```
