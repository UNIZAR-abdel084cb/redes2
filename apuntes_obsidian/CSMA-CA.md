## 0. Idea general: CSMA/CA y DCF

En WiFi (802.11) **todos comparten el mismo medio (el aire)**:
- Antes de transmitir, cada estación:
    - Escucha el canal.
    - Si está libre, **no transmite inmediatamente**, sino que:
        - espera un tiempo fijo (DIFS),
        - luego hace una **espera aleatoria** (backoff) en unidades de slot.

A este mecanismo completo se le llama:
- **CSMA/CA**: Carrier Sense Multiple Access with Collision Avoidance.
- **DCF**: Distributed Coordination Function → implementación de CSMA/CA en 802.11.
## 1. Tiempos clave: SIFS, DIFS, slot, backoff
### 1.1 SIFS (Short InterFrame Space)
- Es el **intervalo de tiempo más corto** entre tramas.
- Se usa para **respuestas críticas**:
    - DATA → SIFS → ACK
    - RTS → SIFS → CTS
    - CTS → SIFS → DATA (en algunos esquemas)
- Como es más corto que DIFS, las tramas que van tras SIFS tienen **prioridad** frente a cualquier estación que esté esperando DIFS + backoff para enviar algo nuevo.

Idea mental:

> “Si acabo de recibir algo, tengo derecho a contestar primero (SIFS), antes de que nadie nuevo entre en la contienda”.

### 1.2 DIFS (Distributed InterFrame Space)

- Intervalo que una estación debe esperar **después de que el canal quede libre** antes de empezar el backoff para una nueva transmisión.
- Se usa para **empezar transmisiones nuevas** (no respuestas).
- Secuencia típica:
    1. El medio está ocupado.
    2. Queda libre.
    3. La estación espera un **DIFS** entero sin oír nada.
    4. Después de DIFS, arranca el **backoff**.

Idea mental:

> “El canal lleva un ratito libre (DIFS), ahora puedo entrar en la cola y competir (backoff)”.

### 1.3 Slot time y backoff
- Se define un **slot time** (por ejemplo, 15 microsegundos).
- Cuando termina DIFS, cada estación que quiere transmitir:
    - Elige al azar un **número de slots de backoff** dentro de una ventana [0, CW].

Proceso de backoff:
1. Tras DIFS, la estación carga su contador de backoff (en slots).
2. Cada vez que pasa un slot con el canal libre, el contador baja 1.
3. Si durante el backoff alguien empieza a transmitir:
    - El canal deja de estar libre.
    - El contador se **congela**.
        
4. Cuando el canal vuelve a quedar libre y pasa DIFS otra vez:
    - El contador continúa bajando.
        
5. Cuando el contador llega a 0:
    - La estación **transmite su trama**.

En cálculos de examen:
- Se usa un **backoff medio**, por ejemplo:
    - Intervalo [7, 15] slots → aprox. (15 − 7) / 2 = 4 slots.

## 2. Ciclo de acceso sin RTS/CTS
![[csma-ca-no-cts.png]]

Secuencia completa para enviar un DATA con ACK, sin RTS/CTS:
1. El medio queda libre.
2. La estación que quiere transmitir:
    - Espera **DIFS**.
    - Realiza el **backoff** (slots).
        
3. Cuando el backoff llega a 0:
    - Envía **preámbulo + cabecera PHY (DATA)**.
    - Envía la **trama MAC de datos**.
        
4. El receptor:
    - Espera **SIFS**.
    - Envía **preámbulo + cabecera PHY (ACK)**.
    - Envía la **trama MAC ACK**.

Tiempo total típico que se suma en los problemas:
- T_total = DIFS
    - backoff medio
    - (preámbulo+cabecera PHY DATA)
    - tiempo MAC+datos a R
    - SIFS
    - (preámbulo+cabecera PHY ACK)
    - tiempo ACK a tasa básica

## 3. Ciclo con RTS/CTS
![[csma-ca-cts.png]]
Cuando se activa el mecanismo RTS/CTS, la secuencia completa es:
1. **DIFS + backoff** → estación espera su turno.
2. Cuando le toca:
    - Envía **RTS** (Request To Send):
        - Trama corta de control.
        - Lleva un campo **Duration** indicando cuánto tiempo estará reservado el canal (RTS + CTS + DATA + ACK).
            
3. El receptor:
    - Espera **SIFS**.
    - Envía **CTS** (Clear To Send):
        - Otra trama corta de control.
        - Con un campo Duration que indica el tiempo restante de reserva.
            
4. El emisor:
    - Espera **SIFS**.
    - Envía **DATA** (preámbulo + cabecera PHY + MAC + datos).
        
5. El receptor:
    - Espera **SIFS**.
    - Envía **ACK**.

Resumen del orden temporal:

- DIFS → backoff → RTS → SIFS → CTS → SIFS → DATA → SIFS → ACK.
    

**Importante:**  
Mientras dura esta secuencia, el canal se considera “reservado” para ese emisor-receptor.

## 4. NAV (Network Allocation Vector) y estaciones vecinas
- Cada trama RTS y CTS incluye un campo **Duration**, que indica durante cuánto tiempo se va a usar el canal para ese intercambio completo (RTS + CTS + DATA + ACK).
- Cualquier estación que **escucha un RTS o un CTS**:
    - Actualiza su **NAV** con ese valor de tiempo.
    - Mientras NAV > 0, **no entra en contienda**: no inicia DIFS ni backoff.
    - Da igual a quién pensara enviar: si oye RTS/CTS, se calla.

> No solo se callan las estaciones que iban a hablar con el mismo destino.  
> Se calla **cualquiera que reciba el RTS o el CTS**, para evitar interferencias (problemas de terminal oculto/expuesto).


## "Formulas"

Sin RTS/CTS
![[formula-backoff-tiempo-no-cts.png]]

Con RTS/CTS
![[formula-con-cts.png]]
![[formula-rts-cts-concreto.png]]

## Fragmentación
![[csma-ca-fragmentacion.png]]
Monopoliza la transferencia y solo repite los SIFS tras tener la aprobación mediante el CTS.

![[tramas_fragmentadas_wifi.png]]

En vez de una trama grande:
> 30 B + 2310 B + 4 B

tienes tres tramas:
> 3 × ( 30 B + 770 B + 4 B )