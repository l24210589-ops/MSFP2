[![Open in MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)](https://matlab.mathworks.com/open/github/v1?repo=l24210589-ops/MSFP2)
# Práctica 2 — Modelado y control del sistema cardiovascular mediante el modelo Windkessel de cuatro elementos

Repositorio académico de la asignatura **Modelado de Sistemas Fisiológicos**, correspondiente al programa de **Ingeniería Biomédica** del Tecnológico Nacional de México / Instituto Tecnológico de Tijuana. Esta práctica integra modelado matemático, analogías eléctricas de sistemas fisiológicos, análisis temporal y de estabilidad, simulación en MATLAB/Simulink y Python, y diseño de controladores PID *in silico*.

El caso de estudio representa el **sistema arterial** mediante el **modelo Windkessel de cuatro elementos** ($Z$, $L$, $R$, $C$), un circuito eléctrico equivalente de segundo orden. Se compara la presión $P_p(t)$ de un individuo **normotenso** (control) con la de un individuo **hipotenso** y uno **hipertenso** (casos), y se diseña un controlador PID para reducir la diferencia entre la respuesta de cada caso y la respuesta de referencia.

**Docente:** Dr. Paul Antonio Valle Trujillo  
**Correo:** paul.valle@tectijuana.edu.mx  
**Departamento:** Ingeniería Eléctrica y Electrónica  
**Institución:** Tecnológico Nacional de México / Instituto Tecnológico de Tijuana  
**Asignatura:** Modelado de Sistemas Fisiológicos  
**Programa:** Ingeniería Biomédica  
**Estudiante:** Morales Díaz Ramon Ivan · 24210589 · L24210589@tijuana.tecnm.mx

**Repositorio de la asignatura:** https://github.com/DrPaulValle/MSF-IBF1020  
**Sede:** Blvd. Alberto Limón Padilla s/n, Tijuana, C. P. 22454, B. C., México.

---

<a id="contenido"></a>
## Contenido

1. [Resumen](#resumen)
2. [Competencia y objetivos](#objetivos)
3. [Relación con el temario](#temario)
4. [Modelo físico y analogía eléctrica](#analogia)
5. [Formulación matemática](#modelo)
6. [Análisis temporal y de estabilidad](#analisis)
7. [Condiciones de simulación y señales](#senales)
8. [Implementación en MATLAB y Simulink](#matlab)
9. [Control en lazo cerrado con PID](#pid)
10. [Reproducción en Python](#python)
11. [Resultados y verificación cruzada](#resultados)
12. [Actividades del estudiante](#actividades)
13. [Estructura del repositorio](#estructura)
14. [Requisitos](#requisitos)
15. [Reproducibilidad y entregables](#reproducibilidad)
16. [Referencias](#referencias)

---

<a id="resumen"></a>
## Resumen

La práctica modela la hemodinámica arterial con un circuito eléctrico formado por una impedancia característica $Z$ en paralelo con una inertancia $L$, seguidas de una compliancia $C$ en paralelo con una resistencia periférica $R$. A pesar de tener cuatro elementos, el modelo contiene solo **dos elementos independientes de almacenamiento de energía** ($C$ y $L$), por lo que es un sistema de **segundo orden**. A partir de las leyes de Kirchhoff y la transformada de Laplace se obtiene su función de transferencia $P_p(s)/P_a(s)$, el error en estado estacionario y la estabilidad mediante los polos.

Se consideran tres conjuntos de parámetros que difieren **únicamente en la distensibilidad arterial $C$**: hipotenso ($C=3.5$), normotenso ($C=1.5$, referencia) e hipertenso ($C=1.0$). Se simulan los tres individuos en lazo abierto y se diseña, para cada caso, un **controlador PID con retroalimentación negativa** que toma la respuesta del normotenso como referencia. Los resultados se obtienen con MATLAB/Simulink (modelo `sistema2020_470P2_2`) y se reproducen en Python con la señal de entrada exportada desde Simulink. Además, en MATLAB se programa el modelo en variables de estado y se integra numéricamente (método de Euler explícito) como comprobación independiente de la simulación en Simulink.

**Palabras clave:** circuito RLC; controlador PID; mecánica pulmonar; modelado de sistemas fisiológicos; MATLAB; Simulink; Simscape Electrical; Python; simulación *in silico*; instrumentación electrónica.

[Volver al contenido](#contenido)

---

<a id="objetivos"></a>
## Competencia y objetivos

### Competencia a desarrollar

Modelar sistemas fisiológicos mediante circuitos eléctricos equivalentes, ecuaciones diferenciales y funciones de transferencia para analizar su comportamiento dinámico y diseñar estrategias de control mediante técnicas de control clásico. En particular, el estudiante analiza el modelo Windkessel de cuatro elementos, compara la dinámica cardiovascular de un individuo normotenso (*control*) con dos casos simulados, hipotenso e hipertenso, y diseña estrategias de control *in silico* orientadas a reducir las diferencias entre las respuestas de los casos y la respuesta de referencia.

### Objetivo general

Analizar, simular y controlar el modelo Windkessel de cuatro elementos para tres condiciones hemodinámicas (hipotensión, normotensión e hipertensión), y diseñar un controlador PID que acerque la presión de cada caso a la del individuo de referencia.

### Objetivos específicos

- Identificar entrada, salida, flujos y parámetros del circuito equivalente del sistema arterial.
- Obtener la función de transferencia $P_p(s)/P_a(s)$ a partir de las leyes de Kirchhoff y la transformada de Laplace.
- Formular el modelo de ecuaciones integro-diferenciales y su representación en variables de estado con $\mathbf{x}(t)=[F_L(t)\;\;P_p(t)]^T$.
- Calcular el error en estado estacionario y analizar la estabilidad y el tipo de amortiguamiento de cada individuo mediante sus polos.
- Simular los tres individuos en lazo abierto y comparar sus respuestas.
- Integrar numéricamente el modelo de ecuaciones diferenciales en MATLAB (Euler explícito) y compararlo con la simulación de Simulink.
- Definir las señales de error $e_{\text{hipo}}(t)$ y $e_{\text{hiper}}(t)$ respecto al individuo normotenso.
- Diseñar e implementar un controlador PID con retroalimentación negativa para el caso hipotenso y para el hipertenso.
- Reproducir las respuestas de lazo abierto y lazo cerrado en Python a partir de la señal exportada de Simulink.
- Documentar los resultados en un repositorio de GitHub.

[Volver al contenido](#contenido)

---

<a id="temario"></a>
## Relación con el temario

La práctica integra contenidos de tres unidades de la asignatura **Modelado de Sistemas Fisiológicos (IBF-1020)**:

| Unidad | Área | Contenidos aplicados |
|---|---|---|
| I | Sistemas de control retroalimentados | Diagramas de bloques, señal de error, lazo abierto y cerrado, error en estado estacionario y estabilidad |
| II | Modelado matemático | Analogía circuito–sistema arterial, leyes de Kirchhoff, modelo de segundo orden y función de transferencia |
| III | Controladores | Diseño del PID y ajuste de ganancias |

[Volver al contenido](#contenido)

---

<a id="analogia"></a>
## Modelo físico y analogía eléctrica

Las características hemodinámicas del sistema arterial permiten estudiar su comportamiento dinámico mediante parámetros como la resistencia periférica total, la distensibilidad arterial, la impedancia característica de la aorta proximal y la inertancia asociada al movimiento de la sangre. El modelo Windkessel original (atribuido a Otto Frank) usa una sección distensible y una resistencia periférica concentrada; se amplió después con la impedancia característica de la región proximal y, finalmente, con una inertancia que representa los efectos de la masa de sangre en movimiento, lo que da el **modelo de cuatro elementos** (Figura 5.1 del instructivo).

La analogía se establece mediante las correspondencias **presión ↔ voltaje** y **flujo sanguíneo ↔ corriente**:

| Símbolo | Equivalente eléctrico | Interpretación fisiológica |
|---|---|---|
| $P_a(t)$ | Voltaje de entrada | Presión arterial de entrada |
| $P_p(t)$ | Voltaje de salida (nodo del capacitor) | Presión asociada a la distensibilidad arterial |
| $F_a(t)$ | Corriente de entrada | Flujo dirigido hacia la aorta o arteria pulmonar |
| $F_Z(t)$ | Corriente en $Z$ | Flujo por la impedancia característica |
| $F_L(t)$ | Corriente en $L$ | Flujo por la inertancia arterial |
| $F_C(t)$ | Corriente en $C$ | Flujo que almacena volumen por distensión |
| $F_R(t)$ | Corriente en $R$ | Flujo por la resistencia periférica |
| $Z$ | Resistencia | Impedancia característica de la región proximal |
| $L$ | Inductancia | Inertancia arterial |
| $R$ | Resistencia | Resistencia periférica |
| $C$ | Capacitancia | Distensibilidad arterial total |

**Topología:** $Z$ y $L$ están en paralelo entre $P_a$ y el nodo $P_p$; de ese nodo a tierra se conectan en paralelo $C$ y $R$. Por tanto $F_a=F_Z+F_L=F_C+F_R$.

### Conjuntos de parámetros

| Parámetro | Hipotenso | Normotenso (control) | Hipertenso | Unidades |
|---|---:|---:|---:|---|
| $Z$ | 0.010 | 0.010 | 0.010 | $\mathrm{mmHg\cdot s\cdot ml^{-1}}$ |
| $L$ | 0.001 | 0.001 | 0.001 | $\mathrm{mmHg\cdot s^{2}\cdot ml^{-1}}$ |
| $R$ | 1.000 | 1.000 | 1.000 | $\mathrm{mmHg\cdot s\cdot ml^{-1}}$ |
| $C$ | 3.500 | 1.500 | 1.000 | $\mathrm{ml\cdot(mmHg)^{-1}}$ |

Las diferencias se establecen modificando **solo $C$**. Un $C$ mayor (hipotenso) representa un sistema arterial con mayor capacidad de distensión, lo que tiende a atenuar los incrementos de presión de la entrada cardiovascular. Un $C$ menor (hipertenso) representa menor distensibilidad y mayor rigidez arterial, de modo que una variación de volumen produce cambios de presión más pronunciados.

> **Nota sobre unidades:** fisiológicamente las presiones están en $\mathrm{mmHg}$; en las simulaciones y figuras las señales se grafican en voltios, de acuerdo con la analogía eléctrica.

[Volver al contenido](#contenido)

---

<a id="modelo"></a>
## Formulación matemática

### 1. Ecuaciones principales

$$
F_a(t)=F_Z(t)+F_L(t)=F_C(t)+F_R(t),
$$

con

$$
F_Z(t)=\frac{P_a(t)-P_p(t)}{Z},\qquad
F_L(t)=\frac{1}{L}\int\big[P_a(t)-P_p(t)\big]dt,\qquad
F_C(t)=C\frac{dP_p(t)}{dt},\qquad
F_R(t)=\frac{P_p(t)}{R}.
$$

### 2. Ecuación integro-diferencial

Sustituyendo en $F_Z+F_L=F_C+F_R$:

$$
\frac{P_a(t)-P_p(t)}{Z}+\frac{1}{L}\int\big[P_a(t)-P_p(t)\big]dt=C\frac{dP_p(t)}{dt}+\frac{P_p(t)}{R}.
$$

Despejando $P_p(t)$:

$$
P_p(t)=\left[\frac{P_a(t)}{Z}+\frac{1}{L}\int\big[P_a(t)-P_p(t)\big]dt-C\frac{dP_p(t)}{dt}\right]\frac{RZ}{Z+R}.
$$

La única incógnita es la salida $P_p(t)$, y **no puede despejarse** de los términos donde aparece derivada o integrada; por eso, en el diagrama de bloques, $P_p$ se retroalimenta a esos términos.

### 3. Función de transferencia

Con condiciones iniciales nulas, en el dominio de Laplace:

$$
\frac{P_a(s)-P_p(s)}{Z}+\frac{P_a(s)-P_p(s)}{Ls}=CsP_p(s)+\frac{P_p(s)}{R}.
$$

Agrupando términos:

$$
\left(\frac{1}{Z}+\frac{1}{Ls}\right)P_a(s)=\left(Cs+\frac{1}{R}+\frac{1}{Z}+\frac{1}{Ls}\right)P_p(s),
$$

y finalmente

$$
\boxed{G(s)=\frac{P_p(s)}{P_a(s)}=\frac{LRs+RZ}{CLRZ\,s^2+L(Z+R)\,s+RZ}.}
$$

El denominador es de segundo grado por los dos elementos de almacenamiento ($C$ y $L$).

### 4. Representación en variables de estado

Con el vector de estados $\mathbf{x}(t)=\begin{bmatrix}F_L(t)\\P_p(t)\end{bmatrix}$ y teniendo en cuenta que $F_C=F_Z+F_L-F_R$:

$$
\begin{aligned}
\dot x_1(t)&=\frac{P_a(t)-x_2(t)}{L},\\
\dot x_2(t)&=\frac{1}{C}\left[x_1(t)+\frac{P_a(t)-x_2(t)}{Z}-\frac{x_2(t)}{R}\right],\\
y(t)&=x_2(t)=P_p(t).
\end{aligned}
$$

Tomando la transformada de Laplace de este sistema se recupera la misma $G(s)$.

[Volver al contenido](#contenido)

---

<a id="analisis"></a>
## Análisis temporal y de estabilidad

### 1. Error en estado estacionario

Para una entrada escalón unitario $P_a(s)=1/s$:

$$
e_{ss}=\lim_{s\to0}sP_a(s)\big[1-G(s)\big]=\lim_{s\to0}\left[1-\frac{LRs+RZ}{CLRZ\,s^2+L(Z+R)s+RZ}\right]=0.
$$

La ganancia en continua es $G(0)=RZ/RZ=1$ para los tres individuos, de modo que el error en estado estacionario ante escalón es **0 V** en lazo abierto.

### 2. Polos, ceros y estabilidad

Los polos satisfacen $CLRZ\,s^2+L(Z+R)s+RZ=0$:

$$
\lambda_{1,2}=\frac{-L(Z+R)\pm\sqrt{L^2(Z+R)^2-4CLR^2Z^2}}{2CLRZ}.
$$

Con $Z=0.010$, $L=0.001$ y $R=1.000$, el denominador de cada caso es:

| Individuo | $C$ | Denominador | Polos | Comportamiento |
|---|---:|---|---|---|
| Hipotenso | 3.500 | $3.5\times10^{-5}s^2+0.00101s+0.01$ | $-14.429\pm8.805j$ | Estable, **subamortiguado** |
| Normotenso | 1.500 | $1.5\times10^{-5}s^2+0.00101s+0.01$ | $-12.062$, $-55.272$ | Estable, **sobreamortiguado** |
| Hipertenso | 1.000 | $1.0\times10^{-5}s^2+0.00101s+0.01$ | $-11.127$, $-89.873$ | Estable, **sobreamortiguado** |

Los tres tienen polos con parte real negativa, por lo que son estables. Como $Z$, $L$ y $R$ no cambian, el numerador $LR(s+Z/L)$ es el mismo en los tres casos y aporta un **cero en $s=-Z/L=-10$**.

[Volver al contenido](#contenido)

---

<a id="senales"></a>
## Condiciones de simulación y señales

| Parámetro | MATLAB/Simulink | Python |
|---|---|---|
| Intervalo | $0\le t\le15\,\mathrm{s}$ (`StopTime = 15`) | $t_0=0$, $t_{end}=15\,\mathrm{s}$ |
| Paso | `MaxStep = 1e-2` s | $\Delta t=10^{-2}$ s ($N=1501$ muestras) |
| Solver | `ode15s` | `control.forced_response` |
| Condiciones iniciales | Nulas | $x_0=0$ |

La señal de entrada $P_a(t)$ se genera en el modelo de Simulink `sistema2020_470P2_2` (fuente $V_s(t)$ de la Figura 5.2) y se exporta con `writematrix(x.Pa,'signal.xlsx')`. El script de Python lee ese archivo y lo remuestrea al vector de tiempo con `scipy.signal.resample`, de modo que **ambas herramientas usan la misma entrada**.

Para el modelo de EDOs de MATLAB, la entrada que entrega Simulink (una señal escalonada con muestras cada $0.1\,\mathrm{s}$) se interpola a un paso de $10^{-3}\,\mathrm{s}$ con `interp1` y el método `previous` (retención del valor anterior), lo que da 15 001 muestras entre 0 y 15 s. Sobre ese vector de tiempo se integra con Euler explícito y $\Delta t=10^{-3}\,\mathrm{s}$.

El modelo entrega al *Workspace* las señales: `t`, `Pa`, `PpN` (normotenso), `PpL` (hipotenso), `PpH` (hipertenso), `PIDL` (hipotenso con PID) y `PIDH` (hipertenso con PID).

Para cada caso se define una señal de error independiente respecto al individuo de referencia:

$$
e_{\text{hipo}}(t)=P_{p,\text{norm}}(t)-P_{p,\text{hipo}}(t),\qquad
e_{\text{hiper}}(t)=P_{p,\text{norm}}(t)-P_{p,\text{hiper}}(t).
$$

[Volver al contenido](#contenido)

---

<a id="matlab"></a>
## Implementación en MATLAB y Simulink

El cuaderno MATLAB (*live script*, `Morales_24210589.mlx`) limpia el entorno, abre el modelo `sistema2020_470P2_2`, configura la simulación (`StopTime` de 15 s, solver `ode15s` y `MaxStep` de 1e-2 s), la ejecuta, interpola la entrada $P_a(t)$ a un paso de $10^{-3}\,\mathrm{s}$ (para el modelo de EDOs) y guarda la señal original de Simulink en `signal.xlsx` con `writematrix`. Después grafica los resultados con la función `plotsignals`, que se llama cuatro veces:

1. **Individuos:** normotenso, hipotenso e hipertenso en lazo abierto (`PpN`, `PpL`, `PpH`).
2. **Hipotenso:** normotenso, hipotenso y respuesta con PID (`PpN`, `PpL`, `PIDL`).
3. **Hipertenso:** normotenso, hipertenso y respuesta con PID (`PpN`, `PpH`, `PIDH`).
4. **Modelo de EDOs:** los tres individuos obtenidos con la integración numérica descrita más abajo (etiqueta `odes`, archivo `odes_matlab.pdf`).

La función `plotsignals(t,x1,x2,x3,signal)` grafica tres señales contra el tiempo (normotenso con línea continua, segunda señal discontinua y tercera punteada), con ejes en $0\le t\le10\,\mathrm{s}$ y $-0.4\le V_i\le1.2\,\mathrm{V}$, y exporta la figura en PDF vectorial con el nombre `<signal>_matlab.pdf`.

### Modelo de EDOs (integración numérica)

El cuaderno incluye una segunda implementación, escrita directamente a partir del modelo en variables de estado, sin usar Simulink. La función `cardio(par, Pa, t)` recibe el vector de parámetros $[Z,\,L,\,R,\,C]$, la entrada y el vector de tiempo, y calcula la presión de salida con condiciones iniciales nulas ($F_L(0)=0$, $P_p(0)=0$). En cada paso de $\Delta t=10^{-3}\,\mathrm{s}$ avanza con Euler explícito:

$$
F_L[k+1]=F_L[k]+\frac{P_a[k]-P_p[k]}{L}\,\Delta t,
$$

$$
P_p[k+1]=P_p[k]+\left[\frac{F_L[k]}{C}-\left(\frac{1}{ZC}+\frac{1}{RC}\right)P_p[k]+\frac{P_a[k]}{ZC}\right]\Delta t.
$$

Estas expresiones son exactamente las ecuaciones de estado de la sección de formulación matemática. Los parámetros de cada individuo son los de la tabla de parámetros:

| Individuo | $[Z,\,L,\,R,\,C]$ |
|---|---|
| Hipotenso | $[0.01,\;0.001,\;1.00,\;3.500]$ |
| Normotenso | $[0.01,\;0.001,\;1.00,\;1.500]$ |
| Hipertenso | $[0.01,\;0.001,\;1.00,\;1.000]$ |

La entrada del normotenso es $P_a$ interpolada; las salidas del hipotenso y del hipertenso se calculan con la salida del normotenso como entrada (ver notas de verificación). Con un paso de $10^{-3}\,\mathrm{s}$ y polos de hasta $\approx-90\,\mathrm{s^{-1}}$, el producto paso por polo queda en torno a $0.09$, por lo que el método explícito es numéricamente estable en este caso.

### Esquema general de control (Figura 5.2 del instructivo)

Cada caso se analiza de manera independiente con el esquema siguiente:

1. La señal $V_s(t)$ alimenta al **individuo sano** (*control*) y, en paralelo, al **paciente** (*caso*).
2. La salida del individuo sano es la **referencia** del lazo de control.
3. En el sumador se resta (retroalimentación negativa) la salida del paciente con tratamiento.
4. El error pasa por el **PID** (*protocolo*), que alimenta al **paciente** (*tratamiento*).
5. Las señales se envían a un osciloscopio (*scope*) para compararlas.

[Volver al contenido](#contenido)

---

<a id="pid"></a>
## Control en lazo cerrado con PID

### 1. Retroalimentación negativa

La referencia es la presión del normotenso, así que el error del lazo es

$$
e(t)=P_{p,\text{norm}}(t)-P_{p,\text{caso}}(t),
$$

y la función de transferencia de lazo cerrado, con $K(s)$ el controlador y $G(s)$ la planta del caso, es

$$
T(s)=\frac{K(s)G(s)}{1+K(s)G(s)}.
$$

### 2. Ganancias del controlador

| Caso | $k_P$ | $k_I$ | $k_D$ |
|---|---:|---:|---:|
| Hipotenso | 460.710 | 1 122 930.282 | 0.00626 |
| Hipertenso | 129.995 | 312 844.959 | 0.00174 |

### 3. Realización con resistencias y capacitores

El script de Python implementa el PID como un circuito con amplificadores operacionales, con $C_r=1\,\mu\mathrm{F}$ fijo y los demás elementos calculados a partir de las ganancias:

$$
R_e=\frac{1}{k_IC_r},\qquad R_r=k_PR_e,\qquad C_e=\frac{k_D}{R_r}.
$$

La función de transferencia del controlador resultante es

$$
K(s)=\frac{R_eR_rC_eC_r\,s^2+(R_eC_e+R_rC_r)\,s+1}{R_eC_r\,s}.
$$

| Caso | $R_e$ | $R_r$ | $C_e$ |
|---|---:|---:|---:|
| Hipotenso | 0.8905 Ω | 410.27 Ω | 15.26 µF |
| Hipertenso | 3.196 Ω | 415.53 Ω | 4.187 µF |

> **Verificación del diseño:** al dividir $K(s)$ algebraicamente se obtiene $K(s)=k_P^{ef}+\dfrac{k_I}{s}+k_Ds$ con $k_I=\dfrac{1}{R_eC_r}$, $k_D=R_rC_e$ y una ganancia proporcional efectiva $k_P^{ef}=\dfrac{R_r}{R_e}+\dfrac{C_e}{C_r}$. El script calcula $R_r=k_PR_e$, es decir, toma $k_P=R_r/R_e$, por lo que la ganancia proporcional efectiva resulta ligeramente mayor que la nominal: $\approx475.97$ frente a $460.710$ (hipotenso) y $\approx134.18$ frente a $129.995$ (hipertenso). Conviene tenerlo presente al comparar con los valores sintonizados en Simulink.

### 4. Polos en lazo cerrado

Con la estructura de controlador anterior, la ecuación característica de lazo cerrado es de tercer orden. Sus polos, calculados numéricamente, son:

| Caso | Polos de lazo cerrado |
|---|---|
| Hipotenso | $-8254.84$, $-3296.98$, $-9.99997$ |
| Hipertenso | $-8294.19$, $-3212.83$, $-9.99997$ |

Los tres polos tienen parte real negativa (lazo cerrado estable). Un polo queda prácticamente en $s=-10$, justo sobre el cero de la planta ($-Z/L$), con el que casi se cancela; los otros dos son mucho más rápidos que los de la planta en lazo abierto.

[Volver al contenido](#contenido)

---

<a id="python"></a>
## Reproducción en Python

El script de Python del estudiante (`Morales_24210589.py`) reproduce los resultados con una segunda herramienta. Sus apartados son:

1. **Lectura de la entrada y vector de tiempo:** carga `signal.xlsx`, construye $t\in[0,15]$ con $\Delta t=10^{-2}$ s y remuestrea la señal.
2. **Función `cardio(Z,L,R,C)`:** construye la función de transferencia $G(s)$ con `num=[L*R, R*Z]` y `den=[C*L*R*Z, L*R+L*Z, R*Z]`, y calcula los polos con `np.roots`.
3. **Tres individuos:** hipotenso, normotenso e hipertenso, imprimiendo función de transferencia y polos.
4. **Respuestas en lazo abierto:** `ctrl.forced_response` para cada individuo; figura `Individuales_python.pdf`.
5. **Función `controlador(kP,kI,kD,sys)`:** arma el PID con componentes y cierra el lazo con `ctrl.feedback(..., sign=-1)`.
6. **Lazo cerrado:** respuesta del hipotenso y del hipertenso con PID usando como referencia la salida del normotenso; figuras `Hipotenso_python.pdf` e `Hipertenso_python.pdf`.

Las figuras de Python usan $0\le t\le15\,\mathrm{s}$ y $-0.6\le P_p\le1.4\,\mathrm{V}$.

Para ejecutarlo, primero se instalan las bibliotecas `numpy`, `control`, `matplotlib`, `scipy`, `pandas` y `openpyxl` con `pip`, y después se corre el script. El archivo `signal.xlsx` debe estar en la misma carpeta que el script (se genera desde MATLAB).

[Volver al contenido](#contenido)

---

<a id="resultados"></a>
## Resultados y verificación cruzada

### Archivos de resultados

| Figura | MATLAB/Simulink | Python |
|---|---|---|
| Individuos en lazo abierto | [`Individuos_matlab.pdf`](Individuos_matlab.pdf) | [`Individuales_python.pdf`](Individuales_python.pdf) |
| Individuos con el modelo de EDOs (Euler) | [`odes_matlab.pdf`](odes_matlab.pdf) | — |
| Normotenso, hipotenso y PID | [`Hipotenso_matlab.pdf`](Hipotenso_matlab.pdf) | [`Hipotenso_python.pdf`](Hipotenso_python.pdf) |
| Normotenso, hipertenso y PID | [`Hipertenso_matlab.pdf`](Hipertenso_matlab.pdf) | [`Hipertenso_python.pdf`](Hipertenso_python.pdf) |

Los PDF se enlazan para su consulta porque GitHub no los presenta como imágenes incrustadas.

### Aspectos para discutir

- Diferencias entre las respuestas de los tres individuos en lazo abierto y su relación con $C$ y con el tipo de amortiguamiento de cada uno.
- Efecto del PID: cuánto se acerca la respuesta del hipotenso y del hipertenso a la del normotenso.
- Consistencia entre las figuras de MATLAB y de Python.
- Cumplimiento del error en estado estacionario nulo y de la estabilidad en lazo cerrado.
- Limitaciones del modelo Windkessel de cuatro elementos y de la analogía eléctrica.

### Notas de verificación

- **Entrada de los casos en Python:** en el script, las salidas de lazo abierto de hipotenso e hipertenso (`PpL`, `PpH`) se calculan con `PpN` como entrada; según la Figura 5.2, el paciente *caso* recibe $V_s(t)$ directamente (en el script, `u`). Conviene confirmar cuál es la intención antes de comparar con MATLAB.
- **Entrada de los casos en el modelo de EDOs:** igual que en Python, `PpL` y `PpH` se calculan con `PpN` como entrada de `cardio`, no con $P_a$.
- **Entrada interpolada vs. remuestreada:** MATLAB (EDOs) retiene el valor anterior de la señal escalonada con `interp1(...,'previous')`, mientras que Python usa `scipy.signal.resample` sobre `signal.xlsx`; ambos métodos pueden dar diferencias pequeñas entre las figuras.
- **Leyenda en `plotsignals` (rama "Hipertenso"):** la segunda curva (`x.PpH`) aparece rotulada como "Hipotenso" en la figura del hipertenso.
- **Nombres y escalas:** la figura de lazo abierto se llama `Individuos_matlab.pdf` en MATLAB e `Individuales_python.pdf` en Python; además, los ejes difieren (MATLAB: 0–10 s y −0.4 a 1.2 V; Python: 0–15 s y −0.6 a 1.4 V) y también la asignación de colores a cada curva.
- **Amortiguamiento del hipertenso:** los polos calculados ($-11.127$ y $-89.873$) corresponden a una respuesta **sobreamortiguada**, no críticamente amortiguada.

**Resultados del estudiante:** agregar en la versión final las figuras exportadas y un comentario interpretativo propio sobre cada familia de respuestas.

[Volver al contenido](#contenido)

---

<a id="actividades"></a>
## Actividades del estudiante

### Etapa 1. Formulación y análisis

- [ ] Identificar en el circuito $P_a(t)$, $P_p(t)$, $F_a$, $F_Z$, $F_L$, $F_C$, $F_R$, $Z$, $L$, $R$ y $C$.
- [ ] Derivar las ecuaciones integro-diferenciales.
- [ ] Obtener la función de transferencia $P_p(s)/P_a(s)$.
- [ ] Expresar el modelo en variables de estado con $\mathbf{x}=[F_L\;P_p]^T$.
- [ ] Calcular el error en estado estacionario ante escalón unitario.
- [ ] Calcular los polos de los tres individuos y clasificar su amortiguamiento.

### Etapa 2. Simulación de los individuos

- [ ] Ejecutar `sistema2020_470P2_2` con $t_{end}=15$ s, `ode15s` y `MaxStep = 1e-2`.
- [ ] Exportar la entrada a `signal.xlsx`.
- [ ] Graficar las respuestas de los tres individuos en lazo abierto.
- [ ] Programar el modelo de EDOs con Euler explícito y comparar sus resultados con los de Simulink.

### Etapa 3. Diseño y comprobación del controlador

- [ ] Definir las señales de error $e_{\text{hipo}}$ y $e_{\text{hiper}}$.
- [ ] Implementar el PID con retroalimentación negativa para cada caso.
- [ ] Registrar $k_P$, $k_I$ y $k_D$.
- [ ] Graficar las respuestas de lazo cerrado en MATLAB y exportarlas a PDF.
- [ ] Reproducir las respuestas en Python con las mismas ganancias.
- [ ] Verificar la relación entre $k_P$ nominal y efectiva del PID con componentes.

### Etapa 4. Documentación

- [ ] Comparar MATLAB y Python.
- [ ] Redactar una interpretación de los resultados y sus diferencias.
- [ ] Publicar los archivos y un `CITATION.cff` correctamente identificados en GitHub.

[Volver al contenido](#contenido)

---

<a id="estructura"></a>
## Estructura del repositorio

Organización de la entrega:

```text
.
├── README.md                          # Reporte y guía de reproducción
├── CITATION.cff                       # Datos de citación del repositorio
├── Morales_24210589.pdf               # Exportación del cuaderno MATLAB
├── Morales_24210589.mlx               # Cuaderno MATLAB (live script)
├── Morales_24210589.py                # Implementación en Python
├── Analisis_Matematico_Morales.pdf    # Desarrollo analítico a mano
├── sistema2020_470P2_2.slx            # Modelo Simulink
├── signal.xlsx                        # Entrada Pa(t) exportada desde Simulink
├── Individuos_matlab.pdf
├── Hipotenso_matlab.pdf
├── Hipertenso_matlab.pdf
├── odes_matlab.pdf
├── Individuales_python.pdf
├── Hipotenso_python.pdf
└── Hipertenso_python.pdf
```

**Criterio de nombres:** los nombres de las figuras corresponden a los que generan los scripts. El nombre del cuaderno (`Morales_24210589`), del modelo y de la exportación analítica siguen el formato `Apellido_NoControl`; si se usan otros nombres o subcarpetas, actualizar las rutas del README.

[Volver al contenido](#contenido)

---

<a id="requisitos"></a>
## Requisitos

### Software

- **MATLAB** y **Simulink**, con el modelo `sistema2020_470P2_2` y el cuaderno de la práctica.
- **Python** (por ejemplo, con **Visual Studio Code**) y las bibliotecas `numpy`, `control`, `matplotlib`, `scipy`, `pandas` y `openpyxl` (esta última para leer `signal.xlsx`).
- **Git y GitHub** para presentar el repositorio.

Las versiones mínimas del software no se especifican en el instructivo.

[Volver al contenido](#contenido)

---

<a id="reproducibilidad"></a>
## Reproducibilidad y entregables

### Secuencia de reproducción

1. Consultar el [repositorio general de la asignatura](https://github.com/DrPaulValle/MSF-IBF1020) y descargar los archivos de la práctica.
2. Verificar los valores de $Z$, $L$, $R$, $C$ de cada individuo y las condiciones iniciales.
3. Derivar el modelo y completar el análisis de error y polos.
4. Ejecutar el cuaderno MATLAB (`Morales_24210589.mlx`): simulación, exportación de `signal.xlsx` y figuras en PDF.
5. Colocar `signal.xlsx` junto al script de Python (`Morales_24210589.py`) y ejecutarlo para obtener las tres figuras.
6. Comparar las figuras de MATLAB y Python.
7. Completar este README con las evidencias propias y publicar el repositorio junto con su `CITATION.cff`.

### Resultados mínimos esperados

| Entregable | Evidencia solicitada |
|---|---|
| Formulación matemática | Ecuaciones, función de transferencia y modelo en variables de estado |
| Análisis del sistema | Error en estado estacionario, polos y estabilidad de los tres individuos |
| Simulación | Modelo `.slx` y respuestas en lazo abierto |
| Control | Ganancias del PID y respuestas en lazo cerrado de hipotenso e hipertenso |
| MATLAB | Cuaderno exportado a PDF (`Morales_24210589.pdf`) y cuatro figuras (incluida la del modelo de EDOs) |
| Python | Código `Morales_24210589.py` y tres figuras |
| Documentación | `README.md`, `CITATION.cff` e interpretación de los resultados |

### Criterios de presentación académica

Debe distinguirse entre **resultados de referencia reproducidos** y **aportes o modificaciones propias**. La ejecución de scripts y la generación de figuras no sustituyen la discusión del comportamiento fisiológico, matemático y de control del sistema.

[Volver al contenido](#contenido)

---

<a id="referencias"></a>
## Referencias

[1] P. A. Valle, *Práctica: Modelado y control del sistema cardiovascular mediante el modelo Windkessel* (sección 5.4), instructivo de la asignatura Modelado de Sistemas Fisiológicos, Tecnológico Nacional de México / Instituto Tecnológico de Tijuana, pp. 76–78.

[2] M. C. Khoo, *Physiological Control Systems: Analysis, Simulation, and Estimation*, 2nd ed., IEEE Press, 2018.

[3] F. C. Hoppensteadt y C. S. Peskin, *Modeling and Simulation in Medicine and the Life Sciences*, Springer.

[4] J. E. Hall, *Guyton and Hall Textbook of Medical Physiology*, 14th ed., Elsevier, 2021.

[5] K. Ogata, *Ingeniería de Control Moderna*, 5.ª ed., Pearson Educación, 2010.

[6] N. S. Nise, *Control Systems Engineering*, 8th ed., John Wiley & Sons, 2020.

[7] P. A. Valle, *Syllabus para Modelado de Sistemas Fisiológicos*, Tecnológico Nacional de México / Instituto Tecnológico de Tijuana, Tijuana, B. C., México, 2026. Disponible en: https://biomath.xyz/course/

[8] Tecnológico Nacional de México, *Programa de estudio de la asignatura Modelado de Sistemas Fisiológicos, IBF-1020*.

---
