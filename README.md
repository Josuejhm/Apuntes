## 1. Abreviaturas y definiciones
- `CMOS Estático`: El CMOS estático es un circuito combinacional con red pull-up (PUN) y red pull-down (PDN).
- `Bubble Pushing`: Cambio entre representaciones equivalentes de compuertas lógicas con la Ley de DeMorgan.
- `Compuertas Compuestas`: El CMOS estático puede manejar varias combinaciones inversoras de funciones AND/OR en una sola etapa, para conseguir funciones complejas. 
- `Esfuerzo Lógico` ($G$): Es la relación entre la capacitancia de entrada de la puerta y la capacitancia de entrada de un inversor que pueda suministrar la misma corriente de salida.
- `Esfuerzo Eléctrico` ($H$) : Es Relación entre la capacitancia de salida y la de entrada.
- `Esfuerzo Difurcación` ($B$) : Contabiliza la ramificación entre etapas en la ruta.
- `Esfuerzo de ruta` ($F$) :  El producto de los esfuerzos de la etapa.
- `Retardo parasítico` ($P$): Es el retardo de la puerta cuando conduce una carga cero.
- `Retardo de ruta` ($NF^{1/N}$) : Es la suma de los esfuerzos de la etapa.
-  `Mejor esfuerzo etapa` ($\hat{f} = F^{1/ N}$)
-  `cg`: complex gate (compuerta compleja)



## 2. Referencias
[1] N. Weste and D. Harris, CMOS VLSI Design: A Circuits and Systems Perspective, 4 edition. Boston: Addison-Wesley, 2010.

## 3. Diseño de un circuito CMOS estático para el cálculo de la función $F =(A + B)(C + D)$ con el menor retardo
Para el desarrollo de los circuitos a comparar, primero se buscan las compuertas equivalentes con la Ley de DeMorgan, como se muestra en la Fig.1 las diferentes equivalentes que hay para una misma función.

<p align="center">
<img src="https://github.com/user-attachments/assets/ca0424e2-9071-4502-bfb7-dca787ede6bb"/>
<div align="center">
 Figura 1. Descomposición en celdas simples utilizando el teorema de De Morgan
</div>
</p>

Teniendo en cuenta que cada entrada puede presentar un máximo de 30 $\lambda$ de ancho de transistor y que la salida debe conducir una carga equivalente a 500 $\lambda$ de ancho de transistor. 

Se calcula el fanOut general que es el mismo para los dos casos correspondiente a: 
 
$\Huge H=\frac{Cout(path)}{Cin(path)}=\frac{500}{30}=\frac{50}{3}$  

 
## 3.1 Análisis de retardo y consumo 

### 3.1.1 Análisis para el caso de compuerta lógica compleja

En la Fig. 2 se observa el diagrama de compuertas necesarias para aplicar la función lógica solicitada, en la Fig. 3 se observa el circuito con transistores, de tamaño 2 para los NMOS y 4 para los PMOS, para obtener resistencia unitaria en el peor de los casos. En la Fig. 4 se observa el

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/Complex_gate.jpg"/>
<div align="center">
 Figura 2. Circuito para la compuerta lógica compleja
</div>
</p>

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/initial_complex_gate.png"/>
<div align="center">
 Figura 3. Circuito transistorizado para la compuerta compleja 
</div>
</p>

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/values_cg.png"/>
<div align="center">
 Figura 4. Circuito transistorizado con capacitancias simplificadas para la compuerta compleja 
</div>
</p>


### - Velocidad mediante la teoría de esfuerzo lógico
Siguiendo la teoría del esfuerzo lógico, se plantea que: 

$\Huge G = g_{cg}⋅g_{inv} = 2 ⋅ 1 = 2$

$\Huge B = 1 $

$\Huge F = GHB = 2 ⋅ 1 ⋅ \frac{50}{3} = \frac{100}{3}$

$\Huge P = p_{cg} + p_{inv} = 4 + 1 = 5$

$\Huge N = 2$

$\Huge D = N⋅F^{\frac{1}{n}} + P = 2⋅(\frac{100}{3})^{\frac{1}{2}} + 5 = 16.54 \tau$

$\Huge \hat{f} = \sqrt(\frac{100}{3}) = 5.7$

$\Huge C_{in2} = \frac{500\lambda \cdot 1}{5.7} \approx 86\lambda$

$\Huge C_{in1} = \frac{86\lambda \cdot \frac{6}{1}}{5.7} \approx 30\lambda$


### - Consumo de potencia dinámica

Inicialmente se plantea el caso individual de la compuerta OR.


| A  | B | Q
| ------------- | ------------- |------------- |
|  0 | 0 | 0 | 
|  0 | 1 | 1 | 
|  1 | 0 | 1 | 
|  1 | 1 | 1 | 


De la tabla de la verdad se puede identificar:

$\Huge p_{x} = p_{y} = \frac{3}{4}$

La probabilidad de transicion estatica es:
$\Huge \alpha = \frac{3}{4} \cdot \frac{1}{4} = \frac{3}{16}$

Ahora bien, ya con el valor de las probabilidades tanto para x como y se procede a calcular la probabilidad resultante de F.

$\Huge p_{Z} = 1 -(\bar{p_{x}} \bar{p_{y}} ) =1- (\frac{3}{4}) \cdot (\frac{3}{4}) = \frac{7}{16}$

$\alpha = \frac{7}{16} \cdot (1 - \frac{7}{16}) = \frac{63}{256}$

$\Huge p_{F} = 1 - p_{Z}  = 1 - \frac{7}{16} = \frac{9}{16} $

$\alpha = \frac{9}{16} \cdot (1 - \frac{9}{16}) = \frac{63}{256}$

Tomando el peor de los casos
$\Huge f_{max} = \frac{1}{16.54 \cdot 25.5 ps} = 2.37 \ \text{GHz}$


Para el cálculo de las capacitancias de entrada independiente del tipo de compuerta lógica, se basa en la tecnología con la que se está trabajando en donde:

$\Huge C_{gate} = C_{ox} \cdot W \cdot L$

Los cuales corresponden a:

- W: ancho del transistor = 2.7 $\mu$ m
- L longitud del canal = 180 nm
- $C_{ox}$ capacitancia por unidad de área del óxido de puerta

Aproximadante,

$\Huge C_{ox} = \frac{ε_{ox}}{t_{ox}} = \frac {ε_{0} \cdot ε_{r}}{t_{ox}} $
$\Huge C_{ox} = \frac{3.45×10^{−11} F/m}{4×10^{−9} m} = 8.625×10^{−3}F/m^{2}$

$\Huge C_{gate} = 8.625×10^{−3} \cdot 2.7×10^{−6} \cdot 1.8×10^{−7}$

$\Huge C_{gate} = 4.19×10^{−15} F = 4.19fF ≈ 4.2fF$

Finalmente, después de sustituir todos los valores:

$\Huge P_{sw} = \alpha \cdot C \cdot (VDD)^2 \cdot f = \frac{63}{256} \cdot (4.2f F)  \cdot (1.8)^2 \cdot 2.37\ \text{GHz}$


$\Huge P_{sw} = 7.94 \ \mu \text{W}$



### 3.1.2 Análisis para el caso de compuertas simples en etapas 

Tal que el circuito equivalente se muestra en la Fig. 3.

<p align="center">
<img src="https://github.com/user-attachments/assets/73baf665-2345-4d4a-834e-9f4f87141126"/>
<div align="center">
 Figura 5. Circuito para el caso de compuertas simples en etapas
</div>
</p>

### - Velocidad mediante la teoría de esfuerzo lógico

Posteriormente se realiza el análisis del circuito compuesto por varias compuertas.

$\Huge G = \frac{5}{3} \cdot \frac{5}{3} = \frac{25}{9}$
$\Huge H = \frac{50}{3}$
$\Huge B = 1$
$\Huge N = 2$
$\Huge P = 2 + 2 = 4$

$\Huge F = GBH = \frac{1250}{24}$

$\Huge \hat{F} = \sqrt{F} = 6.8$

$\Huge D = 2 \cdot 6.8 + 4 = 17.6 \tau$

$\Huge C_{in2} = \frac{500\lambda \cdot \frac{5}{3}}{6.8} = 122.55\lambda$

$\Huge C_{in1} = \frac{122.55\lambda \cdot \frac{3}{5}}{6.8} = 30\lambda$

### - Consumo de potencia dinámica
Inicialmente se plantea el caso individual de la compuerta Nor.


| A  | B | Q
| ------------- | ------------- |------------- |
|  0 | 0 | 1 | 
|  0 | 1 | 0 | 
|  1 | 0 | 0 | 
|  1 | 1 | 0 | 


De la tabla de la verdad se puede identificar:

$\Huge p_{x} = p_{y} = \frac{1}{4}$

La probabilidad de transicion estatica es:
$\Huge \alpha = \frac{3}{4} \cdot \frac{1}{4} = \frac{3}{16}$

Ahora bien, ya con el valor de las probabilidades tanto para x como y se procede a calcular la probabilidad resultante de F.

$\Huge p_{F} = \bar{p_{x}} \cdot \bar{p_{y}} = (1-\frac{1}{4}) \cdot (1-\frac{1}{4}) = \frac{9}{16}$

$\Huge \alpha = \frac{9}{16} \cdot (1 - \frac{9}{16}) = \frac{63}{256}$

Tomando el peor de los casos se escoge como

$\Huge f_{max} = \frac{1}{17.6 \cdot 25.5 ps} = 2.23 \ \text{GHz}$

De igual forma, para el cálculo de las capacitancias de entrada independiente del tipo de compuerta lógica, se basa en la tecnología con la que se está trabajando en donde:

$\Huge C_{gate} = C_{ox} \cdot W \cdot L$

Los cuales corresponden a:

- W: ancho del transistor = 2.7 $\mu$ m
- L longitud del canal = 180 nm
- $C_{ox}$ capacitancia por unidad de área del óxido de puerta

Aproximadante,

$\Huge C_{ox} = \frac{ε_{ox}}{t_{ox}} = \frac {ε_{0} \cdot ε_{r}}{t_{ox}} $
$\Huge C_{ox} = \frac{3.45×10^{−11} F/m}{4×10^{−9} m} = 8.625×10^{−3}F/m^{2}$

$\Huge C_{gate} = 8.625×10^{−3} \cdot 2.7×10^{−6} \cdot 1.8×10^{−7}$

$\Huge C_{gate} = 4.19×10^{−15} F = 4.19fF ≈ 4.2fF$

Finalmente, después de sustituir todos los valores:

$\Huge P_{sw} = \alpha \cdot C \cdot (VDD)^2 \cdot f = \frac{63}{256} \cdot (4.2f F)  \cdot (1.8)^2 \cdot 2.37\ \text{GHz}$


$\Huge P_{sw} = 7.94 \ \mu \text{W}$


## 3.2 Tiempos de retardo y contaminación utilizando Elmore

### 3.2.1 Analisis de tiempos para compuerta compleja

Para este análisis, se debe tener en cuenta la compuerta compleja y el inversor. Además se utiliza el $\tau$ , tal que $\tau_n$=17.3 ps y $\tau_p$=25.5 ps.

<p align="center">
<img src="https://github.com/user-attachments/assets/951af82c-6b24-428c-bcad-3e624749d10c"/>
<div align="center">
 Figura 6. Diagramas para calculo del tpdf de la compuerta compleja
</div>
</p>

Para el tiempo de retardo de bajada, se toman solo los dos transistores N activos en serie, podría ser A = 1 y D = 1, originalmente todas las entradas en 0, originando el esquema de la Fig. 6, notese el 12C + 3C, 3C se debe a la conexión del inversor, entonces tenemos:

$\Huge t{pdf} = (\frac{R}{2}+\frac{R}{2})(12+3)C + \frac{R}{2} 2C = 16RC$

$\Huge t{pdf} = 16RC = 16 \tau = 276.8 ps$


<p align="center">
<img src="https://github.com/user-attachments/assets/65773ee9-b260-40be-b6ed-dcb69a88a271"/>
<div align="center">
 Figura 7. Diagramas para calculo del tpdr de la compuerta compleja
</div>
</p>


Para el tiempo de retardo de subida, se toma un transistor N activo, para permitir que el capacitor inferior se cargue en el proceso,y dos P activos de modo que A = 1, B = 0, C = 0, D = 0, originalmente todos en 1 menos A, notese que la resistencia inferior no se encuentra en el camino hacia la salida por lo tanto no se tiene en cuenta, entonces tenemos:

$\Huge  t_{pdr} = (15C+2C)(\frac{R}{2}+\frac{R}{2}) + 4C (\frac{R}{2})= 19RC$

$\Huge t_{pdr} = 19RC = 19 \tau = 329 ps$

<p align="center">
<img src="https://github.com/user-attachments/assets/96010a5b-b7a1-4138-bea5-236ebc43080a"/>
<div align="center">
 Figura 8. Diagramas para calculo del tcdr de la compuerta compleja
</div>
</p>


Para el tiempo de contaminación de subida, hay cero transistores N activos, de modo que A = B = C = D = 0, anteriormente con cualquier condición que provocara un cero en la salida, por tanto:

$\Huge t_{cdr} = 15C (\frac{R}{4}+\frac{R}{4})+4C(\frac{R}{4})= \frac{17}{2}RC$

$\Huge t_{cdr} = \frac{17}{2}RC = \frac{17}{2} \tau = 147.05 ps$


<p align="center">
<img src="https://github.com/user-attachments/assets/1c2ed4f7-865e-40d6-81ff-8a2c0f27a304"/>
<div align="center">
 Figura 9. Diagramas para calculo del tcdf de la compuerta compleja
</div>
</p>

Para el tiempo de contaminación de bajada, se toman todos los transistores N activos, generando la menor resistencia en el camino, entonces tenemos:

$\Huge t_{cdf} = 15C(\frac{R}{4}+\frac{R}{4})+2C(\frac{R}{4})= 8RC$

$\Huge t_{cdf} = 8RC = 8\tau = 138.4 ps$


<p align="center">
<img src="https://github.com/user-attachments/assets/9cc499b8-fbee-42da-a6d1-46e53df02f7d"/>
<div align="center">
 Figura 10. Diagramas para calculo de los retardos del inversor
</div>
</p>

Para este caso el tpdr se analiza igual al tcdr y el tpdf se analiza igual al tcdf, ya que no existe un mejor caso porque siempre hay un transistor activo, sea P o N, de modo que:

$\Huge t{pdf} = 3\tau_n = 3\cdot 25.4 ps = 76.2 ps$

$\Huge t{pdr} = 3\tau_p = 3\cdot 17.3ps = 51.9 ps$

Una vez obtenidos los tiempos para la compuerta compleja y el inversor, en la siguiente tabla se muestran los valores finales de retardo para el ánalisis de este caso.

| Tipo  | Compleja (ps) | Inversor (ps) | 
|-------|---------------|---------------|
| tpdf  | 276.8         | 82.5         | 
| tpdr  | 346.0         | 51.9         | 
| tcdf  | 138.4         | 82.5         | 
| tcdr  | 147.05        | 51.9         | 



### 3.2.2 Analisis de tiempos para compuertas simples

Para el análisis de los tiempos de retardo y contaminación del circuito compuesto por compuertas simples, se toma en cuenta que todas son compuertas NOR, por lo que en la primera etapa, ambas se analizan de la misma forma.

<p align="center">
<img src="https://github.com/user-attachments/assets/e37e715f-0dd9-4aee-a263-4a6c37e1d00b"/>
<div align="center">
 Figura 11. Diagramas para calculo del tpdf y tcdf de las compuertas simples
</div>
</p>

Para el tiempo de retardo de bajada tenemos:

$\Huge tpdf = R(11C) = 11\tau = 190.3 ps$

Para el tiempo de contaminación de bajada tenemos:

$\Huge tcdf = (\frac{R}{2})(11C) = \frac{11\tau}{2} = 95.15 ps$

<p align="center">
<img src="https://github.com/user-attachments/assets/e3de5b27-28f1-48f3-90ef-4ca00400c222"/>
<div align="center">
 Figura 12. Diagrama para calculo del tpdr y tcdr de las dos compuertas simples de entrada (A=0 y B=0)
</div>
</p>

Para el tiempo de retardo de subida tenemos:

$\Huge tpdr = R(11C) + \frac{R}{2}(4C) = 13RC$

$\Huge tpdr = 13RC = 13\tau_p = 331.5 ps$

Además se obtiene que el tiempo de retardo de subida es igual al tiempo de contaminación de subida al analizar el mismo circuito.

$\Huge tpdr = tcdr = 331.5 ps$

Se debe tener en cuenta que las dos compuertas NOR de la primera etapa comparten tiempos de retardo y contaminación, sin embargo, la NOR de salida es diferente ya que tiene a las dos compuertas anteriores como entrada, por ello se calculan sus tiempos por separado.

<p align="center">
<img src="https://github.com/user-attachments/assets/efb976bc-ba35-491c-821c-2d17a92f1de8"/>
<div align="center">
 Figura 13. Diagrama para calculo del tpdr y tcdr de la compuerta simple de salida 
</div>
</p>

Para el tiempo de bajada tienen que darse estas entradas, las compuertas de entrada deben ser A=0, B=0, C=1 y D=0, por lo que se usan los calculos de estas configuraciones de entrada para obtener tpdf, entonces:

$\Huge tpdf = R(6)C \approx 6RC = 103.8 ps$

Para el tiempo de contaminación de bajada, tienen que darse estas entradas, las compuertas de entrada deben ser A=0, B=0, C=0 y D=0, por lo que se usan los calculos de estas configuraciones de entrada para obtener tcdf, entonces:

$\Huge tcdf = (\frac{6}{2})RC \approx 3RC = 51.9 ps$


<p align="center">
<img src="https://github.com/user-attachments/assets/b1fc6269-e65c-4b85-b911-514fe4117d79"/>
<div align="center">
 Figura 14. Diagrama para calculo del tpdr y tcdr de la compuerta simple de salida (X=0 y Y=0)
</div>
</p>

Para el tiempo de retardo de subida, tiene que darse estas entradas, las compuertas de entrada deben ser A=1, B=1, C=1 y D=1, por lo que se usan los calculos de estas configuraciones de entrada para obtener tpdr, entonces:

$\Huge tpdr = R(6)C + \frac{R}{2}(4C) = (8)RC$

$\Huge tpdr = (8)RC = 204 ps$

Para el tiempo de contaminación de subida es el mismo por lo tanto tenemos:

$\Huge tcdr = R(6)C + \frac{R}{2}(4C) = (8)RC$

$\Huge tcdr = (8)RC = 204\ ps$

| Tipo  | Etapa 1 (ps) | Etapa 2 (ps) |
|-------|--------------|--------------|
| tpdf  | 190.3        | 103.8        |
| tpdr  | 331.5       | 204.0        |
| tcdf  | 93.15       | 51.9         |
| tcdr  | 331.5        | 204.0        |



## 3.3. Diseño de un trazado que minimice las capacitancias parásitas entre nodos en las compuertas 

### 3.3.1 Diseño para el caso de compuertas simples en etapas
### - Cálculo del tamaño de los transistores
Para la primera etapa que consta de compuertas lógicas NOR-2, para encontrar el tamaño de los transistores se dimensiona con las capacitancias de entrada encontradas en el primer punto y relacionando el ancho w que pueden tener en la entrada como: 

$\Huge 30 \lambda \rightarrow 5C$

$\Huge C \rightarrow \lambda  $

Y tomando el caso de la NOR unitaria con relación 4/1 los tamaños de los transistores de la primera etapa son: 


| NMOS | PMOS | 
| ------------- | ------------- |
|  $6 \lambda$| $24 \lambda$ | 
 
 

Seguidamente en la etapa 2 hay una única compuerta lógica NOR-2, la cual se dimensiona de igual manera con la capacitancia de entrada y un ancho w que puede soportar en la salida: 

$\Huge 122.55\lambda \rightarrow 5C$

$\Huge C \rightarrow \frac{122.55}{5}= 24.51$

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/simple_one_bigger.png"/>
<div align="center">
 Figura 15. Tamaños de los transistores de las etapas de la compuerta 
</div>
</p>

En donde se nota que el tamaño es prácticamente 4 veces la primera etapa como se muestra en la Figura #, por lo que se decide redondear para abajo y escoger como tamaño 24 para solo tener que construir transistores del mismo tamaño. También una forma se conseguir el tamaño del transistor de la segunda etapa se muestra en la Figura #, la configuración de 4 paralelos de compuertas NOR. 

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/simple_parallel.png"/>
<div align="center">
 Figura 16. Tamaños de los transistores de las etapas de la compuerta simplificada
</div>
</p>


### - Caminos de Euler
Como para la primer etapa y segunda las compuertas lógicas son la misma, genéricamente se analiza el caso general como se muestra en la Figura # y para la creación del diagrama de ayuda para el trazado de la compuerta con la Figura #.  

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/NOR_GATE.png"/>
<div align="center">
 Figura 17. Compuerta NOR-2 complementaria
</div>
</p>

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/stick_diagram_NOR.png"/>
<div align="center">
 Figura 18. Diagrama de palitos de la compuerta NOR
</div>
</p>

### 3.3.2 Diseño para el caso de compuerta lógica compleja

### - Cálculo del tamaño de los transistores

En la siguiente Figura se muestra el esquematico que nos guía a que la capacitancia de entrada de la compuerta compleja es $6C$ y para el inversor $3C$

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/complete_sch.png"/>
<div align="center">
 Figura 19. Esquemático de la compuerta compleja
</div>
</p>

Para este caso:

Compuerta Compleja

$\Huge 30 \lambda \rightarrow 6C$

$\Huge C \rightarrow \frac{30 \lambda}{5}= 6 \lambda$

Inversor

$\Huge 86 \lambda \rightarrow 3C$

$\Huge C \rightarrow \frac{86 \lambda}{3}= 29 \lambda$

De este modo lo tamaños de las compuertas son los siguientes:

| Compuerta | NMOS         | PMOS         |
|-----------|--------------|--------------|
| Compleja  | 10 $\lambda$ | 20 $\lambda$ |
| Inversor  | 28 $\lambda$ | 56 $\lambda$ |

### - Caminos de Euler

A partir de la Figura del esquematico se realizó el diagrama de palitos, obteniendo el siguiente diagrama para la compuerta compleja y el inversor

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/stick_complex.png"/>
<div align="center">
 Figura 20. Diagrama de palitos de la compuerta compleja e inversor
</div>
</p>


## 3.4. Diseño de esquematico y trazado
### 3.4.1 Diseño para el caso de compuertas simples en etapas

Para el caso de la tecnología con la que se está trabajando que es un proceso de $180 nm$, la longitud máxima de los transistores es igual a $l = 180\ nm$ y para las reglas de diseño que corresponde a un $\lambda = 90\ nm$. Según los tamaños encontrados anteriormente, el transistor NMOS tiene un ancho igual a $w = 6 \times \lambda = 6 \times 90\ nm= 540\ nm$ y el transistor PMOS $w = 24 \times \lambda = 24 \times 90\ nm= 2160\ nm$. En el esquematico se observa la división de los transistores, donde se utilizan 2 NMOS en paralelo de $270\ nm$ y 4 PMOS de $540\ nm$


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/sch_nor.png"/>
<div align="center">
 Figura 21. Esquemático en Cadence Virtuoso de la compuerta NOR2
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/layout_nor.png"/>
<div align="center">
 Figura 22. Layout en Cadence Virtuoso de la compuerta NOR2
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/ae_nor.png"/>
<div align="center">
 Figura 23. Extracción Analógica del  Layout en Cadence Virtuoso  de la compuerta NOR2
</div>
</p>


### 3.4.2 Diseño para el caso de compuerta lógica compleja


Para el caso de la compuerta compleja, el transistor NMOS tiene un ancho igual a $w = 10 \times \lambda = 10 \times 90\ nm= 900\ nm$ y el transistor PMOS $w = 20 \times \lambda = 24 \times  90\ nm= 1800\ nm$.Se decidió dividir en 4 los transistores como se muestra en el esquematico, utilizando 4 PMOs de $450\ nm$ y 4 NMOS de $225\ nm$. 

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/sch_cg.png"/>
<div align="center">
 Figura 24. Esquemático en Cadence Virtuoso de la compuerta compleja
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/layout_cg.png"/>
<div align="center">
 Figura 25. Layout en Cadence Virtuoso de la compuerta compleja
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/ae_cg.png"/>
<div align="center">
 Figura 26. Extracción Analógica del  Layout en Cadence Virtuoso  de la compuerta compleja
</div>
</p>

En cuanto al inversor se utilizaron 7 PMOS en paralelo, lo mismo para los NMOS, donde los PMOS tienen ancho de $740\ nm$, dando un ancho total de $7 \times 740\ nm = 5180\ nm$ convirtiendo este valor en terminos de $\lambda$ se obtiene que es $57\lambda$ aproximadamente los $58.7\lambda$ requeridos, y para los NMOS sería $7 \times 370\ nm = 2590\ nm$ aproximadamente $28.7\lambda$ cercano a los $29 \lambda$ requeridos.

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/sch_inv.png"/>
<div align="center">
 Figura 27. Esquemático en Cadence Virtuoso del inversor
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/layout_inv.png"/>
<div align="center">
 Figura 28. Layout en Cadence Virtuoso del inversor
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/ae_inv.png"/>
<div align="center">
 Figura 29. Extracción Analógica del  Layout en Cadence Virtuoso del inversor
</div>
</p>


## 3.5. Funcionalidad eléctrica y lógica de los circuitos por simulación (Esquematico y Layout)

En la Figura # se observa el esquematico de prueba utilizado, se denota el bloque de estimulos, con las entradas necesarias para realizar el análisis de tiempos en las distintas etapas.


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/test_sch.png"/>
<div align="center">
 Figura 30. Esquemático de prueba del sistema
</div>
</p>


### 3.5.1 Funcionalidad del esquemático

Primero se realizó la simulación del modelo esquematizado, en este se pueden denotar el correcto funcionamiento lógico de ambas compuertas.

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/sch_outputs.png"/>
<div align="center">
 Figura 31. Salidas obtenidas en ambos tipos de compuerta
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/sch_complex.png"/>
<div align="center">
 Figura 32. Salida y conexión interna de la compuerta compleja
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/sch_simple.png"/>
<div align="center">
 Figura 33. Salida y conexiónes internas de las compuertas simples
</div>
</p>

En las siguientes Tablas se muestra un resumen del comportamiento temporal de las compuertas

| Tipo  | Compuerta Compleja (ps)   | Inversor (ps) |
|-------|--------------             |-------------- |
| tpdf  | 126                       | 55.9          |
| tpdr  | 390                       | 39.91         |
| tcdf  | 56.45                     | 55.9          |
| tcdr  | 154.2                     | 39.91         |


| Tipo  | NOR2 Etapa1 (ps)          | NOR2 Etapa2 (ps) |
|-------|--------------             |-------------- |
| tpdf  | 123.1                     | 60.33         |
| tpdr  | 239.3                     | 71.86         |
| tcdf  | 62.01                     | 44.74         |
| tcdr  | 227.1                     | 60.28         |


### 3.5.2 Funcionalidad del layout (Extracción Analógica)

Despues se realizó la simulación con el layout (extracción analógica), dando como resultado las respectivas respuestas.

<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/layout_outputs.png"/>
<div align="center">
 Figura 34. Salidas obtenidas en ambos tipos de compuerta
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/layout_int_complex.png"/>
<div align="center">
 Figura 35. Salida y conexión interna de la compuerta compleja
</div>
</p>


<p align="center">
<img src="https://github.com/vlsi-i-25/tarea2-t2grupo04/blob/main/figures/layout_int_simple.png"/>
<div align="center">
 Figura 36. Salida y conexiónes internas de las compuertas simples
</div>
</p>

En las siguientes Tablas se muestra un resumen del comportamiento temporal de las compuertas


| Tipo  | Compuerta Compleja (ps)   | Inversor (ps) |
|-------|--------------             |-------------- |
| tpdf  | 141.6                     | 63.03         |
| tpdr  | 432.8                     | 42.95         |
| tcdf  | 62.56                     | 63.03         |
| tcdr  | 178.1                     | 42.95         |


| Tipo  | NOR2 Etapa1 (ps)          | NOR2 Etapa2 (ps) |
|-------|--------------             |-------------- |
| tpdf  | 145.3                     | 72.69        |
| tpdr  | 295.4                     | 72.85         |
| tcdf  | 70.03                     | 52.8         |
| tcdr  | 289.9                     | 63.1         |

Se puede observar que para ambos casos, tanto de esquematico como layout, los tiempos calculados difieren de lo obtenido por simulación, posiblemente no sea del todo suficiente la aproximación para ciertos casos, posiblemente hay capacitancias parasitas que generan los tiempos mayores a los calculados.


## 3.6. Extra: Análisis del consumo de potencia preciso

La estimación previa de consumo de potencia de los circuitos no refleja con precisión la potencia real, ya que asume condiciones estáticas con entradas fijas. En sistemas reales, las entradas varían de forma dinámica, por lo que para una evaluación más realista del consumo de potencia, debemos considerar la probabilidad de que cada entrada esté en estado alto (1).

Dado que las entradas son independientes entre sí y se consideran equiprobables, la probabilidad de que una entrada esté activa es:

```math
P_{\text{entrada}} = \frac{1}{4}
```

Con esta probabilidad, se puede determinar el **factor de actividad** (α), , y utilizando la máxima frecuencia operativa, se puede calcular la potencia dinámica por compuerta utilizando la ecuación:

```math
P_s = \alpha \cdot C \cdot V_{dd}^2 \cdot f
```

Donde:
- `α` es el factor de actividad,
- `C` es la capacitancia de carga,
- `V_dd` es el voltaje de alimentación,
- `f` es la frecuencia de conmutación.

La capacitancia la podemos expresar en función del área ocupada:

```math
C = C_{\text{área}} \cdot Área
```

Por lo tanto, la ecuación se reescribe como:

```math
P_s = \alpha \cdot (C_{\text{área}} \cdot Área) \cdot V_{dd}^2 \cdot f
```

Tal que:

- **Pitch:** 4.48 µm  
- **Capacitancia por unidad de área:** 450 pF/mm²  
- **Voltaje de alimentación:** 1.8 V  
- **Factor de actividad:** 63/256 (basado en la probabilidad de conmutación)  


### Compuertas Simples

Área: `4.48 µm × 30 µm`  
Frecuencia: `2.23 GHz`  

```math
P_{\text{simple}} = \frac{63}{256} \cdot (450 \, \text{pF/mm}^2 \cdot 4.48 \, \mu m \cdot 30 \, \mu m) \cdot (1.8)^2 \cdot 2.23 \times 10^9
\approx 107.54 \, \mu W
```

### Compuerta Compleja

Área aproximada: `4.48 µm × 23 µm`  
Frecuencia: `2.37 GHz`  

```math
P_{\text{compleja}} = \frac{63}{256} \cdot (450 \, \text{pF/mm}^2 \cdot 4.48 \, \mu m \cdot 23 \, \mu m) \cdot (1.8)^2 \cdot 2.37 \times 10^9
\approx 87.62 \, \mu W



