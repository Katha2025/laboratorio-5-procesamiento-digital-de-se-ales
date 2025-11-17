# laboratorio-5-procesamiento-digital-de-seÑales


# HEART RATE VARIABILITY
En esta práctica se analizó cómo varía la frecuencia cardíaca (HRV) bajo dos condiciones: reposo y lectura en voz alta. Para ello se adquirió una señal ECG de cuatro minutos, se aplicaron filtros digitales, se detectaron los picos R y se calcularon los intervalos R-R. Con estos datos se compararon los parámetros básicos de HRV y se construyó el diagrama de Poincaré, con el fin de evaluar los cambios en el balance simpático-parasimpático generados por cada actividad.

# Parte A
En primer lugar se realizó el fundamento teórico necesario para comprender la práctica, por lo que el estudiante debe investigar y explicar conceptos esenciales relacionados con la actividad simpática y parasimpática del sistema nervioso autónomo, su efecto directo sobre la frecuencia cardíaca y la forma en que estos cambios se manifiestan en la variabilidad de la frecuencia cardíaca (HRV) obtenida a partir de la señal ECG. Además, se estudió el diagrama de Poincaré como herramienta para analizar la dinámica de la serie R-R. Con base en esta revisión conceptual, se planteó un plan de acción que guíe el desarrollo del laboratorio y representarlo mediante un diagrama de flujo.

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

**Código de adquisión de la señal con filtro IIR**

```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.widgets import Button
import nidaqmx
from nidaqmx.constants import AcquisitionType
from threading import Thread, Event
from collections import deque
import datetime
import time
from scipy import signal  

fs = 2000        
canal = "Dev16/ai0"    
tamano_bloque = int(fs * 0.05)   
ventana_tiempo = 5.0             

lowcut = 0.5
highcut = 40
orden = 4

sos = signal.butter(orden, [lowcut/(fs/2), highcut/(fs/2)], 
                    btype='bandpass', output='sos')
zi = signal.sosfilt_zi(sos)  

buffer_graf = deque(maxlen=int(fs * ventana_tiempo))
datos_guardados = []

adquiriendo = Event()
detener_hilo = Event()
thread_lectura = None

def hilo_lectura():
    global datos_guardados, buffer_graf, zi
    task = nidaqmx.Task()
    task.ai_channels.add_ai_voltage_chan(canal)
    task.timing.cfg_samp_clk_timing(rate=fs, sample_mode=AcquisitionType.CONTINUOUS)
    task.start()
    print(f"\n▶ Adquisición iniciada en {canal} ({fs} Hz).")

    while not detener_hilo.is_set():
        if adquiriendo.is_set():
            try:
                datos = np.array(task.read(number_of_samples_per_channel=tamano_bloque))
            
                datos_filtrados, zi = signal.sosfilt(sos, datos, zi=zi)
                
                buffer_graf.extend(datos_filtrados)
                datos_guardados.extend(datos_filtrados)

            except Exception as e:
                print("⚠ Error de lectura:", e)
                break
        else:
            time.sleep(0.05)

    task.stop()
    task.close()
    print("Adquisición detenida")

def iniciar(event):
    global thread_lectura
    if not adquiriendo.is_set():
        if thread_lectura is None or not thread_lectura.is_alive():
            detener_hilo.clear()
            thread_lectura = Thread(target=hilo_lectura, daemon=True)
            thread_lectura.start()
        adquiriendo.set()
        print("▶ Grabando...")

def detener(event):
    """Detiene y guarda los datos."""
    adquiriendo.clear()
    detener_hilo.set()
    time.sleep(0.3)

    if datos_guardados:
        timestamp = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
        nombre_archivo = f"ECG_filtrado__ahorasi_ultimaaaaa_otravezz{timestamp}.txt"
        tiempos = np.arange(len(datos_guardados)) / fs
        data = np.column_stack((tiempos, datos_guardados))
        np.savetxt(nombre_archivo, data, fmt="%.6f", header="Tiempo(s)\tVoltaje(V)")
        print(f"Señal guardada en {nombre_archivo} ({len(datos_guardados)} muestras)")
    else:
        print("No se capturaron datos")

fig, ax = plt.subplots(figsize=(10, 4))
plt.subplots_adjust(bottom=0.25)
linea, = ax.plot([], [], lw=1.2, color='royalblue')
ax.set_xlim(0, ventana_tiempo)
ax.set_ylim(-2, 2)
ax.set_xlabel("Tiempo [s]")
ax.set_ylabel("Voltaje [V]")
ax.set_title("ECG filtrado (IIR tiempo real)")
ax.grid(True, linestyle="--", alpha=0.6)

x = np.linspace(0, ventana_tiempo, int(fs * ventana_tiempo))
y = np.zeros_like(x)

def actualizar(frame):
    if len(buffer_graf) > 0:
        y = np.array(buffer_graf)
        if len(y) < len(x):
            y = np.pad(y, (len(x)-len(y), 0), constant_values=0)
        linea.set_data(x, y)
    return linea,

ax_iniciar = plt.axes([0.3, 0.1, 0.15, 0.075])
ax_detener = plt.axes([0.55, 0.1, 0.2, 0.075])
btn_iniciar = Button(ax_iniciar, 'Iniciar', color='purple', hovercolor='purple')
btn_detener = Button(ax_detener, 'Detener y Guardar', color='pink', hovercolor='pink')
btn_iniciar.on_clicked(iniciar)
btn_detener.on_clicked(detener)

from matplotlib.animation import FuncAnimation
ani = FuncAnimation(fig, actualizar, interval=50, blit=True)
plt.tight_layout()
plt.show()

```

**Señales obtenidas**

<img width="1244" height="624" alt="image" src="https://github.com/user-attachments/assets/b185e210-1aa9-443f-bf07-72da0845fe96" />



# Parte B
En la Parte B se realiza el procesamiento inicial de la señal ECG adquirida, aplicando filtros digitales que permitan eliminar artefactos y ruido presentes en el registro. Para ello, se diseñó  un filtro IIR adecuado para las características de la señal, luego se pasó a obtener su ecuación en diferencias e implementarlo asumiendo condiciones iniciales nulas. Una vez filtrada la señal, esta se divide en dos segmentos de dos minutos correspondientes a las etapas de reposo y lectura. En cada segmento se identificaron los picos R y se calcularon los intervalos R-R, generando la serie temporal que servirá para el análisis de la variabilidad. Finalmente, se compararon parámetros básicos del dominio del tiempo —como la media y la desviación estándar de los intervalos R-R— con el fin de observar cambios asociados a la actividad autonómica entre ambas condiciones.

**Pre procesamiento de la señal**

**Código de pre procesamiento**

**Ecuación de diferencias filtro IIR**

```python
#Ecuación en diferencias de filtro IIR (codigo de captura)

from scipy.signal import butter

fs = 2000
lowcut = 0.5
highcut = 40
orden = 4

sos = butter(orden, [lowcut/(fs/2), highcut/(fs/2)], btype='bandpass', output='sos')

for i, sec in enumerate(sos):
    b0, b1, b2, a0, a1, a2 = sec
    print(f"Sección {i+1}:")
    print(f"y[n] = {b0:.6f}·x[n] + {b1:.6f}·x[n-1] + {b2:.6f}·x[n-2] - {a1:.6f}·y[n-1] - {a2:.6f}·y[n-2]\n")
```

<img width="796" height="212" alt="image" src="https://github.com/user-attachments/assets/99b5ca0b-9aef-4ad9-86e5-23b5eda26f1a" />



Luego de tener la señal filtrada en tiempo real con un filtro IIR se procedió a hacer un segundo filtrado con un filtro FIR.


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

<img width="1220" height="601" alt="image" src="https://github.com/user-attachments/assets/ea66d5c3-464a-43af-9458-99dcfc651086" />


**Ecuación de Diferencias filtro FIR**

```python
h = firwin(num_taps, [low_cutoff, high_cutoff], pass_zero=False)

print("FIR: ",h)
```

<img width="580" height="460" alt="image" src="https://github.com/user-attachments/assets/c94461f1-a057-4dc8-823d-e60dfb30e534" />


**División de la señal filtrada en dos segmentos de señal con duración de 2 minutos cada uno**


```python
uracion_segmento = 120 #segundos -> 2 minutos

muestras_por_segmento = fs * duracion_segmento

t1 = t[:muestras_por_segmento]
s1 = senal[:muestras_por_segmento]

t2 = t[muestras_por_segmento:2*muestras_por_segmento]
s2 = senal[muestras_por_segmento:2*muestras_por_segmento]

import matplotlib.pyplot as plt

plt.figure(figsize=(12,6))

plt.subplot(2,1,1)
plt.plot(t1, s1, color='slateblue')
plt.title("Primeros 2 minutos, relajación")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud (V)")
plt.grid(True)

plt.subplot(2,1,2)
plt.plot(t2, s2, color='slateblue')
plt.title("Siguientes 2 minutos, durante lectura")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud (V)")
plt.grid(True)

plt.tight_layout()
plt.show()

```

**Gráficas obtenidas**

<img width="1204" height="596" alt="image" src="https://github.com/user-attachments/assets/d5946a7b-0a91-4e06-a3b9-09c4c76c9af7" />


**Detección de los picos R**

```python
picos1, _ = find_peaks(s1, prominence=0.2, distance=int(0.3 * fs))
picos2, _ = find_peaks(s2, prominence=0.2, distance=int(0.3 * fs))

tiempos_R1 = t1[picos1]
tiempos_R2 = t2[picos2]
RR1 = np.diff(tiempos_R1)
RR2 = np.diff(tiempos_R2)

mean_RR1 = np.mean(RR1)
std_RR1 = np.std(RR1)
rmssd1 = np.sqrt(np.mean(np.square(np.diff(RR1))))

mean_RR2 = np.mean(RR2)
std_RR2 = np.std(RR2)
rmssd2 = np.sqrt(np.mean(np.square(np.diff(RR2))))

plt.figure(figsize=(12,4))
plt.plot(t1, s1, label='ECG Segmento 1', color='rebeccapurple')
plt.plot(t1[picos1], s1[picos1], 'ro', label='Picos R')
plt.title("Segmento 1 con picos R")
plt.xlabel("Tiempo (s)")
plt.ylabel("Voltaje (V)")
plt.grid(True)
plt.legend()
plt.tight_layout()
plt.show()

plt.figure(figsize=(12,4))
plt.plot(t2, s2, label='ECG Segmento 2', color='rebeccapurple')
plt.plot(t2[picos2], s2[picos2], 'ro', label='Picos R')
plt.title("Segmento 2 con picos R")
plt.xlabel("Tiempo (s)")
plt.ylabel("Voltaje (V)")
plt.grid(True)
plt.legend()
plt.tight_layout()
plt.show()

```

<img width="1005" height="657" alt="image" src="https://github.com/user-attachments/assets/c75122b8-060e-4a86-aba9-ea66816572c9" />

**Cálculo de los intervalos  R-R y obtención de una nueva señal**

```python
def construir_senal_rr(tiempos_R, RR, duracion_total, fs):
    tiempo = np.linspace(0, duracion_total, int(fs * duracion_total))
    senal_rr = np.zeros_like(tiempo)

    for i in range(len(RR)):
        inicio = int(tiempos_R[i] * fs)
        fin = int(tiempos_R[i+1] * fs)
        senal_rr[inicio:fin] = RR[i]

    return tiempo, senal_rr

tiempo_rr1, senal_rr1 = construir_senal_rr(tiempos_R1, RR1, duracion_segmento, fs)
tiempo_rr2, senal_rr2 = construir_senal_rr(tiempos_R2, RR2, duracion_segmento, fs)

plt.figure(figsize=(12,5))

plt.subplot(2,1,1)
plt.plot(tiempo_rr1, senal_rr1, color='darkslategrey')
plt.title("Señal R-R (segmento 1)")
plt.xlabel("Tiempo (s)")
plt.ylabel("Intervalo R-R (s)")
plt.grid(True)

plt.subplot(2,1,2)
plt.plot(tiempo_rr2, senal_rr2, color='teal')
plt.title("Señal R-R (segmento 2)")
plt.xlabel("Tiempo (s)")
plt.ylabel("Intervalo R-R (s)")
plt.grid(True)

plt.tight_layout()
plt.show()

```
<img width="1005" height="410" alt="image" src="https://github.com/user-attachments/assets/5bd73af7-43b8-4336-8f69-86c45a34e7f8" />


**Análisis de la HRV en el dominio del tiempo**

Cálculo de la media, desviación estandar y RMSSD

```python
print("Segmento 1:")
print(f"Media R-R: {mean_RR1:.3f} s")
print(f"Desviación estándar R-R: {std_RR1:.3f} s")
print(f"RMSSD: {rmssd1:.3f} s")

print("\nSegmento 2:")
print(f"Media R-R: {mean_RR2:.3f} s")
print(f"Desviación estándar R-R: {std_RR2:.3f} s")
print(f"RMSSD: {rmssd2:.3f} s")
```

<img width="343" height="137" alt="image" src="https://github.com/user-attachments/assets/5052d728-3e17-445a-97a3-b701bc5474f8" />

Cálculo de los índices de variabilidad de la frecuencia cardiaca (HRV) en el dominio frecuencial usando el método Welch.

```python

from scipy.signal import welch
import numpy as np
from scipy.integrate import trapezoid

f1, psd1 = welch(rr_uniforme1, fs=fs_interp, nperseg=256)
f2, psd2 = welch(rr_uniforme2, fs=fs_interp, nperseg=256)

lf_band = (f1 >= 0.04) & (f1 < 0.15)
hf_band = (f1 >= 0.15) & (f1 < 0.4)

lf1 = trapezoid(psd1[lf_band], f1[lf_band])
hf1 = trapezoid(psd1[hf_band], f1[hf_band])
lf2 = trapezoid(psd2[lf_band2], f2[lf_band2])
hf2 = trapezoid(psd2[hf_band2], f2[hf_band2])

lfhf1 = lf1 / hf1 if hf1 != 0 else np.nan

lf_band2 = (f2 >= 0.04) & (f2 < 0.15)
hf_band2 = (f2 >= 0.15) & (f2 < 0.4)

lfhf2 = lf2 / hf2 if hf2 != 0 else np.nan

print("Segmento 1:")
print(f"LF: {lf1:.3f}, HF: {hf1:.3f}, LF/HF: {lfhf1:.3f}, predominio parasimpático")

print("\nSegmento 2:")
print(f"LF: {lf2:.3f}, HF: {hf2:.3f}, LF/HF: {lfhf2:.3f}, predominio simpático")

```

<img width="432" height="85" alt="image" src="https://github.com/user-attachments/assets/2246cea8-c1a7-4a5e-abc8-81ab9c60d543" />


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




