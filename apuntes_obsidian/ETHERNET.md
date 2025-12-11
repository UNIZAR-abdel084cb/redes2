Buenísima pregunta, aquí hay mucha “chicha” de Ethernet 👇

---

## 1. De dónde salen esos 26 bytes “ETH”

Tu compi está sumando:

[  
10M + 40 + 26  
]

donde:

- **10M bytes** → datos de audio (10 B por muestra × M muestras)
    
- **40 bytes** → IP (20) + UDP (8) + RTP (12)
    
- **26 bytes** → todo lo que añade **Ethernet** antes de meterlo al cable
    

Esos **26 bytes** los podemos desglosar así:

### a) Cabecera Ethernet (14 B)

La cabecera clásica de Ethernet son **14 bytes**:

1. **6 B – MAC destino**  
    A qué tarjeta de red va dirigida la trama (siguiente salto, no el destino final IP).
    
2. **6 B – MAC origen**  
    Qué tarjeta de red la envía.
    
3. **2 B – EtherType/Longitud**  
    Indica qué protocolo va dentro (por ejemplo, 0x0800 = IPv4).
    

👉 Estos 14 bytes se consideran “cabecera Ethernet”.

### b) FCS / CRC (4 B)

Al final de la trama Ethernet hay un **Frame Check Sequence** de **4 bytes**:

- Es un **CRC-32**, sirve para detectar errores en la trama.
    
- Si el receptor calcula un CRC distinto, descarta la trama.
    

Hasta aquí llevamos:

[  
14\ \text{(cabecera)} + 4\ \text{(FCS)} = 18\ \text{bytes}  
]

Eso es lo que mucha gente llama “cabecera + cola Ethernet”.

### c) Preambulo + SFD (8 B)

Antes de la cabecera Ethernet, por el cable se envía:

- **7 bytes de preámbulo**: patrón 10101010…  
    → sirve para que el receptor sincronice el reloj y “enganche” la señal.
    
- **1 byte de SFD (Start Frame Delimiter)**: 10101011  
    → marca el **inicio real** de la trama (justo después viene la MAC destino).
    

Total: **7 + 1 = 8 bytes**.

Estos 8 bytes **no cuentan como tamaño de trama** cuando se habla de MTU, pero **sí ocupan tiempo en el enlace**, por eso tu compi los suma como overhead.

Entonces:

[  
18\ \text{(cabecera+FCS)} + 8\ \text{(preambulo+SFD)} = 26\ \text{bytes}  
]

---

## 2. ¿Qué es el IFG y por qué son 96 bits?

Después de cada trama Ethernet, el estándar obliga a dejar un tiempo mínimo de silencio:

- **IFG – Inter Frame Gap**: 12 bytes de “hueco”, es decir, **96 bits** de línea en reposo.
    

Sirve para:

- Dar tiempo a las tarjetas de red a **procesar la trama** recibida.
    
- En los medios compartidos clásicos (Ethernet antiguo CSMA/CD), ayudaba a evitar colisiones y daba “fairness” entre nodos.
    
- Aun en full-duplex, el estándar lo mantiene.
    

Tu compi lo añade **aparte**:

[  
L_{\text{trama}} = (10M + 40 + 26)\cdot 8 + 96  
]

- ((10M + 40 + 26)\cdot 8) → todos los **bytes** que se mandan efectivamente (datos + IP/UDP/RTP + cabecera/FCS/preambulo/SFD) pasados a bits.
    
- **+ 96** → los bits del IFG, que también consumen tiempo en el enlace aunque no sean “datos”.
    

Al multiplicar y agrupar:

[  
(10M + 66)\cdot 8 + 96 = 80M + 528 + 96 = 80M + 624\ \text{bits}  
]

Ese es el **tamaño efectivo en bits** que ocupa **una trama de audio completa en el cable**, contando todo el overhead de Ethernet (incluido el hueco entre tramas). Con eso luego calcula el tiempo de transmisión en el enlace de 10 Mbps.