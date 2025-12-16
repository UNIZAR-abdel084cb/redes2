Solapamientos, bloque madre....

Hosts utilizables = `2^h − 2`
- 1 se “pierde” por ser **dirección de red**
- 1 se “pierde” por ser **broadcast**
## 1. Qué es una subred (versión sencilla)

Una dirección IPv4 tiene **32 bits**.  
Esos 32 bits se dividen en:
- **Parte de red** (qué red es).
- **Parte de host** (qué máquina dentro de esa red).

Eso se indica con la **barra**: `/algo`.
### Ejemplo: 10.0.1.0/24

- `/24` significa:
    - **24 bits** para la parte de **red**
    - **8 bits** para la parte de **host** (porque 32 − 24 = 8)
- 8 bits de host ⇒ 2⁸ = 256 direcciones posibles:
    - la primera: **dirección de red** → 10.0.1.0
    - la última: **broadcast** → 10.0.1.255
    - las demás (10.0.1.1–10.0.1.254) se usan para **máquinas**.

Así que:

> 10.0.1.0/24 = una **subred** con hasta 254 hosts.

## 2. Cómo usar subredes en tu ejercicio

El profe quiere que demuestres que sabes **organizar** las IPs en grupos (subredes) según la topología:
- Muchas ubicaciones remotas.
- Cada ubicación con **200 sensores + 1 cacharro concentrador**.
- Un switch central y un router + servidor.

Una forma limpia (no única, pero válida) es:

### 2.1. Elegimos un bloque grande para todo
Por ejemplo:
> **Red general**: `10.0.0.0/16`
- `/16` → 16 bits de red, 16 de host
- Eso son **65 536 direcciones** posibles en total → de sobra.

Dentro de este /16 vamos a **partirlo en trozos** (/24, /30…) para organizar.

---

### 2.2. Subred por **ubicación remota**

Cada ubicación remota tiene ~200 sensores, así que un `/24` viene perfecto:

- Para la **ubicación 1**:
    - Subred: `10.0.1.0/24`
    - Sensores: 10.0.1.1 – 10.0.1.200
    - Dispositivo concentrador: 10.0.1.201
    - (el resto por si quieres más cosas)
        
- Ubicación 2:
    - Subred: `10.0.2.0/24`
    - Sensores: 10.0.2.1 – 10.0.2.200
    - Concentrador: 10.0.2.201
- …

En general, para la ubicación **k**:
> `10.0.k.0/24`

### 2.3. Subred para la red “central” (switch + APs + router)

Puedes reservar otra subred /24 para la LAN central:

- `10.0.200.0/24`
    - Router (interfaz LAN): 10.0.200.1
    - Switch (IP de gestión): 10.0.200.2
    - APs (si quieres darles IP en esa LAN): 10.0.200.10, 10.0.200.11, …

### 2.4. Subred para el enlace router–servidor

Aquí usamos una **/30** (enlace punto a punto):
- `10.0.250.0/30`
    - Router (lado ATM): 10.0.250.1
    - Servidor: 10.0.250.2



## Parte de red 
La parte de red está para que **Internet no sea un caos absoluto** 
Piensa que una IP se divide en:
- **Parte de red** → “a qué red pertenezco”
- **Parte de host** → “qué máquina soy dentro de esa red”

### ¿Para qué sirve la parte de red?
1. **Para que los routers sepan por dónde enviar los paquetes**
    

Un router **no mira cada IP una por una**, mira **redes**.
Ejemplo:
- Red `10.0.1.0/24` está detrás del router A
- Red `10.0.2.0/24` está detrás del router B
    

En las tablas de encaminamiento se guarda:
- “Para llegar a `10.0.1.0/24`, manda por aquí”
- “Para llegar a `10.0.2.0/24`, manda por allá”
    

Si no hubiera parte de red, el router tendría que tener una entrada por **cada IP individual**, sería imposible de gestionar.

2. **Para agrupar máquinas que están “juntas”**
Todas las IP de la misma subred:
- Están en la **misma red de capa 2** (mismo switch / misma LAN).
- Pueden hablar directamente entre ellas **sin pasar por router**.

Por ejemplo, tus sensores en una ubicación:

- `10.0.1.1`, `10.0.1.2`, …, `10.0.1.200`  
    todos en `10.0.1.0/24`  
    → se ven como “vecinos”.

3. **Para separar redes distintas**
Si pones:
- Ubicación 1 → `10.0.1.0/24`
- Ubicación 2 → `10.0.2.0/24`

## 1. Qué significa 10.0.0.0/24
- **/24** = 24 bits de red, 8 bits de host.
- En notación por octetos:
    - **Red** = `10.0.0`
    - **Host** = el último octeto `.X`
    
Así que:
- **Red**: `10.0.0.0/24`
- **Hosts válidos**: `10.0.0.1` a `10.0.0.254`
- **Broadcast**: `10.0.0.255`

Dentro de **ESA** subred /24:
- Los **tres primeros octetos** (`10.0.0`) son **fijos** → parte de red.
- El **último octeto** (`.X`) es el que cambia para los hosts.

## 2. ¿Y las otras subredes tipo 10.0.1.0/24, 10.0.2.0/24…?

Eso ya son **otras subredes distintas**, pero con la **misma máscara /24**:
- `10.0.1.0/24`
- `10.0.2.0/24`
- `10.0.3.0/24`
- …
    

Todas ellas caben dentro de una superred más grande, por ejemplo `10.0.0.0/16`:
- `10.0.0.0/16` → parte de red = `10.0`, 16 bits de red.

> `10.0.0.0/24` **NO** “contiene subredes dentro”.  
> Es _una_ subred concreta donde solo cambia el último octeto.

Lo que sí puedes tener es:

- Un bloque grande: `10.0.0.0/16`
    
- Y dentro de ese /16, muchas subredes /24:
    - `10.0.0.0/24`
    - `10.0.1.0/24`
    - `10.0.2.0/24`
    - etc.


## 2. Redes alineadas
Asegurate que estas usando los bits de host para host y los bits de red para red, aunque sea /18 usa los bits como si fuese /16, es más facil. Para que este alineada todos los bits de host deben de ser 0.

## 3.  Mirar la clase de las redes privadas A, B, C...
# Ejercicio 6 Primera convocatoria (2023/2024)


Tenemos 80 AP distintos

1. Creo 80 redes para los sensores, switch y AP
2. Creo una red para los 80 AP, switch, router
3. Creo una red para el router servidor

## 1
(red + broadcast + N sensores + 1 switch + 1 AP)
Asumimos el máximo de los casos posibels N = 1573

1577 hosts posibles

log2(1577) = 10,6

11 bits de host como mínimo -> /24

10.k.0.0/24

k = 0..79
## 2
(red + broadcast + 80 AP + 1 switch + 1 router)

84 hosts posibles

log2(84) = 6,3

7 bits de host como mínimo -> /25

10.80.0.0/25
## 3
(red + broadcast +router + servidor)

4 hosts posibles

log2(4) = 2

2 bits de host como mínimo -> /30

10.100.0.0/30