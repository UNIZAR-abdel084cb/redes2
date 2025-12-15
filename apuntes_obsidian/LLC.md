## 1. ¿Qué es LLC?

- LLC = **Logical Link Control**, estándar **IEEE 802.2**.
- Es una **subcapa de nivel 2**.
- Capa 2 se ve así:
    - **MAC** (802.3, 802.11, …):  
        acceso al medio, direcciones, formato de trama.
    - **LLC (802.2)**:  
        interfaz genérica hacia la capa 3.
        

> Capa 3 (IP) → LLC → MAC → PHY

## 2. Funciones de LLC
1. **Identificar el protocolo de nivel 3** que va dentro
    - IP, ARP, IPX…
    - Cuando la MAC **no tiene campo Type** (como WiFi), LLC/SNAP indica el protocolo.
        
2. **Dar una interfaz común a IP**
    - IP siempre habla con LLC.
    - LLC se adapta a la MAC que haya debajo (Ethernet, WiFi, ATM…).

## 3. Formato típico: LLC/SNAP

En la práctica moderna se usa casi siempre **LLC/SNAP**:
- Tamaño típico: **8 bytes**
    - 3 B LLC
    - 5 B SNAP (incluye un “EtherType” como en Ethernet II)
        

En algunos problemas:

> “Cab. 8 oct” delante de IP = **LLC/SNAP (8 B)**

## 4. LLC en distintas tecnologías

### 4.1 WiFi (802.11)

Trama de datos típica:

```text
[ PHY ]
[ MAC 802.11 (24 B) ]
[ LLC/SNAP (8 B) ]   <-- LLC aquí
[ IP (20 B) ]
[ UDP/TCP/RTP + datos ]
[ FCS (4 B) ]
```

- Siempre que se transporta IP, se añade **LLC/SNAP (8 B)** tras la cabecera MAC.
- En ejercicios:
    - Cabeceras ≈ 24 B (MAC) + 8 B (LLC) + IP+UDP/TCP+…
    - Esos 8 B **sí se suman**.

---

### 4.2 ATM sobre AAL5 (ejemplo ADSL)

PDU de “capas superiores” que se trocea en celdas ATM:

```text
[ LLC/SNAP 8 B ]  [ Datagrama IP: L B ]  [ PAD ]  [ Trailer AAL5 8 B ]
```

- “Cab. 8 oct” = **LLC/SNAP** delante del datagrama IP.
- Para calcular nº de celdas:
    - tamaño PDU = **L + 8 (LLC) + 8 (trailer AAL5)**

### 4.3 Ethernet (Ethernet II con IP)

Formato que usáis casi siempre:

```text
[ Dest MAC 6 ] [ Src MAC 6 ] [ Type 2 ] [ IP ... ] [ FCS 4 ]
```

- Ya existe un campo **Type (2 B)** que dice si lo de dentro es IP, ARP, etc.
- Por eso **NO se usa LLC** con IP en Ethernet II.
- En los ejercicios:
    - Cabecera MAC = **14 B** (6 + 6 + 2).
    - **No sumas los 8 B de LLC**.
        

_(Solo en el formato 802.3 “puro” habría Length + LLC, pero no lo usáis con IP.)_