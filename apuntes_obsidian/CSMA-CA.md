## 0. Idea general: CSMA/CA

En WiFi todos comparten el mismo medio (el aire), así que:
- Antes de enviar, cada estación **escucha el canal**.
- Si está libre, **no envía inmediatamente**, sino que respeta unos tiempos y una espera aleatoria para evitar choques.

Ahí aparecen:
> **DIFS, SIFS, slot, backoff, ACK, etc.**
## 1. SIFS (Short InterFrame Space)

- Es el **tiempo más corto** entre tramas: unos pocos microsegundos.
- Lo usa el receptor para cosas “críticas”:
    - DATA → **SIFS** → ACK
    - DATA → **SIFS** → CTS
        
- Como es el intervalo **más corto**, estas respuestas tienen **prioridad** sobre cualquier otro que quiera empezar una nueva transmisión.

CTS = **Clear To Send** (“listo para enviar”).

Es una **trama de control de WiFi** que solo aparece cuando se usa el mecanismo **RTS/CTS**:
1. La estación que quiere enviar datos manda una trama **RTS** (_Request To Send_) al AP o al receptor.
2. Si el receptor ve que el canal está libre y puede recibir, responde con **CTS** después de un **SIFS**.
3. Al recibir el CTS, el emisor ya sabe que tiene el canal “reservado” y envía la trama DATA.

Sirve para:
- Evitar colisiones en situaciones jodidas (nodos ocultos).
- Avisar al resto de estaciones de cuánto tiempo va a durar la transmisión (nav duration).


- DATA → **SIFS** → ACK.
## 2. DIFS (Distributed InterFrame Space)
- Es un intervalo **más largo que SIFS**.
- Lo usa una estación cuando quiere **iniciar** una transmisión nueva de datos normales.
- El proceso típico:
    1. El medio está ocupado (alguien está transmitiendo).
    2. Queda libre.
    3. La estación espera **DIFS** sin oír nada.
    4. Cuando ha pasado DIFS, empieza la **contienda/backoff** (no transmite aún).
El tiempo desde que el medio queda libre hasta que puede empezar su backoff.
## 3. Slot time y backoff
Como muchas estaciones podrían decidir enviar después de un DIFS, se necesita algo para que **no hablen todas a la vez**:
- Se define un **slot time** (por ejemplo 15 µs).
- Cada estación elige un número aleatorio de **slots de backoff** dentro de una ventana.
- Se suele tomar el valor medio de la n-tupla (ventana) para hacer los cálculos

Proceso:
1. Tras DIFS, la estación **pone un contador** con ese número de slots.
2. Mientras el canal esté libre, **va bajando** el contador cada slot.
3. Si alguien transmite, el contador se **pausa**.
4. Cuando el contador llega a 0 → **transmite su trama**.
## 4. Preámbulo y cabecera PHY
Antes de los datos “MAC” se envía la parte física:
- **Preamble**: bits especiales para sincronizar el receptor (ajustar reloj, ganancia, etc.).
- **PHY header**: indica la tasa, longitud, etc.
## 5. Cabecera MAC 
Dentro del frame WiFi:
- **Cabecera MAC**: 24 bytes (direcciones, control, etc.).
## 6. ACK
Después de recibir correctamente la trama de datos:
1. El receptor espera **SIFS**.
2. Envía un **ACK** (trama cortita) para confirmar.

Ese ACK tiene:
- su preámbulo
- su cabecera PHY
- su cabecera MAC (14 B en tu hoja)
- y se manda a una tasa (a veces la más baja, aquí tu compi la calcula como 56 µs).

Se suma entero al tiempo total de la transmisión de esa trama de datos.


---
### 1. El medio se queda libre y empieza el proceso

Imagina que ha terminado una transmisión en WiFi y el aire queda “callado”.  
Todas las estaciones están escuchando.

Cuando el medio se queda libre, **nadie puede hablar inmediatamente**.  
Primero, cada estación que quiere transmitir debe esperar un tiempo fijo llamado **DIFS (Distributed InterFrame Space)**.

> DIFS = “si el canal lleva este ratito libre, puedo empezar a competir por él”.

---

### 2. Espera aleatoria: el backoff
Después de DIFS, las estaciones **no envían aún**, porque si varias quisieran hablar a la vez, chocarían.

Entonces cada estación que quiere transmitir elige un **número aleatorio de slots de backoff**.

Durante el backoff:
- Cada _slot time_ que pasa con el canal libre, la estación **reduce su contador** en 1.
- Si durante el backoff otra estación empieza a transmitir, el canal deja de estar libre y el **contador se congela**.
- Cuando el canal vuelve a quedar libre y pasa DIFS, la estación reanuda donde se quedó.

Cuando una estación llega a **backoff = 0**, le toca el turno: es **su momento de transmitir**.

---
### 3. Primera fase: RTS (Request To Send)
En el mecanismo RTS/CTS, la estación **no envía todavía los datos grandes**.  
Primero manda una trama cortita llamada **RTS (Request To Send)**:
- RTS dice: _“Quiero enviar datos al receptor X y voy a ocupar el canal durante tanto tiempo”_ (ese tiempo va en el campo Duration).

Esta RTS va dirigida a un receptor concreto (por ejemplo, el AP).

---
### 4. Respuesta rápida: SIFS y CTS (Clear To Send)

El receptor escucha la RTS.  
Si puede recibir (no está saturado, no ve errores, etc.), responde con una trama **CTS (Clear To Send)**.

El orden de tiempos aquí es importante:

1. El receptor espera un tiempo muy corto llamado **SIFS (Short InterFrame Space)**, menor que DIFS.
2. Justo después de SIFS, envía el **CTS**.

¿Por qué SIFS es tan corto?  
Porque así, las respuestas **RTS→CTS** tienen prioridad sobre cualquier otra estación que esté esperando DIFS + backoff para iniciar una nueva transmisión.  
Es decir, cuando empieza el intercambio RTS/CTS, **se “reserva” el canal** para ese emisor y ese receptor.

Cuando el CTS se emite, **todas las demás estaciones lo oyen** y ven el campo Duration.  
Ese campo les dice: “el canal estará ocupado durante X microsegundos, no intentéis transmitir”.  
Entonces esas estaciones ajustan su **Network Allocation Vector (NAV)** y permanecen calladas ese tiempo.

---

### 5. Envío de los datos: DATA

El emisor recibe el CTS.  
Ahora sabe que tiene el permiso y que el resto de estaciones se callarán.

El emisor:
1. Espera de nuevo un **SIFS** (porque la siguiente respuesta tras CTS es prioritaria).
2. Después del SIFS, envía su trama **DATA** (los datos de usuario, con cabeceras MAC, IP, TCP/UDP, etc.).

Durante todo este tiempo, las demás estaciones respetan el silencio porque el CTS les ha indicado que el medio está reservado.

---

### 6. Confirmación final: SIFS y ACK

El receptor recibe la trama DATA:
- Si la trama llega bien (sin errores en el CRC), el receptor debe confirmarlo.
- Lo hace enviando una trama **ACK (Acknowledgement)**.
    

El orden es:
1. El receptor espera un **SIFS** tras terminar la recepción de DATA.
2. Justo después del SIFS, manda el **ACK**.

De nuevo, al usar SIFS, el ACK tiene prioridad sobre cualquier estación que esté esperando un DIFS para empezar una nueva transmisión.

Si el emisor **recibe el ACK**, sabe que la transmisión ha ido bien y termina el proceso.  
Si **no recibe el ACK**, asume que hubo un problema (colisión o error) y más tarde reintentará la transmisión, incrementando la ventana de backoff.

---

### 7. Resumen en palabras

El ciclo completo con RTS/CTS es:
1. **DIFS + backoff** → la estación espera su turno.
2. Cuando le toca, envía **RTS**.
3. El receptor responde tras **SIFS** con **CTS**.
4. El emisor, tras otro **SIFS**, envía la **DATA**.
5. El receptor, tras otro **SIFS**, envía el **ACK**.

Y todo esto encaja con la idea de:
- DIFS + backoff → **contienda normal** para empezar.
- SIFS → **respuestas prioritarias** (CTS, luego DATA, luego ACK).
- RTS/CTS → mecanismo para **reservar el canal** y avisar a todos.
- ACK → mecanismo para **confirmar que la trama de datos ha llegado bien**.