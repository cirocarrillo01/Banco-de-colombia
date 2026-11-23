# Simulación de Colas en el Banco de Colombia (M/M/1)

**Actividad didáctica 2-M3 · Teoría de Colas y Simulación de Eventos Discretos · Estándar Q1**  
**IU Digital de Antioquia**

**Estudiante:** Ciro Antonio Carrillo Mendoza  
**Grupo:** Grupos de trabajo 35  
**Asignatura:** Simulación  
**Lenguaje de programación:** Python

---

## 📌 1. Descripción del proyecto

Proyecto académico de carácter práctico-experimental orientado a la modelación estocástica y **simulación de eventos discretos (DES)** aplicada al sistema de atención al cliente en las cajas del **Banco de Colombia**, utilizando Python y herramientas de análisis estadístico.

El simulador modela una jornada laboral de 8 horas ($T = 480\text{ min}$) mediante un sistema de colas paralelas independientes tipo **M/M/1** con disciplina **FIFO**, evaluando el impacto de distintas configuraciones de cajas (mixtas vs. exclusivas) y la incorporación de un cajero adicional sobre los tiempos de espera ($W_q$), tiempos en el sistema ($W$) y tasas de utilización ($\rho$).

### Pregunta de negocio

> *Con 3 cajas disponibles, ¿conviene asignar 1 caja exclusiva para retiros y 2 para pagos, o 2 para retiros y 1 para pagos? ¿Hace falta un cajero adicional?*

## 🎯 2. Objetivos

### Objetivo general

Implementar, verificar y analizar un modelo de simulación de eventos discretos en Python para evaluar diferentes políticas de asignación de cajeros en el Banco de Colombia y determinar la configuración óptima de servicio.

### Objetivos específicos

- Modelar el proceso estocástico de llegada y atención de clientes (Retiros y Pagos) segmentados en cuatro perfiles de usuario (*Rápido*, *Normal*, *Lento* y *Muy lento*).
- Verificar la validez del motor de simulación frente a la solución analítica de la teoría clásica de colas M/M/1.
- Dimensionar estadísticamente el número óptimo de réplicas mediante un estudio piloto y el método de semiancho de intervalo de confianza del 95 %.
- Comparar el desempeño operacional de 5 escenarios de atención utilizando la técnica de **números aleatorios comunes (CRN)** y pruebas $t$ pareadas.
- Evaluar la sensibilidad del sistema frente a dos políticas de selección de fila: *fila más corta* (política principal) vs. *asignación al azar*.

## 🧠 3. Modelo probabilístico y supuestos

El sistema modela dos tipos de transacciones bancarias (**Retiro**: 70 % y **Pago**: 30 %) distribuidas en cuatro perfiles de velocidad de cliente, con tiempos entre llegadas y tiempos de servicio gobernados por distribuciones exponenciales.

### Parámetros del modelo

| Acción | Probabilidad | Perfil de usuario | Prob. perfil | Media llegada (min) | Media servicio (min) |
| :--- | :---: | :--- | :---: | :---: | :---: |
| **Retiro** | 70 % | Rápido | 23 % | 1.0 | 1.0 |
| **Retiro** | 70 % | Normal | 40 % | 2.0 | 2.0 |
| **Retiro** | 70 % | Lento | 17 % | 3.0 | 3.0 |
| **Retiro** | 70 % | Muy lento | 20 % | 3.0 | 4.0 |
| **Pago** | 30 % | Rápido | 10 % | 1.0 | 3.0 |
| **Pago** | 30 % | Normal | 20 % | 2.0 | 3.0 |
| **Pago** | 30 % | Lento | 30 % | 3.0 | 5.0 |
| **Pago** | 30 % | Muy lento | 40 % | 4.0 | 7.0 |

* **Tasa media de llegadas ($\lambda$):** $0.417\text{ clientes/min}$ (tiempo medio entre llegadas de $2.398\text{ min}$, equivalente a $\approx 200\text{ clientes/día}$).
* **Tiempo medio de servicio ($E[S]$):** $2.34\text{ min}$ para retiros, $5.20\text{ min}$ para pagos y $3.198\text{ min}$ global.
* **Carga teórica del sistema:** $1.33\text{ cajeros-equivalentes}$ ($0.68$ en retiros + $0.65$ en pagos), garantizando estabilidad ($\rho = 0.445 < 1$) con 3 cajas.

### Escenarios evaluados

| Escenario | Configuración de cajas | Propósito experimental |
| :--- | :--- | :--- |
| **E1: 3 mixtas** | 3 cajas (Retiros y Pagos) | Configuración base de referencia (3 cajeros). |
| **E2: 1 retiro + 2 pagos** | 1 exclusiva Retiros + 2 exclusivas Pagos | Evaluación de especialización orientada a pagos. |
| **E3: 2 retiros + 1 pago** | 2 exclusivas Retiros + 1 exclusiva Pagos | Evaluación de especialización orientada a retiros. |
| **E4: 4 mixtas** | 4 cajas (Retiros y Pagos) | Evaluación de necesidad de cajero adicional (mixto). |
| **E5: 2 retiros + 2 pagos** | 2 exclusivas Retiros + 2 exclusivas Pagos | Evaluación de necesidad de cajero adicional (exclusivo). |

### Supuestos operativos documentados

1. **Horizonte de simulación:** Jornada de $480\text{ minutos}$ (8 horas); se aceptan llegadas hasta el minuto 480 y se concluye la atención de todos los clientes que ingresaron antes del cierre.
2. **Disciplina de cola:** Cada cajero opera como una cola **M/M/1** independiente con disciplina **FIFO** (*First-In, First-Out*).
3. **Políticas de enrutamiento:** En la política principal, el cliente elige la fila más corta entre los cajeros elegibles (desempate por el cajero que se libere antes); en el análisis de sensibilidad, elige un cajero elegible al azar.
4. **Condiciones de frontera:** Sistema inicialmente vacío, sin abandonos, sin fallas de cajeros y tiempos de desplazamiento despreciables.

## 🛠️ 4. Tecnologías utilizadas

| Tecnología | Aplicación |
| :--- | :--- |
| **Python 3** | Lenguaje de programación principal y motor de eventos discretos. |
| **Google Colab / Jupyter** | Entorno de ejecución interactivo y documentación científica. |
| **NumPy** | Generación de variables aleatorias (`default_rng`) y cálculo vectorial. |
| **Pandas** | Estructuración de trazas de clientes, tablas dinámicas y agregación por réplica. |
| **SciPy (`stats`)** | Intervalos de confianza ($t$-Student), pruebas $t$ pareadas, ANOVA y Kruskal-Wallis. |
| **Matplotlib** | Visualización gráfica de comparativas, distribuciones y análisis de sensibilidad. |
| **Git y GitHub** | Control de versiones y publicación del repositorio académico. |

## 📂 5. Estructura del proyecto

```text
Simulacion_Colas_Banco_Colombia/
│
├── Simulacion_Colas_Banco_Colombia.ipynb
│
├── docs/
│   └── Carrillo_Ciro_EA2_M3.docx
│
├── figuras/
│   ├── fig1_comparacion_escenarios.png
│   ├── fig2_espera_por_operacion.png
│   ├── fig3_utilizacion_cajeros.png
│   └── fig4_sensibilidad_politica.png
│
├── requirements.txt
│
└── README.md
```

### Descripción de los archivos

| Archivo | Descripción |
| :--- | :--- |
| `Simulacion_Colas_Banco_Colombia.ipynb` | Notebook principal con la implementación del simulador, verificación teórica, análisis estadístico y generación de gráficas. |
| `docs/Carrillo_Ciro_EA2_M3.docx` | Informe académico con el desarrollo metodológico, respuestas a los puntos 1–5 y conclusiones gerenciales. |
| `figuras/*.png` | Gráficas generadas automáticamente por el notebook para el análisis comparativo de escenarios y políticas. |
| `requirements.txt` | Listado de librerías y dependencias necesarias para reproducir el entorno. |

## ▶️ 6. Cómo ejecutar el proyecto

### Google Colab

1. Abrir el notebook `Simulacion_Colas_Banco_Colombia.ipynb` en Google Colab.
2. Seleccionar **Entorno de ejecución → Ejecutar todo**.
3. Revisar las tablas estadísticas generadas en cada sección y las gráficas exportadas en la carpeta `figuras/`.

### Ejecución local

1. Clonar o descargar el repositorio.
2. Instalar las dependencias requeridas:

   ```bash
   pip install -r requirements.txt
   ```

3. Iniciar Jupyter Notebook o JupyterLab y ejecutar todas las celdas en orden secuencial.

## 🔁 7. Verificación y reproducibilidad experimental

* **Verificación analítica (M/M/1):** Prueba de estrés con $150,000$ clientes ($\lambda = 0.5$, $\mu = 0.833$, $\rho = 0.6$), obteniendo un tiempo medio en cola simulado $W_q = 1.820\text{ min}$ frente al teórico $W_q = 1.800\text{ min}$ (error relativo de $1.1\%$).
* **Semilla base reproducible:** `SEED_BASE = 2026` (con semillas derivadas `2026 + r` por réplica).
* **Dimensionamiento de réplicas:** A partir de un piloto de 10 réplicas y un semiancho deseado $H = 1.0\text{ min}$ al 95 % de confianza, se determinaron **$N_{\text{REP}} = 40\text{ réplicas}$** ($40,505$ clientes simulados por escenario).
* **Reducción de varianza:** Uso de **Números Aleatorios Comunes (CRN)** para que todos los escenarios procesen exactamente la misma secuencia de llegadas en cada réplica, habilitando comparaciones pareadas.

## 📊 8. Resultados y hallazgos principales

| Escenario | Clientes/día | $W_q$ global (min) | $W_q$ retiros (min) | $W_q$ pagos (min) | $W$ sistema (min) | % espera $> 5\text{ min}$ |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **E1: 3 mixtas** | $202.53 \pm 4.42$ | **$0.66 \pm 0.10$** | $0.67 \pm 0.12$ | $0.62 \pm 0.12$ | $3.94 \pm 0.16$ | $3.96\%$ |
| **E2: 1 retiro + 2 pagos** | $202.53 \pm 4.42$ | $4.85 \pm 0.84$ | $6.55 \pm 1.17$ | $0.83 \pm 0.24$ | $8.14 \pm 0.88$ | $30.77\%$ |
| **E3: 2 retiros + 1 pago** | $202.53 \pm 4.42$ | $3.61 \pm 0.76$ | $0.48 \pm 0.10$ | $10.67 \pm 2.37$ | $6.90 \pm 0.80$ | $16.29\%$ |
| **E4: 4 mixtas** | $202.53 \pm 4.42$ | **$0.11 \pm 0.03$** | $0.12 \pm 0.03$ | $0.10 \pm 0.03$ | $3.40 \pm 0.10$ | $0.46\%$ |
| **E5: 2 retiros + 2 pagos** | $202.53 \pm 4.42$ | $0.59 \pm 0.10$ | $0.48 \pm 0.10$ | $0.83 \pm 0.24$ | $3.88 \pm 0.15$ | $3.55\%$ |

### Síntesis de respuestas a los puntos de la actividad

1. **Tiempos por cajero (Punto 1):** En **E1** los tres cajeros mixtos presentan tiempos de servicio estadísticamente equivalentes (entre $3.23$ y $3.47\text{ min}$; ANOVA $F = 2.71, p = 0.071$; Kruskal-Wallis $H = 4.38, p = 0.112$). En **E2** y **E3**, el menor tiempo promedio corresponde a los cajeros de retiros ($\approx 2.35\text{–}2.37\text{ min}$) y el mayor a los de pagos ($\approx 5.39\text{–}5.54\text{ min}$).
2. **Distribución de clientes (Puntos 2 y 3):** El promedio diario es de $202.5 \pm 4.4\text{ clientes}$ (frente a $200.2$ teóricos), liderado por *Retiro · Normal* ($54.5/\text{día}$). La réplica con menor afluencia total es la **#8** ($168\text{ clientes}$) y la de mayor afluencia es la **#6** ($225\text{ clientes}$).
3. **¿Hace falta un cajero adicional? (Punto 4):** **No**, siempre que se opere con **3 cajas mixtas (E1)** bajo política de fila más corta ($W_q = 0.66\text{ min} \le 2.0\text{ min}$ y solo $3.96\%$ espera $> 5\text{ min}$, cumpliendo el nivel de servicio). Agregar un cuarto cajero mixto (**E4**) reduce $W_q$ en apenas $0.54\text{ min}$ ($p < 0.0001$), un margen operativo que no justifica el costo adicional.
4. **Cajas exclusivas (Punto 5):** Entre las configuraciones exclusivas, **E3 (2 retiros + 1 pago)** supera a **E2 (1 retiro + 2 pagos)** en espera global ($3.61$ vs. $4.85\text{ min}$; diferencia pareada $1.24 \pm 1.10\text{ min}$, $p = 0.0279$, ganando en 28 de 40 réplicas), aunque penaliza severamente a los clientes de pagos ($W_q = 10.67\text{ min}$). No obstante, **ambas configuraciones exclusivas son marcadamente inferiores a operar con 3 cajas mixtas (E1)**.

## 🔗 9. Enlaces de interés y entrega

| Recurso | Enlace |
| :--- | :--- |
| Repositorio GitHub | `[Enlace al repositorio]` |
| Notebook Google Colab | `[Enlace al notebook en Colab]` |
| Documento académico | `docs/Carrillo_Ciro_EA2_M3.docx` |

## 👨‍🎓 10. Autor

**Ciro Antonio Carrillo Mendoza**

IU Digital de Antioquia  
Actividad didáctica 2-M3 · Simulación

---

> **Nota:** Este proyecto fue desarrollado con fines académicos como parte de las actividades de la asignatura Simulación. Su propósito es fortalecer las competencias en simulación de eventos discretos, teoría de colas, reducción de varianza e inferencia estadística aplicada a la toma de decisiones operativas.