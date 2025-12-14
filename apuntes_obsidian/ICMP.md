![[datagrama_icmp.png]]
![[mensajes_icmp.png]]
## 1. ¿Dónde vive ICMP?

ICMP es un **protocolo de control de la capa de red (IPv4)**.
En un paquete “normal” de ICMP (por ejemplo un ping) lo que va por el cable es:
> Ethernet → IP → ICMP → (datos ICMP opcionales)
- **Ethernet** (capa 2)
- **IP**  (capa 3)
- **ICMP**: mensaje de control (“sub-protocolo” de IP)
- **Datos ICMP**: opcionales

## 2. Formato básico de un mensaje ICMP

Dentro del **datagrama IP**, el **payload** es el mensaje ICMP.
Cabecera ICMP mínima:
- **8 bytes de "cabecera"**
- Sección de datos de tamaño variable