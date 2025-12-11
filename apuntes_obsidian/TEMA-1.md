## 1. Qué cabeceras y tamaños debes tener en la cabeza

### a) Capa aplicación / transporte que suelen usar en problemas
- **RTP (audio, vídeo en tiempo real)**
    - Cabecera típica: **12 B**.
    - Lleva: versión, número de secuencia, timestamp, SSRC…
- **UDP**
    - Cabecera fija: **8 B**.
    - Campos: puerto origen, puerto destino, longitud, checksum.
- **TCP**
    - Cabecera mínima (sin opciones): **20 B**.
    - Campos: puertos, nº de secuencia, nº ACK, flags, ventana, checksum, etc.
- **IP (IPv4)**
    - Cabecera mínima: **20 B** (sin opciones).
    - Tamaño total máx. datagrama: **65 535 B**.
    - Campos clave para examen:
        - **Longitud total**, **Identificación**, **DF/MF + offset** (fragmentación),
        - **TTL**, **Protocolo** (p.ej. 6=TCP, 17=UDP, 1=ICMP),
        - **Checksum de cabecera**.

> En problemas: si te dicen “se paquetiza sobre IPv4 añadiendo 20 B IP y 8 B UDP”, lo sumas a la **carga útil** de aplicación; si además hay Ethernet, lo sumas después.

---
### b) Capa enlace: Ethernet
Para cálculos de tiempo/tamaño de trama en cable:
- **Preambulo + SFD**: 7 B + 1 B = **8 B** (a veces el enunciado los pone aparte).
- **Cabecera MAC Ethernet**:
    - Dest (6 B) + Origen (6 B) + EtherType/longitud (2 B) = **14 B**.
- **FCS**: 4 B.
- **Overhead sobre lo que te da IP**:
    - Normalmente se habla de “cabecera + FCS” = **18 B**.
- **IFG (Inte Frame Gap)**: 12 B → **96 bits**, tiempo “muerto” entre tramas.

---
### c) Capa enlace: WiFi 802.11 “DAR” del enunciado
Típico para tus cuentas:
- **Preambulo**: 72 bits a **2 Mbps** → 36 µs.
- **Cabecera PHY**: 48 bits a **6 Mbps** → 8 µs.
- **Cabecera MAC datos**: **24 B**.
- **Trama ACK**: suelen poner **14 B** de MAC + su preámbulo/cabecera PHY.
- **Tiempos de control**:
    - **SIFS** (Short IFS): 8 µs.
    - **DIFS**: 32 µs.
    - **Slot de contención**: 15 µs.
    - **Backoff**: nº aleatorio de slots.
- Velocidad de datos **R**

---

### d) ATM / AAL5
- Celda ATM: **53 B** = 5 B cabecera + 48 B datos.
- AAL5:
    - Añade un **trailer de 8 B** al final de la PDU (datos IP) y padding hasta múltiplo de 48 B.
- Para calcular celdas/IP-datagrama:
    1. Sumas payload + cabeceras superiores (IP, TCP/UDP/RTP/Ethernet si lo pide).
    2. Añades 8 B de AAL5 y padding.
    3. Divides entre 48 B → nº celdas.
---
### Protocolos de control

- **ICMP**: para errores y diagnóstico. Tipos típicos:
    - Echo request/reply (ping),
    - Destination Unreachable (códigos: red, host, puerto…),
    - Time Exceeded (TTL → traceroute),
    - Redirect.
        
- **ARP**: resolución IP→MAC; tabla ARP, expiración; **proxy ARP**, **ARP gratuito** y para qué se usan.
- **DHCP**: asignación dinámica de IP. Mensajes: Discover, Offer, Request, Ack (DORA); puertos UDP 67/68; lease time.