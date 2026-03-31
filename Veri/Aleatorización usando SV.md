Se usa para poder generar suficientes caso de uso, porque estos tienden al infinito. **CRT:** Constrained-Random Tests.

**Pruebas dirigidas**: pruebas enfocadas en buscar bugs específicos. Esto solo prueba escenarios que a uno se le ocurren. Son necesarias muchas pruebas para poder cubrir el plan de validación. Son rígidas (es muy difícil reutilizar el código).

**Pruebas con aleatoriedad controlada**: la idea es cubrir grupos de escenarios. La aleatoriedad controlada significa que se escoge aleatoriamente entre los casos posibles, donde, al escoger uno de ellos, la salida deja de ser aleatoria. 

**¿Qué se debe aleatorizar?**
Eso depende del dispositivo. En general:
- Datos de entrada.
- Configuración del dispositivo.
- Configuración del ambiente.
- Excepciones en los protocolos.
- Tipos de errores y violaciones que ocurren y su secuencia de ocurrencia. -> El tb debe ser capaz de enviar estímulos funcionalmente correctos y con un flip en la configuración comenzar a inyectar tipos random de errores en intervalos random.
- Retardos en la ocurrencia de eventos y sincronización.

**Semilla**
Se usan un generador de números pseudoaleatorios (es una función) con un valor inicial (o vector) conocido como semilla. Se generan de acuerdo con una distribución. Si cambia la semilla, los valores generados cambian; por eso, al encontrar un bug, hay que guardar la semilla. Por lo general se utiliza el reloj del sistema.

**Proceso**
Restricciones: poner limitación a la aleatoriedad de una variable. 
Se corre una series de semillas, se identifican huecos y se realizan modificaciones. Si es necesario, se hacen pruebas dirigidas.

**En SV**
rand -> uniforme, todas las variables tienen la misma probabilidad de ocurrir. El área bajo la curva es 1 y si se grafica, se ve como una línea recta. Al colocarlo en una estructura de datos, se hereda la función randomize(). randomize() se aplica sobre la clase y no sobre atributos, por lo que al llamarla va randomizar todos los atributos posibles en la clase.

randc -> una vez que se obtiene un resultado, este se excluye de los posibles valores hasta que se gastan. Se conoce como probabilidad sin reemplazo.

Al llamar la función randomize() genera valores aleatorios sobre las estructuras de datos (atributos).

**Bloques de restricción**
Se define así:
```systemverilog
constraint [name_of_constraint] { [expresion 1];
								  [expresion 2]; }
```
 Es importante colocar un constraint si se va a randomizar un arreglo dinámico, porque se puede obtener como resultado un arreglo sumamente grande. Aplica lo mismo para las colas.
 
**Operador inside**
Se puede utilizar para solo elegir valores dentro una lista. También se puede usar para excluir valores con el operador de negado (!).

Como las restricciones están activas al mismo tiempo, hay que revisar que no haya conflictos entre ellas.

**Solve before**
Básicamente consiste en resolver una variable antes de intentar la otra. En este ejemplo se puede ver.

```systemverilog
class ABC;
	rand bit       a;
	rand bit [1:0] b;
	
	constraint c_ab { a -> b == 3'h3;
	                  solve a before b;
	                }
endclass

module tb;
	initial begin
		ABC abc = new;
		for (int i; i < 8, i++) begin
			abc.randomize();
			$display("a=%0d b=%0d", abc.a, abc.b);
		end
	end
endmodule
```

En este caso cambia la probabilidad. La probabilidad de que $a = 0$ es $0.5$, entonces: $P(a=0\hspace{1mm}y\hspace{1mm}B=x)=(1/2) \cdot (1/4) = 1/8$. Ahora $P(a=0\hspace{1mm}y\hspace{1mm}B=3)=(1/2)$.

Sin colocar el solve before la probabilidad es distinta. Cuando a es 0 b puede tener 4 valores, mientras que cuando es 1, b puede tener 1 valor. La probabilidad de cada combinación posible es de $1/5$.
 