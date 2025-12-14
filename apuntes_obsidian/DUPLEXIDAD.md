### Simplex
- Solo se transmite en **una dirección**.
- Ejemplo típico: radio FM (la emisora habla, tú solo escuchas).
### Half-duplex
- Se puede transmitir en **ambas direcciones**, pero **no a la vez**.
- Primero habla uno, luego el otro; si hablan a la vez, chocan.
- Es como un walkie-talkie: pulso y hablo, suelto y escucho.
### Full-duplex
- Se puede transmitir en **las dos direcciones al mismo tiempo**.
- Es como una conversación de teléfono: ambos pueden hablar y oír a la vez.
## 2. En Ethernet
### Half-duplex en Ethernet
- Se usaba con **hubs** (repetidores).
- Todos los equipos comparten el mismo medio → puede haber **colisiones**.
- Para evitar/gestionar colisiones se usa **CSMA/CD**:
    - Escucha el medio.
    - Si está libre, transmite.
    - Si detecta colisión, corta, espera un tiempo aleatorio y reintenta.
        
- De aquí viene:
    - El **tamaño mínimo de trama (64 bytes)** para poder detectar colisiones a tiempo.
    - El concepto de **dominio de colisión**.

### Full-duplex en Ethernet
- Se usa cuando hay enlaces **punto a punto**:
    - PC ↔ switch
    - Switch ↔ switch
- Cada sentido tiene su propio par de hilos/fibra → **no hay colisiones**.
- Consecuencias:
    - **No se usa CSMA/CD** (no hace falta).
    - Se puede enviar y recibir a la vez → 100 Mb/s en cada dirección, por ejemplo.
    - El límite de 2τ y la justificación del mínimo de 64 B dejan de ser relevantes (se mantiene por compatibilidad, pero ya no por colisiones).

## 3. WiFi y duplexidad

- WiFi (802.11) es, en la práctica, **half-duplex**:
    - Todos comparten el aire.
    - No se puede transmitir y recibir a la vez en el mismo canal.
    - Usa **CSMA/CA** (evitar colisiones) en vez de CSMA/CD.