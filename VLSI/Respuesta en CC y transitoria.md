# Teoría del transistor MOS
El transistor MOS es un dispositivo de tres o cuatro terminales (gate, drain, source y body) donde la corriente entre drain y source se controla mediante el voltaje en la gate. 

Recordar que el el voltaje de threshold ($V_t$) es **voltaje de gate crítico** necesario para que un transistor pase del estado de corte al de conducción, permitiendo la formación de una **capa de inversión** (canal) bajo el gate.

1. Estructura y Modos de Operación del Capacitor MOS
La puerta y el cuerpo forman un capacitor MOS con tres modos de operación según el voltaje de gate ($V_g$​).
- **Accumulation:** Con $V_g​ < 0,$ los huecos se atraen debajo del gate.
- **Depletion:** Con $0 < V_g​ < V_t​$, los huecos son repelidos, creando una región sin portadores libres.
- **Inversion:** Con $V_g​ > V_t​$ (voltaje de umbral), se atraen electrones creando una capa conductora o "canal".

2. Regiones de Operación (modelo nMOS)
El comportamiento depende de los voltajes en las terminales ($V_{gs}$​ y $V_{ds}$​).
- Cutoff: $V_{gs} ​< V_t​$. No hay canal y la corriente es prácticamente cero ($I_{ds} ​\approx 0$). Se dice que el transistor está apagado.
- Linear: $V_{gs}​ > V_t​$ y $V_{ds} ​< V_{gs} ​− V_t$​. Se forma el canal y el transistor actúa como una resistencia controlada, donde $I_{ds}​$ aumenta con $V_{ds}$.
- Saturation: $V_{gs} ​> V_t​$ y $V_{ds} ​> V_{gs} ​− V_t​$. El canal se estrecha ("pinch-off") cerca del drain; la corriente se vuelve independiente de $V_{ds}$​ y actúa como una fuente de corriente.

**Nota:** La corriente de leakage cuando el transistor está apagado puede ser significativa cuando se multiplica por millones y millones de transistores en un chip.

3. Características de Corriente-Voltaje (I-V)
El modelo de primer orden (Shockley) describe la corriente según la carga en el canal y su velocidad de movimiento.
- **Carga del canal:** Se modela como un capacitor de placas paralelas donde $Q_{canal}​=C_{ox}​WL(V_{gs}​−V_{ds}/​2−V_t​)$.
- **Velocidad de portadores:** Proporcional al campo eléctrico lateral ($E=V_{ds}​/L$) y a la **movilidad** ($\mu$), donde $v=\mu E$.
- **Ecuación Lineal:** $I_{ds}​=\beta(V_{gs}​−V_t​−V_{ds}​/2)V_{ds}$.
- **Ecuación de Saturación:** $I_{ds}​=\frac{\beta}{2}​(V_{gs}​−V{t}​)^2​$.
- **Parámetro Beta ($\beta$**):** Depende de la tecnología y la geometría: $\beta=\mu C_{ox}​\frac{W}{L}$.

En los transistores **pMOS**, todas las polaridades y dopados se invierten. Su movilidad ($\mu_p$​) es típicamente 2-3 veces menor que la de los nMOS ($\mu_n​$), por lo que deben ser más anchos para ofrecer la misma corriente. Esto es porque la movilidad de los huecos en el silicio es típicamente menor que la de los electrones. Entonces, los transistores pMOS brindan una corriente menor que los transistores nMOS de un tamaño comparable y por ende son más lentos.

4. Capacitancia
- **Capacitancia de Gate ($C_g$​):** Fundamental para crear la carga del canal. Es necesaria para atraer carga para invertir el canal, entonces se necesita un valor alto para obtener una $I_{ds}$ alta. Se aproxima como $C_{gs}​=C_{ox}​WL$.
- **Capacitancia de Difusión:** Capacitancia parásita no deseada entre el source/drain y el body (sustrato). Depende del área y el perímetro de las regiones de difusión, de los niveles de dopaje y del voltaje.

5. Respuesta en CC
Analiza la relación entre el voltaje de salida ($V_{out​}$) y el de entrada ($V_{in}$​) cuando los cambios son lentos. Básicamente consiste en medir qué pasa con la salida cuando se pone un valor constante en la entrada.

- **Inversor CMOS**
Para un inversor, se cumple que la corriente que sale del nMOS debe ser igual a la que entra al pMOS ($∣I_{dsn}​∣=∣I_{dsp}​∣$). La curva de transferencia de voltaje (VTC) presenta cinco regiones según el estado de los transistores.
**Región A:** nMOS en corte, pMOS lineal ($V_{out}​=V_{DD}$​).
**Región B:** nMOS saturado, pMOS lineal.
**Región C:** Ambos en saturación (punto de máxima ganancia y conmutación).
**Región D:** nMOS lineal, pMOS saturado.
**Región E:** nMOS lineal, pMOS corte ($V_{out}​=0$).

# Márgenes de Ruido e Inversores Desviados
**Márgenes de Ruido:** Indican cuánto ruido puede tolerar una entrada antes de dejar de ser reconocida correctamente. Se definen en los puntos de ganancia unitaria (pendiente = -1) de la curva de transferencia.
**Relación Beta ($\beta_p​/\beta_n​$):** Si esta relación no es 1, el punto de conmutación se aleja de $V_{DD}​/2$ (va a cruzar un poco antes), creando una compuerta desviada ("skewed gate"). **Nota:** El $\beta$ depende de su tamaño (WL), del $C_{ox}$ y $t_{ox}$ y de la movilidad. Como la movilidad de los transistores p es menor que la de los n, hay que compensar y **no** mantener los dos del mismo tamaño. Como no se puede jugar con la movilidad (eso es del material) y $C_{ox}$ y $t_{ox}$ son características de las tecnologías, entonces se juega con el WL. Al fabricar las compuertas se escoge el L para todos los transistores y se juega con el W. EL W del p debe ser mayor al del n. Con esto se maximizan los márgenes de ruido en alto y bajo.

- Transistores de Paso
Los transistores nMOS pasan un "0" fuerte pero un "1" degradado ($V_{DD}​−V_t​$) debido a que se apagan cuando $V_{gs}​<V_t​$. Por el contrario, los pMOS pasan un "1" fuerte pero un "0" degradado. Para pasar ambos valores íntegramente se utilizan **compuertas de transmisión** (n y p en paralelo)

6. Respuesta Transitoria y Modelos de Retardo
El análisis transitorio describe $V_{out}​(t)$ cuando $V_{in}​(t)$ cambia en el tiempo. Se tratan de ecuaciones diferenciales porque hay capacitores. Cada vez que hay ecuaciones diferenciales es porque hay un sistema que depende del estado pasado. En un sistema meramente resistivo no hay dependencia del estado pasado.

- Tipos de retardo
$t_{pdr}​$/$t_{pdf}$​ **(Propagation delay):** Tiempo máximo desde que la entrada cruza el 50% hasta que la salida sube o cae al 50%. Como se trata del inversor, esto se mide respecto a su única entrada. En otras compuertas con más entradas, se tienen tiempos de propagación para cada entrada hacia la salida. Siempre se habla del tiempo más grande, tanto de subida como de bajada. También van a depender de las condiciones iniciales de la compuerta. Por definición, el tiempo de propagación es el tiempo más largo que existe desde que cambia la entrada hasta que la salida es **estable**.

$t_{pd}$: tiempo promedio de propagación. $t_{pd} = (t_{pdr} + t_{pdf})/2$.

$t_r​$/$t_f$​ **(Rise/Fall time):** Tiempo en que la salida pasa del 20% al 80% (o viceversa). Esto es un porcentaje de $V_{DD}$. Para celdas modernas se usa mucho del 30% al 70% (o viceversa). Esto porque entre 30% y 70% es una sección más lineal de la salida.

$t_{cdr}​$/$t_{cdf}​$ **(Contamination delay):** Tiempo mínimo que tarda la salida en empezar a cambiar tras un cambio en la entrada. Se mide igual que el tiempo de propagación. Va a tener sentido para compuertas de múltiples entradas. Para un inversor, que tiene una sola entrada, los tiempos de propagación y contaminación son exactamente lo mismo. El tiempo de contaminación es el tiempo desde que cambia la entrada hasta que se empiezan a ver cambios en la salida. Es menor que el tiempo de propagación.

$t_{cd}$: tiempo promedio de contaminación. $t_{cd} = (t_{cdr} + t_{cdf})/2$.

# Modelos de retardo
Dado que resolver ecuaciones diferenciales es complejo, se utiliza un modelo simplificado donde el transistor se trata como un interruptor ideal con una **resistencia efectiva (R)** (no es un valor real de resistencia, es el promedio de la resistencia presente en el transistor a lo largo de la transición) y una **capacitancia (C)**. 

El retardo se estima como $t_{pd}​=RC_{total}$.
**Transistor unitario nMOS:** Resistencia R, capacitancia C.​
**Transistor unitario pMOS:** Resistencia 2R, capacitancia C (por su menor movilidad).
La resistencia es inversamente proporcional al ancho (W) y la capacitancia es directamente proporcional a W.

Se asume el tamaño de un transistor unitario, el cual se define una biblioteca estándar para una cierta tecnología. Normalmente se utilizan las dimensiones más pequeñas posibles. Esto se hace para el nMOS. Para cálculos a mano se asume que la movilidad del transistor pMOS es la mitad de la movilidad del nMOS. Por eso la resistencia es $2R$. **Nota:** en la herramienta esto no es así y ese factor de la movilidad hay que calcularlo.

**Recordatorio**: La capacitancia es directamente proporcional al ancho del transistor y la resistencia es inversamente proporcional a ese ancho. Los transistores van a ser múltiplos enteros del unitario; por lo tanto: $kC$ y $R/k$. C no es exactamente la capacitancia total del transistor, sino que se asume que las capacitancias de source y drain son iguales a la del gate, que es $kC$. La capacitancia del gate depende del tamaño de la placa y el tamaño de la placa es el tamaño del gate. Para el transistor pMOS es casi lo mismo solo que con $2R/k$. 

**Observación:** Para consumir menos potencia se pueden hacen transistores más largos. El problema es que se necesita hacer un poquito más ancho para que switchee más rápido, porque entre más largo, más lento switchea. 

**Importante:** desde el punto de vista de temporización cargar y descargar es lo mismo, por eso se suman los capacitores.

- Retardo de Elmore
Se usa para estimar el retardo en redes RC en forma de escalera o árbol (como redes de _pull-up_ o _pull-down_). Es la suma de cada capacitancia multiplicada por la resistencia en el camino compartido desde la fuente hasta el nodo.

**Ejemplo NAND3:** Para una carga h, el retardo de caída es $t_{pdf}​=(12+5h)RC$ y el de subida es $t_{pdr}​=(9+5h)RC$, incluyendo retardos parásitos internos. La $h$ es la cantidad de compuertas NAND que se conectan en la salida. Se busca que la suma de las resistencias de bajada (idealmente) sea 1. Para subir se ocupa que al menos uno de los transistores se encienda; por lo que se ocupa una resistencia de R para que sea unitaria.

- Componentes de retardo
**Retardo Parásito:** Causado por la capacitancia de difusión interna de la propia compuerta, independiente de la carga externa.
**Retardo de Esfuerzo (Effort delay):** Proporcional a la capacitancia de carga externa que la compuerta debe manejar.

**Layout Comparison**
El layout más rápido es el de la izquierda, porque las capacitancias parásitas de los laterales (el drain de cada uno) no van a descargarse, por lo que no afectan la temporización.



# Servidor
172.21.6.221:user -> tigerVnc viewer

Passw = 100-uservls1.6

Para cambiar la contraseña se abre una terminal y se escribe vncpasswd y se escribe la contra.

Usuario: 11