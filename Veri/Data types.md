**Estructuras de datos:** Organizar datos de forma eficiente. Son un medio para manejar grandes cantidades de datos de manera eficiente. Sirven para representar flujos de información (son centrales por esto).

```systemverilog
int array2 [0:7][0:3]
```
En este caso se tiene un arreglo empaquetado de 2 dimensiones, donde hay 8 entradas y 4 entradas
Otra forma (son equivalentes):

```systemverilog
int array3 [8][4]
```

Para inicializarlo es necesario colocar un ' antes. Por ejemplo:

```systemverilog
initial begin
	static int ascend[4] = '{0,1,2,3}; // initialize 4 elements
	int descend [5]
	
	descend = '{4,3,2,1,0};   // set 5 elements
	descend[0:2] = '{7,6,5};  // set just first 3 elements
	ascend = '{4{8}};         // four values of 8
	ascend = '{deafault:42};  // all elements are set to 42
	
end	
```

Las inicializaciones de las variables normalmente se hacen una sola vez al inicio. Entonces, por lo general se hacen bloques *initial.* 

Sintaxis importante:
```systemverilog
foreach(md[i,j])
```

# Arreglos empaquetados y no empaquetados
La diferencia está, sobre todo, cuando se hacen asignaciones. Estos se hacen, normalmente, en arreglos empaquetados. Las variables empaquetadas tienen que tener el mismo tamaño.

**Arreglos no empaquetados:** Guardan el valor en la porción baja de la palabra mientras que los bits superiores se dejan sin uso.
```systemverilog
bit [7:0] b_unpacked [3];
```
Esto se vería así:
`b_unpacked[0]:`           UNUSED SPACE               7 6 5 4 3 2 1 0
`b_unpacked[1]:`           UNUSED SPACE               7 6 5 4 3 2 1 0
`b_unpacked[2]:`           UNUSED SPACE               7 6 5 4 3 2 1 0


**Arreglos empaquetados:** Son tratados como un arreglo y como un valor. Se guarda como un set contiguo de bits que no tiene espacio sin utilizar con los arreglos no empaquetados.
```systemverilog
bit [3:0] [7:0] bytes;
```
Esto se vería así:
`bytes` 7 6 5 4 3 2 1 0 7 6 5 4 3 2 1 0 7 6 5 4 3 2 1 0 7 6 5 4 3 2 1 0

Para acceder a uno de ellos se hace de esta forma: `bytes[3][7]`. Con esto se estaría accediendo al primer 7 de izquierda a derecha.


Nota: Las entradas no empaquetadas son instancias de las entradas empaquetadas.

Por otro lado, la ventaja es que una matriz empaquetada es útil si se necesita convertir hacia y desde escalares. Por ejemplo, es posible que deba hacer referencia a una memoria como un byte o como una palabra.

# Arreglos dinámicos
Los arreglos dinámicos no tienen tamaño y pueden ir variando. Cuando se utiliza hay que inicializarlo con `new[x]`, donde `x` es la cantidad de elementos.
```systemverilog
dyn = new[5];
dyn = new[20] (dyn);
```
En el caso del segundo se tienen 20 entradas y se van a copiar los primeros 5 valores que se tenían antes.

Nota: esto por dentro es una [[OOP#Clase|Clase]].

# Queue
Es una combinación entre una lista enlazada y un arreglo. Se pueden añadir y quitar elementos en cualquier parte de la cola, pero es más rápido operar en los extremos. Una cola es declarada con un signo de dólar de la siguiente manera:

```systemverilog
q2[$] = {3,4};   // queue literals do not use '
```

# Arreglos asociativos
Son arreglos que no necesariamente se tienen que acceder a través de un puntero en la posición del arreglo, sino que se pueden acceder a través de una llave que puede ser un string, un número o lo que sea. Básicamente es un arreglo de cosas que tienen nombre.
**Ventaja:** pueden emular el comportamiento de una memoria sin tener que reservar la enorme cantidad de espacio (por ejemplo).

**Desventaja:** es mucho más lento accederlos que acceder a una pila (en los extremos), o un arreglo de tamaño fijo o dinámico.

```systemverilog
module tb;
	int    array1 [int];          // an integer array with integer index
	int    array2 [string];       // an integer array with string index
	string array3 [string];       // an string array with string index
	
	intial begin
		// initialize  each dynamic array with some values
		array1 = '{1 : 22,
		           6 : 34};
		           
		array2 = '{"Ross" : 100,
		           "Joey" : 60};
		           
		array3 = '{"Apples" : "Oranges",
		           "Pears" : "44"};
		           
		// print each array
		$display ("array1 = %p", array1);
		$display ("array2 = %p", array2);
		$display ("array3 = %p", array3);
	end
endmodule
```

**Nota:** `%p` imprime todo el contenido de un arreglo.




