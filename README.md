#  Objetivos del Proyecto

## ✔ 1. Gestionar autobuses de forma eficiente
Mantener una colección de autobuses que puedan:

- Cambiar de estado automáticamente (**Disponible**, **En tránsito**, **En destino**).  
- Ser reutilizados sin crearlos de nuevo (**Pool Pattern**).  
- Reportar cuántos asientos quedan disponibles.

---

## ✔ 2. Modelar rutas complejas (**Composite Pattern**)

Las rutas deben permitir:

- Incluir múltiples paradas.  
- Crear rutas compuestas a partir de subrutas.  
- Calcular distancias totales dinámicamente.

Esto permite modelar desde rutas simples hasta recorridos multilínea.

---

## ✔ 3. Permitir personalización de boletos (**Decorator Pattern**)

El sistema debe permitir agregar servicios opcionales al boleto sin alterar la estructura base, como:

- WiFi  
- Snack  
- Asiento reclinable  
- Cualquier adición futura

Cada servicio modifica:

- El precio  
- La descripción

---

## ✔ 4. Implementar un flujo robusto de compra (**State Pattern**)

El objetivo es controlar todo el ciclo de compra:

- Monto ingresado  
- Verificación de pago  
- Confirmación  
- Validación de asientos disponibles  
- Transición entre estados de compra  

Esto garantiza que la compra:

- Nunca quede inconsistente  
- No venda más asientos de los disponibles  
- Sea segura y extensible  

---

## ✔ 5. Mantener un diseño limpio, modular y extensible

El sistema es **100% extensible**:

- Puedes crear nuevos decoradores de boleto.  
- Puedes agregar nuevos estados de compra.  
- Puedes agregar nuevos tipos de rutas.  
- Puedes ampliar el pool de autobuses sin modificar el núcleo.

---

#  Propósito del Proyecto

El propósito principal del sistema es servir como base para una plataforma de venta y gestión de viajes, integrando patrones de diseño profesionales. Sus finalidades incluyen:

- Simular compra de boletos en terminales.  
- Administrar disponibilidad de autobuses automáticamente.  
- Construir rutas y subrutas reutilizables.  
- Crear boletos complejos sin duplicación de código.  
- Representar estados de usuario en tiempo real.  
- Servir como base para aplicaciones reales de transporte.  

También es un proyecto excelente para demostrar dominio de **patrones de diseño avanzados en C#**.

---

#  Cómo Funciona el Sistema

##  1. **BusPool — Administración de Autobuses (Pool Pattern)**

- El sistema inicializa un conjunto de autobuses.  
- Estos autobuses existen todo el tiempo, no se crean ni destruyen.  
- El método `UpdateStates()` actualiza su estado según la hora real.  
- `GetByName()` permite obtener un autobús específico.

**RESULTADO:**  
Gestión eficiente y sin sobrecarga de creación de objetos.

---

## 2. **BaseRoute & Stop — Rutas Compuestas (Composite Pattern)**

Una ruta principal contiene múltiples componentes (`IRouteComponent`).  
Un componente puede ser:

- Una **parada individual** (`Stop`)  
- Otra **ruta completa** (`BaseRoute`)  

Esto permite crear estructuras jerárquicas:

