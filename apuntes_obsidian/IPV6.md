# **Estructura completa de una trama/packet IPv6**

Vamos a desglosar capa por capa lo que contiene una transmisión IPv6 completa:

## **1. Encabezado IPv6 Básico (40 bytes FIJOS)**
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version| Traffic Class |           Flow Label                  |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Payload Length        |  Next Header  |   Hop Limit   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                         Source Address                       +
|                                                               |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                      Destination Address                     +
|                                                               |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```
- **Tamaño fijo**: 40 bytes (vs 20-60 bytes variable en IPv4)
- **Características clave**: No hay checksum, opciones movidas a encabezados de extensión

## **2. Encabezados de Extensión (opcionales, en orden específico)**
Se insertan ENTRE el encabezado base y la carga útil:

### **Orden recomendado de encabezados de extensión:**
1. **Encabezado Hop-by-Hop Options** (0) - Solo si es necesario
2. **Encabezado Destino Options** (60) - Para opciones procesadas por destino
3. **Encabezado Routing** (43) - Para routing de origen
4. **Encabezado Fragment** (44) - Para fragmentación (solo en origen)
5. **Encabezado Authentication (AH)** (51) - Para IPSec
6. **Encabezado Encapsulating Security Payload (ESP)** (50) - Para IPSec
7. **Encabezado Destino Options** (60) - Para opciones procesadas solo por destino final

### **Estructura genérica de encabezados de extensión:**
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Next Header  |  Hdr Ext Len  |                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               +
|                                                               |
~                           Options                            ~
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

## **3. Carga Útil (Payload)**
Contiene datos del protocolo de capa superior:
- **Cabecera TCP**: 20-60 bytes
- **Cabecera UDP**: 8 bytes  
- **Cabecera ICMPv6**: Varía según tipo
- **Datos de aplicación**

## **4. Encapsulación en Trama de Enlace (Capa 2)**
Para transmitir por un medio físico, se agrega:

### **Ejemplo con Ethernet (Capa 2):**
```
+-------------------+-------------------+----------------------+
| Preámbulo Ethernet| Encabezado Ethernet | Datos (Paquete IPv6)|
| 7 bytes          | 14 bytes           | (40+extensiones+carga)|
+-------------------+-------------------+----------------------+
```

**Encabezado Ethernet típico:**
- **Dirección MAC destino**: 6 bytes
- **Dirección MAC origen**: 6 bytes  
- **EtherType** (0x86DD para IPv6): 2 bytes
- **Total**: 14 bytes + posiblemente 4 bytes de CRC al final

## **5. Ejemplo Numérico Completo**

**Caso: Ping ICMPv6 con 100 bytes de datos sobre Ethernet**

1. **Capa 2 - Ethernet**:
   - Preámbulo: 7 bytes
   - Encabezado Ethernet: 14 bytes (6+6+2)
   - CRC: 4 bytes (al final)

2. **Capa 3 - IPv6**:
   - Encabezado base IPv6: 40 bytes

3. **Capa 4 - ICMPv6**:
   - Cabecera ICMPv6: 8 bytes
   - Datos ICMPv6: 100 bytes

**Cálculo total en la trama Ethernet**:
```
Encabezado Ethernet: 14 bytes
Paquete IPv6: 40 (IPv6) + 8 (ICMPv6) + 100 (datos) = 148 bytes
CRC: 4 bytes
TOTAL en el medio: 14 + 148 + 4 = 166 bytes

+ Preámbulo (no contado como parte de la trama): 7 bytes
TOTAL transmitido: 173 bytes
```

## **6. MTU en IPv6**
- **MTU mínimo requerido**: 1280 bytes (vs 576 bytes en IPv4)
- **Path MTU Discovery**: Obligatorio para detectar el MTU del camino
- **Fragmentación**: Solo realizada por el origen, no por routers intermedios

## **7. Resumen de lo que se suma**
```
TRANSMISIÓN COMPLETA IPv6:
===========================================
[CAPA 2 - ENLACE]
• Preámbulo/Sincronización (depende del medio)
• Encabezado de trama (ej: 14 bytes Ethernet)
• [Aquí va el paquete IPv6 completo]
• CRC/FCS (ej: 4 bytes Ethernet)

[CAPA 3 - RED - PAQUETE IPv6]
• Encabezado IPv6 base: 40 bytes
• Encabezados de extensión (opcionales): N bytes
• [Aquí va la carga útil de capa superior]

[CAPA 4 - TRANSPORTE/OTROS]
• Cabecera TCP/UDP/ICMPv6: variable
• Datos de aplicación: variable
===========================================
```

## **8. Diferencia clave con IPv4**
- **IPv4**: Encabezado variable (20-60 bytes) con opciones incluidas
- **IPv6**: Encabezado fijo (40 bytes) + encabezados de extensión separados
- **Fragmentación**: En IPv6 solo en origen, usando encabezado de fragmentación
- **Checksum**: Eliminado en IPv6 (lo hace capa 2 y capas superiores)

¿Necesitas un ejemplo específico con algún tipo de tráfico o encabezado de extensión en particular?