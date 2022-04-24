# Nginx SSl/TSL Load Balancer solution

### Created an EC2 VM (Ubuntu) named Nginx LB.

### Opened TCP port 80 for HTTP connections, also open TCP port 443 for secured HTTPS connections


## Update and Install Nginx
sudo apt update
sudo apt install nginx
Configure Nginx LB using Web Servers’ names defined in /etc/hosts

Update /etc/hosts file for local DNS with Web Servers’ names (Web1,Web2 and Web 3) and their local IP addresses
sudo vi /etc/hosts

172.31.30.39  web1
172.31.18.76  web2
172.31.18.189  web3

sudo vi /etc/nginx/nginx.conf

#insert following configuration into http section

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
    server Web3 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;
Restart Nginx and make sure the service is up and running

sudo systemctl restart nginx
sudo systemctl status nginx

# I used one of my parked domains workachoo.com

# created a hosted zone in Route53

NS and SOA records automatically created

## nameservers from route53 updated on mydomain.com


# create an elastic IP and associated it to our instance

18.130.62.8

## 2 records created in Route 53

blank name and elastic ip address

www and elastic ip address

# In etc/nginx/nginx.conf   update server_name from www.domain.com to www.workachoo.com

# run workachoo.com in the browser window

Our site shows up

# domain pointing is successful









