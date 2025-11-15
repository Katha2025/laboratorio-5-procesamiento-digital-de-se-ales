# laboratorio-5-procesamiento-digital-de-se-ales


# HEART RATE VARIABILITY
En esta práctica se analizó cómo varía la frecuencia cardíaca (HRV) bajo dos condiciones: reposo y lectura en voz alta. Para ello se adquirió una señal ECG de cuatro minutos, se aplicaron filtros digitales, se detectaron los picos R y se calcularon los intervalos R-R. Con estos datos se compararon los parámetros básicos de HRV y se construyó el diagrama de Poincaré, con el fin de evaluar los cambios en el balance simpático-parasimpático generados por cada actividad.

# Parte A
En primer lugar se realizó el fundamento teórico necesario para comprender la práctica, por lo que el estudiante debe investigar y explicar conceptos esenciales relacionados con la actividad simpática y parasimpática del sistema nervioso autónomo, su efecto directo sobre la frecuencia cardíaca y la forma en que estos cambios se manifiestan en la variabilidad de la frecuencia cardíaca (HRV) obtenida a partir de la señal ECG. Además, se estudia el diagrama de Poincaré como herramienta para analizar la dinámica de la serie R-R. Con base en esta revisión conceptual, el estudiante debe plantear un plan de acción que guíe el desarrollo del laboratorio y representarlo mediante un diagrama de flujo que organice de forma lógica las etapas del procedimiento.

**A)**

**Fundamento Teórico**


**1 Actividad simpática y parasimpática del sistema nervioso autónomo**

El sistema nervioso autónomo está compuesto por dos ramas: el sistema simpático y el sistema parasimpático. El simpático prepara al organismo para situaciones de alerta o estrés, aumentando parámetros como la frecuencia cardíaca, la presión arterial y la liberación de adrenalina. Por el contrario, el sistema parasimpático promueve estados de descanso y recuperación, disminuyendo la frecuencia cardíaca y favoreciendo procesos de relajación y conservación de energía. Ambos sistemas actúan de manera continua y complementaria para mantener el equilibrio fisiológico del organismo.

**2.La evaluación de la actividad simpática y parasimpática del sistema nervioso autónomo (SNA)**

La frecuencia cardíaca está directamente modulada por el balance entre ambas ramas del sistema autónomo. Cuando predomina la actividad simpática, el corazón late más rápido debido al aumento en la velocidad de despolarización del nodo SA. En cambio, cuando predomina la actividad parasimpática, el nervio vago reduce la frecuencia cardíaca y aumenta la variabilidad entre latidos. Por esto, una mayor influencia vagal generalmente se asocia a una frecuencia más baja y una HRV más alta, mientras que un dominio simpático reduce la variabilidad al hacer el ritmo más rígido y constante.

**3.Variabilidad de la frecuenia cardiaca (HVR) obteniuda a partir de la señal electrocardiográfica (ECG)**

La HRV se calcula analizando los intervalos R-R extraídos de la señal de ECG, es decir, el tiempo que separa cada latido consecutivo. Estos intervalos cambian de manera natural debido a la modulación autonómica sobre el nodo SA. Una alta variabilidad suele indicar un sistema parasimpático activo, flexible y capaz de adaptarse, mientras que una variabilidad reducida puede asociarse a estrés, fatiga, exigencia cognitiva o predominio simpático. La HRV es, por tanto, un indicador no invasivo del balance autonómico y del estado fisiológico del individuo.

**4.Diagrama de Poincaré como herramienta de análisis de la serie r-r**

El diagrama de Poincaré es una representación gráfica donde cada intervalo R-R se grafica frente al siguiente (R-Rₙ en el eje x y R-Rₙ₊₁ en el eje y). Este método permite visualizar patrones de dispersión que reflejan la dinámica de la HRV: una nube amplia y dispersa sugiere alta variabilidad (actividad parasimpática), mientras que una figura más alargada y estrecha indica baja variabilidad (actividad simpática). A partir del diagrama se pueden calcular índices como SD1 (componente vagal), SD2 (componente simpática) y, en esta guía, los índices CSI y CVI para cuantificar este balance.


**5.Variabilidad de la frecuencia cardiaca (HRV) y balance automático**

La variabilidad de la frecuencia cardíaca (HRV) es una medida no invasiva de las fluctuaciones en los intervalos R–R que refleja la modulación simultánea de las ramas simpática y parasimpática del sistema nervioso autónomo; por eso se utiliza como un indicador del balance autonómico entre activación (simpática) y recuperación (vagal). 
Una HRV alta suele interpretarse como mayor influencia vagal y mejor capacidad de adaptación fisiológica, mientras que una HRV baja se asocia a predominio simpático, estrés o disminución de la capacidad de regulación autonómica. 
La HRV puede analizarse en dominios temporal, frecuencial y no lineal (por ejemplo, diagrama de Poincaré) para obtener diferentes índices que ayudan a cuantificar aspectos del tono vagal y simpático y así caracterizar el balance autonómico en distintas condiciones experimentales o clínicas.

**PLAN DE ACCIÓN**

**B)**

**Código de adquisión de la señal**







**Señal Adquirida**




# Parte B
En la Parte B se realiza el procesamiento inicial de la señal ECG adquirida, aplicando filtros digitales que permitan eliminar artefactos y ruido presentes en el registro. Para ello, el estudiante debe diseñar un filtro IIR adecuado para las características de la señal, obtener su ecuación en diferencias e implementarlo asumiendo condiciones iniciales nulas. Una vez filtrada la señal, esta se divide en dos segmentos de dos minutos correspondientes a las etapas de reposo y lectura. En cada segmento se identifican los picos R y se calculan los intervalos R-R, generando la serie temporal que servirá para el análisis de la variabilidad. Finalmente, se comparan parámetros básicos del dominio del tiempo —como la media y la desviación estándar de los intervalos R-R— con el fin de observar cambios asociados a la actividad autonómica entre ambas condiciones.

**Pre procesamiento de la señal**

**Código de pre procesamiento**


```python
import numpy as np
import matplotlib.pyplot as plt

ecg = np.loadtxt('ECG_filtrado__ahorasi_ultimaaaaa_otravezz20251112_153136.txt', comments='#')

t = ecg[:,0]
senal = ecg[:,1]

print(f"Longitud tiempo: {len(t)} | Longitud señal: {len(senal)}")

inicio = t <= 10
ti= t[inicio]
si= senal[inicio]

tf = t.max()
final = t >= (tf -10)
tf = t[final]
sf =senal[final]

plt.figure(figsize=(12,6))

plt.subplot(2,1,1)
plt.plot(ti, si, color='midnightblue')
plt.title("Señal original, relajación")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud (V)")
plt.grid(True)

plt.subplot(2,1,2)
plt.plot(tf, sf, color='midnightblue')
plt.title("Señal original, durante lectura")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud (V)")
plt.grid(True)

plt.tight_layout()
plt.show()
```

<img width="1217" height="585" alt="image" src="https://github.com/user-attachments/assets/846329df-5633-43f8-bca8-ed842984d1d3" />




# Parte C
En la Parte C se construye el diagrama de Poincaré para cada uno de los segmentos de señal, graficando cada intervalo R-R frente al intervalo siguiente para visualizar la dinámica de la variabilidad cardíaca. A partir de la dispersión de estos puntos, se analizan las diferencias entre las condiciones de reposo y verbalización, evaluando la influencia del sistema nervioso autónomo. Con el diagrama se calculan los índices CVI y CSI, relacionados con la actividad vagal y simpática, respectivamente, lo que permite cuantificar el balance autonómico y observar cómo se modifica ante la carga cognitiva y emocional inducida durante la lectura.
