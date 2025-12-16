Número de celdas = C = floor((L+8)/48)
## 1. ¿qué es ATM y  AAL5?
- **ATM (Asynchronous Transfer Mode)**:
    - Tecnología de conmutación de celdas.
    - Cada **celda ATM** tiene:
        - **5 bytes de cabecera**
        - **48 bytes de carga útil (payload)**  
        - Total: **53 bytes**.
- Problema: las aplicaciones (IP, TCP, voz, video, etc.) **no trabajan en trozos de 48 bytes**, sino en PDU más grandes.
- Solución: usar una **capa de adaptación AAL (ATM Adaptation Layer)**.
    - Hay varios tipos: AAL1, AAL2, AAL3/4, AAL5…
    - **AAL5** es la más usada para datos (IP sobre ATM, por ejemplo).
        
 * AAL5 se encarga de:
	1. Coger una PDU grande (ej: un datagrama IP, un paquete PPP, etc.).
	2. Añadirle su cabecera/tráiler.
	3. **Segementarla en trozos de 48 bytes** para meterlos en las celdas ATM.
	4. En el receptor, volver a **reensamblar** los trozos y comprobar que todo llegó bien.
## 2. Estructura de AAL5: CPCS-PDU y tráiler

En AAL5 casi todo gira alrededor de la **CPCS-PDU** .
### 2.1. Estructura general

Una **CPCS-PDU** tiene:

```text
+-----------------------------+
|     Datos de usuario        |  <-- payload (por ejemplo, un datagrama IP)
+-----------------------------+
|      Relleno (padding)      |  <-- 0 a 47 bytes, para alinear
+-----------------------------+
|      CPCS Trailer (8 B)     |
+-----------------------------+
```

El **CPCS Trailer** tiene 8 bytes y su formato es:

```text
+---------------+---------------+-------------------+-------------------+
|   UU (1 B)    |  CPI (1 B)    |  Length (2 B)     |   CRC-32 (4 B)    |
+---------------+---------------+-------------------+-------------------+
```
