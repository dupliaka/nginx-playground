# Nginx-playground

This project contains use cases for Nginx sever

## Installation
```bash
yum install nginx
```
## How to use 

### Static data sharing
Configure the server so it can recognize request and response with a resource. 
As an example lets create an image server that waits for /images request and responds by image
For this purpose reconfigure the `/etc/nginx/nginx.conf`
#### Create test data

```bash
mkdir /data/www
mkdir /data/images
cp ~/test_image.png /data/images/test_image.png
````
#### Configure Nginx
```bash
...
    server {
    
   	listen		8080;
   	server_name	image_server;
   	
   	location / {
   		root /data/www;
   	}   
   	
   	location /images/ {
   		root /data;
   	}
...
```
#### Run nginx

```bash
nginx
```
#### Test image server
```bash 
wget http://localhost:8080/images/test_image.png
```
Returns image `test_image.png`
Logs available  `/var/log/nginx/error.log` and `/var/log/nginx/access.log`

## Proxy configuration
Next setup as a proxy server, which means a server that receives requests, passes them to the proxied servers, retrieves responses from them, and sends them to the clients. 

For this use case we create Ann server that returns images or passes request to Bob server
#### Prepare index file
```bash
mkdir /data/up1
echo "<html><head>Hello World!</head></html>" > /data/up1/index.html
```
#### Reconfigure Nginx
```bash
...
    # This server will serve for proxied requests
    server {
    	server_name Bob;
        listen 8080;
        root /data/up1;

        location / {
        }
    }
    # This is the proxy server. 
    # It response with images or requests Bob server.
    server {
        server_name Ann;
        location / {
            proxy_pass http://localhost:8080;
        }

        location /images/ {
            root /data;
        }
    }
...
```

For more info checkout [Nginx documentation](http://nginx.org/ru/docs/beginners_guide.html)