## 1. Clasificación y conceptos generales
- Arquitecturas:
    - **Infraestructura**: STA ↔ AP ↔ red cableada (BSS, ESS).
    - **Ad-hoc (IBSS)**: nodos se hablan directamente, sin AP.

---

## 2. WiFi / IEEE 802.11

### 2.1 Estándares importantes

Suele bastar con:
- **802.11b**: 2,4 GHz, DSSS, 11 Mbps.
- **802.11g**: 2,4 GHz, OFDM, 54 Mbps.
- **802.11a**: 5 GHz, OFDM, 54 Mbps.
- **802.11n/ac/ax**: MIMO, OFDM/OFDMA, más tasa.

---

## 3. Formato de tramas 802.11 (MAC + PHY)

### 3.1 Cabecera MAC de datos (DATA)

- **MAC Header**: típica **24 B** (Frame Control 2, Duration 2, 3 direcciones 6+6+6, Sequence 2)
- **FCS** (CRC): 4 B (CRC-32).

¿Dentro del Frame Body suele ir una cabecera **LLC/SNAP** de 8 B antes de IP.?

👉 Para problemas:  
**Trama DATA WiFi ≈ 24 B MAC + ¿8 B LLC/SNAP? + (cabecera IP+UDP/TCP+payload) + 4 B FCS.**

### 3.2 Tramas de control
Mínimo saber tamaños de MAC (sin PHY):
- **ACK**: Frame Control 2 + Duration 2 + Address1 6 + FCS 4 = **14 B**.
- **CTS**: igual que ACK → **14 B**.
- **RTS**: Frame Control 2 + Duration 2 + Addr1 6 + Addr2 6 + FCS 4 = **20 B**.

(En los problemas suelen darte directamente “ACK = 14 B, RTS = 20 B, CTS = 14 B”.)
### 3.3 Capa física / PLCP (802.11b “preambulo corto”)
Para tiempos:
- **Preambulo**: 72 bits (SYNC+SFD) a **1 Mbps** → **72 µs**.
- **PLCP header**: 48 bits (SERVICE, LENGTH, SIGNAL, CRC) a **1 o 2 Mbps** → típicamente **24 µs**.
- Total preámbulo+cabecera: **96 µs** para 802.11b formato corto.

En ejercicios:  
**Tpre+H_PHY ≈ 96 µs por cada trama MAC (DATA, ACK, RTS, CTS).**

---
## 4. Protocolo MAC: DCF / CSMA-CA

Tiempos y conceptos que te van a pedir:
- **SIFS** (Short IFS): el más corto. Para respuestas “críticas”:
    - DATA → SIFS → ACK
    - RTS → SIFS → CTS
- **DIFS** (DCF IFS): espera antes de empezar contienda para una nueva trama:  
    DIFS = SIFS + 2·slot.
- **Slot time**: duración del slot de backoff (e.g. 20 µs en 802.11b, 15 µs en tu ejercicio).
- **Backoff**:
    - Número aleatorio de slots en [0, CW].
    - CW va de **CWmin** a **CWmax** y crece exponencialmente tras colisiones.
        

Secuencia **SIN RTS/CTS**:

> **DIFS → backoff → DATA → SIFS → ACK**

Secuencia **CON RTS/CTS**:

> **DIFS → backoff → RTS → SIFS → CTS → SIFS → DATA → SIFS → ACK**

En problemas de capacidad:
- Tiempo de una transmisión completa sin RTS/CTS =  
    `DIFS + backoff medio + Tpre+PHY(DATA) + TDATA + SIFS + Tpre+PHY(ACK) + TACK`.
- Con RTS/CTS sumas además:  
    `Tpre+PHY(RTS)+TRTS + SIFS + Tpre+PHY(CTS)+TCTS + SIFS`.
