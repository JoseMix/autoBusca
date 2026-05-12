# 🚗💥 Buscador de Autos — Ejemplo de Mala UX

Aplicación web de una sola página (`index.html`) que implementa un buscador funcional de autos con datos mockeados, diseñada intencionalmente para ilustrar patrones de **mala experiencia de usuario**. No tiene dependencias externas: vanilla HTML, CSS y JavaScript.

---

## Propósito

El proyecto es un ejercicio didáctico de anti-patrones UX. Cada decisión de diseño fue tomada para maximizar la fricción, confusión o frustración del usuario, en contraste con las buenas prácticas que debería aplicar una interfaz real.

---

## Datos mockeados

La aplicación incluye un array estático de **24 vehículos** con los siguientes atributos por registro:

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | number | Identificador único |
| `brand` | string | Marca (Toyota, Ford, BMW, etc.) |
| `model` | string | Modelo del vehículo |
| `year` | number | Año de fabricación (2018–2022) |
| `price` | number | Precio en USD |
| `km` | number | Kilometraje |
| `available` | boolean | Disponibilidad de stock |

Las marcas disponibles son: Toyota, Ford, Chevrolet, BMW, Mercedes, Volkswagen, Honda, Hyundai, Audi, Renault y Peugeot.

---

## Filtros y su funcionamiento

La búsqueda aplica cinco filtros en forma simultánea. Todos deben configurarse antes de presionar el botón.

### Paso 1 — Marca (Ruleta / Slot machine)

En lugar de un `<select>` estándar, la marca se elige mediante una ruleta giratoria.

- **GIRAR** inicia el ciclo que itera por todas las marcas a 90ms por cuadro.
- **PARAR** detiene la ruleta y fija la marca visible como seleccionada.
- **TODAS** resetea la selección a "CUALQUIERA" (sin filtro de marca).

Si el usuario no para en la marca deseada, debe volver a girar hasta conseguirla.

### Paso 2 — Año mínimo (Slider invertido)

Un `<input type="range">` con rango 1990–2024, aplicado con `transform: scaleX(-1)` en CSS para invertir su dirección visual. Las etiquetas laterales indican "2024 (VIEJO)" a la izquierda y "1990 (NUEVO)" a la derecha, al revés de la realidad. El valor real del slider (el año mínimo para filtrar) se muestra en el centro.

### Paso 3 — Precio máximo (Stepper de $1.000)

Dos botones `+` y `−` modifican el precio máximo de a $1.000 por toque, con un rango de $0 a $500.000. Para llegar al máximo desde el valor inicial de $50.000 se requieren 450 clicks consecutivos. No soporta pulsación sostenida.

### Paso 4 — Kilometraje máximo (Stepper de 1 km)

Idéntico al anterior pero con incremento de **1 km por toque**, rango 0–500.000 km. El hint en pantalla indica "mantén presionado para ir más rápido", funcionalidad que no existe.

### Paso 5 — Checkbox de disponibilidad (lógica invertida)

El checkbox aparece marcado por defecto con la etiqueta *"INCLUIR AUTOS NO DISPONIBLES (recomendado)"*. La nota debajo dice *"Marcar esta opción oculta los no disponibles"*, lo que contradice la etiqueta principal. El comportamiento real es:

- **Marcado** → muestra únicamente autos con `available: true`.
- **Desmarcado** → muestra todos los autos sin filtrar por disponibilidad.

Es decir, seguir la recomendación de la UI oculta exactamente lo que el label dice incluir.

### Paso 6 — Orden de resultados

Ninguna opción de ordenamiento es útil para el usuario:

| Opción | Criterio real |
|---|---|
| Orden aleatorio (recomendado) | `Math.random()` en cada búsqueda |
| Modelo Z → A | Orden alfabético inverso |
| Más kilometraje primero | `km` descendente |
| Precio menos conveniente | `price` ascendente (más barato primero, sin contexto) |
| Más viejo primero | `year` ascendente |

---

## Elementos de diseño con mala UX

### Esquema de colores

Fondo violeta con pattern diagonal de líneas rojas y verdes, texto en rojo, labels en amarillo sobre naranja, bordes en verde neón. Ninguna combinación cumple con ratios mínimos de contraste WCAG.

### Botón de búsqueda que se escapa

El botón principal tiene un `transform: translateX(220px) translateY(-60px) rotate(18deg)` en `:hover`, lo que hace que se desplace fuera del área visible al intentar clickearlo.

### Barra de carga falsa

Al ejecutar la búsqueda se muestra un modal con una barra de progreso que:
1. Avanza rápido hasta ~85%.
2. Se frena progresivamente hasta detenerse en el **97%** durante 2,4 segundos.
3. Salta al 100% y cierra el modal.

Los mensajes de carga rotan por 12 textos irrelevantes ("Validando certificados SSL...", "Verificando licencia del sistema...", etc.) que no tienen relación con la operación real.

### Alert innecesario post-búsqueda

Una vez que los resultados están listos, se dispara un `alert()` nativo con un "ID de transacción" aleatorio, obligando al usuario a hacer click en OK antes de poder ver los resultados.

### Temporizador de sesión

Un contador visible en la parte superior regresiona de 60 a 0 cada segundo. Al llegar a 0 **resetea todos los filtros** y muestra otro `alert()` informando que la sesión expiró, forzando al usuario a comenzar desde el paso 1.

### Tarjetas de resultados

- El **precio** se renderiza en `font-size: 4px` con el mismo color que el fondo de la card (`#FF6600`), haciéndolo prácticamente invisible.
- El indicador de **disponibilidad** tiene `font-size: 3px` y `color` igual al `background`, por lo que es completamente invisible.
- El **año** se muestra en `writing-mode: vertical-rl` en la esquina superior izquierda.
- Los campos de cada card se ordenan **aleatoriamente** en cada render.
- Las cards tienen una rotación aleatoria de hasta ±2,5 grados.
- El ícono del auto es un emoji elegido al azar, sin relación con la marca o modelo.

### Campo de búsqueda decorativo

Un `<input>` deshabilitado en la parte superior con placeholder *"campo decorativo — no funciona"* que sugiere al usuario que puede buscar por texto, pero no hace nada.

### Título parpadeante

El `<h1>` tiene una animación CSS `blink` de 1,2 segundos que reduce la opacidad al 10%, generando parpadeo constante en toda la página.

---

## Cómo usar

1. Abrir index.html` directamente en cualquier navegador moderno.
2. No requiere servidor, build tools ni conexión a internet.
3. Completar los 6 pasos del panel de filtros.
4. Presionar el botón de búsqueda (si se puede alcanzar).
5. Esperar la barra de carga, confirmar el alert y ver los resultados.
