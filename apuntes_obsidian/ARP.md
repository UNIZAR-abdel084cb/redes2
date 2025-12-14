![[arp.png]]

## 1. Estructura general de ARP
Un mensaje ARP va **dentro de una trama Ethernet**  y tiene:

Cabecera ARP **8 bytes**
1. **Hardware Type (HTYPE)** – 2 B  
    Ej.: 1 = Ethernet.
2. **Protocol Type (PTYPE)** – 2 B  
    Ej.: 0x0800 = IPv4.
3. **Hardware Address Length (HLEN)** – 1 B  
    Longitud de la dirección de hardware, en bytes.  
    Ej.: Ethernet → 6.
4. **Protocol Address Length (PLEN)** – 1 B  
    Longitud de la dirección de protocolo.  
    Ej.: IPv4 → 4.
5. **Opcode** – 2 B  
    1 = request, 2 = reply, etc.

### B) Parte variable (depende de HLEN y PLEN)
Después vienen las **direcciones**, cuyo tamaño depende de HLEN y PLEN:
- **Sender Hardware Address (SHA)** – HLEN bytes
- **Sender Protocol Address (SPA)** – PLEN bytes
- **Target Hardware Address (THA)** – HLEN bytes
- **Target Protocol Address (TPA)** – PLEN bytes
    

Tamaño total de la parte variable:
- **2·HLEN + 2·PLEN bytes**
## 2. Caso típico: ARP en Ethernet/IPv4
Este es el que casi siempre usan en los ejercicios:

- HTYPE = 1 (Ethernet)
- PTYPE = 0x0800 (IPv4)
- HLEN = 6 (MAC)
- PLEN = 4 (IPv4)

Entonces:
- Parte fija: 8 B
- Parte variable:
    - 2·HLEN + 2·PLEN = 2·6 + 2·4 = 12 + 8 = 20 B

**Tamaño total ARP = 8 + 20 = 28 bytes**