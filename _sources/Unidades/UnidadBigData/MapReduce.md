# MapReduce

MapReduce es un **modelo de programación** y un framework de software diseñado para procesar y generar grandes volúmenes de datos de forma **paralela y distribuida**. Imagina que tienes una tarea gigantesca, como contar todas las palabras en todos los libros de Wikipedia. Hacerlo con un solo ordenador te tomaría una eternidad. MapReduce es como contratar a un ejército de trabajadores, darles instrucciones muy simples, y luego juntar sus resultados para obtener la respuesta final de forma increíblemente rápida. 🤓

Este paradigma fue popularizado por Google y es la tecnología fundamental detrás de muchas herramientas de Big Data, como **Apache Hadoop**. Se divide en dos fases principales: **`Map`** y **`Reduce`**, con una fase intermedia crucial llamada **`Shuffle & Sort`**.

---

### 1. La Fase `Map` (Mapeo) - ¡Dividir y Delegar!

La primera fase, **Map**, se encarga de tomar el conjunto de datos masivo, dividirlo en pequeños trozos (splits) y distribuirlo entre múltiples nodos o "trabajadores". Cada trabajador aplica una función (la función `map`) a su pequeño trozo de datos para procesarlo de forma aislada y en paralelo.

La función `map` toma datos de entrada y los transforma en pares **clave-valor** `(clave, valor)`. El objetivo es "mapear" cada dato de entrada a un formato intermedio que sea fácil de agrupar y contar después.

**Analogía:** Piensa que le das una página de un libro a cada trabajador (`Map worker`). Su instrucción es simple: "Por cada palabra en tu página, escribe en una tarjeta una nota que diga `(palabra, 1)`".

Al final de esta fase, tendrás un montón de tarjetas. Si la palabra "datos" aparece 3 veces en una página, el trabajador de esa página habrá generado 3 tarjetas: `(datos, 1)`, `(datos, 1)`, `(datos, 1)`.


---

### 2. La Fase `Shuffle & Sort` (Mezcla y Ordenamiento) - ¡Organizar el Caos!

Esta es la fase intermedia y, aunque a menudo es invisible para el programador, es la magia del sistema. Una vez que todos los trabajadores de `Map` han terminado, el framework automáticamente recoge todas las tarjetas (los pares clave-valor) y las reorganiza.

* **Shuffle (Mezcla):** Agrupa todas las tarjetas que tienen la **misma clave**. Todas las tarjetas con la palabra "datos" van a un mismo montón, todas las que dicen "procesamiento" van a otro, y así sucesivamente.
* **Sort (Ordenamiento):** Dentro de cada montón, los valores se ordenan, aunque para un simple conteo de palabras, esto no es tan relevante.

**Analogía:** Un coordinador recoge todas las tarjetas de todos los trabajadores. Luego, crea montones separados para cada palabra única. El resultado es una estructura organizada:

* **Montón "datos":** `[1, 1, 1, 1, 1, ...]`
* **Montón "procesamiento":** `[1, 1, 1, ...]`
* **Montón "paralelo":** `[1, 1, ...]`



---

### 3. La Fase `Reduce` (Reducción) - ¡Resumir y Finalizar!

La última fase, **Reduce**, toma los datos ya agrupados y aplica una función `reduce` para resumir o agregar los valores de cada clave. Se asigna un trabajador `Reduce` para cada clave única (o un grupo de claves).

La función `reduce` recibe una clave y la lista de todos sus valores asociados y produce un único resultado.

**Analogía:** Ahora se asigna un nuevo grupo de trabajadores (`Reduce workers`). A uno se le da el montón de "datos" y se le dice: "Suma todos los números de tu montón". Este trabajador sumará todos los `1` y escribirá una tarjeta final: `(datos, 250)`. Otro trabajador hará lo mismo para "procesamiento" y obtendrá `(procesamiento, 120)`, y así sucesivamente.

Al final de esta fase, tenemos el resultado final y consolidado.



---

### Ejemplo Completo: Conteo de Palabras

Veamos el flujo completo con una frase simple: "**el perro come, el gato bebe**".

#### 1. **Input & Split**
El sistema divide la entrada. Para simplificar, digamos que la primera mitad va al Mapper 1 y la segunda al Mapper 2.
* Mapper 1 recibe: `"el perro come,"`
* Mapper 2 recibe: `"el gato bebe"`

#### 2. **Fase `Map`**
Cada Mapper emite sus pares `(palabra, 1)`.
* **Output Mapper 1:** `(el, 1)`, `(perro, 1)`, `(come, 1)`
* **Output Mapper 2:** `(el, 1)`, `(gato, 1)`, `(bebe, 1)`

#### 3. **Fase `Shuffle & Sort`**
El sistema agrupa los resultados por clave.
* `el:` `[1, 1]`
* `perro:` `[1]`
* `come:` `[1]`
* `gato:` `[1]`
* `bebe:` `[1]`

#### 4. **Fase `Reduce`**
Un Reducer toma cada grupo, suma los valores y emite el resultado final.
* Reducer para "el": `1 + 1` -> `(el, 2)`
* Reducer para "perro": `1` -> `(perro, 1)`
* Reducer para "come": `1` -> `(come, 1)`
* Reducer para "gato": `1` -> `(gato, 1)`
* Reducer para "bebe": `1` -> `(bebe, 1)`

#### 5. **Final Output**
El resultado consolidado es la lista de conteos de palabras.

---

### Ventajas Clave de MapReduce

1.  **Escalabilidad Masiva:** Puedes añadir más ordenadores (nodos) a tu clúster para procesar datos más grandes en menos tiempo. Si tu biblioteca se duplica en tamaño, simplemente contratas el doble de bibliotecarios.
2.  **Tolerancia a Fallos:** El framework está diseñado para ser robusto. Si uno de los trabajadores (un ordenador) falla a mitad del proceso, el sistema simplemente reasigna su tarea a otro trabajador disponible, sin que todo el trabajo se detenga. ¡Es a prueba de fallos! 💪

En resumen, MapReduce es un concepto poderoso y elegante que permite convertir un problema masivo y complejo en millones de tareas pequeñas y simples que pueden resolverse en paralelo.


>![MapReduce](https://editor.analyticsvidhya.com/uploads/90908example-of-mapreduce.png)

### Ejemplo de Video sobre MapReduce

Aquí hay un video que explica el concepto de MapReduce visualmente.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/cHGaQz0E7AU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

