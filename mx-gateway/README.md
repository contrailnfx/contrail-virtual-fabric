
#### Contrail Virtual Fabric
# MX Gateway

# Topology

```
   +-------------+
   |  ext-host   |
   | 172.16.1.10 |
   +-------------+
          |         provider-network-1: 172.16.11.0/24
          |         provider-network-2: 172.16.12.0/24
  +-----------------+-----------------+
  |  xe-0/0/1       |                 |
  |  172.16.1.254   |                 |
  |                                   |
  | gateway-2 (External Gateway)      |
  |  em0: 10.6.8.41                   |
  |  lo0: 10.6.0.41                   |
  |  ASN: 64041                       |
  |                                   |
  |  10.6.30.1/30   |                 |
  |  xe-0/0/0       |                 |
  +-----------------+-----------------+
        |
 -------+-------------------------------
        |
  +-----------------+-----------------+
  |  xe-0/0/0       |                 |
  |  10.6.30.2/30   |                 |
  |                                   |
  | gateway-1 (Contrail Gateway)      |
  |  em0: 10.6.8.31                   |
  |  lo0: 10.6.0.31                   |
  |  ASN: 64031                       |
  |                                   |
  |  10.6.20.1/30   |                 |
  |  xe-0/0/1       |                 |
  +-----------------+-----------------+
        |
        |
  +----------------+------------------+
  |  xe-0/0/0      |                  |
  |  10.6.20.2/30  |                  |
  |                                   |
  | leaf-1                            |
  |  em0: 10.6.8.11                   |
  |  lo0: 10.6.0.11                   |
  |  ASN: 64011                       |
  |                                   |
  |  xe-0/0/1  |  xe-0/0/2 | xe-0/0/3 |
  +------------+-----------+----------+
       |
     br-r1
       |
  +----------------------+
  | contrail   10.6.8.1  |
  | openstack  10.6.8.2  |
  | csn        10.6.8.3  |
  | compute-1  10.6.8.4  |
  | compute-2  10.6.8.5  |
  +----------------------+
       |                    management:      10.6.8.0/24
     br-int                 loopback:        10.6.0.0/24
   10.6.8.254               gateway-leaf:    10.6.20.0/24
       |                    gateway-gateway: 10.6.30.0/24
       |                    rack-1:          10.6.11.0/24
    HAProxy

  Contrail web UI:   https://<host>:8143
```


## Resource
```
                  vCPU    memory(GB)    disk(GB)    OS
contrail           4        64            150      CentOS 7.5-1805
openstack          4        64            150      CentOS 7.5-1805
csn                2        16             80      CentOS 7.5-1805
compute-1          4        16             80      CentOS 7.5-1805
compute-2          4        16             80      CentOS 7.5-1805
ext-host           1         4             40      CentOS 7.5-1805
leaf-1-re          1         1                     Junos 18.1
leaf-1-pfe         1         2                     Junos 18.1
gateway-1-re       1         1                     Junos 18.3R1
gateway-1-pfe      4         2                     Junos 18.3R1
gateway-2-re       1         1                     Junos 18.3R1
gateway-2-pfe      4         2                     Junos 18.3R1
----------------------------------------------------------------
Total              31       189
```



