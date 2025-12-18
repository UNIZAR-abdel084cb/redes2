En las formulas se tiene en cuenta los usuarios posibles y en la tabla los canales
## 0. ¿Para qué usamos Erlang?

Se usa para **dimensionar sistemas de voz/datos** donde hay:
- Usuarios que generan sesiones (llamadas VoIP, circuitos, etc.).
- Un número finito de **recursos** (canales, enlaces, agentes…).
- Queremos que:
    - la **probabilidad de pérdida** sea pequeña (no se pueda cursar una llamada),
    - o la **probabilidad de espera** sea pequeña (colas cortas).

## 1. Tráfico en Erlangs

### Definición
- 1 **Erlang** = un recurso **ocupado el 100 % del tiempo**.
- Tráfico total ofrecido:
    - A = λ · T
        - λ = tasa de llegadas (llamadas por segundo/minuto/hora)
        - T = duración media de una llamada
- Si tengo **N usuarios**, cada uno con tráfico medio **a** Erlangs:
    - A = N · a


- **A**
    - Se llama **tráfico total ofrecido**.
    - Es lo que se mide en **Erlangs**.
    - Responde a: “¿cuánto tiempo, en total, están ocupados los recursos durante la hora considerada?”.
        
- **λ**
    - Es la **tasa de llegadas** de llamadas/sesiones.
    - “Cuántas llamadas empiezan, de media, por unidad de tiempo”.
    - Ej.: 30 llamadas por hora ⇒ λ = 30 llamadas/hora.
        
- **T**
    - Es la **duración media** de cada llamada/sesión.
    - Ej.: si una llamada dura de media 3 minutos, T = 3 min.
- **N**
    - Es el número de **usuarios/fuentes** que pueden generar llamadas.
    - Ej.: una empresa co
    - n 200 empleados que pueden llamar ⇒ N = 200.
        
- **a**
    - Es el **tráfico medio por usuario**, en Erlangs.
    - Cada usuario, de forma individual, ofrece a Erlangs.
    - Si un usuario habla de media 6 minutos por hora:
        - a = 6 min / 60 min = 0,1 Erlangs.

En la práctica (voz):
- Se mira la **hora cargada** (la hora con más tráfico).
- Se suman las **duraciones de todas las llamadas** de esa hora.
- Se divide por 3600 s → Erlangs.
    

Ejemplo rápido:  
2000 s de conversación en una hora → 2000 / 3600 ≈ 0,56 Erlangs.

## 2. Modelo general de colas
Elementos de cualquier modelo que vais a usar:
- **Población**: número de usuarios potenciales.
    - En la mayoría de ejercicios → **población “infinita”** (aprox).
        
- **Llegadas**:
    - Tasa λ (Poisson, M/M/…).
        
- **Servidores**:
    - N canales / líneas / agentes.

- **Cola**:
    - Puede no haber cola (pérdidas directas).
    - Puede haber cola infinita (nadie se pierde, solo espera).
    - Puede haber cola limitada (espera máxima y luego se pierde).

## 3. Erlang B – Sistemas con pérdidas (M/M/N/N)
### Cuándo usar Erlang B

Sistema sin cola:
- Hay **N canales** (o servidores).
- Si llega una llamada y **todos están ocupados**, la llamada **se pierde**.
- Población suficientemente grande (aprox infinita).
- Métrica de calidad: **probabilidad de pérdida B**.

### Parámetros
- N = número de servidores/canales.
- A = tráfico **ofrecido** total (Erlangs).
- B(A,N) = probabilidad de pérdida.
    

> B = ERLANG-B(A, N)

En la práctica, se calcula con **tablas o Excel** (ErlangB), no a mano.

### Tipos de ejercicios
1. **Dado A y N → B**
    - Miras B = Erlang-B(A, N) en la tabla.
2. **Dado A y B_max → N mínimo**
    - Buscas el menor N tal que B(A, N) ≤ B_max.
3. **Dado N y B_max → A máximo**
    - Buscas el mayor A tal que B(A, N) ≤ B_max.
4. **Tráfico por usuario**
    - Una vez obtenido A:  
        a = A / N_usuarios.

## 4. Erlang C – Sistemas con demora (M/M/N con cola infinita)

### Cuándo usar Erlang C
Sistema con cola:
- Hay **N servidores**.
- Si una llamada llega y todos están ocupados:
    - **NO se pierde**, entra en **cola**.
- Cola de capacidad ilimitada (modelo teórico; se ignoran pérdidas).
- Métrica de calidad: **probabilidad de demora** (probabilidad de que tengas que esperar).
### Parámetros
- N = número de servidores.
- A = tráfico ofrecido total.
- P_D(A,N) = probabilidad de demora.

> P_D = ERLANG-C(A, N)

También se usa con tablas/Excel (ErlangC).
### Tipos de ejercicios

1. **Dado A y N → P_D**
    - P_D = Erlang-C(A, N).
        
2. **Dado A y P_D_max → N mínimo**
    - Buscar el menor N tal que P_D(A, N) ≤ P_D_max.
        
3. Otros posibles:
    - Tiempo medio de espera (a partir de P_D), si lo piden.
## 5. Sistemas con demora y pérdidas (cola limitada)

Situación:
- Hay cola, pero **limitada** (máx llamadas esperando).
- Si se llena la cola:
    - llamadas nuevas se **pierden** (tras un time-out de espera, por ejemplo).
- Métrica principal: **probabilidad de pérdida**.
- Modelo exacto si se tienen todos los parámetros (no suele aparecer en ejercicios simples).
- muchas veces se **aproxima** con Erlang C sabiendo que hay un error (cola infinita).

## Qué mide Erlang

- Erlang (A) mide **ocupación media** de un recurso durante un intervalo (aquí: la hora cargada).
- 1 Erlang significa: “en promedio, el recurso está ocupado toda la hora”.
- Ejemplo mental:
    - 0,5 Erlang = ocupado la mitad de la hora (de media).
    - 0,004 Erlang = ocupado un 0,4% de la hora (de media).
        

## Tiempo activo de un sensor si ofrece 0,004 Erlang
- Si 1 Erlang = 3600 s de ocupación media en una hora,
- entonces 0,004 Erlang = 0,004 * 3600 s = 14,4 s.

✅ Respuesta: un sensor está activo, de media, **14,4 segundos** en la hora cargada.


# “Número máximo de sensores presentes” qué significa
- “Sensores presentes” = **cuántos sensores existen en el sistema** (instalados/registrados).
- No significa “cuántos están transmitiendo a la vez”.

Diferencia clave:
- Presentes: población total (los que podrían transmitir).
- Activos: los que transmiten en ese instante. (canales generalmente)

# Diferencia entre N_canales y N_usuarios

## N_canales
- Es la **capacidad simultánea**:
    - cuántos sensores pueden transmitir “a la vez” sin degradación.
- Si llega otro cuando ya está lleno, **se bloquea** (se pierde).
    

## N_usuarios (o sensores presentes)

- Es la **población total** que puede intentar transmitir.
- De esa población, solo una parte estará activa en cada momento.
- N_canales = “huecos simultáneos”
- N_usuarios = “cantidad de sensores existentes”

# Cómo se calcula el máximo de sensores presentes para pérdida < 3%

1. Tráfico total ofrecido en la hora cargada:
- A_total = N_presentes * 0,004 Erlang
1. Probabilidad de pérdida (bloqueo):
- Se usa Erlang B porque “si está lleno, se pierde” (no espera).
2. Condición que piden:

- Encontrar el máximo N_presentes tal que:
    - ErlangB(A_total, N_canales) < 0,03


## Examen (no me acuerdo cual)

En Erlang B hay **dos “N” distintos** si no vas con cuidado:

1. **N_canales** = nº de canales / servidores (las 3 señales de vídeo simultáneas).
2. **N_usuarios** = nº de usuarios que generan tráfico (los 200 sensores).
    

En la fórmula de Erlang B:

```text
B(A, c)
```

- **c** = nº de canales = **3** 
- **A** = tráfico **total ofrecido** por todos los usuarios juntos (en Erlangs).

Ese **A** no es “por sensor”, es el tráfico global:

```text
A = N_usuarios · a
```

donde:

- N_usuarios = 200 sensores,
- a = tráfico de vídeo de **cada sensor** (lo que te piden).
    

### Qué hemos hecho

1. De la tabla (columna c = 3) buscamos el **A máximo** que cumple:
    

```text
B(A, 3) ≤ 0,03
→ A_max ≈ 0,70 Erlangs
```

Ese 0,70 es el **tráfico total de vídeo** que pueden ofrecer **los 200 sensores juntos**.

2. Como todos los sensores son iguales:
    

```text
A_max = N_usuarios · a
0,70 = 200 · a
a = 0,70 / 200 = 0,0035 Erlangs por sensor
```

Por eso se divide entre **200**: porque queremos pasar del **tráfico total A** al **tráfico por cada sensor**.

Los **3** no van en ese cociente porque ya se usaron antes, al elegir la **columna 3** de la tabla (nº de canales).