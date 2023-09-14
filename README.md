## Haproxy has been TESTED and Working on Centos 7/8 By MUGHU

This HAProxy configuration has been thoroughly tested and verified to work seamlessly on CentOS 7 and CentOS 8 by MUGHU. It ensures load balancing and high availability for web services by distributing incoming traffic across multiple backend servers. The configuration includes the installation of essential packages, firewall setup, and detailed steps for setting up HAProxy.

By following these instructions, you can confidently deploy and manage HAProxy for your CentOS 7 or CentOS 8 system, providing efficient and reliable load balancing for your web applications. MUGHU has rigorously tested and documented this configuration to ensure its compatibility and functionality on these CentOS versions, making it a reliable choice for your server infrastructure.

> [!NOTE]
> You can change the version with another version on the official website of haproxy https://www.haproxy.org/

## Install required packages and HAProxy
```
sudo yum install gcc pcre-devel tar make -y

wget http://www.haproxy.org/download/2.0/src/haproxy-2.0.7.tar.gz -O ~/haproxy.tar.gz
tar xzvf ~/haproxy.tar.gz -C ~/
cd ~/haproxy-2.0.7
make TARGET=linux-glibc
sudo make install
```
## Create directories and files
```
sudo mkdir -p /etc/haproxy
sudo mkdir -p /var/lib/haproxy 
sudo touch /var/lib/haproxy/stats

sudo ln -s /usr/local/sbin/haproxy /usr/sbin/haproxy
sudo cp ~/haproxy-2.0.7/examples/haproxy.init /etc/init.d/haproxy
sudo chmod 755 /etc/init.d/haproxy
sudo systemctl daemon-reload

sudo chkconfig haproxy on
sudo useradd -r haproxy
```
## Verify HAProxy version
```
haproxy -v
```
## Configure firewall
```
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --permanent --zone=public --add-port=8181/tcp
sudo firewall-cmd --reload
```
## Edit HAProxy configuration
```
sudo vi /etc/haproxy/haproxy.cfg
```
## Paste the HAProxy configuration here
```
global
   log /dev/log local0
   log /dev/log local1 notice
   chroot /var/lib/haproxy
   stats timeout 30s
   user haproxy
   group haproxy
   daemon

defaults
   log global
   mode http
   option httplog
   option dontlognull
   timeout connect 5000
   timeout client 50000
   timeout server 50000

frontend http_front
   bind *:80
   stats uri /haproxy?stats
   default_backend http_back

backend http_back
   balance roundrobin
   server server_name1 SERVER_IP_1:80 check
   server server_name2 SERVER_IP_2:80 check
```
## Restart HAProxy
```
sudo systemctl restart haproxy
```

> [!NOTE]
> ALL DONE , now you can check haproxy status with visit your ip with the end of permalink **/haproxy?stats**
