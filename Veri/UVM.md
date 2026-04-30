UVM es una biblioteca escrita en SV. Lo que hace es estandarizar un montón de cosas. Por ejemplo, ya hay una función print que tiene un formato muy bonito e imprime un montón de cosas, por lo que ya no hay que preocuparse por eso. También hay puertos declarados que muy probablemente esté implementado con mailboxes, solo que ya no hay que preocuparse por eso.

UVM provee una clase para cada componente de un ambiente de verificación (scoreboards, drivers, monitores, estímulos). Estas clases tienen funciones que permiten instanciar, conectar y construir cada uno de los componentes de un ambiente de verificación. 

A cada una de esas entidades del ambiente de verificación se les llama "componentes" y a los datos que manejan se les llama "objetos" o "sequence items".
# Jerarquía de clases en UVM
Objetos:
- transaction
- sequence_item: es un paquete que se manda para comunicarse entre dispositivos.
- sequence_base: conjunto de sequence_items.

Componentes (son clases virtuales y la idea es heredar de ellas):
- sequencer: manda los sequence_items de la secuencia que quiere mandar.

Diferencias con Aleatorización controlada en capas:
- cada interface tiene su forma de leer/escribir, por lo que es necesario un agente por cada una. El agente contiene: driver, monitor y secuenciador (el cual se conecta al driver).
- el scoreboard ahora encapsula el checker (también llamado "Golden Reference", que es la entidad que dice si algo está bien o no) y el scoreboard.

# Clases
- UVM_void: es la clase que sirve como base para todas las demás clases de UVM.
- UVM_root: es una clase que sirve como contenedor de nivel superior para todos los componentes de UVM en el ambiente de verificación. Una instancia de este se llama uvm_top. Se crea automáticamente cuando se inicializa UVM y está disponible a lo largo de la simulación.
- UVM_object: es el papá de todos los objetos. Si se quiere heredar algo que tenga vida finita, se tiene que heredar del UVM_object. Su rol principal es definir un set de funciones de utilidad como print, copy, compare.
- UVM_component: clase base que sirve como fundación para todos los componentes de UVM.

# Secuencia UVM
Conjunto de transacciones que tienen una razón de ser. Serie de comandos que van a la interface.

- Sequence items: usualmente se usan para comunicarse con el driver.

# Características principales de UVM
UVM RAL (Register Access Layer o Capa de Registro): manera estandarizada de conocer, predecir y forzar valores de los registro del DUT. Abstracción de todos los registros y memoria que hay en el DUT.
Se hace por medio un script porque hacerlo a mano no tiene sentido, porque son demasiados registros.

- Puerta delantera: forzar que el driver siga el protocolo normal. Ventajas: más orgánico porque se simula el comportamiento de la interfaz y tiene cobertura.
- Puerta trasera: forzar un valor en un registro. No se pasó por ninguna interfaz, solo se forzó el valor. Ventaja: es prácticamente inmediato y puede ahorrar varios ciclos de simulación.

# Conexión usando TLM
TLM ayuda a enviar datos entre componentes en forma de transacciones y objetos de clase. También brinda una forma de transmitir un paquete a sus oyentes sin tener que crear canales específicos.

# Fases de UVM
- Build: se crea una instancia. Como todos los componentes deben estar sincronizados, si alguno está levantado, no puede salir de la fase build.
- Conexión: cada componente se conecta entre sí.
- Ejecución: se consume tiempo de simulación.
- Fase final: se detiene la simulación.
**Observación:** en las subfase de la fase run se puede hacer lo que sea.

Las fases que están después de run sirven para revisar logs.

# Macros de utilidad y de campo de UVM
Un Macro es una palabra clave que se sustituye por código. Donde está el macro, se pega el código.

- Macros de utilidad: se utiliza para registrar objetos en la fábrica. **La fábrica es un patrón de diseño que permite cambiar el tipo de un objeto en tiempo de corrida**. Buscar que son los patrones de diseño. En la fábrica están registrados todos los objetos. Cada vez que se genera un objeto, hay que registrarlo en la fábrica. Para crear objetos se usa la fábrica y no el constructor. Cuando se está en UVM_object, se utiliza el constructor explícito con el nombre de la clase. En UVM_component se tiene que añadir el nombre del padre.
- Macros de campo: permite utilizar funciones que están incluidas en UVM, como `do_copy`, `do_compare`, `do_print`.

**Nota:** En UVM siempre hay que usar constructores explícitos. 

# El objeto copy/clone
Al asignar prioridad baja (UVM_LOW) se le puede indicar que solo imprima los que tienen prioridad media para arriba. Las prioridades son números del 0 al 200 y sirven para modificar lo que se imprime en tiempo de corrida. Es una manera de filtrar. Para cambiar la prioridad hay que volver a compilar. Pero para seleccionar a partir de cuáles imprime se indica en la corrida.

El clone asigna la instancia, por eso es que en el ejemplo el objeto 2 no tiene instancia.

**Importante:** la función copy copia los contenidos de un objeto en otro. El método clone funciona exactamente igual que copy, solo que clone retorna un objeto con los contenidos copiados. Por lo tanto, no hace falta crear el segundo objeto antes de hacer la copia.

El do_copy personaliza la función copy.

Compare permite comparar objetos para ver si son iguales (esto se hace viendo los atributos). Todos los atributos deben estar registrados por medio macros de campo.
# PACK/UNPACK
Son mecanismos que empaquetan variables de clase en flujos de bits/bytes, y descomprimir un flujo de bits y completar el contenido de la clase. Manda y se descomprime todo el objeto.
- Útil cuando se trabaja con formas de comunicación serial. 
- Es una forma de asegurar los datos o de mandar la información de tal manera que al otro lado sea legible para un ser humano.

# Tutorial

## Módulo top
- El `tb_top` es un módulo y funciona como un contenedor estático para mantener todo lo demás.
- Es obligatorio importar el paquete `uvm_pkg` para poder usar las construcciones de UVM en este módulo.
- Aquí se genera la señal de reloj principal. Para diseños simples, se puede poner un bloque always directamente en el top. Para diseños complejos con múltiples relojes, se instancian módulos generadores separados.
- El reloj se pasa al identificador de la interfaz (`dut_if1`), la cual se instancia aquí y se conecta a nuestro Diseño Bajo Prueba (DUT).
- Usamos la base de datos de configuración (`uvm_config_db`) a través del método set para guardar nuestra interfaz virtual, de modo que las clases de prueba puedan recuperarla luego.
- La simulación arranca invocando el método `run_test()`, al cual se le puede pasar el nombre de la clase de prueba como argumento.

## Ambiente (uvm_env)
Es el contenedor donde se colocan todos los componentes del banco de pruebas y se usa el mismo entorno con diferentes configuraciones para cada prueba. Esto es útil para no tener que crear un banco de pruebas desde cero para cada caso para verificar diseños complejos.

Pasos:
- Crear una clase que herede de `uvm_env`, registrarla en la fábrica e instanciar el constructor.
- Declarar y construir los componentes (Agentes, Scoreboards, Cobertura) dentro de la fase `build_phase`.
- Conectar los componentes dentro de la fase `connect_phase`.

## Agente (uvm_agent)
 Agrupa un secuenciador, un driver y un monitor. Su característica principal es que se puede configurar de dos maneras:
 - UVM_ACTIVE: instancia los tres componentes y controla los estímulos del DUT
 - UVM_PASSIVE: Solo instancia el monitor para observar y recolectar datos.

## Monitor (uvm_monitor)
Vigila la actividad de los pines físicos de la interfaz del diseño, empaqueta los 1s y 0s en objetos de datos lógicos (transacciones), y los transmite a quien le interese escuchar.

## El Driver, el Secuenciador y el Handshake
El secuenciador es un generador/enrutador de objetos de datos. El Driver es la clase activa que pide esos objetos y "mueve" los pines físicos. Ambos hablan de forma sincronizada utilizando un handshake. 
Por lo general no se necesita escribir una clase personalizada para el secuenciador, solo se parametriza la clase base `uvm_sequencer #(my_data)`.

Esta línea se puede colocar en cualquier lado, pero normalmente va en el ambiente.
```systemverilog
uvm_config_db #(int)::set(this, "m_top_env.my_agent", "is_active", UVM_PASSIVE);
```

## El Scoreboard
Recibe objetos de nivel de transacción desde los monitores a través de los puertos TLM.
- Crear una clase que herede de `uvm_scoreboard` y registrarla en la fábrica.
- Declarar un puerto de implementación de análisis (`uvm_analysis_imp`) para recibir los datos.
- Instanciar el puerto en la fase `build_phase`.
- Definir obligatoriamente la función `write()`, que dictará qué hacer cada vez que llegue un paquete.
- (Opcional) Usar la fase `check_phase` para validaciones al final de la simulación. 

El Scoreboard por sí solo no hace nada si no se le manda información. Por eso se conecta al Monitor del Agente en la fase de conexión del Ambiente.

## Los Suscriptores
Se utilizan para escuchar los puertos de análisis pero no para comparar datos, sino para recolectar cobertura funcional. Para esto está la clase `uvm_subscriber`. Es un oyente prefabricado que trae un puerto `analysis_export` incorporado y solo hay que extender de esta clase e implementar la función `write()`.

## El Caso de Prueba Base (base_test)
Establece la infraestructura general. Construye el entorno de verificación y, por lo general, ejecuta una secuencia básica o de inicialización.

## Tests Derivados
Por ejemplo, si se quiere correr una prueba de escritura y lectura de registros, no hace falta cambiar el ambiente. Solo se heredan del `base_test` y cambian la secuencia.