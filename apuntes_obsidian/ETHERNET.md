![[trama_ethernet.png]]

## 1. Estructura general de una trama Ethernet
Lo que va “por el cable” es:
- **Preamble (8 bytes)**
    - 7 bytes de sincronización (10101010…)
    - 1 byte SFD (Start of Frame Delimiter, 10101011)
    - Sirve solo para que el receptor se sincronice.
    - A veces no se cuenta dentro del tamaño de trama “lógica”.

Luego viene la **trama MAC**:
1. **Destination address** – 6 B
2. **Source address** – 6 B
3. **Type/Length** – 2 B
    - En Ethernet II (a) se interpreta como **Type** (p.ej. 0x0800 = IPv4).
    - En IEEE 802.3 (b) se interpreta como **Length** (nº de bytes de datos) y encima va LLC/SNAP para indicar el tipo de protocolo.
4. **Data** – entre 0 y 1500 B
5. **Pad (relleno)** – entre 0 y 46 B
    - Si los datos son muy pocos, se añaden bytes de relleno para que la trama MAC (desde dest hasta FCS) tenga **al menos 64 B**.
6. **Checksum (FCS)** – 4 B (CRC-32)

Tamaño de la trama MAC (sin preámbulo ni IFG):
- **mínimo 64 B**
- **máximo 1518 B** (14 cabecera + 1500 datos + 4 FCS)
## 2. ¿Por qué “todas las tramas deben durar más de 2τ”?
- En CSMA/CD, para detectar una colisión, el emisor debe seguir transmitiendo **mientras la señal recorre el cable y vuelve** → eso es **2τ** (tiempo ida y vuelta).
- Por eso se fija un **tamaño mínimo de trama** (64 B a 10 Mb/s ⇒ cierto tiempo mínimo en el medio).
- Si la trama fuera más corta, podrías acabar de transmitir sin enterarte de que chocaste con otro.


![[trama_ethernet_tag.png]]

La imagen muestra una **trama Ethernet etiquetada (802.1Q)**, que es como una trama Ethernet “normal”, pero con un **tag VLAN** añadido para dos cosas:
1. **Identificar la VLAN** (VLAN ID)
2. **Marcar prioridad/QoS** (los 3 bits de prioridad, “Prio”, también llamado **PCP / 802.1p**)

### ¿Qué cambia en la trama?
En Ethernet sin VLAN, después de **MAC destino (6B)** y **MAC fuente (6B)** viene directamente el **EtherType/Longitud (2B)**.
Con **802.1Q**, se insertan **4 bytes** justo después de la MAC fuente:
- **TPID (2 bytes)** = `0x8100` (esto “dice” que hay tag 802.1Q)
- **TCI (2 bytes)**, que contiene:
    - **PCP / Prioridad: 3 bits** → valores **0 a 7** (por eso “hasta 8 colas”)
    - **DEI/CFI: 1 bit** (en muchos esquemas se omite o no se resalta)
    - **VLAN ID: 12 bits** → valores **1 a 4094** (0 y 4095 son reservados)

Luego de esos 4 bytes aparece el **EtherType original (2 bytes)** (IPv4 0x0800, ARP 0x0806, etc.), y después **Datos + PAD** y el **CRC/FCS**.

## IFG
El **IFG (Inter Frame Gap)** es, literalmente, **el silencio obligatorio entre dos tramas Ethernet**.
- Cuando una trama termina, **no puedes empezar otra inmediatamente**.
- El estándar obliga a dejar un hueco de 96 bits de tiempo** en el medio.