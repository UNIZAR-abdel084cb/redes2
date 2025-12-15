![[trama_wifi.png]]


Es la **trama PLCP de 802.11b** (PPDU, PLCP Protocol Data Unit):

>  Preámbulo PLCP  + Cabecera PLCP  +  Trama MAC 

La **trama MAC** es lo que tú sueles contar como “24 B MAC + datos + FCS”.  
Delante de eso, la capa física añade  PLCP.

#### Formato largo
- **Preámbulo**: 144 bits a **1 Mbps** → 144 µs
    - SYNC (128 bits): secuencia para que el receptor se sincronice.
    - SFD (16 bits): Start Frame Delimiter (marca inicio de la cabecera PHY).
        
- **Cabecera**: 48 bits a **1 Mbps** → 48 µs
    - SIGNAL (8): indica a qué tasa se envía la trama MAC.
    - SERVICE (8).
    - LENGTH (16): duración de la trama MAC.
    - CRC (16): CRC solo de esta cabecera.

Total preámbulo largo + cabecera:  
144 + 48 = 192 bits a 1 Mbps → **192 µs**.

#### Formato corto

Es el que usa tu profesor (y el que hemos usado en los ejercicios):
- **Preámbulo**: 72 bits a 1 Mbps → **72 µs**
    - SYNC (56) + SFD (16).
- **Cabecera**: 48 bits a **2 Mbps** → **24 µs**
    - Mismos campos: SIGNAL, SERVICE, LENGTH, CRC.
        
Total formato corto:  
72 µs + 24 µs = **96 µs** por cada trama (DATA, ACK, etc.).

Luego la **trama MAC** va a la tasa indicada (2, 5,5 u 11 Mbps en 802.11b).


![[trama_wifi_mac.png]]

Hay otra diapositiva que no muestra el QoS, asi que la MAC header quedaria como 30B + 4B de CRC:
![[trama_wifi_sin_QoS.png]]


Te lo traduzco a “qué tengo que sumar en los ejercicios” y qué significa cada cosa.
#### Frame Body (DATA)

Es **todo lo que viene después de la cabecera MAC** y antes del FCS.
- Longitud: entre **0 y 2312 bytes**.
- Contiene:
    - Información propia del tipo/subtipo de trama.
    - Los **datos de nivel superior** (IP, ARP, etc.).
    - Opcionalmente campos de seguridad (WEP: IV e ICV).

En problemas de datos normales (sin WEP) puedes pensar:

> Frame Body = **LLC/SNAP (8 B)** + **IP + UDP/TCP/RTP + payload**

#### LLC/SNAP (802.2) – 8 bytes

Va al principio del Frame Body (dentro de “Data” en el dibujo).
Sirve para lo que en Ethernet hace el campo Type:
- Indica **qué protocolo de nivel 3** va dentro:
    - IPv4, ARP, IPv6…

Por eso en WiFi, cuando construyes una trama DATA con IP, normalmente sumas:
- 24 B cabecera MAC
- **8 B LLC/SNAP**
- 20 B IP + 8 B UDP/TCP + datos…

#### Cabecera MAC

En el dibujo se ve la versión “completa” (con Address4, QoS, etc.), pero en tus ejercicios se suele asumir:
- Cabecera MAC de **24 bytes**:
    - Frame Control (2)
    - Duration/ID (2)
    - Address1 (6)
    - Address2 (6)
    - Address3 (6)
    - Sequence Control (2)

Address4 y QoS Control normalmente no se usan → te quedas en 24 B.
#### FCS (32 bits)
- Campo final de **4 bytes** (CRC-32).
- Cubre **toda la cabecera MAC + todo el Frame Body**.
- Siempre está, igual que el FCS en Ethernet.

En ejercicios, si el profe no dice lo contrario, es correcto sumar:

> **24 B MAC + 8 B LLC/SNAP + (IP+UDP/TCP/RTP+datos) + 4 B FCS**

y luego, aparte, añadir **preámbulo + cabecera PHY** en tiempo cuando calculas tiempos en la WiFi.

![[mas_tramas_mac.png]]