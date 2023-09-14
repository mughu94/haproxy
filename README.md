# haproxy
install haproxy centos 7/8

# Install required packages and HAProxy

```bash
sudo yum install gcc pcre-devel tar make -y

wget http://www.haproxy.org/download/2.0/src/haproxy-2.0.7.tar.gz -O ~/haproxy.tar.gz
tar xzvf ~/haproxy.tar.gz -C ~/
cd ~/haproxy-2.0.7
make TARGET=linux-glibc
sudo make install
