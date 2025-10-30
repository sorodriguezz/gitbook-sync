# Event Loop

## 🌍 Un poco de historia

### JavaScript nace (1995)

* Brendan Eich lo crea en **Netscape Navigator** en 10 días.
* Diseñado para correr **dentro del navegador** como un lenguaje liviano para manipular el **DOM** (Document Object Model).
* Desde el inicio: **single-threaded**, porque el navegador ya estaba haciendo muchas cosas (render, redes, input) y no querían que JS complicara más con hilos.

### El problema

* JS se usaba para interacciones (clicks, validaciones, AJAX).
* ¿Cómo hacer **cosas asincrónicas** (esperar una respuesta HTTP, un temporizador) **sin bloquear** al usuario?
* Ahí aparece la necesidad de un **event loop**.

### 🔄 ¿Qué es el Event Loop?

El **event loop** es un **patrón de arquitectura**:

* Hay un **stack** donde se ejecuta tu código JS.
* Hay **colas de tareas** (timers, I/O, etc.) alimentadas por el sistema.
* El event loop es el **coordinador**: revisa si el stack está vacío, mete la siguiente tarea desde la cola y la ejecuta.

👉 **No es exclusivo de JS.**\
Es una idea que viene de la **programación orientada a eventos** (GUIs, servidores). Ya existía en entornos como Tcl/Tk, Visual Basic, libevent (C), etc.

JS simplemente adoptó este modelo porque era perfecto para un lenguaje de scripting **single-threaded**.

### ⚙️ ¿Dónde corre?

Aquí está lo clave:

1. **En el navegador**:
   * El **event loop** no lo implementa JS como lenguaje.
   * Lo implementa el **runtime del navegador** (Chromium, Gecko, WebKit).
   * Chrome → usa el motor **V8** para ejecutar JS + un **event loop** implementado en **C++** como parte de Chromium.
2. **En Node.js (2009)**:
   * Ryan Dahl quería correr JS **fuera del navegador**.
   * Usó **V8** (el motor de Chrome) + una librería C llamada **libuv** para manejar I/O asincrónico y el event loop.
   * Node = V8 (ejecutor de JS) + libuv (event loop + thread pool) + bindings (HTTP, FS, etc.).
3. **Otros runtimes**:
   * **Deno** (de Ryan Dahl también): usa V8 + Rust + Tokio (otra librería async).
   * **Bun**: usa JavaScriptCore (motor de Safari) + Zig + su propio event loop.

👉 Entonces: **el event loop depende del runtime** (navegador, Node, Deno, Bun).\
JS como lenguaje (ECMAScript) **no define el event loop**, solo dice que ciertas operaciones son “asíncronas”.\
El runtime decide **cómo** se agendan y ejecutan.

### 📚 ¿El event loop es una librería aparte?

* En Node.js → **sí, está separado** en **libuv** (open source, escrita en C).
  * libuv provee: event loop, thread pool, abstracción de sockets, file system async.
  * Otros lenguajes también pueden usar libuv (ej: Python addons, Rust).
* En navegador → no es una librería aparte, está integrado en el motor del browser (Blink en Chrome, Gecko en Firefox).

### 🔑 Idea central

* JS es solo el **lenguaje**.
* El **runtime** (browser, Node, Bun, Deno) le da **superpoderes** con APIs y un **event loop** que coordina todo.
* Por eso, `setTimeout`, `fetch`, `fs.readFile` **no son JS** → son funciones expuestas por el runtime, que usan el event loop para ejecutarse de forma no bloqueante.

### 📡 ¿Qué es I/O?

“I/O” significa **Input/Output** (entrada/salida).\
En programación no se refiere a teclado y pantalla solamente, sino a **cualquier operación que implique comunicación con el exterior o el sistema operativo**.

#### Ejemplos de I/O

* Leer o escribir un archivo (`fs.readFile`, `fs.writeFile`).
* Hacer una petición HTTP (`fetch`, `http.get`).
* Consultar una base de datos (Postgres, MongoDB).
* Leer datos de un socket (WebSocket, TCP).
* Interacciones con dispositivos (ej: disco, red).

👉 Estas operaciones son **mucho más lentas** que ejecutar código en memoria.\
Ejemplo: sumar dos números tarda nanosegundos. Leer un archivo de 1 MB puede tardar milisegundos o más (¡millones de veces más lento!).

Por eso el event loop y librerías como **libuv** existen: para que **no se bloquee** el hilo principal esperando esos resultados. En lugar de parar todo, el runtime delega esa tarea a:

* El **sistema operativo** (que tiene APIs para sockets, disco, timers).
* O a un **thread pool** (hilos nativos en C) cuando el SO no lo soporta directamente.

Cuando el I/O termina → el runtime pone un callback en la cola del event loop → JS lo ejecuta.

### 🪢 ¿Qué es un Binding?

Un **binding** es un “puente” entre dos mundos:

* Tu código en **JavaScript**.
* El código nativo en **C/C++** del motor (V8, libuv, etc.) o del sistema operativo.

#### Ejemplo en Node.js

Cuando haces:

```js
const fs = require('fs');
fs.readFile('archivo.txt', (err, data) => {
  console.log(data.toString());
});
```

1. Llamas a `fs.readFile` (parece JS, pero no lo es).
2. Ese método es un **binding** a la función `uv_fs_read` de **libuv** (en C).
3. libuv llama a la syscall del sistema operativo (`read` en Linux, `ReadFile` en Windows).
4. Cuando el SO avisa “listo, ya leí el archivo”, libuv mete la callback en la cola → el event loop la ejecuta → llega a tu JS.

Así, los **bindings** son funciones escritas en C/C++ que se exponen como objetos de JS.\
En Node, esos bindings están implementados en C++ y se inyectan al contexto de V8.

### 🔑 Idea central

* **I/O**: cualquier operación de entrada/salida (disco, red, etc.). Son lentas → se manejan de forma **no bloqueante** gracias al event loop y/o thread pool.
* **Binding**: el “puente” entre el mundo JS y el código nativo (C/C++). Permite que desde JS llames a operaciones que en verdad ejecuta el SO.

## 1.2A — Node.js y libuv (visión precisa)

1. **Arquitectura**: Node = **V8** (ejecución JS) + **libuv** (event loop + thread pool + abstracciones I/O) + **bindings** (puentes C/C++↔JS) + APIs (`fs`, `net`, `http`, etc.).
2. **Event loop libuv** (fases simplificadas):
   * **timers**: callbacks de `setTimeout/setInterval` cuyo tiempo venció.
   * **pending callbacks**: I/O diferidos de la iteración previa.
   * **idle/prepare**: internos.
   * **poll**: espera/recibe eventos de I/O; puede bloquear si no hay nada que hacer.
   * **check**: `setImmediate` corre aquí.
   * **close callbacks**: cierre de handles/sockets.
3. **Microtasks vs nextTick**:
   * **Microtasks** (Promesas) se vacían **después** de cada callback de JS.
   * **`process.nextTick`** se ejecuta **antes** incluso de las microtasks (aún más prioritario). Úsalo con moderación.
4. **Thread pool** (por defecto 4 hilos, configurable): tareas “pseudo-bloqueantes” (crypto, DNS, compresión, FS) se derivan aquí; cuando terminan, su callback vuelve al loop.
5. **Orden típico**: sync → microtasks → (libuv loop: timers/poll/check…) → microtasks → siguiente vuelta. `setImmediate` suele disparar **antes** que un `setTimeout(0)` si vienes de I/O, por caer en **check** vs **timers**.

## 1.2B — Navegador y el pipeline de render

1. **Main thread único**: ejecuta JS, manipula DOM/CSSOM y coordina trabajo con **compositor/raster** (otros hilos internos del browser).
2. **Ciclo de frame (\~16.6ms a 60 Hz)**:
   * JS (tareas) → **microtasks** → **layout** (si hay mediciones) → **paint** → **compositor** (transform/opacity) → muestra frame.
3. **Colas**:
   * **Macrotasks** (ej. `setTimeout`) → se procesa **una** y luego microtasks.
   * **Microtasks** (Promesas) → se vacían **todas** antes del render.
4. **Animaciones con `requestAnimationFrame`**: el callback corre justo **antes** del render; ideal para lecturas/escrituras coordinadas y evitar _jank_.
5. **Reflow vs repaint**:
   * **Reflow (layout)**: cálculo de posiciones/tamaños; caro.
   * **Repaint**: volver a pintar píxeles; más barato que layout.
   * Evita **layout thrashing** (leer y escribir layout alternado) agrupando: **lee todo → escribe todo** en el mismo tick.

### Mini-ejemplo mental (no ejecutar)

* Node: `fs.readFile` → I/O al SO → cuando termina, callback en **poll** → corre → luego microtasks.
* Browser: `fetch` (promesa) → cuando llega respuesta, encola microtask de `.then` → se vacían microtasks → si cambias DOM, quizá **layout** → **paint**.

### 1) Mapa mental de libuv (resumido pero preciso)

Cada vuelta del loop de Node pasa (simplificado) por estas **fases**:

1. **timers** → dispara callbacks de `setTimeout/setInterval` cuyo tiempo ya venció.
2. **pending callbacks** → I/O diferidos de la vuelta anterior (errores, etc.).
3. **idle / prepare** → internos de libuv.
4. **poll** → corazón de I/O:
   * si hay eventos de I/O listos, procesa callbacks;
   * si **no** hay nada y **sí** hay `setImmediate` pendientes → **sale pronto** a `check`;
   * si **no** hay nada y **no** hay `setImmediate` → **puede bloquear** esperando I/O.
5. **check** → aquí corre **`setImmediate`**.
6. **close callbacks** → cierres de handles/sockets.

> Entre cada callback JS: **`process.nextTick`** (prioridad más alta) y luego **microtasks** (Promesas) se vacían antes de seguir.

### 2) Reglas prácticas (te salvan en entrevistas/debug)

* **`setImmediate` vs `setTimeout(0)`**
  * Si vienes desde **I/O** (e.g. callback de `fs.readFile`), suele ganar **`setImmediate`** (corre en **check** de la misma vuelta).
  * Si estás en tope de script (no I/O previo), `setTimeout(0)` puede disparar antes por caer en **timers** de la próxima vuelta.
* **`process.nextTick`**: va **antes** de Promesas; úsalo para compatibilidad/“micro-colas” cortas, **no** para trabajo pesado.
* **Microtasks (Promesas)**: tras **cada** callback JS se vacía la cola completa; cuidado con “**microtask storms**”.
* **Thread pool**: crypto, compresión, DNS de resolución, FS pesado → **no** bloquean el main thread; vuelven al loop cuando terminan.

### 3) Mini-labs mentales (no ejecutes, solo piensa el orden)

#### Lab A (top-level, sin I/O)

```js
setTimeout(() => console.log('TO'), 0);
setImmediate(() => console.log('IM'));
Promise.resolve().then(() => console.log('MI'));
console.log('SYNC');
```

Pistas:

* Corre **SYNC** (sincrónico).
* Luego **microtasks** → `MI`.
* Ahora empieza la vuelta: `timers` (TO) vs `check` (IM). ¿Quién llega primero **en este contexto**?

#### Lab B (dentro de I/O → `fs.readFile`)

```js
const fs = require('fs');
fs.readFile(__filename, () => {
  setTimeout(() => console.log('TO'), 0);
  setImmediate(() => console.log('IM'));
  Promise.resolve().then(() => console.log('MI'));
  console.log('IO');
});
```

Pistas:

* Entra a **poll** (I/O listo), ejecuta callback → imprime `IO` (sync).
* Luego **microtasks** → `MI`.
* Y después decide entre **check** (`IM`) y **timers** (`TO`) _en la misma vuelta_.

### 4) Trampas reales que he visto

* **Bloqueo invisible**: meter un `while(...)` pesado dentro de `nextTick`/Promesas → nunca sale a macrotasks.
* **Timers imprecisos**: `setTimeout(0)` **no** es inmediato; el mínimo real depende de carga del loop y OS.
* **Orden no garantizado entre múltiples timers** con el mismo delay; Node _intenta_ pero bajo carga pueden permutar.
* **Zonas críticas**: dentro de `poll` si no hay I/O y sí hay `setImmediate`, el loop **salta** a `check` (por eso `setImmediate` “gana” en callbacks de I/O).

### 5) Patrón de diagnóstico (cuando no entiendes el orden)

* Loguea con prefijos: `[sync]`, `[micro]`, `[timer]`, `[immediate]`, `[io]`.
* Aísla contexto: tope de script vs dentro de un callback de I/O.
* Añade `process.hrtime.bigint()` para ver latencias reales entre logs.

## 🧩 Diccionario del Event Loop (Node.js y navegador)

#### 1. **Call Stack (pila de llamadas)**

* Es la **pila de ejecución** donde van entrando las funciones JS.
* Ejemplo: si llamas `a()` que dentro llama `b()`, la pila se ve como: `main → a → b`.
* Cuando terminas `b()`, se saca de la pila, y sigues con `a()`.
* 🔑 Solo puede haber **un stack por thread** → por eso JS se dice _single-threaded_.

#### 2. **Callback**

* Una **función que se pasa como argumento** y que se ejecutará después.
*   Ejemplo:

    ```js
    fs.readFile('file.txt', (err, data) => {
      console.log('listo el archivo');
    });
    ```

    Aquí `(err, data) => {...}` es un callback.
* El runtime lo guarda y lo pone en cola cuando el evento ocurre.

#### 3. **Macrotask (o “task”)**

* Una **unidad grande de trabajo** en el loop.
* Ejemplos:
  * `setTimeout`, `setInterval`.
  * Eventos de I/O (archivo listo, socket recibió datos).
  * `setImmediate` en Node.
* El loop ejecuta **una macrotask a la vez** → luego revisa microtasks.

#### 4. **Microtask**

* Una **unidad pequeña y prioritaria** de trabajo.
* Ejemplos:
  * `Promise.then/catch/finally`.
  * `queueMicrotask`.
  * `process.nextTick` (Node, aún más prioritario que Promesas).
* Se vacían **todas** las microtasks pendientes **después** de terminar un bloque JS o un callback, **antes** de volver al loop.
* 🔑 Por eso las Promesas parecen “más rápidas” que `setTimeout(0)`.

#### 5. **Timers (fase de libuv)**

* Fase del loop donde se disparan callbacks de `setTimeout` y `setInterval` cuyo tiempo ya venció.
* No son exactos: si tu código bloquea el loop, el timer se retrasa.
*   Ejemplo:

    ```js
    setTimeout(() => console.log('hola'), 0);
    ```

    Esto en realidad significa: “ejecuta **lo antes posible**, pero no antes de que termine lo actual + microtasks”.

#### 6. **Poll (fase de libuv)**

* La **fase central** en Node.
* Aquí el loop espera y recibe eventos de I/O (lectura de archivos, sockets, etc.).
* Si hay callbacks de I/O listos → se ejecutan aquí.
* Si no hay nada que hacer y **sí** hay `setImmediate` pendientes → el loop se salta a la fase `check`.
* Si no hay nada de nada → puede quedarse “bloqueado” esperando eventos de SO.

#### 7. **Check (fase de libuv)**

* Fase especial de Node donde corren los callbacks de `setImmediate`.
* Sucede **después del poll**.
* Por eso, dentro de un callback de I/O, `setImmediate` suele ejecutarse **antes** que `setTimeout(0)`.

#### 8. **Close callbacks (fase final)**

* Cuando cierras un socket o handle, su callback de “close” se ejecuta aquí.
*   Ejemplo:

    ```js
    socket.on('close', () => console.log('cerrado'));
    ```

## 🔄 Flujo completo (versión simplificada)

1. Ejecuta tu **script principal** (stack).
2. Vacía **todas las microtasks** pendientes.
3. Pasa a **timers** → ejecuta timers vencidos.
4. Luego **pending callbacks** (errores de I/O anteriores).
5. Luego **poll** → ejecuta I/O listos, o espera.
6. Luego **check** → ejecuta `setImmediate`.
7. Luego **close callbacks**.
8. Al final de cada callback, antes de pasar de fase, vacía **microtasks** otra vez.
9. Vuelve a 3, repite.

📌 Ejemplo visual muy resumido:

```
[Stack vacío?] → [Microtasks] → [Timers] → [Poll] → [Check] → [Close] → (loop)
```

Piensa en el **event loop** como un restaurante:

### 🍽️ Restaurante Event Loop

#### Macrotask = **plato principal**

* Cada **plato principal** tarda un rato y se sirve **uno por turno**.
* Ejemplos: `setTimeout`, `setImmediate`, callbacks de I/O.
* El mesero (event loop) dice: _“sirvo este plato → luego miro si hay postres (microtasks) → y después voy al siguiente plato”_.

#### Microtask = **postre**

* Los **postres** son más chiquitos y rápidos.
* Se sirven **después de cada plato principal, pero antes de pasar al siguiente plato**.
* Ejemplos: `Promise.then`, `queueMicrotask`, `process.nextTick`.
* Si hay muchos postres, el mesero no avanza a servir el siguiente plato principal hasta que **termine todos los postres acumulados**.

### 🔑 Diferencia clave

* **Macrotasks**: grandes → se procesan de a **una por vuelta** del loop.
* **Microtasks**: pequeños → se vacían **todos antes de continuar** con la siguiente macrotask.

#### Ejemplo en código

```js
setTimeout(() => console.log("PLATO (timeout)"), 0);
Promise.resolve().then(() => console.log("POSTRE (promise)"));
console.log("SYNC (orden del cliente)");
```

Orden:

1. `"SYNC"` (sincrónico, pedido del cliente).
2. `"POSTRE (promise)"` (microtask).
3. `"PLATO (timeout)"` (macrotask).

### 1) Quién manda aquí

* **Motor JS** (V8/SpiderMonkey/…): ejecuta tu JS en el **main thread**.
* **Motor de render** (Blink/WebKit/Gecko): construye **DOM** + **CSSOM**, calcula **layout**, hace **paint** y **composición** (compositor).
* Ambos comparten el **main thread**: si JS se queda calculando, el navegador **no puede renderizar** ese frame.

### 2) El ciclo de un frame (≈16.6 ms a 60Hz)

1. Ejecuta **tareas** (macrotasks) pendientes: p. ej. un handler de evento o `setTimeout`.
2. Vacía **microtasks**: `Promise.then`, `queueMicrotask`, `MutationObserver`.
3. Si hubo cambios que afectan el documento: **estilo → layout → paint → composite**.
4. Presenta el frame.

> Idea clave: **antes** de pintar, el navegador **siempre** vacía microtasks. Por eso una `Promise` puede adelantar trabajo visual si modificas el DOM dentro de ella.

### 3) Reflow vs Repaint vs Composite

* **Reflow (layout)**: recalcular posiciones/tamaños. Costoso. Leer `offsetWidth`/`getBoundingClientRect()` puede **forzar layout** si hay cambios pendientes (_layout thrashing_).
* **Repaint**: repintar píxeles, sin recalcular layout (más barato).
* **Composite**: pegar capas (por GPU, si están “promovidas”); cambios en `transform`/`opacity` suelen ir directo al compositor sin layout/paint.

**Regla práctica anti-jank**

* Agrupa: **lee primero** todas las métricas → **escribe después** (clases/estilos).
* Para animaciones, usa `transform` y `opacity` cuando sea posible (evitan layout).

### 4) rAF vs setTimeout para animar

* **`requestAnimationFrame(cb)`**: el navegador llama a `cb` **justo antes** del próximo render. Se alinea con la tasa de refresco y **pausa** en pestaña oculta (ahorra batería).
* `setTimeout(fn, 16)`: no está sincronizado con el render; puede perder frames o apilarse bajo carga.

**Patrón de oro para animar**

```js
let id;
function tick(ts) {
  // lee (si necesitas), calcula, escribe transform/opacity
  id = requestAnimationFrame(tick);
}
id = requestAnimationFrame(tick);
// cancelAnimationFrame(id) para parar
```

### 5) Microtasks típicas en el navegador

* **Promesas/`queueMicrotask`**: corren _después_ del JS actual y _antes_ del render.
* **`MutationObserver`**: observa cambios en DOM; sus callbacks son **microtasks** (se ejecutan antes del siguiente paint).
* **`IntersectionObserver`**: NOTA: sus callbacks **no** son microtasks; se disparan como tareas “normalizadas” alineadas al frame (excelente para lazy-loading).

### 6) “Fuentes” de tareas (HTML spec)

El navegador mantiene **colas** por “origen” (networking, timers, input, etc.). No necesitas memorizarlas; lo útil es saber que diferentes APIs entran por **colas distintas**, pero todas respetan: **tarea → microtasks → (posible) render**.

### 7) Trampas comunes

*   **Layout thrashing**:

    ```js
    // MAL: leer-escribir-leer-escribir alternado en mismo tick
    el.style.width = "200px";
    el.offsetWidth;               // fuerza layout
    el.style.height = "100px";
    el.getBoundingClientRect();   // fuerza layout de nuevo
    ```

    **Mejor**:

    ```js
    // lee todo
    const w = el.offsetWidth;
    const r = el.getBoundingClientRect();
    // escribe todo
    el.style.width = "200px";
    el.style.height = "100px";
    ```
* **Trabajo pesado en el main thread**: usa **Web Workers** para cálculos largos; el DOM no es accesible desde el worker, pero puedes pasar datos por `postMessage`.

### 8) Checklist rápido de rendimiento

* Animaciones → `requestAnimationFrame` + `transform/opacity`.
* Lecturas y escrituras de layout → **agrupar**.
* Tareas largas → **Web Worker**.
* Medir → **Performance API** (`performance.now`, `performance.mark/measure`), **DevTools** (Performance panel).

## 2.1 — Fundamentos de inmutabilidad (JS/TS)

### 1) ¿Qué es inmutabilidad?

* **Mutar** = cambiar el **mismo** objeto/array en memoria (misma referencia).
* **Inmutar** = **no** modificar la referencia original: crear **una nueva** con los cambios.

```js
// mutación (mala si compartes la referencia)
user.age = 29;

// inmutable (preferible para trazabilidad / menos bugs)
const user2 = { ...user, age: 29 };
```

**¿Por qué importa?**

* Evita **efectos colaterales**: otro módulo que tiene la referencia antigua no se ve “sorprendido”.
* Facilita **debugging** y **time-travel** (pensar en estados como snapshots).
* En TS, permite diseños **más seguros**, donde el tipo te protege de escribir accidentalmente.

### 2) Costo real de copiar

Copiar “todo” puede ser caro. Por eso usamos:

* **Copy-on-write**: solo copias el **camino** que cambias (no todo).
* **Structural sharing**: nuevos objetos **reutilizan** partes no cambiadas.

Ejemplo (array):

```js
const xs = [1,2,3];
// en vez de xs.push(4)
const ys = [...xs, 4]; // comparte [1,2,3] bajo el capó (en JS no es garantizado, pero el patrón es eso)
```

> ⚠️ El spread crea **nuevas estructuras superficiales**. Para nested updates profundos, debemos “copiar el camino” (ver 2.2).

### 3) JS: técnicas básicas

#### 3.1 Objetos

* **Clonar superficial**: `{ ...obj }`, `Object.assign({}, obj)`
* **Agregar/actualizar campo**: `{ ...obj, a: 1 }`
* **Eliminar campo**: `(({ a, ...rest }) => rest)(obj)` o `const { a, ...rest } = obj`

#### 3.2 Arrays

* **Agregar al final**: `[...arr, x]`
* **Agregar al inicio**: `[x, ...arr]`
*   **Reemplazar un índice**:

    ```js
    const i = 2;
    const arr2 = arr.map((v, idx) => idx === i ? nuevo : v);
    ```
* **Eliminar por predicado**: `arr.filter(x => !cond(x))`
*   **Insertar en medio sin mutar**:

    ```js
    const arr2 = [...arr.slice(0, idx), nuevo, ...arr.slice(idx)];
    ```

#### 3.3 API funcional útil

* `map`, `filter`, `reduce` crean **nuevos arrays**.
*   Evita `sort`, `reverse`, `splice` directamente en el original (mutan). Prefiere:

    ```js
    const sorted = [...arr].sort(cmp);
    const reversed = [...arr].reverse();
    const spliced = [...arr]; spliced.splice(i, 1); // si necesitas splice “controlado”
    ```

### 4) TypeScript: hacer cumplir inmutabilidad

#### 4.1 `readonly` en propiedades

```ts
type User = {
  readonly id: string;
  name: string;
}
```

* `user.id = 'x'` → error de tipos.
* Protege **la intención** (no garantiza en runtime).

#### 4.2 `Readonly<T>` y `ReadonlyArray<T>`

```ts
type U = Readonly<{ id: string; name: string }>;
const xs: ReadonlyArray<number> = [1,2,3];
// xs.push(4) // error de tipo
```

#### 4.3 `as const` (const assertion)

```ts
const ROLES = ['ADMIN', 'USER'] as const;
// tipo: readonly ['ADMIN', 'USER']
// útil para unions: type Role = typeof ROLES[number];
```

#### 4.4 Deep readonly (avanzado)

TypeScript no trae `DeepReadonly` nativo, pero puedes declararlo:

```ts
type DeepReadonly<T> = {
  readonly [K in keyof T]:
    T[K] extends object ? DeepReadonly<T[K]> : T[K]
};
```

_(simplificado; hay edge-cases con Arrays/Map/Set)_

> **Idea:** usa `readonly` donde **sí** quieras prohibir mutaciones; no lo uses en **todo** el código si te entorpece. Aplica **gradualmente** en límites de módulos/entidades.

### 5) Rendimiento y memoria (práctico)

* **Copia selectiva**: solo el **camino** modificado (no deep clone ciego).
*   **Evita copias por accidente** en loops calientes. Ej:

    ```js
    // malo si arr es grande y estás en hot path
    for (const x of arr) heavy([...arr, x]);

    // mejor: calcula afuera lo inmutable, no copies en cada iteración
    ```
* **Memoización**: si una función pura depende de `(state, params)` y no cambian, **reusa** el resultado.

### 6) Caches y estructuras clave

* **Map/Set**: evitan colisiones de claves, O(1) promedio.
* **WeakMap/WeakSet**: no evitan GC (claves objeto no previenen recolección) → ideales para **memo** con objetos como claves.
* Evita caches sin límite → usa **LRU** (veremos en 2.3) o TTL.

### 7) Anti-patrones comunes

*   **Mutar parámetros**:

    ```js
    function foo(config) {
      config.debug = true; // ❌
    }
    ```

    Mejor:

    ```js
    function foo(config) {
      const cfg = { ...config, debug: true };
      return cfg;
    }
    ```
* **Deep clone para todo** (`JSON.parse(JSON.stringify(x))`) → lento, pierde tipos/fechas/functions.
*   **Confundir const con inmutabilidad**: `const` congela la **referencia**, **no** el contenido:

    ```js
    const a = { x: 1 };
    a.x = 2; // permitido. const no evita esto
    ```

### 8) Herramientas runtime

* `Object.freeze(obj)` → **shallow** freeze (solo primer nivel). Útil para detectar mutaciones accidentales en dev; en prod úsalo selectivamente (overhead).
* **Immer** (si algún día lo quieres): te deja “escribir como mutable” y produce copia inmutable con structural sharing. (No lo usaremos ahora para que aprendas los fundamentos a mano.)

### 9) Patrón de “actualización anidada” (sin librerías)

Cuando tienes estructuras profundas:

```ts
type State = {
  user: { profile: { name: string; skills: string[] } };
}
```

Actualizar `skills[2]` sin mutar todo:

```js
const state2 = {
  ...state,
  user: {
    ...state.user,
    profile: {
      ...state.user.profile,
      skills: state.user.profile.skills.map((s, i) => i === 2 ? 'TS' : s)
    }
  }
};
```

Sí, es verboso. En 2.2 te enseño `updateIn(path, fn)` para hacerlo elegante y **O(profundidad)**.

### 10) ¿Cuándo _no_ obsesionarse?

*   Códigos **hot** con arrays enormes y cambios locales controlados pueden mutar **internamente** si **documentas** y encapsulas la mutación detrás de una API **pura**:

    ```ts
    // interfaz pública pura
    function addSkill(state: State, skill: string): State {
      // implementación interna podría mutar un buffer y luego devolver copia final
    }
    ```
* El objetivo es **predecibilidad** y **seguridad**, no “nunca mutar jamás”.







