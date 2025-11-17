# laboratorio-5-procesamiento-digital-de-seÑales


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

**Aplicación del filtro FIR**

```python
from scipy.signal import firwin, lfilter

fs = 2000
nyq = fs/2

num_taps = 101 #numero de coeficientes

low_cutoff = 0.5 / nyq
high_cutoff = 40 / nyq

filtro_fir = firwin(num_taps, [low_cutoff, high_cutoff], pass_zero=False)

ecg_filtrado = lfilter(filtro_fir, 1.0, senal)

senal2 = ecg_filtrado

inicio2 = t <= 10
ti= t[inicio2]
si2= senal2[inicio2]

tf = t.max()
final2 = t >= (tf -10)
tf = t[final2]
sf2 =senal2[final2]

plt.figure(figsize=(12,6))

plt.subplot(2,1,1)
plt.plot(ti, si2, color='midnightblue')
plt.title("Señal filtrada, relajación")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud (V)")
plt.grid(True)

plt.subplot(2,1,2)
plt.plot(tf, sf2, color='midnightblue')
plt.title("Señal filtrada, durante lectura")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud (V)")
plt.grid(True)

plt.tight_layout()
plt.show()

```

<img width="1211" height="595" alt="image" src="https://github.com/user-attachments/assets/445d7eb6-ac95-498d-9449-ce0b9554dea8" />



**Ecuación de Diferencias filtro FIR**

```python
h = firwin(num_taps, [low_cutoff, high_cutoff], pass_zero=False)

print("FIR: ",h)
```

<img width="622" height="466" alt="image" src="https://github.com/user-attachments/assets/e5274f84-08e9-43e1-8732-50acd1f44953" />

# Parte C
En la Parte C se construye el diagrama de Poincaré para cada uno de los segmentos de señal, graficando cada intervalo R-R frente al intervalo siguiente para visualizar la dinámica de la variabilidad cardíaca. A partir de la dispersión de estos puntos, se analizan las diferencias entre las condiciones de reposo y verbalización, evaluando la influencia del sistema nervioso autónomo. Con el diagrama se calculan los índices CVI y CSI, relacionados con la actividad vagal y simpática, respectivamente, lo que permite cuantificar el balance autonómico y observar cómo se modifica ante la carga cognitiva y emocional inducida durante la lectura.


**Código para diagrama de Poincaré**

```python

import matplotlib.pyplot as plt
import numpy as np

RR1_x = RR1[:-1]
RR1_y = RR1[1:]

RR2_x = RR2[:-1]
RR2_y = RR2[1:]

plt.figure(figsize=(12,5))

plt.subplot(1, 2, 1)
plt.scatter(RR1_x, RR1_y, alpha=0.6, color='mediumvioletred')
plt.title("Diagrama de Poincaré - Segmento 1")
plt.xlabel("RR[n] (s)")
plt.ylabel("RR[n+1] (s)")
plt.axis('equal')
plt.grid(True)

plt.subplot(1, 2, 2)
plt.scatter(RR2_x, RR2_y, alpha=0.6, color='hotpink')
plt.title("Diagrama de Poincaré - Segmento 2")
plt.xlabel("RR[n] (s)")
plt.ylabel("RR[n+1] (s)")
plt.axis('equal')
plt.grid(True)

plt.tight_layout()
plt.show()
```


**Diagramas obtenidos**

<img width="1211" height="496" alt="image" src="https://github.com/user-attachments/assets/b6c76a05-0c4d-42bd-9f59-f128acde5929" />



**Código de para la obtención de los valores CVI y CSI**
```python
def calcular_sd1_sd2(rr):
    rr_n = rr[:-1]
    rr_n1 = rr[1:]
    diff = rr_n1 - rr_n
    suma = rr_n1 + rr_n

    sd1 = np.std(diff / np.sqrt(2))
    sd2 = np.std(suma / np.sqrt(2))
    cvi = np.log10(sd2 / sd1) if sd1 != 0 else np.nan
    csi = sd2 / sd1 if sd1 != 0 else np.nan

    return sd1, sd2, cvi, csi

sd1_1, sd2_1, cvi1, csi1 = calcular_sd1_sd2(RR1)
sd1_2, sd2_2, cvi2, csi2 = calcular_sd1_sd2(RR2)

print("Segmento 1:")
print(f"SD1: {sd1_1:.4f} s (actividad vagal)")
print(f"SD2: {sd2_1:.4f} s (actividad simpática)")
print(f"CVI: {cvi1:.4f}")
print(f"CSI: {csi1:.4f}")

print("\nSegmento 2:")
print(f"SD1: {sd1_2:.4f} s (actividad vagal)")
print(f"SD2: {sd2_2:.4f} s (actividad simpática)")
print(f"CVI: {cvi2:.4f}")
print(f"CSI: {csi2:.4f}")

```


**valores de los índices tanto de actividad vagal (CVI) y de actividad simpática (CSI) obtenidos a partir de Poincaré**


<img width="326" height="196" alt="image" src="https://github.com/user-attachments/assets/eeb8ff01-f629-4935-a821-aabd905f95cd" />




