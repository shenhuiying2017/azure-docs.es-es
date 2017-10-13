---
title: "Administración de plantillas de ancho de banda de la serie StorSimple 8000 | Microsoft Docs"
description: "Describe cómo administrar plantillas de ancho de banda de StorSimple, que le permiten controlar el consumo de ancho de banda."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 50d0a920bef097013feddc828d2c37133b9057b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Uso del servicio StorSimple Device Manager para administrar plantillas de ancho de banda de StorSimple

## <a name="overview"></a>Información general

Las plantillas de ancho de banda permiten configurar el uso del ancho de banda de red en varias programaciones de hora del día para interconectar los datos del dispositivo StorSimple en la nube.

Mediante programaciones de limitación de ancho de banda puede:

* Especifique programaciones de ancho de banda personalizadas según los usos de la red de cargas de trabajo.
* Centralizar la administración y reutilizar las programaciones en varios dispositivos de forma fácil y transparente.

> [!NOTE]
> Esta característica está disponible solo para dispositivos físicos de StorSimple (modelos 8100 y 8600) y no para StorSimple Cloud Appliances (modelos 8010 y 8020).


## <a name="the-bandwidth-templates-blade"></a>Hoja Plantillas de ancho de banda

La hoja **Plantillas de ancho de banda** tiene todas las plantillas de ancho de banda del servicio en formato tabular y contiene la siguiente información:

* **Nombre** : nombre único asignado a la plantilla de ancho de banda cuando se creó.
* **Programación** : número de programaciones contenidas en una plantilla de ancho de banda determinada.
* **Usado por** : número de volúmenes que usan las plantillas de ancho de banda.

También puede encontrar información adicional para ayudarle a configurar plantillas de ancho de banda en:

* [Preguntas y respuestas acerca de las plantillas de ancho de banda](#questions-and-answers-about-bandwidth-templates)
* [Procedimientos recomendados para plantillas de ancho de banda](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Agregar una plantilla de ancho de banda

Realice los siguientes pasos para crear una nueva plantilla de ancho de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para agregar una plantilla de ancho de banda

1. Vaya al servicio StorSimple Device Manager, haga clic en **Plantillas de ancho de banda** y, a continuación, haga clic en **+ Agregar plantilla de ancho de banda**.

    ![Hacer clic en + Agregar plantilla de ancho de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. En la hoja **Agregar plantilla de ancho de banda**, realice los pasos siguientes:
   
    1. Especifique un nombre único para la plantilla de ancho de banda.
    2. Defina una programación de ancho de banda. Para crear una programación:
   
        1. En la lista desplegable, seleccione los **días** de la semana para los que se configura la programación. Puede seleccionar varios días.        
        
        2. Escriba una **hora de inicio** en formato _hh:mm_. Esta es la hora a la que se iniciará la programación.

        3. Escriba una **hora de finalización** en formato _hh:mm_. Esta es la hora a la que finalizará la programación.
      
           > [!NOTE]
           > No se permiten las programaciones superpuestas. Si las horas de inicio y finalización provocan una programación superpuesta, verá un mensaje de error que lo indica.

        4. Especifique la **velocidad de ancho de banda**. Este es el ancho de banda en Megabits por segundo (Mbps) utilizado por el dispositivo StorSimple en operaciones que afectan a la nube (tanto cargas como descargas). Proporcione un número entre 1 y 1.000 para este campo.

            ![Definir programación de ancho de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Repita los pasos anteriores para definir varias programaciones para la plantilla hasta que haya terminado.

        5. Haga clic en **Agregar** para comenzar a crear una plantilla de ancho de banda. La plantilla creada se agrega a la lista de plantillas de ancho de banda.
      

## <a name="edit-a-bandwidth-template"></a>Editar una plantilla de ancho de banda

Realice los siguientes pasos para editar una plantilla de ancho de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar una plantilla de ancho de banda

1. Vaya al servicio StorSimple Device Manager y haga clic en **Plantillas de ancho de banda**.
2. En la lista de plantillas de ancho de banda, seleccione la plantilla que quiere eliminar. Haga clic con el botón derecho y en el menú contextual, seleccione **Eliminar**.
3. Cuando se le pida confirmación, haga clic en **Aceptar**. Con esto, se eliminará la plantilla de ancho de banda. 
4. La lista de plantillas de ancho de banda se actualizará para reflejar la eliminación.

> [!NOTE]
> No podrá guardar los cambios si la programación editada se superpone a una programación existente en la plantilla de ancho de banda que se va a modificar.

## <a name="delete-a-bandwidth-template"></a>Eliminar una plantilla de ancho de banda

Realice los siguientes pasos para eliminar una plantilla de ancho de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para eliminar una plantilla de ancho de banda

1. Vaya al servicio StorSimple Device Manager y haga clic en **Plantillas de ancho de banda**.
2. En la lista de plantillas de ancho de banda, seleccione la plantilla que quiere eliminar. Haga clic con el botón derecho y en el menú contextual, seleccione Eliminar.
3. Cuando se le pida confirmación, haga clic en **Aceptar**. Con esto, se eliminará la plantilla de ancho de banda.
4. La lista de plantillas de ancho de banda se actualizará para reflejar la eliminación.

Si la plantilla está siendo utilizada por algún volumen, no podrá eliminarla. Verá un mensaje de error que indicará que la plantilla está en uso. Aparecerá un cuadro de diálogo de mensaje de error avisándole de que se deben quitar todas las referencias a la plantilla.

Puede eliminar todas las referencias a la plantilla mediante el acceso a la página **Contenedores de volúmenes** y modificando los contenedores de volúmenes que usan esta plantilla para que puedan usar otra plantilla o una configuración personalizada o ilimitada de ancho de banda. Cuando se hayan eliminado todas las referencias, puede eliminar la plantilla.

## <a name="use-a-default-bandwidth-template"></a>Usar una plantilla de ancho de banda predeterminada

Los contenedores de volúmenes proporcionan y usan una plantilla de ancho de banda de manera predeterminada para aplicar controles de ancho de banda al obtener acceso a la nube. La plantilla predeterminada también sirve como referencia lista para los usuarios que crean sus propias plantillas. Los detalles de esta plantilla predeterminada son:

* **Nombre** : noches y fines de semana ilimitados
* **Programación**: una sola programación de lunes a viernes que aplica a una velocidad de ancho de banda de 1 Mbps entre las 8.00 y las 17.00 (hora del dispositivo). El ancho de banda se establece en Sin límite para el resto de la semana.

Puede editar la plantilla predeterminada. Se realiza un seguimiento del uso de esta plantilla (incluidas las versiones modificadas).

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Crear una plantilla de ancho de banda para todo el día que comience a una hora especificada

Siga este procedimiento para crear una programación que se inicie en un momento determinado y se ejecute todo el día. En el ejemplo, la programación se inicia a las 9 a.m. y se ejecuta hasta las 9 a.m. del día siguiente. Es importante tener en cuenta que los tiempos de inicio y finalización de una programación determinada deben estar en la misma programación de 24 horas y no pueden abarcar varios días. Si necesita configurar las plantillas de ancho de banda que abarcan varios días, necesitará usar varias programaciones (como se muestra en el ejemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para crear una plantilla de ancho de banda para todo el día

1. Cree una programación que se inicie a las 9 a.m. de la mañana y se ejecuta hasta la medianoche.
2. Agregar otra programación. Configure la segunda programación para ejecutar desde la medianoche hasta las 9 a.m. de la mañana.
3. Guarde la plantilla de ancho de banda.

La programación compuesta se iniciará en el momento que desee y se ejecutará todo el día.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Preguntas y respuestas acerca de las plantillas de ancho de banda

**P**. ¿Qué les sucede a los controles de ancho de banda cuando se encuentra entre programaciones? (Una programación ha terminado y todavía no se ha iniciado otra).

**R**. En estos casos, no se utilizará ningún control de ancho de banda. Esto significa que el dispositivo puede utilizar un ancho de banda ilimitado durante la disposición de datos a la nube.

**P**. ¿Puede modificar las plantillas de ancho de banda en un dispositivo sin conexión?

**R**. No podrá modificar las plantillas de ancho de banda de los contenedores de volúmenes si el dispositivo correspondiente está sin conexión.

**P**. ¿Puede editar una plantilla de ancho de banda asociada a un contenedor de volúmenes cuando los volúmenes asociados están sin conexión?

**R**. Puede modificar una plantilla de ancho de banda asociada a un contenedor de volúmenes cuyos volúmenes están sin conexión. Tenga en cuenta que cuando los volúmenes están sin conexión, no se pueden colocar datos desde el dispositivo en la nube.

**P**. ¿Puede eliminar una plantilla predeterminada?

**R**. Si bien puede eliminar una plantilla predeterminada, no es una buena idea hacerlo. Se realiza un seguimiento del uso de una plantilla predeterminada (incluidas las versiones modificadas). Los datos de seguimiento se analizan y en el transcurso del tiempo, se utilizan para mejorar la plantilla predeterminada.

**P**. ¿Cómo se determina que es necesario modificar las plantillas de ancho de banda?

**R**. Uno de los síntomas que indican que debe modificar las plantillas de ancho de banda es cuando empiece a ver que la red se ralentiza o retrae varias veces al día. Si esto ocurre, supervise la red de almacenamiento y uso examinando los gráficos de rendimiento de E/S y de rendimiento de la red.

En los datos de rendimiento de la red, identifique la hora del día y los contenedores de volúmenes en los que se producen los cuello de botella de la red. Si esto sucede cuando se están colocando los datos en la nube (obtenga esta información del rendimiento de E/S de todos los contenedores de volúmenes del dispositivo a la nube), será necesario modificar las plantillas de ancho de banda asociadas a los contenedores de volúmenes.

Una vez que se utilizan las plantillas modificadas, deberá volver a supervisar la red para comprobar si se producen latencias importantes. En caso afirmativo, deberá volver a visitar las plantillas de ancho de banda.

**P**. ¿Qué sucede si varios contenedores de volúmenes de mi dispositivo disponen de programaciones que se superponen pero se aplican límites distintos a cada una?

**R**. Supongamos que tiene un dispositivo con 3 contenedores de volúmenes. Las programaciones asociadas a estos contenedores se superponen completamente. Para cada uno de estos contenedores, los límites de ancho de banda utilizados son 5, 10 y 15 Mbps respectivamente. Cuando se producen operaciones de E/S en todos estos contenedores al mismo tiempo, se puede aplicar el mínimo de 3 límites de ancho de banda: en este caso, 5 Mbps debido a que estas solicitudes de E/S de salida comparten la misma cola.

## <a name="best-practices-for-bandwidth-templates"></a>Prácticas recomendadas para plantillas de ancho de banda

Siga estas prácticas recomendadas para el dispositivo StorSimple:

* Configure plantillas de ancho de banda en el dispositivo para habilitar la limitación variable del rendimiento de la red por parte del dispositivo en distintos momentos del día. Cuando se utilizan estas plantillas de ancho de banda con programaciones de copia de seguridad, pueden aprovechar eficazmente el ancho de banda de red adicional para las operaciones de nube durante las horas de menos tráfico.
* Calcule el ancho de banda real necesario para una implementación concreta en función del tamaño de la implementación y del objetivo de tiempo de recuperación necesario (RTO).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre el [uso del servicio StorSimple Device Manager para administrar su dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

