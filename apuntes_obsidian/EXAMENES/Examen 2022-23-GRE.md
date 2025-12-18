![[tunelGRE2022-23-segunda.png]]


![[tunelGRE-emiliano.png]]

**PC1 y PC3 como si estuvieran en la misma VLAN**
**PC2 y PC4 en otra VLAN**

**OvS-gestion-1-tunelGRE-OvS-gestion-2**

# Pregunta 1 (direccionamiento + rutas)
**Definir IP a interfaces y tablas de encaminamiento IP**

Voy a usar VLAN 10 = (PC1, PC3) y VLAN 20 = (PC2, PC4). 

### Redes IP
- VLAN 10 (PC1–PC3): 192.168.1.0/24
- VLAN 20 (PC2–PC4): 192.168.2.0/24
- OvS1–R1 (Ethernet 100M): 192.168.10.0/24
- R1–R2 (ATM): 192.168.30.0/30
- R2–OvS2 (Ethernet 100M): 192.168.20.0/24

### IPs por equipo/interfaz

**PCs**
- PC1: 192.168.1.1/24 GW 192.168.1.254
- PC2: 192.168.2.2/24 GW 192.168.2.254
- PC3: 192.168.1.3/24 GW 192.168.1.254
- PC4: 192.168.2.4/24 GW 192.168.2.254

**OvS-gestion-1 (tiene el “router” entre VLANs)**
- gw10 (internal): 192.168.1.254/24
- gw20 (internal): 192.168.2.254/24
- enlace a R1 (p.ej. eth3): 192.168.10.1/24 GW por defecto 192.168.10.254

**OvS-gestion-2**
- enlace a R2 (p.ej. eth3): 192.168.20.1/24 GW por defecto 192.168.20.254

**Routers**
- R1 hacia OvS1: 192.168.10.254/24
- R1 hacia R2 (ATM): 192.168.30.1/30
- R2 hacia R1 (ATM): 192.168.30.2/30
- R2 hacia OvS2: 192.168.20.254/24

### Encaminamiento (rutas mínimas)
**PC1/PC3**
- default: 0.0.0.0/0 via 192.168.1.254

**PC2/PC4**
- default: 0.0.0.0/0 via 192.168.2.254

**OvS-gestion-1**
- default: 0.0.0.0/0 via 192.168.10.254

**OvS-gestion-2**
- default: 0.0.0.0/0 via 192.168.20.254

**R1**
- 192.168.20.0/24 via 192.168.30.2

**R2**
- 192.168.10.0/24 via 192.168.30.1
## Pregunta 2 (comandos OvS: GRE + learning-switch + router en OvS1)
**Instrucciones en OvS1 y 2 para que router interconecta las VLAN este en OvS1, los OvS trabajan como learning-switch.**

**ovs-vsctl add-port br0 eth0 tag=vlan, ovs-vsctl add-port br0 eth0 untag=vlan, ovs-vsctl add-port br0 eth15 untag=… trunk=… -- set Interface eth15 type=gre…**

Borramos todos los bridges

ovs-vsctl del-br br0
ovs-vsctl del-br br1
ovs-vsctl del-br br2
ovs-vsctl del-br br3

> Ajusta ethX según tu GNS3 (yo asumo: OvS1 eth1=PC1, eth2=PC2, eth3=R1; OvS2 eth1=PC3, eth2=PC4, eth3=R2).

### OvS-gestion-1

```bash
# Bridge
ovs-vsctl del-br br0
ovs-vsctl del-br br1
ovs-vsctl del-br br2
ovs-vsctl del-br br3
ovs-vsctl add-br br0

# Puertos "router" (internos) para interconectar VLANs
ovs-vsctl add-port br0 gw10 -- set interface gw10 type=internal
ovs-vsctl add-port br0 gw20 -- set interface gw20 type=internal
ifconfig gw10 192.168.1.254 netmask 255.255.255.0 up
ifconfig gw20 192.168.2.254 netmask 255.255.255.0 up
sysctl -w net.ipv4.ip_forward=1

# Puertos PCs (untagged)
ovs-vsctl add-port br0 eth1 untag=10
ovs-vsctl add-port br0 eth2 untag=20
ovs-vsctl add-port br0 eth3 tag=10,20
ifconfig eth3 192.168.20.51 netmask 255.255.255.0 broadcast 192.168.20.255 up

# Túnel GRE entre switches (trunk con las VLANs)
ovs-vsctl add-port br0 tunel1 -- set Interface tunel1 type=gre options:remote_ip=192.168.20.1

route add default gw 192.168.10.254
# Como es learning puedo saltarme lo de los flujos...

```

### OvS-gestion-2

```bash
# Bridge
ovs-vsctl del-br br0
ovs-vsctl del-br br1
ovs-vsctl del-br br2
ovs-vsctl del-br br3
ovs-vsctl add-br br0

# Puertos PCs (untagged)
ovs-vsctl add-port br0 eth1 untag=10
ovs-vsctl add-port br0 eth2 untag=20
ovs-vsctl add-port br0 eth3 tag=10,20
ifconfig eth3 192.168.20.1 netmask 255.255.255.0 broadcast 192.168.10.255 up

# Túnel GRE entre switches (trunk con las VLANs)
ovs-vsctl add-port br0 tunel2 -- set Interface tunel1 type=gre options:remote_ip=192.168.10.1

route add default gw 192.168.20.254
# Como es learning puedo saltarme lo de los flujos...
```

# 3
Apareceran 2 tramas

![[tunelGRE_emiliano.png]]
Se omite preambulo y CRC en wireshark

# Como lo tiene el:
![[Pasted image 20251218180923.png]]
![[Pasted image 20251218180915.png]]