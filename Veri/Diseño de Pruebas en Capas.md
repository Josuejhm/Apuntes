**Regresión**: lista de pruebas que ya han pasado para un dispositivo. Indica que lo que funciona antes de agregar algo nuevo.
**DFX**: Features que se introducen para poder probar el dispositivo después de fabricado.

La entradas y salidas tienen un protocolo específico. Se generan estímulos por tiempo y se revisan las salidas para ver si están bien. En la vida real es muy complicado revisar ciclo por ciclo, entonces se hace una verificación estadística para ver que la salida está dentro de lo esperado.

Las pruebas estimulan ciertos features en específico.

La distribución estadística se saca por medio de un histograma.

**Taxonomía de un ambiente de prueba en capas**
Aleatorización controlada en capas.
- Transactor: pieza de software que funciona de manera "independiente" que ejecuta instrucciones de otra entidad.
- Interface: es un montón de cables. En SV es una entidad que contiene un montón de cables que tienen nombre.
- En la capa de comando hay varios dispositivos que son transactores. Se generan por medio de threads.

**Capa de Comando:**
Cada uno de estos corre en threads distintos.
- Driver: tiene la función de manejar señales en el DUT que viven en el tiempo de simulación. Tiene control del ciclo de reloj, cuándo hay un flanco positivo, cuándo hay un ciclo negativo y demás. Se utilizan lenguajes de nivel superior para darle indicaciones. El Driver utiliza protocolos para hacer lo que se le está indicando. El Driver se encarga de ver qué señales tiene que levantar. Maneja las entradas y el DUT maneja las salidas
- Aserciones: Funciona como un monitor y se puede ver como un ambiente en sí mismo. Normalmente viven en las interfaces y verifican protocolos (algo que siempre ocurre de manera ordenada). Son "entidades" que verifican que una condición se está dando. No son sintetizables y pueden estar dentro del RTL.
- Monitor: su función es escuchar; no maneja ninguna señal y no se debería comunicar con el Driver. Se conectan a las interfaces. Lo único que hace es decirle al scoreboard lo que está ocurriendo; no opina si eso está bien o está mal. La información la empaqueta dentro de un objeto y la manda al nivel superior para determinar si está bien o no. Escucha al Driver y al DUT. También le indica al nivel superior lo que va a hacer el Driver. El monitor está escuchando lo que ocurre en la interface del dispositivo. **Nota:** Recordar que el objetivo del ambiente de verificación es revisar el dispositivo.

**Nota:** El monitor y el Driver entienden el protocolo.

**Capa Funcional:**
Esta capa es software puro y duro. No vive en el tiempo de simulación. Todo lo que hacen tarda 0 ciclos de reloj. Son transactores (una entidad de software que toma instrucciones y las ejecuta). Como corre en tiempo 0, solo se puede detener hasta que pare lo de la capa funcional. Normalmente se escribe en SV, pero si está en otro lenguaje se conecta por medio de DPI (último capítulo del libro).
- Agente: No es lo mismo que el agente de UVM. Es el dispositivo de nivel superior que le da instrucciones al Driver. Normalmente es un objeto y el Driver toma ese objeto como una instrucción y por medio del protocolo determina cómo se hace lo que se está pidiendo. Le dice al Scoreboard todo lo que hace.
- Scoreboard: Es como una base de datos (una estructura de datos que lleva el control de lo que ha ocurrido en el sistema).
- Checker: Revisa lo que le llega del Monitor con lo que hay en el Scoreboard. Indica si lo que ocurre es correcto o no. Tiene su propia lógica. Siempre tiene que ser capaz de detectar si hay un error.

**Capa de Escenario:**
- Generador: Envía instrucciones de un nivel superior al Agente. Por ejemplo: Le puede decir que configure el dispositivo en modo audio y el Agente entiende que eso consiste en un conjunto de lecturas y escrituras y se lo comunica al Driver.

**Ambiente:** Es la encapsulación de todas las capas. El testbench es una instancia del Ambiente.

**Test:** Conjunto de instrucciones que se le van a dar al Generador. Son limitaciones en los escenarios.

# Ejemplo FIFO
Al hacer pop a una FIFO se le está diciendo que ya se leyó el dato. El dato en Dout siempre es visible.

En algunos casos los Flops no tienen reset para que sean más pequeños y consuman menos potencia. Como la función del reset es setear el dispositivo en un estado conocido, van a ver X. La idea es las X no estén en un punto crítico, como la entrada de una máquina de estados.

En la mayoría de los dispositivos no es buena idea meter un reset a la mitad porque es complicado. Además de que el reset vuelve al dispositivo al estado inicial.

Cuando un DUT es parametrizable, el ambiente debe de serlo también. Esto para que el ambiente se acomode al dispositivo si se le cambia un parámetro. Esto significa que si en el DUT hay un parámetro, en el ambiente también tiene que ser un parámetro.

Probar con 5s y As se hace para generar máxima interferencia y para ver el peor caso de consumo posible (power bug). Consiste en togglear todos los bits de manera más frecuente posible. También para poder probar temas de disipación, porque todo ese trabajo de bits va a ocasionar que el dispositivo se caliente, causando que se degrade.

PlusArg: Argumento que se pone en la línea de corrida y no en la de compilación que tiene esta forma:
```systmeverilog
+Argumento = X
```
 Esto cambia los constraints. Evita tener que compilar todo el ambiente otra vez.
 Su objetivo es no tener que volver a compilar y así no se tiene que correr toda la regresión. Al cambiar el DUT hay que volver a sintetizar y, por ende, hay que correr toda la regresión para ver que sigue funcionando todo lo que ya funcionaba. También su objetivo es ver si la solución agregar nuevos bugs.

En los paréntesis que están en el diagrama está el tipo de paquete o mensaje que se va a mandar de un transactor a otro.

Es más fácil debuggear errores de compilación que errores de corrida. Por eso mejor limitar algo, hacerlo paramétrico.