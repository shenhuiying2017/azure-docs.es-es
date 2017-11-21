---
title: "Administración de plantillas de ancho de banda de StorSimple | Microsoft Docs"
description: "Describe cómo administrar plantillas de ancho de banda de StorSimple, que le permiten controlar el consumo de ancho de banda."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0027b90e-91a5-437d-9bd0-06b05674aa5f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b426a0fc8e0a6c8615b348d3e832c134be4dfbaf
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Usar el servicio de Administrador de StorSimple para administrar plantillas de ancho de banda de StorSimple
> [!NOTE]
> El portal clásico para StorSimple está en desuso. Los administradores de dispositivos StorSimple realizarán la transición automáticamente al nuevo Azure Portal según la programación de puesta en desuso. Recibirá un correo electrónico y una notificación del portal en los que se avisa de este paso. Este documento también se retirará pronto. Para ver la versión de este artículo para el nuevo Azure Portal, vaya a [Usar el servicio de Administrador de StorSimple para administrar plantillas de ancho de banda de StorSimple](storsimple-8000-manage-bandwidth-templates.md). Si tiene alguna pregunta sobre este paso, vea las [preguntas frecuentes de la migración a Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Información general
Las plantillas de ancho de banda permiten configurar el uso del ancho de banda de red en varias programaciones de hora del día para interconectar los datos del dispositivo StorSimple en la nube.

Mediante programaciones de limitación de ancho de banda puede:

* Especifique programaciones de ancho de banda personalizadas según los usos de la red de cargas de trabajo.
* Centralizar la administración y reutilizar las programaciones en varios dispositivos de forma fácil y transparente.

> [!NOTE]
> Esta característica está disponible solo para dispositivos físicos StorSimple y no para los dispositivos virtuales.
> 
> 

Todas las plantillas de ancho de banda del servicio se muestran en formato tabular y contienen la siguiente información:

* **Nombre** : nombre único asignado a la plantilla de ancho de banda cuando se creó.
* **Programación** : número de programaciones contenidas en una plantilla de ancho de banda determinada.
* **Usado por** : número de volúmenes que usan las plantillas de ancho de banda.

La página **Configurar** del servicio StorSimple Manager del Portal de Azure clásico se usa para administrar plantillas de ancho de banda.

También puede encontrar información adicional para ayudarle a configurar plantillas de ancho de banda en:

* Preguntas y respuestas acerca de las plantillas de ancho de banda
* Prácticas recomendadas para plantillas de ancho de banda

## <a name="add-a-bandwidth-template"></a>Agregar una plantilla de ancho de banda
Realice los siguientes pasos para crear una nueva plantilla de ancho de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para agregar una plantilla de ancho de banda
1. En la página **Configurar** del servicio StorSimple Manager, haga clic en **Agregar/editar plantillas de ancho de banda**.
2. En el cuadro de diálogo **Agregar/Editar plantilla de ancho de banda** :
   
   1. Desde la lista desplegable **Plantilla**, seleccione **Crear nueva** para agregar una nueva plantilla de ancho de banda.
   2. Especifique un nombre único para la plantilla de ancho de banda.
3. Defina una **Programación de ancho de banda**. Para crear una programación:
   
   1. En la lista desplegable, seleccione los días de la semana para los que se configura la programación. Puede seleccionar varios días seleccionando las casillas de verificación situadas antes de los días correspondientes en la lista.
   2. Seleccione la opción **Todo el día** si la programación se aplica todo el día. Cuando se activa esta opción, ya no se puede especificar una **Hora de inicio** ni una **Hora de finalización**. La programación se ejecuta desde las 12:00 a.m. hasta las 11:59 p.m.
   3. En la lista desplegable, seleccione una **hora de inicio**. Esta es la hora a la que se iniciará la programación.
   4. En la lista desplegable, seleccione una **hora de finalización**. Esta es la hora a la que finalizará la programación.
      
      > [!NOTE]
      > No se permiten las programaciones superpuestas. Si las horas de inicio y finalización provocan una programación superpuesta, verá un mensaje de error que lo indica.
      > 
      > 
   5. Especifique la **velocidad de ancho de banda**. Este es el ancho de banda en Megabits por segundo (Mbps) utilizado por el dispositivo StorSimple en operaciones que afectan a la nube (tanto cargas como descargas). Proporcione un número entre 1 y 1.000 para este campo.
   6. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). La plantilla que ha creado se agregará a la lista de plantillas de ancho de banda en la página **configurar** del servicio.
      
      ![Crear nueva plantilla de ancho de banda](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)
4. Haga clic en **Guardar** en la parte inferior de la página y, luego, haga clic en **Sí** cuando se pida confirmación. Así guardará los cambios de configuración realizados.

## <a name="edit-a-bandwidth-template"></a>Editar una plantilla de ancho de banda
Realice los siguientes pasos para editar una plantilla de ancho de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar una plantilla de ancho de banda
1. Haga clic en **Agregar/Editar plantilla de ancho de banda**.
2. En el cuadro de diálogo **Agregar/Editar plantilla de ancho de banda** :
   
   1. En la lista desplegable **Plantilla** , elija una plantilla de ancho de banda existente que desee modificar.
   2. Complete los cambios. (Puede modificar cualquiera de las opciones existentes).
   3. Haga clic en el icono de marca de verificación  ![Icono de marca de verificación](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Verá la plantilla modificada en la lista de plantillas de ancho de banda en la página de configuración del servicio.
3. Para guardar los cambios, haga clic en **Guardar** en la parte inferior de la página. Haga clic en **Sí** cuando se pida confirmación.

> [!NOTE]
> No podrá guardar los cambios si la programación editada se superpone a una programación existente en la plantilla de ancho de banda que se va a modificar.
> 
> 

## <a name="delete-a-bandwidth-template"></a>Eliminar una plantilla de ancho de banda
Realice los siguientes pasos para eliminar una plantilla de ancho de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para eliminar una plantilla de ancho de banda
1. En la lista tabular de las plantillas de ancho de banda para su servicio, seleccione la plantilla que desee eliminar. Aparecerá un icono de eliminación (**x**) en el extremo derecho de la plantilla seleccionada. Haga clic en el icono **x** para eliminar la plantilla.
2. Se le pedirá confirmación. Haga clic en **Aceptar** para continuar.

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
Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

