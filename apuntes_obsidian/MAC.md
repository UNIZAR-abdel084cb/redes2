# **Direcciones MAC y TTL en comunicación entre LANs a través de un router**

Cuando un paquete viaja de un PC en una LAN a otro PC en otra LAN a través de un router:
- Las direcciones MAC **cambian en cada segmento de red**
- Las direcciones IP **permanecen constantes** (excepto con NAT)
- El campo **TTL (Time To Live)** **se decrementa en cada salto** (router)

## **Escenario básico:**
- **PC1**: LAN 1, IP: 192.168.1.10, MAC: MAC_PC1
- **Router**: 
  - Interfaz LAN 1: IP: 192.168.1.1, MAC: MAC_R1
  - Interfaz LAN 2: IP: 192.168.2.1, MAC: MAC_R2
- **PC2**: LAN 2, IP: 192.168.2.20, MAC: MAC_PC2
- **TTL inicial**: 64 (valor típico desde PC1)

## **Trama en cada segmento:**

### **Segmento 1: De PC1 al Router (dentro de LAN 1)**

**En la trama Ethernet:**
- **MAC Destino**: MAC_R1 (la interfaz del router en LAN 1)
- **MAC Origen**: MAC_PC1
- **IP Destino**: 192.168.2.20 (IP del PC2)
- **IP Origen**: 192.168.1.10 (IP del PC1)
- **TTL**: 64 (valor inicial)

**PC1 obtiene MAC_R1 mediante ARP** para la IP 192.168.1.1 (gateway).

### **Segmento 2: Del Router a PC2 (dentro de LAN 2)**

**El router:**
1. Recibe la trama en su interfaz LAN 1
2. Decapsula hasta nivel IP
3. **Decrementa el TTL en 1** (de 64 a 63). Si TTL llega a 0, descarta el paquete y envía ICMP Time Exceeded.
4. Consulta tabla de routing
5. Reencapsula para enviar por la interfaz LAN 2

**En la trama Ethernet (LAN 2):**
- **MAC Destino**: MAC_PC2
- **MAC Origen**: MAC_R2 (interfaz del router en LAN 2)
- **IP Destino**: 192.168.2.20 (sigue siendo la del PC2)
- **IP Origen**: 192.168.1.10 (sigue siendo la del PC1)
- **TTL**: 63 (decrementado en 1)

**El router obtiene MAC_PC2 mediante ARP** para la IP 192.168.2.20.

## **Tabla resumen:**

| Segmento | MAC Destino | MAC Origen | IP Destino | IP Origen | TTL |
|----------|------------|------------|------------|-----------|-----|
| LAN 1 (PC1 → Router) | MAC_R1 | MAC_PC1 | 192.168.2.20 | 192.168.1.10 | 64 |
| LAN 2 (Router → PC2) | MAC_PC2 | MAC_R2 | 192.168.2.20 | 192.168.1.10 | 63 |

## **Flujo completo con TTL:**
```
PC1 (192.168.1.10)                                   PC2 (192.168.2.20)
     |                                                     ^
     | Trama: MAC_DST=MAC_R1, MAC_SRC=MAC_PC1             |
     |         IP_DST=192.168.2.20, IP_SRC=192.168.1.10   |
     |         TTL=64                                      |
     v                                                     |
Router (192.168.1.1) --- Router (192.168.2.1) ------------|
     |                 (decrementa TTL a 63, reencapsula)  |
     | Trama: MAC_DST=MAC_PC2, MAC_SRC=MAC_R2             |
     |         IP_DST=192.168.2.20, IP_SRC=192.168.1.10   |
     |         TTL=63                                      |
     |----------------------------------------------------->
```

## **Puntos clave sobre TTL:**
1. **Propósito**: Evitar que los paquetes circulen indefinidamente en bucles de routing
2. **Decremento**: Se reduce en 1 cada vez que pasa por un router (capa 3)
3. **Valores típicos iniciales**:
   - Linux/Unix: 64
   - Windows: 128
   - Máximo posible: 255
4. **Cuando TTL = 0**: El router descarta el paquete y envía mensaje ICMP "Time Exceeded" (Tipo 11) al origen
5. **Traceroute utiliza TTL**: Envía paquetes con TTL=1,2,3,... para descubrir la ruta

## **Ejemplo con múltiples routers:**
Si hubiera 3 routers en el camino:
- PC1 envía con TTL=64
- Router1 recibe, decrementa a 63, reenvía
- Router2 recibe, decrementa a 62, reenvía
- Router3 recibe, decrementa a 61, reenvía
- PC2 recibe con TTL=61

## **Importante:**
- Los switches (capa 2) **NO decrementan TTL**
- Solo los dispositivos de capa 3 (routers) procesan y modifican el TTL
- El TTL es un campo del encabezado IP, no existe en Ethernet

¿Necesitas que explique cómo afecta el TTL en escenarios con NAT o con más dispositivos intermedios?