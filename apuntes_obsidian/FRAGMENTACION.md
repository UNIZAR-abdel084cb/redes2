La **fragmentación de IP** es un proceso que ocurre cuando un datagrama IP es demasiado grande para el **MTU** (Maximum Transmission Unit) de la red en la que se está transmitiendo.

- **El tamaño total** incluye tanto la **carga útil** como las **cabeceras IP** (no las cabeceras Ethernet).
- **No se duplica** la cabecera IP, cada fragmento lleva su propia **cabecera IP**.
- **Las cabeceras de Ethernet** no se consideran al calcular el tamaño máximo permitido en el MTU, ya que están en la capa 2 (enlace) y no en la capa 3 (red).
    
Cuando se fragmenta un paquete IP que contiene un mensaje ICMP (o cualquier otra carga útil), **la cabecera ICMP no se duplica** en ambos fragmentos. Solo aparece **una vez**, en el **primer fragmento**, y **no se repite** en el segundo fragmento.

### Explicación detallada:
1. **Fragmentación de un paquete IP**:
    - Cuando el paquete IP es fragmentado debido a que excede el tamaño del **MTU**, se crean varios **fragmentos**, cada uno con su propio **encabezado IP**.
    - Sin embargo, **la carga útil** del paquete (que en este caso incluye el **mensaje ICMP**) se divide entre los fragmentos.
        
2. **Cómo se maneja la cabecera ICMP**:
    - La **cabecera ICMP** está **al principio** de la carga útil del datagrama IP, justo después de la **cabecera IP**.
    - En el **primer fragmento**, **se incluye toda la cabecera ICMP** completa junto con la primera parte de los datos del mensaje ICMP.
    - **El segundo fragmento no llevará la cabecera ICMP**, ya que **solo contiene los datos adicionales** que no cabían en el primer fragmento.
        
3. **Identificador de fragmento y desplazamiento**:
    - El **identificador** de fragmento IP es **el mismo** para todos los fragmentos del mismo paquete, lo que permite que el receptor (cuando todos los fragmentos llegan) los reensamble correctamente.
    - La **cabecera ICMP** solo se encuentra en el primer fragmento. **El segundo fragmento** solo lleva la **carga útil restante** (los datos que fueron parte del mensaje ICMP, pero no la cabecera de ICMP).

![[fragmentacion.png]]


Sí, los fragmentos de un paquete IP deben cumplir con una restricción importante: **el tamaño de la carga útil de cada fragmento debe ser un múltiplo de 8 bytes**. Esto se debe a la forma en que se maneja el campo de **desplazamiento de fragmento** en el encabezado IP, que se mide en unidades de **8 bytes**.

**Cómo ajustar a múltiplo de 8**
Si necesitas que un número sea múltiplo de 8:
- **Redondear hacia abajo:** Encontrar el múltiplo de 8 más cercano pero menor
    - Ejemplo: Para 1482 → 1482 ÷ 8 = 185.25
    - Tomar 185 × 8 = 1480 (múltiplo más cercano hacia abajo)
- **Redondear hacia arriba:** Encontrar el múltiplo de 8 más cercano pero mayor
    - Ejemplo: Para 1482 → 186 × 8 = 1488 (múltiplo más cercano hacia arriba)

### Detalles sobre la fragmentación IP:
1. **Desplazamiento de fragmento (Fragment Offset)**:
    - El **campo de desplazamiento** en el encabezado IP indica la posición de los datos del fragmento en relación con el datagrama original.
    - Este campo se mide en **unidades de 8 bytes**, lo que significa que la **longitud de la carga útil de cada fragmento debe ser un múltiplo de 8 bytes**. De lo contrario, no podría alinearse correctamente con el campo de desplazamiento.
        
2. **¿Por qué un múltiplo de 8?**:
    - El **desplazamiento** de fragmentos en el encabezado IP indica el **desplazamiento en bytes** del inicio del fragmento respecto al inicio del datagrama original. Debido a que el desplazamiento se expresa en **bloques de 8 bytes**, **la longitud de la carga útil de cada fragmento debe ser un múltiplo de 8 bytes** para garantizar que la alineación del campo de desplazamiento sea correcta.
        
3. **Ejemplo**:
    - Si un paquete original tiene una carga útil de 1500 bytes, y el MTU es 1500 bytes, el paquete no necesita fragmentarse (su tamaño es adecuado).
    - Sin embargo, si el tamaño del datagrama IP original es 2000 bytes y se debe fragmentar en paquetes de 1500 bytes (con un MTU de 1500 bytes), el primer fragmento podría ser de 1480 bytes de carga útil (más 20 bytes de encabezado IP), y el segundo fragmento tendrá 520 bytes de carga útil (más 20 bytes de encabezado IP).
        
4. **Si la carga útil no es múltiplo de 8 bytes**:
    - En caso de que el tamaño de la carga útil no sea un múltiplo de 8 bytes, se ajustará para cumplir con la restricción de alineación.
    - Esto podría implicar que el último fragmento tiene una **longitud de carga útil que es menor de lo esperado** para cumplir con la restricción de 8 bytes. El **último fragmento** generalmente se completa con **relleno** de modo que su longitud total sea un múltiplo de 8 bytes.
        

### **Ejemplo de fragmentación**:

Supón que tienes un datagrama de **3100 bytes** y el **MTU es 1500 bytes**. El encabezado IP es de **20 bytes**, por lo que:
- El primer fragmento tendrá **1480 bytes de carga útil** y **20 bytes de encabezado IP**.
- El segundo fragmento tendrá **1480 bytes de carga útil** y **20 bytes de encabezado IP**.
- El tercer fragmento tendrá **140 bytes de carga útil** y **20 bytes de encabezado IP**.

El campo de **desplazamiento de fragmento** debe estar alineado con **un múltiplo de 8** bytes. En este caso:
- El primer fragmento tendrá un desplazamiento de **0** (indicado en unidades de 8 bytes).
- El segundo fragmento tendrá un desplazamiento de **185** (indicado como 1480 / 8).
- El tercer fragmento tendrá un desplazamiento de **370** (indicado como 2960 / 8).


¡Excelente pregunta! Esta es una **distinción crucial** en la fragmentación IP. La respuesta corta es:

**No, el último fragmento NO necesita ser múltiplo de 8 bytes.**

## **Reglas de fragmentación IPv4:**

### **Para fragmentos NO FINALES (MF=1):**
- **Los DATOS (payload) deben ser múltiplos de 8 bytes**
- Porque el **campo "Fragment Offset"** se mide en **bloques de 8 bytes**
- Ejemplo: Si datos = 1480 bytes → 1480 ÷ 8 = 185 (exacto) ✅

### **Para el ÚLTIMO fragmento (MF=0):**
- **Puede tener cualquier tamaño**
- No necesita ser múltiplo de 8
- Contiene **lo que queda** del datagrama original

## **¿Por qué esta diferencia?**

El campo **Fragment Offset** en el encabezado IP indica **"dónde empiezan estos datos"** en el datagrama original, medido en **unidades de 8 bytes**.

**Ejemplo con nuestro caso anterior** (MTU=1500, datagrama original=2000 bytes):

1. **Fragmento 1** (MF=1):
   - Datos: 1480 bytes
   - 1480 ÷ 8 = 185 bloques
   - **Offset = 0** (empieza al inicio)
   - **Debe ser múltiplo de 8**: ✅ 1480 es múltiplo de 8

2. **Fragmento 2** (ÚLTIMO, MF=0):
   - Datos: 500 bytes
   - 500 ÷ 8 = 62.5 ❌ **NO es múltiplo de 8**
   - **Offset = 185** (1480 ÷ 8 = 185)
   - **No necesita ser múltiplo de 8**: ✅ permitido

## **Representación con offsets:**

```
Datagrama original: [0.............................1999] (2000 bytes)
                     0       500      1000      1500      2000
                     |--------|--------|--------|--------|
                     
Fragmento 1: Offset = 0, Length = 1500 (1480 datos + 20 header)
            Datos: bytes 0-1479 del original
            MF=1 (hay más fragmentos)
            
Fragmento 2: Offset = 185 (185 × 8 = 1480), Length = 520 (500 datos + 20 header)
            Datos: bytes 1480-1979 del original
            MF=0 (último fragmento)
```

## **Tabla comparativa:**

| Fragmento | MF Flag | Datos (bytes) | ¿Múltiplo de 8? | Offset (bloques) | Offset (bytes) |
|-----------|---------|---------------|-----------------|------------------|----------------|
| 1         | 1       | 1480          | ✅ Sí           | 0                | 0              |
| 2         | 0       | 500           | ❌ No           | 185              | 1480           |

## **Excepción: Fragmento único**
Si el datagrama original NO necesita fragmentación:
- **MF=0** (es el único y último)
- Puede tener cualquier tamaño
- No hay requisito de múltiplo de 8

## **En resumen:**
- **Solo los fragmentos NO FINALES** deben tener tamaños de datos que sean **múltiplos de 8**
- **El último fragmento** puede tener **cualquier tamaño**
- Esto permite que el datagrama original pueda tener **cualquier tamaño total**

## **Ejercicio práctico:**
Si un datagrama de **1527 bytes** (20 header + 1507 datos) se fragmenta con MTU=576:

**Fragmento 1:**
- MTU=576 → datos máx = 576-20 = 556 bytes
- 556 no es múltiplo de 8 → ajustamos hacia abajo a 552
- Fragmento 1: 20 header + 552 datos = 572 bytes, MF=1
- Offset = 0

**Fragmento 2:**
- Datos restantes: 1507 - 552 = 955 bytes
- MTU=576 → datos máx = 556, pero 955 > 556 → necesita otro fragmento
- 556 no es múltiplo de 8 → ajustamos a 552
- Fragmento 2: 20 header + 552 datos = 572 bytes, MF=1
- Offset = 552 ÷ 8 = 69

**Fragmento 3 (ÚLTIMO):**
- Datos restantes: 955 - 552 = 403 bytes
- Fragmento 3: 20 header + 403 datos = 423 bytes, MF=0
- Offset = (552+552) ÷ 8 = 1104 ÷ 8 = 138
- 403 NO es múltiplo de 8 ✅ (permitido en último fragmento)

¿Tienes algún caso específico que quieras analizar?