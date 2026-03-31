El método de Esfuerzo Lógico es un modelo simplificado para **estimar el retardo** y optimizar el tamaño de los transistores en circuitos digitales. Permite a los diseñadores tomar decisiones críticas rápidamente, como elegir la mejor topología de circuito o determinar el número óptimo de etapas para minimizar el retardo. Forma más rápida que los [[Respuesta en CC y transitoria#Modelos de retardo|Modelos de retardo]].


# Retardo en una Compuerta Lógica 
El retardo de una sola compuerta ($d$) se expresa en unidades independientes del proceso . El retardo se expresa en términos (múltiplos) de $\tau = 3RC$ ($d = d_{abs}/\tau$) mediante la ecuación: $d=f+p$. Donde:
- $f$ **(Retardo de esfuerzo o esfuerzo de etapa):** Se divide en el **esfuerzo lógico (g)**, que mide la capacidad relativa de la compuerta para entregar corriente (con g=1 para un inversor), y el **esfuerzo eléctrico (h o fanout)**, que es la relación entre la capacitancia de carga y la de entrada ($h=C_{out​}/C_{in}$​).
- $p$ **(Retardo parásito):** Es el retardo causado por la capacitancia de difusión interna de la propia compuerta cuando no tiene carga externa. Básicamente el retardo que presenta la compuerta cuando no tiene carga. Se da por las capacitancias internas de la compuerta.

**Nota:** el esfuerzo lógico es independiente del tamaño, mientras que el eléctrico sí.

Para un inversor:
$$
\begin{aligned}
d &= f + p \\
&= gh + p \\
d_r &= 1h + 1 \\
d_f &= 1h + 1
\end{aligned}
$$


En el caso de la compuerta NAND de la diapositiva 6 se trata de una compuerta mal diseñada (no se comparte el contacto), osea, es el peor de los casos. A la NAND le cuesta más manejar corriente que el inversor.

- **Observación**: los números de la tabla de la diapositiva 9 son múltiplos de $\tau$. Por ejemplo, la para la NOR de 2 entradas tiene un retardo de $2\tau$. 

**Oscilador de anillo**
Permite genera retardos. $N$ es el número de etapas. El tiempo en 0 es $ND$ y el tiempo en 1 también, por eso el periodo es $2ND$.

**Multistage Logic Networks**
$x$, $y$ y $z$ representan el tamaño real de la compuerta. La idea es ver qué tamaño de compuertas permiten minimizar el retardo de **ruta**. Path Logic Effort -> qué tanto le cuesta a la ruta manejar la carga. 
En este caso $F = GH$. Demostración:
$$
\begin{aligned}
F &= \left(1\frac{x}{10}\right) \left(\frac{5}{3}\frac{y}{x}\right) \left(\frac{4}{3}\frac{z}{y}\right) \left(1\frac{20}{z}\right) \\

F &= \frac{1}{10}\frac{5}{3}\frac{4}{3}20 \\

G &= 1\frac{5}{3}\frac{4}{3}1 \\

H &= \frac{20}{10} \\

GH &= \frac{20 \cdot 5 \cdot 4}{ 10 \cdot 3 \cdot 3}

\end{aligned}
$$
El Path effort delay sí depende del tamaño de la compuerta.

Una compuerta unitaria es tan chiquita que no es tan buena manejando corriente. Como los transistores son tan pequeños, la resistencia es alta y se dura mucho cargando la capacitancia de carga (diapositiva 22). Tomar en cuenta que al agregar etapas también hay retardos parasíticos; por lo tanto, no siempre se va a obtener el valor más óptimo entre mayor sea la cantidad de etapas en una ruta.

Se pueden agregar inversores a la salida porque no cambian la lógica (si la cantidad es par, se mantiene. Si es impar, se invierte pero eso no es mayor problema).