# haproxy
install haproxy centos 7/8

sudo yum install gcc pcre-devel tar make -y

wget http://www.haproxy.org/download/2.0/src/haproxy-2.0.7.tar.gz -O ~/haproxy.tar.gz
tar xzvf ~/haproxy.tar.gz -C ~/
cd ~/haproxy-2.0.7
make TARGET=linux-glibc
sudo make install


sudo mkdir -p /etc/haproxy
sudo mkdir -p /var/lib/haproxy 
sudo touch /var/lib/haproxy/stats

sudo ln -s /usr/local/sbin/haproxy /usr/sbin/haproxy
sudo cp ~/haproxy-2.0.7/examples/haproxy.init /etc/init.d/haproxy
sudo chmod 755 /etc/init.d/haproxy
sudo systemctl daemon-reload

sudo chkconfig haproxy on
sudo useradd -r haproxy

haproxy -v

sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --permanent --zone=public --add-port=8181/tcp
sudo firewall-cmd --reload

sudo vi /etc/haproxy/haproxy.cfg

## file /etc/haproxy/haproxy.cfg
==============================================
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
   server server_name1 23.95.5.105:80 check
   server server_name2 23.95.50.110:80 check
===================================================
## end file /etc/haproxy/haproxy.cfg

sudo systemctl restart haproxy
