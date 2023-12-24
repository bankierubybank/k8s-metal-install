# OpenShift 4 Bare Metal Install - User Provisioned Infrastructure (UPI)

## Architecture Diagram

###### Information

- Cluster Name: k8s
- Base Domain: nsth.demo

## Configure Environmental Services

1. Configure BIND DNS

   Apply configuration

   ```bash
   cp ~/k8s-metal-install/dns/named.conf /etc/named.conf
   cp -R ~/k8s-metal-install/dns/zones /etc/named/
   ```

   Configure the firewall for DNS

   ```bash
   firewall-cmd --add-port=53/udp --permanent
   firewall-cmd --add-port=53/tcp --permanent
   firewall-cmd --reload
   ```

   Enable and start the service

   ```bash
   systemctl enable named
   systemctl start named
   systemctl status named
   ```

1. Configure HAProxy

   Copy HAProxy config

   ```bash
   cp ~/k8s-metal-install/haproxy.cfg /etc/haproxy/haproxy.cfg
   ```

   Configure the Firewall

   > Note: Opening port 9000 in the external zone allows access to HAProxy stats that are useful for monitoring and troubleshooting. The UI can be accessed at: `http://{k8s-bastion_IP_address}:9000/stats`

   ```bash
   firewall-cmd --add-port=6443/tcp --permanent # kube-api-server on control plane nodes
   firewall-cmd --add-service=http --permanent # web services hosted on worker nodes
   firewall-cmd --add-service=https --permanent # web services hosted on worker nodes
   firewall-cmd --add-port=9000/tcp --permanent # HAProxy Stats
   firewall-cmd --reload
   ```

   Enable and start the service

   ```bash
   setsebool -P haproxy_connect_any 1 # SELinux name_bind access
   systemctl enable haproxy
   systemctl start haproxy
   systemctl status haproxy
   ```
