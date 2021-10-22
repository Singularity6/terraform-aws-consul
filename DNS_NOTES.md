# Notes from hacking on dnsmasq

# have a dnsmasq config
ubuntu@ip-10-1-0-26:~$ cat /etc/dnsmasq.d/10-consul
# Enable forward lookup of the 'consul' domain:
server=/consul/169.254.1.1#8600
listen-address=169.254.1.1
bind-interfaces

# add dnsmasq to DNS in resolved.confOB
ubuntu@ip-10-1-0-26:~$ cat /etc/systemd/resolved.conf
[Resolve]
DNS=169.254.1.1
restart dnsmasq, systemd-resolved, docker

ubuntu@ip-10-1-0-26:~$ dig +short google.com
142.250.69.206
ubuntu@ip-10-1-0-26:~$ dig +short consul.service.consul
10.1.0.26

Restart dnsmasq, systemd-resolved, docker

ubuntu@ip-10-1-0-26:~$ sudo docker run -e CONSUL_HTTP_ADDR=169.254.1.1:8500 -e CONSUL_RPC_ADDR=169.254.1.1:8400 busybox ping google.com
PING google.com (142.250.69.206): 56 data bytes
64 bytes from 142.250.69.206: seq=0 ttl=89 time=9.392 ms
^C
--- google.com ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 9.392/9.392/9.392 ms

ubuntu@ip-10-1-0-26:~$ sudo docker run -e CONSUL_HTTP_ADDR=169.254.1.1:8500 -e CONSUL_RPC_ADDR=169.254.1.1:8400 busybox ping consul.service.consul
PING consul.service.consul (10.1.0.26): 56 data bytes
64 bytes from 10.1.0.26: seq=0 ttl=64 time=0.034 ms
64 bytes from 10.1.0.26: seq=1 ttl=64 time=0.046 ms
^C
--- consul.service.consul ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.034/0.040/0.046 ms

ubuntu@ip-10-1-0-26:~$ sudo docker run -e CONSUL_HTTP_ADDR=169.254.1.1:8500 -e CONSUL_RPC_ADDR=169.254.1.1:8400 busybox cat /etc/resolv.conf
# This file is managed by man:systemd-resolved(8). Do not edit.
#
# This is a dynamic resolv.conf file for connecting local clients directly to
# all known uplink DNS servers. This file lists all configured search domains.
#
# Third party programs must not access this file directly, but only through the
# symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,
# replace this symlink by a static file or a different symlink.
#
# See man:systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.
nameserver 169.254.1.1
nameserver 10.1.0.2
search us-west-2.compute.internal

