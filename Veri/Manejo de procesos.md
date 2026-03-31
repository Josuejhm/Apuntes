Transactor -> manera de abstraer procesos que existen de manera independiente. El que maneja los procesos sigue siendo el SO. Los simuladores tienen que llamar al SO.

**Nota:** los statements en un `begin...end` corren secuencialmente, mientras que los que están en un `fork...join` se ejecutan en paralelo.

**Proceso**
Es una pieza de software que se ejecuta como una entidad separada a otros procesos. Cada proceso tiene su PC y demás.
No se puede saber dónde está el PC de los procesos, entonces se necesita algo para poder comunicar los procesos que están corriendo.

- El fork join detiene al proceso padre hasta que los hijos terminen.
- El fork join_any el padre se detiene hasta que uno de los hijos termine. Puede funcionar para obtener información para poder continuar cuando un proceso hijo termina y puede que los demás se necesiten o ya no.
- El fork join_none el proceso padre termina después de generar los hijos. Genera los hijos pero la ejecución continua en el thread del padre.

**Creando Threads en una clase**

```systemverilog
class Gen_drive;
	// Transactor that creates N packets
	task run(input int n);
		Packet p;

		fork
			repeat (n) begin
			p = new();
			`SV_RAND_CHECK(p.randomize());
			transmit(p);
			end
		join_none // Use fork-join_none so run() does not block
	endtask

	task transmit(input Packet p);
	...
	endtask
endclass

Gen_drive gen;

initial begin
	gen = new();
	gen.run(10);
	// Start the checker, monitor, and other threads
	...
end
```

Acá hay varias observaciones. Primero, el transactor no se inicializa en la función `new()`. El constructor solo debería de inicializar los valores, no comenzar ningún thread. Separar el constructor del código permite que se pueda cambiar cualquier variable antes de que se comience a ejecutar el código en el objeto. Esto permite inyectar errores, modificar los defaults y alterar el comportamiento del objeto. El task `run()` es el que inicia el thread. El thread es parte del transactor y debería de ser generado ahí, no en la clase padre.

# Estáticas vs automáticas
Por defecto, todas las variables en SV son estáticas; es decir, tienen vida en todo el programa.

Una variable automática es aquella que se inicializa cada vez que se entra el scope (task, function or block) en el que se encuentra. Una variable automática está en el stack y tiene vida mientras el proceso exista. Cuando se genera el paquete que va a correr el SO hay que describir el proceso mediante el descriptor del proceso.

Con el estático todos los hijos ven el puntero al valor modificado por el padre. Por eso en el ejemplo de cuidado con las variables estáticas, cuando imprime i, sale un 3, porque se llama al SO pero el proceso sigue corriendo. 

# Disabling Threads
Importante el disable fork para ahorra recursos cuando termina el proceso hijo, porque mata a los demás que tal vez ya no hacen falta.

El wait fork sirve para cuando ya terminó un hijo y el padre sigue avanzado con lo que pueda, pero es necesario que los demás hijos terminen para continuar.

Hay que tener cuidado porque desactivar un thread asincrónicamente (a la mitad del proceso) tiene efectos secundarios. Para esto es mejor diseñar el algoritmo para que compruebe si hay interrupciones en los puntos estables y luego libere los recursos.

**Cuidado:** `disable` mata todos los procesos que se están ejecutando en el bloque. Ver ejemplo pág. 241.

Para eliminar múltiples threads se utiliza `disable fork`. Esto detiene todos los threads hijos que se generaron del thread actual. Hay que tener cuidado porque se pueden detener muchos threads con esto, como los que se crearon de task calls alrededor. Para esto mejor rodear el código objetivo con un `fork...join` para limitar el scope del `disable fork`.

# Comunicación entre procesos
 Hay 3 formas en SV:
 - Events: cosas que pasan que todos los transactores subscritos al proceso pueden ver e indican que se tiene que hacer algo. Se tiene una variable en un proceso y se está esperado a que se le de trigger a esa variable para que haga algo. Se pueden pasar como una argumento a una rutina.
 - Semaphores: es un recurso que tiene llaves y se necesitan una o más llaves para poder acceder al recurso. Cuando un proceso toma una llave, ningún otro puede tomarlo.
 - Mailbox: medio mediante el cual se le manda un mensaje a un proceso y este levanta una bandera indicando que tiene un mail. Este lo procesa y lo puede devolver mediante otro mailbox.

Un transactor es como un procesador; es un ente que procesa instrucciones y devuelve resultados.

## Eventos
Se usan para sincronizar 2 o más procesos. Un proceso espera a que se dé el “evento” mientras que otro lo dispara; una vez disparado el evento los procesos que lo esperen pueden seguir ejecutándose. 
-> sirve para disparar el evento.

Un clock es un evento que se dispara periódicamente.  

Para esperar múltiples eventos no es buena idea usar un `wait fork`, porque, aunque espera a que todos los procesos hijos terminen, también espera a que terminen los transactores, drivers y cualquier otro thread que se generó en el ambiente. En las pág.249-250 hay ejemplos para hacer esto de una forma óptima.  La mejor forma es esperar a una cuenta del número de generadores corriendo (ver ejemplo 7.29).

Diferencia entre una función y un task
Un task puede tener delays y son sintetizables. Las funciones no se pueden sintetizar y **no** pueden tener retardo.

## Semáforos
Se usan para controlar el acceso a un recurso. Solo el que tiene la llave puede acceder al recurso. En SV un thread que pide una llave que no está disponible siempre se bloquea. Los threads bloqueados se colocan en una cola en el orden de una FIFO.

Sintaxis:
```systemverilog
semaphore [identifier_name];
```
Son objetos y se tratan como cualquier otro objeto.

Se puede crear un semáforo con una o más llaves usando el método new ( `sem = new(1);`).
Con get llega a pedir llaves y si no tienen, se queda esperando.
Con try_get si no hay, sigue ejecutando y hace polling para revisar periódicamente.

Hay que tener cuidado con los semáforos con múltiples llaves. Primero, se pueden regresar más llaves de las que se tomaron. Segundo, si solo se tiene una llave y un thread solicita dos, se bloquea. Si luego llega un segundo thread a solicitar solo una, este le pasa por encima al primer thread, sobrepasando el orden de la FIFO. Para esto se puede hacer una clase para elegir quién tiene prioridad.  

# Mailbox
Sirven para pasar información entre threads. Pueden tener un tamaño máximo o ilimitado. Es como una FIFO con un source y un sink. El source pone datos en el mailbox y el sink los saca. Cuando un source thread intenta poner un dato y el mailbox está lleno, el thread se bloquea hasta que se libere un espacio. De manera similar, si un sink thread quiere sacar un dato y el mailbox está vacío, el thread se bloquea hasta que se coloque un valor.

El mailbox es un objeto y se instancia llamando la función `new` y se le coloca un argumento (opcional) para limitar el número de entradas. 
Hay 2 tipos:
- Genéricos: pueden almacenar cualquier tipo de dato.
- Paramétricos: son los que pueden almacenar un tipo específico de datos.

Pueden crearse con dos tamaños posibles:
- Limitados: En este caso la caja de correo tiene una cantidad de datos que puede ser limitada, en caso de que la caja se llene, y un proceso trate de ingresar más datos, dicho proceso será suspendido hasta que haya espacio en la caja de correos.
- Ilimitados: Son las cajas de correo que son capaces de guardar una cantidad “ilimitada” de datos.

El put funciona parecido al get de los semáforos y el try_put es similar al try_get. También se puede usar el `peek()` task para obtener una copia de los datos en el mailbox pero no los elimina.

Características:
- Los datos pueden ser un único valor, como un int, o un logic de cualquier tamaño o un puntero.
- Nunca contiene objetos, solo referencias a ellos.
- Por defecto no tienen tipo, entonces se le puede colocar una mezcla. Esto **no** es recomendable, es mejor que se aplique un tipo de datos por mailbox utilizando mailboxes parametrizados. De la siguiente manera:
```systemverilog
mailbox #(Transaction) mbx_tx;  // Parameterized: recommended
mailbox mbx_untyped;            // Unspecialized: avoid
```

**Importante:** Los mailboxes son similares a una FIFO ilimitada; el Productor puede poner cualquier cantidad de valores en el mailbox antes de que el Consumidor los saque. Sin embargo, lo mejor es que los dos threads operen en lockstep de tal manera que el Productor bloquea hasta que el Consumidor termine con el valor. Por defecto el tamaño es cero y cualquier tamaño mayor que cero genera un mailbox limitado. 
Recordar que si se intentan colocar más valores que ese límite, `put()` bloquea hasta que haya una vacante. Un mailbox limitado funciona como un buffer entre dos procesos. En el ejemplo 7.37 el Productor genera el siguiente valor antes de que el Consumidor lea el valor actual. La ventaja de que el Productor no se adelanta al Consumidor es que toda la cadena de estímulos corre en lockstep. De esta manera, el generador del más alto nivel solo se completa cuando la transacción del más bajo nivel completa la transmisión.

# Importancia del peek()
`peek()` juega una papel importante en la sincronización de threads ya que permite que el Consumidor termine la transacción antes de que el Productor genere valores nuevos. Si el loop del Consumidor empieza con un `get()` la transacción se extraería inmediatamente del mailbox, entonces el Productor se levantaría antes de que el Consumidor termine con la transacción. La idea es usar `get()` para extraer los datos una vez termina el Consumidor.

# Sincronizar threads con eventos
Se pueden usar eventos para bloquear al Productor. La idea es que se use un evento para bloquear al Productor luego de que coloque los datos en el mailbox y el Consumidor activa el evento una vez consume esos datos. De esta manera, se bloquea al Productor hasta que el Consumidor termina la transacción.

El mailbox contiene funciones para manejar múltiples procesos que están tratando de accederlo; el queue no. Las mailbox internamente usan semáforos para asegurar la atomicidad de las transacciones de push y pop.

