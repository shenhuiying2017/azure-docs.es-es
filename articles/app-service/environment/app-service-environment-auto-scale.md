---
title: "Escalado automático y App Service Environment v1"
description: "Escalado automático y entorno del Servicio de aplicaciones"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 0feb6e4862f643c35a58c0321181bdda22b032e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Escalado automático y App Service Environment v1

> [!NOTE]
> Este artículo trata sobre App Service Environment v1.  Hay una versión más reciente de App Service Environment que resulta más fácil de usar y se ejecuta en una infraestructura más eficaz. Para aprender más sobre la nueva versión, consulte [Introducción a App Service Environment](intro.md).
> 

Los entornos del Servicio de aplicaciones de Azure admiten el *escalado automático*. Puede usar el escalado automático basado en métricas o programación grupos de trabajo individuales.

![Opciones de escalado automático para un grupo de trabajo.][intro]

El escalado automático optimiza el uso de recursos gracias al crecimiento y reducción automáticos de un entorno del Servicio de aplicaciones para ajustarse a su presupuesto o a su perfil de carga.

## <a name="configure-worker-pool-autoscale"></a>Configuración del escalado automático de grupo de trabajo
Puede acceder a la funcionalidad de escalado automático desde la pestaña **Configuración** del grupo de trabajo.

![Pestaña Configuración del grupo de trabajo.][settings-scale]

A partir de ahí, la interfaz debe resultar bastante familiar ya que se trata de la misma experiencia que ve al escala un plan de App Service. 

![Configuración de la escala manual.][scale-manual]

También puede configurar un perfil de escalado automático.

![Configuración del escalado automático.][scale-profile]

Los perfiles de escalado automático resultan útiles para establecer límites en la escala. De este modo tener una experiencia de rendimiento coherente, mediante el establecimiento de un valor de escala de límite inferior (1) y un techo de gasto predecible, mediante el establecimiento de un límite superior (2).

![Configuración de la escala en el perfil.][scale-profile2]

Después de definir un perfil, puede agregar reglas de escalado automático para escalar o reducir verticalmente el número de instancias en el grupo de trabajo dentro de los límites definidos por el perfil. Las reglas de escalado automático se basan en las métricas.

![Regla de escala.][scale-rule]

 Se puede usar cualquier grupo de trabajo o métrica de front-end para definir las reglas de escalado automático. Son las mismas métricas que puede supervisar en los gráficos de la hoja de recursos o para las que puede configurar alertas.

## <a name="autoscale-example"></a>Ejemplo de escalado automático
La mejor manera de ilustrar el escalado automático de un entorno del Servicio de aplicaciones es mediante un escenario.

Este artículo explica todas las consideraciones necesarias para configurar el escalado automático. En este artículo veremos todas las interacciones que entran en juego cuando se aplica el escalado automático en los App Service Environment que se hospedan en un App Service Environment.

### <a name="scenario-introduction"></a>Introducción al escenario
Frank es administrador de sistemas de una empresa y ha migrado parte de las cargas de trabajo que administra a un entorno del Servicio de aplicaciones.

El entorno del Servicio de aplicaciones está configurado a escala manual de la siguiente manera:

* **Servidores front-end** : 3
* **Grupo de trabajo 1**: 10
* **Grupo de trabajo 2**: 5
* **Grupo de trabajo 3**: 5

El grupo de trabajo 1 se usa para las cargas de trabajo de producción, en tanto que los grupos de trabajo 2 y 3 se usan para las cargas de trabajo de control de calidad y desarrollo.

Los planes de App Service de QA y desarrollo están configurados para la escala manual. El plan de App Service de producción se establece en escalado automático para tratar con las variaciones de carga y el tráfico.

Frank está muy familiarizado con la aplicación. Sabe que las horas pico de carga están entre las 9:00 y 6:00 porque se trata de una aplicación de línea de negocio (LOB) que los empleados usan mientras están en la oficina. El uso cae después, una vez que los usuarios han terminado la jornada. Fuera de las horas punta, sigue habiendo cierta carga porque los usuarios pueden acceder de forma remota a la aplicación mediante sus dispositivos móviles o equipos domésticos. El plan del servicio de Aplicaciones de producción ya está configurado para el escalado automático en función del uso de CPU con las reglas siguientes:

![Configuración específica para las aplicaciones LOB.][asp-scale]

| **Perfil de escalado automático: Días laborables: plan del Servicio de aplicaciones** | **Perfil de escalado automático: Fines de semana: plan del Servicio de aplicaciones** |
| --- | --- |
| **Nombre:** perfil Día laborable |**Nombre:** perfil Fin de semana |
| **Escalar por:** Reglas de rendimiento y programación |**Escalar por:** Reglas de rendimiento y programación |
| **Perfil:** Días laborables |**Perfil:** Fin de semana |
| **Tipo:** periodicidad |**Tipo:** periodicidad |
| **Rango objetivo:** de 5 a 20 instancias |**Rango objetivo:** de 3 a 10 instancias |
| **Días:** lunes, martes, miércoles, jueves, viernes |**Días:** sábado, domingo |
| **Hora de inicio:** 9:00 |**Hora de inicio:** 9:00 |
| **Zona horaria:** UTC-08 |**Zona horaria:** UTC-08 |
|  | |
| **Regla de escalado automático (escalar verticalmente)** |**Regla de escalado automático (escalar verticalmente)** |
| **Recurso:** Producción (entorno del Servicio de aplicaciones) |**Recurso:** Producción (entorno del Servicio de aplicaciones) |
| **Métrica:** % de CPU |**Métrica:** % de CPU |
| **Operación:** Mayor que 60% |**Operación:** Mayor que 80% |
| **Duración:** 5 minutos |**Duración:** 10 minutos |
| **Agregación de tiempo:** media |**Agregación de tiempo:** media |
| **Acción:** Aumentar recuento en 2 |**Acción:** Aumentar recuento en 1 |
| **Tiempo de finalización (minutos):** 15 |**Tiempo de finalización (minutos):** 20 |
|  | |
| **Regla de escalado automático (reducir verticalmente)** |**Regla de escalado automático (reducir verticalmente)** |
| **Recurso:** Producción (entorno del Servicio de aplicaciones) |**Recurso:** Producción (entorno del Servicio de aplicaciones) |
| **Métrica:** % de CPU |**Métrica:** % de CPU |
| **Operación:** menor que el 30 % |**Operación:** menor que el 20 % |
| **Duración:** 10 minutos |**Duración:** 15 minutos |
| **Agregación de tiempo:** media |**Agregación de tiempo:** media |
| **Acción:** Reducir recuento en 1 |**Acción:** Reducir recuento en 1 |
| **Tiempo de finalización (minutos):** 20 |**Tiempo de finalización (minutos):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Tasa de inflación del plan del Servicio de aplicaciones
Los planes de App Service configurados para el escalado automático se realizan a una tasa máxima por hora. Esta tasa se puede calcular en función de los valores indicados en la regla de escalado automático.

Comprender y calcular la *tasa de inflación del plan del Servicio de aplicaciones* es importante para el escalado automático del entorno del Servicio de aplicaciones, ya que los cambios en el escalado de un grupo de trabajo no son instantáneos.

La tasa de inflación del plan del Servicio de aplicaciones se calcula del siguiente modo:

![Cálculo de la tasa de inflación del plan del Servicio de aplicaciones.][ASP-Inflation]

De acuerdo con la regla Escalado automático: escalar verticalmente del perfil Días laborables del plan de App Service de producción:

![Tasa de inflación del plan del Servicio de aplicaciones para los días laborables basándose en el escalado automático: regla escalar verticalmente.][Equation1]

En el caso de la regla Escalado automático: escalar verticalmente del perfil Fines de semana del plan del Servicio de aplicaciones de producción, la fórmula daría como resultado:

![Tasa de inflación del plan del Servicio de aplicaciones para los fines de semana basado en el escalado automático: regla escalar verticalmente.][Equation2]

Este valor también se puede calcular para operaciones de reducción vertical.

De acuerdo con la regla Escalado automático: reducir verticalmente del perfil Días laborables del plan del Servicio de aplicaciones de producción, sería:

![Tasa de inflación del plan del Servicio de aplicaciones para los días laborables basándose en el escalado automático: regla reducir verticalmente.][Equation3]

En el caso de la regla Escalado automático: reducir verticalmente del perfil Fines de semana del plan del Servicio de aplicaciones de producción, la fórmula daría como resultado:  

![Tasa de inflación del plan del Servicio de aplicaciones para los fines de semana basado en el escalado automático: regla reducir verticalmente.][Equation4]

El plan de App Service de producción puede crecer a una tasa máxima de ocho instancias por hora durante la semana y de cuatro instancias por hora durante los fines de semana. Puede liberar las instancias a una tasa máxima de cuatro instancias por hora durante la semana y seis instancias por hora durante los fines de semana.

Si se hospedan varios planes del Servicio de aplicaciones en un grupo de trabajo, debe calcular la *tasa de inflación total* como la suma de la tasa de inflación de todos los planes del Servicio de aplicaciones que se hospedan en dicho grupo de trabajo.

![Cálculo de la tasa de inflación total para varios planes del Servicio de aplicaciones hospedados en un grupo de trabajo.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Uso de la tasa de inflación del plan del Servicio de aplicaciones para definir reglas de escalado automático del grupo de trabajo
Los grupos de trabajo que hospedan planes del App Service que están configurados para el escalado automático tendrán que asignar un búfer de capacidad. El búfer permite que las operaciones de escalado automático aumenten y reduzcan el plan del Servicio de aplicaciones según sea necesario. El búfer mínimo será la tasa de inflación total del plan del Servicio de aplicaciones calculada.

Puesto que las operaciones de escalado del entorno del Servicio de aplicaciones tardan algún tiempo en aplicarse, deben tenerse en cuenta los cambios de demanda adicionales que se pueden producir mientras están en curso una operación de escalado. Para dar cabida a esta latencia, se recomienda usar la tasa de inflación total del plan del Servicio de aplicaciones calculada como el número mínimo de instancias que se agregan para cada operación de escalado automático.

Con esta información, Frank puede definir el siguiente perfil y las siguientes reglas de escalado automático:

![Ejemplo de reglas de perfil de escalado automático para línea de negocio.][Worker-Pool-Scale]

| **Perfil de escalado automático: Días laborables** | **Perfil de escalado automático: Fines de semana** |
| --- | --- |
| **Nombre:** perfil Día laborable |**Nombre:** perfil Fin de semana |
| **Escalar por:** Reglas de rendimiento y programación |**Escalar por:** Reglas de rendimiento y programación |
| **Perfil:** Días laborables |**Perfil:** Fin de semana |
| **Tipo:** periodicidad |**Tipo:** periodicidad |
| **Rango objetivo:** de 13 a 25 instancias |**Rango objetivo:** de 6 a 15 instancias |
| **Días:** lunes, martes, miércoles, jueves, viernes |**Días:** sábado, domingo |
| **Hora de inicio:** 7:00 |**Hora de inicio:** 9:00 |
| **Zona horaria:** UTC-08 |**Zona horaria:** UTC-08 |
|  | |
| **Regla de escalado automático (escalar verticalmente)** |**Regla de escalado automático (escalar verticalmente)** |
| **Recurso:** Grupo de trabajo 1 |**Recurso:** Grupo de trabajo 1 |
| **Métrica:** WorkersAvailable |**Métrica:** WorkersAvailable |
| **Operación:** Menor que 8 |**Operación:** Menor que 3 |
| **Duración:** 20 minutos |**Duración:** 30 minutos |
| **Agregación de tiempo:** media |**Agregación de tiempo:** media |
| **Acción:** Aumentar recuento en 8 |**Acción:** Aumentar recuento en 3 |
| **Tiempo de finalización (minutos):** 180 |**Tiempo de finalización (minutos):** 180 |
|  | |
| **Regla de escalado automático (reducir verticalmente)** |**Regla de escalado automático (reducir verticalmente)** |
| **Recurso:** Grupo de trabajo 1 |**Recurso:** Grupo de trabajo 1 |
| **Métrica:** WorkersAvailable |**Métrica:** WorkersAvailable |
| **Operación:** Mayor que 8 |**Operación:** Mayor que 3 % |
| **Duración:** 20 minutos |**Duración:** 15 minutos |
| **Agregación de tiempo:** media |**Agregación de tiempo:** media |
| **Acción:** Reducir recuento en 2 |**Acción:** Reducir recuento en 3 |
| **Tiempo de finalización (minutos):** 120 |**Tiempo de finalización (minutos):** 120 |

El rango objetivo definido en el perfil se calcula sumando el número mínimo de instancias definido en el perfil para el plan del Servicio de aplicaciones al búfer.

El rango máximo debe ser la suma de todos los rangos máximos de todos los planes del Servicio de aplicaciones alojados en el grupo de trabajo.

El recuento de aumento de las reglas de escalado vertical se debe configurar al menos en 1X de la tasa de inflación del Plan del Servicio de aplicaciones para el escalado vertical.

El recuento de reducción se puede ajustar en un valor situado entre 1/2X y 1X de la tasa de inflación del plan del Servicio de aplicaciones para la reducción vertical.

### <a name="autoscale-for-front-end-pool"></a>Escalado automático para un grupo de servidores front-end
Las reglas de escalado automático de front-end son más sencillas que las de los grupos de trabajo. En primer lugar, debe  
asegurarse de que dicha duración de la medida y los temporizadores de enfriamiento tienen en cuenta que las operaciones de escala no son instantáneas en un plan de App Service.

En este escenario, Frank sabe que la tasa de errores aumenta una vez que los servidores front-end alcanzan el 80 % de uso de CPU y configura la regla de escalado automático para que aumente las instancias como sigue:

![Configuración del escalado automático para un grupo de servidores front-end.][Front-End-Scale]

| **Perfil de escalado automático: Servidores front-end** |
| --- |
| **Nombre:** Escalado automático: Servidores front-end |
| **Escalar por:** Reglas de rendimiento y programación |
| **Perfil:** Todos los días |
| **Tipo:** periodicidad |
| **Rango objetivo:** de 3 a 10 instancias |
| **Días:** Todos los días |
| **Hora de inicio:** 9:00 |
| **Zona horaria:** UTC-08 |
|  |
| **Regla de escalado automático (escalar verticalmente)** |
| **Recurso:** Grupo de servidores front-end |
| **Métrica:** % de CPU |
| **Operación:** Mayor que 60% |
| **Duración:** 20 minutos |
| **Agregación de tiempo:** media |
| **Acción:** Aumentar recuento en 3 |
| **Tiempo de finalización (minutos):** 120 |
|  |
| **Regla de escalado automático (reducir verticalmente)** |
| **Recurso:** Grupo de trabajo 1 |
| **Métrica:** % de CPU |
| **Operación:** menor que el 30 % |
| **Duración:** 20 minutos |
| **Agregación de tiempo:** media |
| **Acción:** Reducir recuento en 3 |
| **Tiempo de finalización (minutos):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
