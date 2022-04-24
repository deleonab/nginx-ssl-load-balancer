## Nginx SSl/TSL Load Balancer solution

# Created an EC2 VM (Ubuntu) named Nginx LB.

### Opened TCP port 80 for HTTP connections, also open TCP port 443 for secured HTTPS connections


## Update and Install Nginx
sudo apt update
sudo apt install nginx
Configure Nginx LB using Web Servers’ names defined in /etc/hosts

### Update /etc/hosts file for local DNS with Web Servers’ names (Web1,Web2 and Web 3) and their local IP addresses
sudo vi /etc/hosts

172.31.30.39         web1
172.31.18.76         web2
172.31.18.189        web3

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

### comment out this line
###       include /etc/nginx/sites-enabled/*;

### Restart Nginx and make sure the service is up and running

sudo systemctl restart nginx
sudo systemctl status nginx

# I used one of my parked domains workachoo.com

### created a hosted zone in Route53

NS and SOA records automatically created

### nameservers from route53 updated on mydomain.com


### create an elastic IP and associated it to our instance

18.130.62.8

### 2 records created in Route 53

blank name and elastic ip address

www and elastic ip address

### In etc/nginx/nginx.conf   update server_name from www.domain.com to www.workachoo.com



### Check that your Web Servers can be reached from your browser using new domain name using HTTP protocol – http://<your-domain-name.com>

Configure Nginx to recognize your new domain name
Update your nginx.conf with server_name www.<your-domain-name.com> instead of server_name www.domain.com

# run workachoo.com in the browser window

Our site shows up

### domain pointing is successful

## Install certbot and request for an SSL/TLS certificate
## Make sure snapd service is active and running

sudo systemctl status snapd

### Install certbot

sudo snap install --classic certbot

Request your certificate (just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file so make sure you have updated it on step 4).

sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
Test secured access to your Web Solution by trying to reach https://<your-domain-name.com>

You shall be able to access your website by using HTTPS protocol (that uses TCP port 443) and see a padlock pictogram in your browser’s search string.
Click on the padlock icon and you can see the details of the certificate issued for your website.



Set up periodical renewal of your SSL/TLS certificate
By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

You can test renewal command in dry-run mode

sudo certbot renew --dry-run
Best pracice is to have a scheduled job that to run renew command periodically. Let us configure a cronjob to run the command twice a day.

To do so, lets edit the crontab file with the following command:

crontab -e
Add following line:

* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1
You can always change the interval of this cronjob if twice a day is too often by adjusting schedule expression









































