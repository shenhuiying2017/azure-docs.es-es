---
title: Cohortes de uso de Azure Application Insights | Microsoft Docs
description: Análisis de conjuntos diferentes o usuarios, sesiones, eventos u operaciones que tienen algo en común
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Cohortes de Application Insights

Una cohorte es un conjuntos de usuarios, sesiones, eventos u operaciones que tienen algo en común. En Azure Application Insights las cohortes se definen mediante una de Analytics. Si descubre que tiene que analizar un conjunto específico de usuarios o eventos de forma repetida, las cohortes pueden proporcionarle una mayor flexibilidad para expresar exactamente el conjunto que le interesa.

![Panel de cohortes](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Cohortes frente a filtros básicos

Aunque las cohortes se usan de forma similar a los filtros, el hecho de que la definición de una cohorte se crea a partir de consultas personalizadas de Analytics les permite ser mucho más complejas y adaptables. A diferencia de los filtros, puede guardar las cohortes para que otros miembros del equipo puedan volver a usarlas.

Puede definir una cohorte de usuarios en la que todos hayan probado una nueva característica de una aplicación. Con esta cohorte guardada en el recurso de Application Insights, facilita el análisis de este grupo específico de usuarios en el futuro con un solo clic.

> [!NOTE]
> Una vez creadas, las cohortes están disponibles en las herramientas Usuarios, Sesiones, Eventos y Flujos de usuario.

## <a name="example-engaged-users"></a>Ejemplo: usuarios dedicados

El equipo define como usuario dedicado a cualquier persona que use la aplicación cinco o más veces en un mes determinado. Vamos a definir una cohorte de estos usuarios dedicados.

1. Abra la herramienta de **Cohortes**.

2. Haga clic en la pestaña **Galería de plantillas**. Aquí encontrará una colección de plantillas para varias cohortes.

3. Elija **Engaged Users** – by Days Used (Usuarios dedicados: por días de uso ).

    Hay tres parámetros para esta cohorte:
      * **Actividades** que le permiten elegir qué eventos y vistas de página deben contar como "uso".
      * **Período** la definición de un mes.
      * **UsedAtleastCustom** el número de veces que los usuarios necesitan utilizar algo de un período para contar como usuario dedicado.

4. Cambie **UsedAtleastCustom** a "5 + días" y deje **Período** con el valor predeterminado de 28 días.

    ![Imagen](.\media\app-insights-usage-cohorts\003.png)

    Esta cohorte representa todos los identificadores de usuarios que se han enviado con cualquier vista de página o evento personalizado en cinco días distintos en los últimos 28 días.

5. Haga clic en **Save**(Guardar).

   > [!TIP]
   >  Dé un nombre a la cohorte como "Usuarios dedicados (5 + días)" y guárdela en "Mis informes" o "Informes compartidos" en función de si desea que otras personas con acceso a este recurso de Appication Insights vean esta cohorte.

6. Haga clic en **Back to Gallery** (Volver a la galería).

### <a name="what-can-you-do-with-this-cohort"></a>¿Qué puede hacer con esta cohorte?

Abra la herramienta **Users** (Usuarios) > en la lista desplegable **Show** (Mostrar) > elija la cohorte que ha creado en **Users who belong to…** (Usuarios que pertenecen a...)

La herramienta de usuarios se filtra ahora con esta cohorte de usuarios:

![Panel de usuarios filtrados para una determinada cohorte](.\media\app-insights-usage-cohorts\004.png)

Hay varios aspectos importantes que se deben tener en cuenta:
   * Se trata de un conjunto que no se habría podido crear con los filtros normales. La lógica de fecha es más avanzada.
   * Puede filtrar aún más esta cohorte con los filtros normales de la herramienta de usuarios. Por lo que mientras que la cohorte se define en ventanas de 28 días, aún se puede ajustar el intervalo de tiempo en la herramienta de usuario para que sea 30, 60 o 90 días. 

Esto le permite realizar preguntas más sofisticadas como: _para las personas que estaban dedicadas en los últimos 28 días, ¿cómo se han comportado esas mismas personas durante los últimos 60 días?_ que de otro modo sería imposible expresar mediante el generador de consultas.

## <a name="example-events-cohort"></a>Ejemplo: cohortes de eventos

También puede hacer cohortes de eventos. Vamos a definir una cohorte de los eventos y las vistas de página, y a continuación, veremos cómo utilizarlas desde las otras herramientas. Esto puede resultar útil para definir un conjunto de eventos que su equipo considere de _uso activo_, o para definir un conjunto de eventos relacionados con una nueva función específica.

1. Abra la herramienta de **Cohortes**.

2. Haga clic en la pestaña **Galería de plantillas**. Aquí encontrará una colección de plantillas para varias cohortes.

3. Elija **Events Picker** (Selector de eventos).

    ![Captura de pantalla del selector de eventos](.\media\app-insights-usage-cohorts\006.png)

4. En la lista desplegable **Actividades**, seleccione los eventos que desearía incluir en la cohorte

5. Guarde la cohorte y asígnele un nombre.

## <a name="example-active-users-where-you-modify-query"></a>Ejemplo: usuarios activos en donde se modifica una consulta

Las dos cohortes anteriores se han definido mediante listas desplegables. Pero también podemos definir las cohortes con consultas de Analytics para una flexibilidad total. Veamos cómo creando una cohorte de usuarios del Reino Unido.

![Imagen animada con recorrido a través del uso de la herramienta de cohortes](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Abra la herramienta **Cohortes** > y haga clic en la pestaña **Galería de plantillas** > elija **Cohortes de usuarios en blanco**.

    ![Cohortes de usuarios en blanco](.\media\app-insights-usage-cohorts\001.png)

    Hay tres opciones:
       * Una sección de texto Markdown donde se describe la cohorte en más detalle para otros usuarios del equipo.

       * Una sección de parámetros que puede usar para hacer que sus propios parámetros, como **Actividades** y otras listas desplegables de los dos ejemplos anteriores.

       * Una sección de consulta que se usa para definir la cohorte con una consulta de Analytics.

    En la sección de la consulta, [escriba una consulta de Analytics](https://docs.loganalytics.io/index) que selecciona el conjunto específico de filas que describen la cohorte que desee definir. La herramienta Cohortes agrega implícitamente una cláusula "| Resumir por identificador de usuario" a la consulta. Esto se muestra una vista previa debajo de la consulta en una tabla para que se asegure de que la consulta devuelve resultados.

    > [!NOTE]
    > Si no ve la consulta, pruebe a cambiar el tamaño de la sección para hacerla más alta y mostrar la consulta. En el .gif animado al principio de esta sección se muestra el comportamiento de cambio de tamaño.

2. Copiar y pegar en el editor de consultas lo siguiente:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Haga clic en **Run Query** (Ejecutar consulta). Debería ver aparecer identificadores de usuario en la tabla. Si no es así, cambie a un país donde la aplicación tenga usuarios.

4. Guarde y dele un nombre a la cohorte.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

_He definido un cohortes de usuarios de un determinado país. Cuando comparo esta cohorte en la herramienta Usuarios con simplemente establecer un filtro en ese país en la herramienta Usuarios, veo resultados diferentes. ¿Por qué?_

Las cohortes y los filtros son diferentes. Suponga que tiene una cohorte de usuarios del Reino Unido (definida como el ejemplo anterior) y compara sus resultados con configurar el filtro "País o región = Reino Unido".

* La versión de cohorte mostrará todos los eventos de los usuarios que han enviado al menos un evento desde el Reino Unido en el intervalo de tiempo actual. Si se divide según el país o región, probablemente verá muchos países y regiones.
* La versión de filtros solo mostrará los eventos del Reino Unido. Mientras que si se divide según el país o región, solo verá el Reino Unido.

## <a name="learn-more"></a>Más información
- [Lenguaje de consulta de Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
- [Usuarios, sesiones, eventos](app-insights-usage-segmentation.md)
- [Flujos de usuario](app-insights-usage-flows.md)
- [Información general del uso](app-insights-usage-overview.md)