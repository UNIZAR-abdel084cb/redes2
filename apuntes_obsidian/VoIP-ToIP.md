## VoIP 

**Definición básica**
- **VoIP = transporte de la voz sobre redes IP.**
- La voz analógica del micrófono se:
    1. **Digitaliza** (PCM u otro método).
    2. **Comprime** con un **códec** (G.711, G.729, G.723…).
    3. Se mete en **paquetes IP** y se envía por la red como cualquier otro tráfico de datos.

**Pila típica de protocolos VoIP (parte de datos)**

```text
Aplicación de voz
   ↓
RTP (Real-time Transport Protocol)   → numeración de paquetes, timestamp
   ↓
UDP                                → sin control de errores, baja latencia
   ↓
IP                                 → direccionamiento
   ↓
Enlace (Ethernet, WiFi, ATM…)
```

**Características importantes**

- Sensible a **retardo, jitter y pérdida de paquetes** (no se puede “retransmitir” como un fichero).
- Se usa **UDP + RTP** para minimizar retrasos.
- Consume un cierto **ancho de banda por flujo**:
    - depende del códec (G.711 ≈ 64 kbps, G.729 ≈ 8 kbps)
    - **+ overhead** de RTP/UDP/IP + cabeceras de enlace.
## ToIP

A veces se usa VoIP y ToIP como sinónimos, pero en la asignatura suelen distinguir:
- **VoIP** → parte “de transporte de voz en paquetes IP”.
- **ToIP (Telefonía sobre IP)** → servicio de **telefonía completo** usando VoIP.

Es decir, **ToIP = VoIP + señalización + servicios de telefonía**.
**Componentes típicos de ToIP**

1. **Señalización** (para establecer, modificar y terminar llamadas):
    - Protocolos: **SIP, H.323, MGCP…**
    - Funciones: llamada, colgar, desvíos, transferencia, etc.
2. **Transporte de voz**:
    - Lo que ya hemos visto como **VoIP** (RTP/UDP/IP).
3. **Infraestructura de telefonía**:
    - Servidores SIP / IP-PBX.
    - Gateways con la red telefónica tradicional (PSTN).
    - Numeración, buzón de voz, etc.
        
4. **Calidad de servicio**:
    - Priorización de tráfico (DiffServ, colas de prioridad…).
    - Dimensionamiento con Erlang, control de carga, etc.

### Resumen ultra corto para examen

- **VoIP**: técnica de **transportar la voz en paquetes IP** usando códecs (G.711, G.729…) y protocolos RTP/UDP/IP.
- **ToIP**: **servicio de telefonía completo sobre redes IP**, que usa VoIP para el audio y añade **señalización, gestión de llamadas y servicios telefónicos** (SIP, IP-PBX, gateways…).