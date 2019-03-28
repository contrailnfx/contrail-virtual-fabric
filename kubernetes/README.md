
# Contrail Virtual Fabric
#### Contrail and Kubernetes

# Topology

```
   +-------------+
   |   client    |
   | 172.16.1.10 |
   +-------------+
          |
        br-ext
          |
  +-----------------+-----------------+
  |  xe-0/0/0       |  xe-0/0/1       |
  |  172.16.1.254   |                 |
  |                                   |
  | vmx-gw1                           |
  |  em0: 10.6.8.31                   |
  |  lo0: 10.6.0.31                   |
  |  ASN: 64031                       |
  |                                   |
  |  10.6.30.1/30   |                 |
  |  xe-0/0/2       |                 |
  +-----------------+-----------------+
        |
        |
        |
        |
  +----------------+------------------+
  |  xe-0/0/0      |  xe-0/0/1        |
  |  10.6.20.2/30  |  10.6.20.10/30   |
  |                                   |
  | vqfx-leaf1                        |
  |  em0: 10.6.8.11                   |
  |  lo0: 10.6.0.11                   |
  |  ASN: 64011                       |
  |                                   |
  |  xe-0/0/4  |  xe-0/0/2 | xe-0/0/3 |
  +------------+-----------+----------+
       |
       |
     br-r1
       |
       |
  +---------------------+
  | master-1  10.6.8.1  |
  | master-2  10.6.8.2  |
  | master-3  10.6.8.3  |
  | node-1    10.6.8.4  |
  | node-2    10.6.8.5  |
  | node-3    10.6.8.6  |
  | node-4    10.6.8.7  |
  +---------------------+
       |                         management: 10.6.8.0/24
     br-int                      loopback:   10.6.0.0/24
   10.6.8.254                    spine-leaf: 10.6.20.0/24 10.6.30.0/24
       |                         rack-1:     10.6.11.0/24
    HAProxy

  Contrail web UI:   https://<host>:8143
```


## Resource
```
                  vCPU    memory(GB)    disk(GB)    OS
master-1           5        60            150      CentOS 7.5-1805
master-2           5        60            150      CentOS 7.5-1805
master-3           5        60            150      CentOS 7.5-1805
node-1             3        16             80      CentOS 7.5-1805
node-2             3        16             80      CentOS 7.5-1805
node-3             3        16             80      CentOS 7.5-1805
node-4             3        16             80      CentOS 7.5-1805
vqfx-leaf1-re      1         1                     Junos 18.1
vqfx-leaf1-pfe     1         2                     Junos 18.1
vmx-gw1-vcp        1         1                     Junos 18.3R1
vmx-gw1-vfp        4         2                     Junos 18.3R1
----------------------------------------------------------------
Total              34       250
```



