# Objetivo del proyecto

El objetivo es conocer las principales métricas obtenidas gracias a un producto "watch" y como se podría ayudar al desarrollo del plan de marketing de la empresa gracias a los datos obtenidos

# Origen de los datos

Todos los datos necesarios están subidos en un repositorio de Kaggle (https://www.kaggle.com/datasets/arashnic/fitbit)

# Programas y paquetes

Todo el análisis en R, paquetes principales: tidyverse, dplyr, tidyr, ggplot2, lubridate y scales.

# Procesos realizados

1. Comparación y unión de bases de datos.
2. Limpieza de datos (nulls y cambios de formato)
3. Creación de gráficos con agrupación
4. Segmentación de los datos por percentiles

# Resultado del análisis

## Steps:
<img src="imagenes/steps_by_user.png" width="400"/> <img src="imagenes/total_steps.png" width="400"/>

## Sleep and asleep time:
<img src="imagenes/sleep_hours.png" width="400"/> <img src="imagenes/sleep_vs_asleep.png" width="400"/>

## Quality of sleep after each activity:
<img src="imagenes/quality_sleep.png" width="600"/>

## Exercise by BMI quantile:
<img src="imagenes/activity_by_BMI.png" width="400"/> <img src="imagenes/calories_by_BMI.png" width="400"/>


# Lecciones aprendidas y recomendaciones

1. Visualización de datos de múltiples maneras
2. Abstracción y búsqueda de respuestas a los propietarios
3. Redacción y documentación de todo el proceso

# Limitaciones y qué puede ser mejorado

Me parece un dataset bastante completo, al final descubrí que era de Chicago y se podría realizar una visualización en base a un mapa que mostrara las principales rutas y estaciones más usadas por cada tipo y poder ver claramente mihipótesis y poder potenciar el análisis. También con información de edades se podría saber a quién , cómo y dónde poner anuncios para conseguir más suscriptores.
Obviamente también tengo limitaciones por ser mi primer proyecto y seguro que lo haré mucho mejor en el futuro si lo intento de nuevo.
