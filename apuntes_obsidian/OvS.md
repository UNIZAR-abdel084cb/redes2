### 1. `ovs-vsctl set br br0 stp_enable=false`

Este comando **deshabilita el protocolo STP** (Spanning Tree Protocol) en el bridge `br0`1.

- **En el contexto de la práctica:** Se utiliza para evitar que el switch genere o procese "información innecesaria" (mensajes de control de STP) cuando se busca tener un control manual total sobre los flujos2.
    
- **Riesgo:** Al desactivarlo, el switch ya no detectará ni bloqueará bucles físicos de forma automática. Si existen múltiples caminos entre switches y no se gestionan manualmente, se podría producir una tormenta de tráfico3333.
    

### 2. `ovs-vsctl set-fail-mode br0 secure`

Este comando establece el "modo de fallo" del bridge a **seguro (secure)**4. Su efecto es fundamental en relación al concepto de **learning switch**:

- **Comportamiento por defecto (Standalone):** Normalmente, un bridge de OvS viene en modo "standalone". Esto significa que, si no detecta una conexión con un controlador SDN, se comporta automáticamente como un **learning switch** convencional (aprende direcciones MAC y conmuta el tráfico por sí solo)5.
    
- **Modo Seguro (Secure):** Al activar este modo, si el switch no tiene conexión con el controlador, **deja de actuar como un learning switch**66. En este estado, el switch no tiene ningún flujo definido por defecto y no aprenderá nada automáticamente77.
    
- **Consecuencia inmediata:** El tráfico (como un simple `ping`) dejará de funcionar inmediatamente88. A partir de este momento, es obligatorio **definir todos los flujos manualmente** mediante comandos como `ovs-ofctl add-flow` para que el switch sepa qué hacer con los paquetes9999.

```bash
# Tramas en gw1
# Si va al PC3, lo sacamos por el puerto 3 (destino: 192.168.100.3)
ovs-ofctl add-flow br0 in_port=1,dl_type=0x0806,nw_dst=192.168.100.3,actions=output:3

# Cualquier otra cosa, devuelvela por el puerto 5, etiquetadas con VLAN 100
ovs-ofctl add-flow br0 in_port=1,dl_type=0x0806,actions=mod_vlan_vid=100,output:5

# Tramas en gw2
# Si va al PC4, lo sacamos por el puerto 4 (destino: 192.168.200.4)
ovs-ofctl add-flow br0 in_port=2,dl_type=0x0806,nw_dst=192.168.200.4,actions=output:4

# Cualquier otra cosa, devuelvela por el puerto 5, etiquetadas con VLAN 200
ovs-ofctl add-flow br0 in_port=2,dl_type=0x0806,actions=mod_vlan_vid=200,output:5

# Mandamos a gw1 lo que venga de PC3, lo dejamos en la 100.254
# Esto se usa para pasar de VLAN 2 a VLAN 3
ovs-ofctl add-flow br0 in_port=3,dl_type=0x0806,nw_dst=192.168.100.254,actions=output:1

# Mandamos al puerto 5 lo que venga de PC3 y quiera ir al otro lado y le metemos etiqueta VLAN 100
ovs-ofctl add-flow br0 in_port=3,dl_type=0x0806,actions=mod_vlan_vid=100,output:5

# Mandamos a gw2 lo que venga de PC4, lo dejamos en la 200.254
# Esto se usa para saltar a PC3
ovs-ofctl add-flow br0 in_port=4,dl_type=0x0806,nw_dst=192.168.200.254,actions=output:2

# Mandamos al puerto 5 lo que venga de PC4 y quiera ir al otro lado y le metemos etiqueta VLAN 200
ovs-ofctl add-flow br0 in_port=4,dl_type=0x0806,actions=mod_vlan_vid=200,output:5

# Si vienen por el puerto 5 de tipo ARP, le quitamos la etiqueta VLAN y lo enviamos a los puertos correspondientes
ovs-ofctl add-flow br0 in_port=5,dl_vlan=100,dl_type=0x0806,actions=strip_vlan,output:1,3

# En cuanto a las tramas IP:
# Si va a PC3, sale directamente por el puerto 3
ovs-ofctl add-flow br0 in_port=5,dl_vlan=100,dl_type=0x0800,nw_dst=192.168.100.3,actions=strip_vlan,output:3

# Si iba al PC3 pero aún no ha entrado en la VLAN2, lo metemos en gw1
ovs-ofctl add-flow br0 in_port=5,dl_vlan=100,dl_type=0x0800,nw_dst=192.168.100.254,actions=strip_vlan,output:1

# Para que funcione el forwarding, le tenemos que poner la máscara /24. Si pongo máscara se interpreta como salto, si no se interpreta como direcciones.
# Desde el puerto 1 salta a la VLAN3.
ovs-ofctl add-flow br0 in_port=5,dl_vlan=100,dl_type=0x0800,nw_dst=192.168.200.254/24,actions=strip_vlan,output:1

# Si vienen por el puerto 5 de tipo ARP de VLAN 200, le quitamos la etiqueta y lo enviamos a los puertos correspondientes
ovs-ofctl add-flow br0 in_port=5,dl_vlan=200,dl_type=0x0806,actions=strip_vlan,output:2,4

# En cuanto a las tramas IP de VLAN 200:
# Lo mismo que el bloque de IP anterior, pero con direcciones diferentes
ovs-ofctl add-flow br0 in_port=5,dl_vlan=200,dl_type=0x0800,nw_dst=192.168.200.4,actions=strip_vlan,output:4
ovs-ofctl add-flow br0 in_port=5,dl_vlan=200,dl_type=0x0800,nw_dst=192.168.200.254,actions=strip_vlan,output:2
ovs-ofctl add-flow br0 in_port=5,dl_vlan=200,dl_type=0x0800,nw_dst=192.168.100.254/24,actions=strip_vlan,output:2

# Ahora algunos flujos generales:
# Lo que entra por el puerto 1 se lo lleva hacia el puerto 3 y lo etiqueta con la VLAN 100
ovs-ofctl add-flow br0 in_port=1,actions=mod_vlan_vid=100,output:3

# Lo que entra por el puerto 2 se lo lleva hacia el puerto 3 y lo etiqueta con la VLAN 200
ovs-ofctl add-flow br0 in_port=2,actions=mod_vlan_vid=200,output:3

# Lo que venga por el puerto 3 con etiqueta 100 se le quita la etiqueta y lo mando al puerto 1
ovs-ofctl add-flow br0 in_port=3,dl_vlan=100,actions=strip_vlan,output:1

# Lo que venga por el puerto 3 con etiqueta 200 se le quita la etiqueta y lo mando al puerto 2
ovs-ofctl add-flow br0 in_port=3,dl_vlan=100,actions=strip_vlan,output:1

# Guardar y levantar los flujos
ovs-ofctl dump-flows br0
```
