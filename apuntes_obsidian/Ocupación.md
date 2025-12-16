* Se puede calcular como U = AB / velocidad del enlace
* Se puede calcular como U = T_usado / T_periodo

T_usado = L / velocidad del enlace
AB = L / T_periodo


Sí, esos apuntes están **bien**, solo matizo un par de cosas para que queden redondos.

---

## 1. Definiciones

- **AB**: tasa media de bits del flujo (Kbps, Mbps)  
    AB = L / T_periodo
    
- **Velocidad del enlace (C)**: capacidad del enlace (Kbps, Mbps)
    
- **T_usado**: tiempo que el enlace está ocupado para enviar **una trama**  
    T_usado = L / C
    
- **T_periodo**: cada cuánto se envía esa trama (por ejemplo M·10 ms en VoIP)
    

---

## 2. Ocupación del enlace (U)

Tus dos fórmulas son:

1. `U = AB / C`
    
2. `U = T_usado / T_periodo`
    

Sustituyendo:

- AB = L / T_periodo
    
- T_usado = L / C
    

Queda:

- U = (L / T_periodo) / C = L / (T_periodo · C)
    
- U = (L / C) / T_periodo = L / (C · T_periodo)
    

→ **Son exactamente lo mismo** ✅

---

## 3. Dos notas para que sean “de 10” en apuntes

1. Asegúrate de que **L** incluye **todos los bits que van por el enlace**:
    
    - datos + cabeceras + preámbulo + IFG (si toca).
        
2. Aclara que la **ocupación U** sale:
    
    - en **tanto por uno** (0–1) o en **%** si multiplicas por 100.
        

Ejemplo de cómo lo pondría yo:

> - AB = L / T_periodo
>     
> - T_usado = L / C
>     
> - Ocupación del enlace:  
>     U = AB / C = T_usado / T_periodo
>     

Y ya está, perfectos para examen.