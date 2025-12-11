## 1. Formato de trama Ethernet (802.3 / Ethernet conmutada)
**Campos y tamaños:**
- **Preambulo (7 B) + SFD (1 B)** → 8 B
    - Sirve para sincronizar el receptor y marcar el inicio de trama.
- **MAC destino** → 6 B
- **MAC origen** → 6 B
- **Type/Length** → 2 B
- ¿**Pad** → 0–46 B para llegar a 46 B de datos mínimo?
- **CRC/FCS** → 4 B

Cuando en problemas te pongan “trama Ethernet”:
- Overhead **MAC+FCS = 18 B** (+8 B preámbulo si lo quieren explícito; +12 B IFG si cuentan hueco entre tramas).

---
## 2. Tramas con VLAN / prioridad (802.1Q / 802.1p)

En una trama **tagged** (con VLAN): se inserta un campo extra entre MAC origen y Type:
- **TPID**: 2 B, valor 0x8100 (indica que hay tag 802.1Q).
- **TCI (Tag Control Information)**: 2 B
    - 3 bits de **prioridad** (802.1p)
    - 1 bit DEI/CFI
    - 12 bits **VLAN ID** (0–4095, típicamente 1–4094).

Esto añade **4 bytes extra** a la trama:
- Trama normal: 14 B cabecera MAC
- Con VLAN: **18 B** de cabecera MAC+VLAN, más 4 de FCS.
    

En ejercicios:
- Si te dicen “puerto en VLAN tagged” → suma **4 B** al tamaño de trama.
- Si es “untagged” → trama normal.
- Si hablan sólo de prioridad 802.1p, sigue siendo el mismo **tag 802.1Q** (mismas 4 B).

---
## 3. Hubs vs switches
**Hub (repetidor multipuerto)**:
- Todos los puertos están en **un único dominio de colisión**.
- Trabaja siempre en **half-duplex**, usa CSMA/CD.
- El tráfico que entra por un puerto se **repite por todos**.

**Switch**:
- Cada puerto es **su propio dominio de colisión**.
- Puede mezclar puertos **half-duplex** (CSMA/CD) y **full-duplex** (sin colisiones).
- Aprende una **tabla de conmutación (MAC → puerto)** y reenvía sólo por el puerto correcto; si no conoce la MAC → **flood** (envía por todos excepto el de entrada).