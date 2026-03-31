OOP -> busca que todas las cosas se vuelvan objetos. Los objetos son entes con funcionalidades. Con OOP se busca generar esas funcionalidades sin revelar cómo está hecho.

# Clase

Una clase es una plantilla para crear objetos de acuerdo con un modelo definido. Es un molde con el que se generan los objetos. Tiene atributos y métodos.

**Conceptos:** 
1. Objeto: instancia de la clase
2. Atributos: características individuales que diferencian un objeto de otro.
3. Métodos: especificación de acciones que puede realizar un objeto.
4. Constructor: método de una clase que coloca valores iniciales a un objeto cuando es creado.
**Conceptos extendidos:** 
5. Clase abstracta: sirve como base para crear otras clases. No se puede instanciar, solo heredar: También llamada clase virtual.
6. Método abstracto: declara un método pero no lo define.
7. Clase estática: sus atributos se definen una sola vez y todos los objetos los comparten. Sus funcionalidades se pueden llamar directamente sin instanciar el objeto, ya que no lo necesitan. 
8. Métodos estáticos: puede ser invocado sin la necesidad de instanciar su clase. El atributo existe independientemente del objeto. Solo puede acceder a variables o atributos estáticos.

**Pilares:**
1.  Abstracción: capa de simplificación del uso de los aparatos.
2.  Encapsulamiento: capacidad de decidir qué partes de una clase serán expuestas hacia otras entidades y cuáles serán ocultas.
3. Polimorfismo: capacidad de objetos de diferentes clases de responder a un mismo mensaje de manera única.
4. Herencia: capacidad de transferir características propias, como atributos y métodos de un objeto a otro. Permite reutilizar código existente y dedicarse solo a lo necesario o propio del objeto.

# Terminología básica en SV
1. Class: tipo definido por el usuario.
2. Object
3. Handle:  puntero a un objeto.
4. Property: variable dentro de un clase. Es un atributo.
5. Method: función que manipula propiedades de una clase.
6. Prototype: encabezado de una rutina que muestra el tipo, la lista de argumentos y el tipo de retorno. Sirve como referencia.

Nota:
```systemverilog
pkt0 = pkt1
```
Iguala los punteros. Para igualar las instancias hay que ir igualando los atributos uno por uno (en SV puro u duro). También se puede hacer un método para esto.

# Diferencia entre new() y new[]
Los dos asignan memoria e incializan valores, pero `new()` es una función que se llama para construir un único objeto, mientras que `new[]` es un operador que se usa para construir un array con múltiples elementos.

# Desasignar objetos
SV lleva o mantiene un registro del número de punteros que apuntan a un objeto; cuando no hay punteros SV libera memoria de el. Para hecerle clear a los punteros se setean a null.

# Una clase dentro de otra
Una clase puede tener una instancia de otra clase por medio de un puntero a un objeto.
**Importante:** Si un método solo va a modificar las propiedades de un objeto, el método debería declarar el puntero como un argumento de entrada. Si quiere modificar el puntero, el método debe declarar el puntero como un argumento `ref`.

# Arreglo de punteros
No existe tal cosa como un "arreglo de objetos". Hay que construir cada objeto en el array antes de usarlo. No hay forma de llamar new a un arreglo entero de punteros.

# Copiar objetos
Se puede copiar un objeto con new:
```systemverilog
src = new();
dst = new src;
```
Si una clase contiene un puntero a otra clase, solo se copia el valor del puntero, no una copia completo del objeto. Cuando se usa el operador `new` solo se crea una copia de un objeto de una clase, pero del otro no. Esto es porque este operador no llama a la función `new()` , como en este ejemplo:

```systemverilog
class Transaction;
	bit [31:0] addr, csm, data[8];
	static int count = 0;
	int id;
	Statistics stats;       // Handle points to statistics object
	
	function new();
		stats = new();      // construct a new Statistics object
		id = count++;
	endfunction
endclass

Transaction src, dst;
initial begin
	src = new();
	src.stats.startT = 42;
	dst = new src;          // copy src to dst with new operator
	dst.stats.startT = 96;  // changes stats for dst and src
	$display(src.stats.startT);  // 96
end
```

Pero para esto se pueden crear copias para obtener los valores de 42 y 96 en un copia completa del objeto.

```systemverilog
class Transaction;
	... // igual al anterior
	function Transaction copy();
		copy = new();               // construct destination object
		copy.addr  = addr;
		copy.csm   = csm;
		copy.data  = data;
		copy.stats = stats.copy(); // call Statistics::copy
	endfunction
endclass


class Statistics
	time startT;
	...          // ver ejemplo 5-22	
	function Statistics copy();
		copy = new();
		copy.startT = startT
	endfunction
endclass
```

Ahora se obtienen valores distintos en el initial del ejemplo tras anterior.

src -> id = 0 stats -> strartT = 42
dst -> id = 1 stats -> strartT = 96

# Public vs. Local
En SV todo es público a menos que se le coloque la etiqueta `local` o `protected`. Un miembro local solo puede ser accedido por los miembros de la misma clase y no por clases "hijas".

# Building a testbench
En la Fig. 5.9 de la página 163, las transacciones entre bloques son objetos, pero cada bloque también es modelado con un clase.

# En SV
this -> este atributo, esta clase.
super -> apunta al padre.
extern -> sirve para hacer prototipos.
void -> no retorna nada, solo modifica los atributos.

Aunque no se ponga el constructor, es implícito, osea, siempre va a estar ahí e inicializa todo en 0 o null.