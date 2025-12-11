Buenísima duda, porque **si entiendes esto, entiendes todo el ejercicio** 😊

Voy a ir a lo esencial:

---

## 1. Qué es “uso del enlace”

Piensa en el enlace como **una autopista de un solo carril**:

- Cuando pasa **tu coche**, la autopista está **ocupada** por ti.
    
- Cuando no pasa nadie, aunque la autopista existe, está **libre**.
    

En redes:

- Cuando se está enviando **tu trama**, el enlace está **ocupado** por ti.
    
- El resto del tiempo está libre o lo usan otros.
    

El **uso** (utilización) es:

> “¿Qué fracción del tiempo está el enlace ocupado por ESTE flujo?”

---

## 2. Por qué aparece (T_\text{trama} / T_d)

Miramos **un sensor** y nos fijamos en un intervalo que se repite siempre: su **periodo**.

- Cada (T_d) segundos el sensor genera **una trama**.
    
- Esa trama tarda (T_\text{trama}) segundos en transmitirse.
    

En cada periodo de duración (T_d):

- El enlace está **ocupado** (T_\text{trama}) segundos por ese sensor.
    
- El resto del periodo, (T_d - T_\text{trama}), el enlace está libre (para otros).
    

Entonces la fracción de tiempo ocupado es:

[  
\text{uso} = \frac{\text{tiempo ocupado}}{\text{tiempo total del ciclo}}  
= \frac{T_\text{trama}}{T_d}  
]

Eso es todo. Es literalmente un **porcentaje de tiempo**.

---

## 3. Ejemplo numérico tontísimo

Imagina:

- Una trama tarda (T_\text{trama} = 1\ \text{ms}) en enviarse.
    
- Mandas **una trama cada (T_d = 10\ \text{ms})**.
    

En una línea de tiempo de 10 ms:

```
0 ms              1 ms                        10 ms
[██████████]------------------------------------->
  ocupado 1 ms       libre 9 ms
```

Uso:

[  
\text{uso} = \frac{1\ \text{ms}}{10\ \text{ms}} = 0{,}1 = 10%  
]

→ Ese flujo usa el 10 % del enlace.  
Podrías tener, idealmente, hasta **10 flujos iguales** (10×10 % = 100 %) antes de saturar.

Si ahora haces tramas más grandes (tardan más) o las mandas más a menudo (periodo más corto), el cociente (T_\text{trama}/T_d) **sube** ⇒ usas más el enlace.

