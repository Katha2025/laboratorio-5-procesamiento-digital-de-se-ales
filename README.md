# laboratorio-5-procesamiento-digital-de-se-ales


# HEART RATE VARIABILITY
En esta práctica se analizó cómo varía la frecuencia cardíaca (HRV) bajo dos condiciones: reposo y lectura en voz alta. Para ello se adquirió una señal ECG de cuatro minutos, se aplicaron filtros digitales, se detectaron los picos R y se calcularon los intervalos R-R. Con estos datos se compararon los parámetros básicos de HRV y se construyó el diagrama de Poincaré, con el fin de evaluar los cambios en el balance simpático-parasimpático generados por cada actividad.

# Parte A
En primer lugar se realizó el fundamento teórico necesario para comprender la práctica, por lo que el estudiante debe investigar y explicar conceptos esenciales relacionados con la actividad simpática y parasimpática del sistema nervioso autónomo, su efecto directo sobre la frecuencia cardíaca y la forma en que estos cambios se manifiestan en la variabilidad de la frecuencia cardíaca (HRV) obtenida a partir de la señal ECG. Además, se estudia el diagrama de Poincaré como herramienta para analizar la dinámica de la serie R-R. Con base en esta revisión conceptual, el estudiante debe plantear un plan de acción que guíe el desarrollo del laboratorio y representarlo mediante un diagrama de flujo que organice de forma lógica las etapas del procedimiento.

**Fundamento Teórico**


**1 Actividad simpática y parasimpática del sistema nervioso autónomo**

**2.La evaluación de la actividad simpática y parasimpática del sistema nervioso autónomo (SNA)**

**3.Variabilidad de la frecuenia cardiaca (HVR) obteniuda a partir de la señal electrocardiográfica (ECG)**

**4.Diagrama de Poincaré como herramienta de análisis de la serie r-r**

**5.Variabilidad de la frecuencia cardiaca (HRV) y balance automático**

**PLAN DE ACCIÓN**


# Parte B
En la Parte B se realiza el procesamiento inicial de la señal ECG adquirida, aplicando filtros digitales que permitan eliminar artefactos y ruido presentes en el registro. Para ello, el estudiante debe diseñar un filtro IIR adecuado para las características de la señal, obtener su ecuación en diferencias e implementarlo asumiendo condiciones iniciales nulas. Una vez filtrada la señal, esta se divide en dos segmentos de dos minutos correspondientes a las etapas de reposo y lectura. En cada segmento se identifican los picos R y se calculan los intervalos R-R, generando la serie temporal que servirá para el análisis de la variabilidad. Finalmente, se comparan parámetros básicos del dominio del tiempo —como la media y la desviación estándar de los intervalos R-R— con el fin de observar cambios asociados a la actividad autonómica entre ambas condiciones.



# Parte C
En la Parte C se construye el diagrama de Poincaré para cada uno de los segmentos de señal, graficando cada intervalo R-R frente al intervalo siguiente para visualizar la dinámica de la variabilidad cardíaca. A partir de la dispersión de estos puntos, se analizan las diferencias entre las condiciones de reposo y verbalización, evaluando la influencia del sistema nervioso autónomo. Con el diagrama se calculan los índices CVI y CSI, relacionados con la actividad vagal y simpática, respectivamente, lo que permite cuantificar el balance autonómico y observar cómo se modifica ante la carga cognitiva y emocional inducida durante la lectura.
