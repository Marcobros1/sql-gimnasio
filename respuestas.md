## A1. Tres tipos de clave

1. **¿Qué columnas no se pueden repetir nunca?**

   id, dni y email. En el modelo, id es la clave primaria; dni y email son claves alternativas con restricción UNIQUE.

2. **¿Cuál es la clave primaria? ¿Cuáles son claves alternativas?**

   Clave primaria: id. Claves alternativas: dni y email.

3. **¿Qué columna apunta a otra tabla? ¿A cuál?**

   plan_id, que referencia planes(id).

4. **El DNI ya identifica a cada socio. ¿Por qué crees que usamos un id numérico como clave primaria en vez del DNI? Da al menos una razón.**

   Usar un id numérico permite que la identidad interna del socio no dependa de un dato personal. El DNI puede cambiar o corregirse, y usar un identificador numérico evita propagar esos cambios por las tablas relacionadas. Además, el id es compacto y no expone directamente el DNI como identificador interno.

## A2. Los tipos de relación y las dos preguntas

| Pareja | Un A, ¿cuántos B? | Un B, ¿cuántos A? | Relación |
|---|---|---|---|
| planes -- socios | Un plan puede tener muchos socios | Un socio tiene un plan | 1:N |
| socios -- tarjetas | Un socio tiene como máximo una tarjeta | Una tarjeta pertenece a un socio | 1:1 |
| monitores -- clases | Un monitor puede impartir muchas clases | Cada clase tiene un monitor | 1:N |
| socios -- clases | Un socio puede reservar muchas clases | Una clase puede tener muchos socios | N:M |
| monitores -- especialidades | Un monitor puede tener muchas especialidades | Una especialidad puede corresponder a muchos monitores | N:M |

## A3. Relación entre monitores y clases

1. **Haz las dos preguntas.**

   **¿Un monitor, con cuántas clases se relaciona?**

   Con muchas, o incluso ninguna si todavía no tiene clases asignadas.

   **¿Una clase, con cuántos monitores se relaciona?**

   Con uno, según el modelo propuesto.

   Tipo de relación: 1:N, de monitores a clases.

2. **¿En qué tabla va la clave ajena? ¿Cómo la llamarías?**

   En clases, con el nombre monitor_id, porque clases es el lado muchos.

3. **Dibuja cómo quedaría si la pusieras en el lado equivocado y explica en una frase por qué falla.**

   | id | nombre | clase_id |
   |---|---|---|
   | 1 | Marta | 2, 4, 7 ❌ |

   Esto falla porque una celda debe contener un único valor, no una lista de identificadores. Además, habría que repetir datos del monitor si se intentara resolver duplicando filas. La solución es guardar monitor_id en cada fila de clases.

4. **¿Debe ser NOT NULL? ¿Por qué?**

   Sí, si toda clase debe tener un monitor asignado. Así se impide crear una clase sin monitor.

5. **¿Qué tabla hay que crear primero?**

   monitores, porque clases.monitor_id la referencia.

## A4. Relación 1:1 entre socios y tarjetas

1. **¿Qué pasaría si quitásemos UNIQUE de socio_id? Explícalo con un ejemplo de datos.**

   La relación podría convertirse en 1:N: el mismo socio podría aparecer en varias tarjetas. Por ejemplo, podrían existir (id=1, codigo='T001', socio_id=5) y (id=2, codigo='T002', socio_id=5). Con UNIQUE, esa repetición no está permitida.

2. **¿Qué evita cada UNIQUE de tarjetas?**

   UNIQUE en codigo evita que dos tarjetas tengan el mismo código. UNIQUE en socio_id evita que un socio tenga más de una tarjeta.

3. **Inventa otra relación 1:1 de la vida real (fuera del gimnasio) y di en qué tabla pondrías la clave ajena.**

   Persona y ficha de identidad interna de una empresa. Pondría persona_id como clave ajena UNIQUE NOT NULL en fichas_identidad, porque la ficha depende de la persona y no debería existir sin ella. Este ejemplo presupone una única ficha por persona.

## A5. Relación N:M entre monitores y especialidades

1. **Haz las dos preguntas y escribe el tipo de relación.**

   **¿Un monitor, con cuántas especialidades se relaciona?**

   Con muchas.

   **¿Una especialidad, con cuántos monitores se relaciona?**

   Con muchos.

   Tipo de relación: N:M.

2. **¿Qué tablas necesitas? Ponles nombre.**

   Se necesitan tres tablas: monitores, especialidades y la tabla intermedia monitor_especialidades.

3. **Escribe las columnas de cada tabla y marca cuál es PK, cuál UNIQUE y cuál FK.**

   | Tabla | Columna | Restricción |
   |---|---|---|
   | monitores | id | PK |
   | monitores | dni | UNIQUE |
   | monitores | nombre | — |
   | monitores | email | UNIQUE |
   | especialidades | id | PK |
   | especialidades | nombre | UNIQUE |
   | monitor_especialidades | id | PK |
   | monitor_especialidades | monitor_id | FK → monitores(id), NOT NULL |
   | monitor_especialidades | especialidad_id | FK → especialidades(id), NOT NULL |
   | monitor_especialidades | (monitor_id, especialidad_id) | UNIQUE compuesto |

4. **¿Qué evita el UNIQUE de la tabla intermedia?**

   El UNIQUE (monitor_id, especialidad_id) evita registrar dos veces la misma especialidad para el mismo monitor. Un monitor puede tener varias especialidades y una especialidad puede pertenecer a varios monitores, pero la misma pareja solo aparece una vez.

5. **¿Qué tienen en común reservas y tu tabla intermedia? ¿En qué se diferencian?**

   En común: ambas son tablas intermedias que convierten una relación N:M en dos relaciones 1:N y contienen dos claves ajenas. Diferencias: reservas representa la inscripción de un socio en una clase y guarda el dato adicional fecha_reserva; monitor_especialidades relaciona monitores con especialidades y, en el modelo actual, no necesita más datos de la relación.

## A6. ¿Dónde va la clave ajena?

### 1. País – ciudad

**¿Cuántas ciudades puede tener un país?**

Muchas.

**¿Cuántos países tiene cada ciudad?**

Uno, cada ciudad pertenece a un país.

Relación: 1:N.

Clave ajena: pais_id en ciudades, que referencia paises(id). Si toda ciudad debe pertenecer a un país, sería NOT NULL.

### 2. Persona – pasaporte

**¿Cuántos pasaportes puede tener una persona?**

Como máximo uno vigente, según la simplificación del ejercicio.

**¿Cuántas personas tiene cada pasaporte?**

Una.

Relación: 1:1.

Clave ajena: persona_id en pasaportes, con UNIQUE NOT NULL, si un pasaporte no puede existir sin su titular y se modela un único pasaporte por persona. En un sistema real habría que decidir cómo representar pasaportes caducados o múltiples pasaportes a lo largo del tiempo.

### 3. Alumno – asignatura

**¿Cuántas asignaturas puede cursar un alumno?**

Muchas.

**¿Cuántos alumnos tiene una asignatura?**

Muchos.

Relación: N:M.

Tabla intermedia: matriculas, con alumno_id FK y asignatura_id FK; normalmente UNIQUE(alumno_id, asignatura_id). También podría guardar curso académico, fecha de matrícula o nota.

### 4. Cliente – pedido

**¿Cuántos pedidos puede realizar un cliente?**

Muchos.

**¿Cuántos clientes tiene cada pedido?**

Uno.

Relación: 1:N.

Clave ajena: cliente_id en pedidos, que referencia clientes(id). Normalmente sería NOT NULL.

### 5. Película – actor

**¿Cuántos actores puede tener una película?**

Muchos.

**¿Cuántas películas puede tener un actor?**

Muchas.

Relación: N:M.

Tabla intermedia: reparto (o pelicula_actor), con pelicula_id FK y actor_id FK. El nombre del personaje se guarda en esta tabla porque depende de la participación de ese actor en esa película. Se puede añadir UNIQUE(pelicula_id, actor_id) si cada actor solo puede tener una fila por película; si puede interpretar varios personajes, el diseño debe permitir varias participaciones, por ejemplo incluyendo personaje en una restricción única apropiada.

### 6. Autor – libro

**¿Un libro puede tener varios autores o solo uno?**

La relación depende de ese dato que falta.

Si cada libro tiene un único autor y un autor puede escribir muchos libros, es 1:N y la FK autor_id va en libros.

Si un libro puede tener varios autores y cada autor puede escribir varios libros, es N:M y se necesita una tabla intermedia, por ejemplo autor_libro, con autor_id y libro_id como FKs.

### 7. Furniro: usuario – pedido (User – Order)

**¿Cuántos pedidos puede realizar un usuario?**

Muchos.

**¿Cuántos usuarios tiene cada pedido?**

Uno.

Relación: 1:N.

Clave ajena: user_id en orders, que referencia users(id). Si no se permiten pedidos sin usuario, debe ser NOT NULL.

### 8. Furniro: pedido – producto (Order – Product)

**¿Cuántos productos puede contener un pedido?**

Muchos.

**¿En cuántos pedidos puede aparecer un producto?**

En muchos.

Relación: N:M.

Tabla intermedia: order_items (o order_products), con order_id FK y product_id FK.

**¿Qué datos guardarías en la tabla intermedia?**

quantity (cantidad pedida) y unit_price (precio unitario en el momento de la compra), para conservar el precio histórico aunque el precio del catálogo cambie. Según el sistema, también podría guardar variante del producto, descuento o subtotal. Se debe definir la clave única según el modelo: por ejemplo, UNIQUE(order_id, product_id) si cada producto aparece una sola vez por pedido; si se permiten varias variantes o líneas del mismo producto, la restricción debe incluir la variante o el identificador de línea.

## B3. Romper la base de datos

Para cada instrucción: predicción antes de ejecutarla, error después, y la clave que lo impide.

### 1. Socio con un plan inexistente

**Antes:** falla porque el plan con id = 99 no existe.

**Después:** ERROR: inserción o actualización en la tabla «socios» viola la llave foránea «socios_plan_id_fkey». La llave (plan_id)=(99) no está presente en la tabla «planes».

### 2. Socio con el email de Ana

**Antes:** falla porque ana@gym.com ya está registrado.

**Después:** ERROR: llave duplicada viola restricción de unicidad «socios_email_key». Ya existe la llave (email)=(ana@gym.com).

### 3. Segunda tarjeta para Ana

**Antes:** falla porque Ana ya tiene una tarjeta asociada a socio_id = 1.

**Después:** ERROR: llave duplicada viola restricción de unicidad «tarjetas_socio_id_key». Ya existe la llave (socio_id)=(1).

### 4. Ana reserva otra vez la misma clase

**Antes:** falla porque ya existe una reserva con socio_id = 1 y clase_id = 1.

**Después:** ERROR: llave duplicada viola restricción de unicidad «reservas_socio_id_clase_id_key». Ya existe la llave (socio_id, clase_id)=(1, 1).

### 5. Reserva para una clase inexistente

**Antes:** falla porque la clase 50 no existe.

**Después:** ERROR: inserción o actualización en la tabla «reservas» viola la llave foránea «reservas_clase_id_fkey». La llave (clase_id)=(50) no está presente en la tabla «clases».

### 6. Borrar el plan Básico

**Antes:** falla porque hay socios que utilizan ese plan.

**Después:** ERROR: update o delete en «planes» viola la llave foránea «socios_plan_id_fkey» en la tabla «socios». La llave (id)=(1) todavía es referida desde la tabla «socios».

### 7. Borrar el plan Mañanas

**Antes:** funciona porque ningún socio tiene asignado ese plan.

**Después:** DELETE 1

### 8. ¿Por qué uno falla y el otro funciona?

La clave ajena protege la integridad referencial. El plan Básico no puede borrarse porque hay socios que dependen de él; Mañanas sí puede eliminarse porque ningún socio lo utiliza.

## B4.11. Consulta 6

**¿Por qué Ana aparece tres veces?**

No es un error. Ana tiene tres reservas y la consulta devuelve una fila por cada reserva, por lo que su nombre aparece tres veces.

## B6. Explícalo con tus palabras

1. **¿Qué diferencia hay entre una clave primaria y una clave UNIQUE?**

   La clave primaria identifica de forma única cada fila y no admite valores NULL. Una restricción UNIQUE también evita duplicados, pero una tabla puede tener varias restricciones UNIQUE y solo una clave primaria.

2. **¿Por qué una clave ajena sí puede repetirse?**

   Porque varias filas pueden apuntar al mismo registro de otra tabla. Por ejemplo, muchos socios pueden tener el mismo plan.

3. **En una relación 1:N, ¿en qué lado va la clave ajena? ¿Por qué no en el otro?**

   Va en el lado N, porque muchas filas de ese lado pueden referenciar una misma fila del lado 1. Ponerla en el lado 1 obligaría a almacenar varios identificadores en una celda o a duplicar datos.

4. **¿Qué convierte una relación 1:N en una 1:1?**

   Añadir UNIQUE a la clave ajena impide que varias filas apunten al mismo registro, limitando la relación a uno por cada lado.

5. **¿Por qué una relación N:M necesita una tabla intermedia?**

   Porque ambos lados pueden relacionarse con múltiples registros. La tabla intermedia almacena las dos claves ajenas y transforma la relación en dos relaciones 1:N.

6. **¿Por qué fecha_reserva está en reservas y no en socios ni en clases?**

   Porque la fecha corresponde a una reserva concreta entre un socio y una clase. No es una propiedad exclusiva del socio ni de la clase.
