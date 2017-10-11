---
title: "Restauración de un volumen desde una copia de seguridad en la serie StorSimple 8000 | Microsoft Docs"
description: "Explica cómo usar el catálogo de copias de seguridad del servicio StorSimple Device Manager para restaurar un volumen de StorSimple desde un conjunto de copias de seguridad."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: aff0710ead4f76bb80c38e2d88fe9cd3ce6a7b48
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar un volumen de StorSimple de un conjunto de copia de seguridad

## <a name="overview"></a>Información general

Este tutorial describe la operación de restauración realizada en un dispositivo de la serie StorSimple 8000 mediante un conjunto de copias de seguridad existente. Use la hoja **Catálogo de copias de seguridad** para restaurar un volumen de una copia de seguridad local o en la nube. La hoja **Catálogo de copias de seguridad** muestra todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automatizadas. La operación de restauración a partir de un conjunto de copias de seguridad pone el volumen en línea inmediatamente mientras los datos se descargan en segundo plano.

Un método alternativo para iniciar la restauración consiste en ir a **Dispositivos > [su dispositivo] > Volúmenes**. En la hoja **Volúmenes**, seleccione un volumen, haga clic con el botón derecho para abrir el menú contextual y, después, seleccione **Restaurar**.

## <a name="before-you-restore"></a>Antes de restaurar

Antes de iniciar una restauración, repase las siguientes advertencias:

* **Debe desconectar el volumen** : desconecte el volumen en el host y en el dispositivo antes de iniciar la operación de restauración. Aunque la operación de restauración pone el volumen automáticamente en línea en el dispositivo, debe poner el dispositivo en línea manualmente en el host. Puede poner el volumen en línea en el host tan pronto como el volumen está conectado en el dispositivo. (No es necesario esperar a que termine la operación de restauración). Para conocer los procedimientos, vaya a [Desconectar un volumen](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **El tipo de volumen después de la restauración** : los volúmenes eliminados se restauran en función del tipo de instantánea; es decir, los volúmenes anclados localmente se restauran como volúmenes anclados localmente y los volúmenes en capas, como volúmenes en capas.

    Para los volúmenes existentes, el tipo de uso actual del volumen reemplaza el tipo que se almacena en la instantánea. Por ejemplo, si restaura un volumen a partir de una instantánea realizada cuando el tipo de volumen se ha almacenado en capas y el tipo de volumen ahora está anclado localmente (debido a la realización de una operación de conversión), el volumen se restaurará como un volumen anclado localmente. De forma similar, si un volumen anclado localmente existente se ha expandido y posteriormente se restaura a partir de una instantánea más antigua que se realiza cuando el volumen es más pequeño, el volumen restaurado conservará el tamaño expandido actual.

    No se puede convertir un volumen de un volumen en capas a un volumen anclado localmente, o viceversa, mientras se está restaurando el volumen. Espere hasta que finalice la operación de restauración y después puede convertir el volumen a otro tipo. Para obtener información sobre cómo convertir un volumen, vaya a [Cambiar el tipo de volumen](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **El tamaño del volumen se reflejará en el volumen restaurado**: esta es una consideración importante si va a restaurar un volumen anclado localmente que se ha eliminado (porque los volúmenes anclados localmente se aprovisionan totalmente). Asegúrese de que dispone de suficiente espacio antes de intentar restaurar un volumen anclado localmente que se haya eliminado anteriormente.

* **No se puede expandir un volumen mientras se está restaurando** : espere a que termine la operación de restauración antes de intentar expandir el volumen. Para obtener información sobre la expansión de un volumen, vaya a [Modificar un volumen](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Puede realizar una copia de seguridad mientras se restaura un volumen local**: para conocer los procedimientos, vaya a [Usar el servicio StorSimple Device Manager para administrar directivas de copia de seguridad](storsimple-8000-manage-backup-policies-u2.md).

* **Puede cancelar una operación de restauración** : si se cancela el trabajo de restauración, el volumen se revertirá al estado en que estaba antes de iniciar la operación de restauración. Para conocer los procedimientos, vaya a [Cancelar un trabajo](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Cómo funciona la restauración

Para los dispositivos que ejecutan Update 4 o una versión posterior, se implementa una restauración basada en el mapa térmico. Como las solicitudes del host para acceder a los datos llegan al dispositivo, se realiza el seguimiento de estas solicitudes y se crea un mapa térmico. Una velocidad de solicitudes alta da como resultado fragmentos de datos con mayor nivel de actividad, mientras que una velocidad de solicitudes inferior se traduce en fragmentos con menos nivel de actividad. Debe acceder a los datos dos veces como mínimo para marcarse como _activo_. Un archivo que se modifica también se marca como _activo_. Una vez que se inicia la restauración, se realiza la hidratación proactiva de los datos en función del mapa térmico. Para las versiones anteriores a Update 4, los datos se descargan durante la restauración únicamente en función del acceso.

Las siguientes advertencias se aplican a las restauraciones basadas en mapas térmicos:

* El seguimiento basado en el mapa térmico solo se habilita para los volúmenes en capa; los volúmenes anclados localmente no son compatibles.

* Tampoco se puede realizar la restauración basada en el mapa térmico al clonar un volumen en otro dispositivo. 

* Si hay una restauración en contexto y una instantánea local para el volumen que se restaurará en el dispositivo, no se realizará la rehidratación (porque los datos ya están disponibles localmente). 

* De forma predeterminada, cuando se restaura, se inician los trabajos de rehidratación que rehidratan los datos de forma proactiva en función del mapa térmico. 

En Update 4, los cmdlets de Windows PowerShell se puede utilizar para consultar los trabajos de rehidratación en ejecución, cancelar un trabajo de rehidratación y obtener el estado del trabajo de rehidratación.

* `Get-HcsRehydrationJob`: este cmdlet obtiene el estado del trabajo de rehidratación. Se desencadena un solo trabajo de rehidratación para un volumen.

* `Set-HcsRehydrationJob`: este cmdlet permite interrumpir, detener o reanudar el trabajo de rehidratación, cuando la rehidratación está en curso.

Para obtener más información sobre los cmdlets de rehidratación, vaya al artículo de [referencia de cmdlet de Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Normalmente, con la rehidratación automática, se espera que el rendimiento de lectura transitorio sea superior. La magnitud real de mejoras depende de varios factores, como el patrón de acceso, la renovación de datos y el tipo de datos. 

Para cancelar un trabajo de rehidratación, puede usar el cmdlet de PowerShell. Si desea desactivar permanentemente los trabajos de rehidratación para todas las restauraciones futuras, póngase en contacto con el [equipo de soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Cómo usar el catálogo de copias de seguridad

La hoja **Catálogo de copias de seguridad** proporciona una consulta que permite limitar la selección de conjuntos de copias de seguridad. Puede filtrar los conjuntos de copias de seguridad que se recuperan en función de los parámetros siguientes:

* **Intervalo de tiempo**: el intervalo de fecha y hora en el que se creó el conjunto de copias de seguridad.
* **Dispositivo** : dispositivo en el que se creó el conjunto de copias de seguridad.
* **Directiva de copia de seguridad** o **volumen**: directiva de copia de seguridad o volumen asociado a este conjunto de copia de seguridad.

A continuación, los conjuntos de copias de seguridad filtrados se presentan en forma de tabla en función de los siguientes atributos:

* **Nombre** : nombre de la directiva de copias de seguridad o del volumen asociado al conjunto de copias de seguridad.
* **Tipo** : los conjuntos de copias de seguridad pueden ser instantáneas locales o instantáneas en la nube. Una instantánea local es una copia de seguridad de todos los datos del volumen que se almacenan localmente en el dispositivo, mientras que una instantánea en la nube hace referencia a la copia de seguridad de los datos del volumen que residen en la nube. Las instantáneas locales proporcionan un acceso más rápido, mientras que las instantáneas en la nube son preferibles para la resistencia de los datos.
* **Tamaño** : tamaño real del conjunto de copias de seguridad.
* **Creado en** : fecha y hora en que se crearon las copias de seguridad. 
* **Volúmenes**: el número de volúmenes asociados con el conjunto de copias de seguridad.
* **Iniciada por**: las copias de seguridad se pueden iniciar automáticamente en función de una programación o manualmente por el usuario. (Puede usar una directiva de copia de seguridad para programar copias de seguridad. Como alternativa, puede usar la opción **Realizar copia de seguridad** para realizar una copia de seguridad interactiva o una copia de seguridad a petición).

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Cómo restaurar un volumen de StorSimple de una copia de seguridad.

Puede usar la hoja **Catálogo de copias de seguridad** para restaurar el volumen StorSimple a partir de una copia de seguridad específica. Sin embargo, debe tener en cuenta que, cuando se restaura un volumen, el volumen volverá al estado en el que se encontraba cuando se realizó la copia de seguridad. Se perderán todos los datos que se agregaron después de la operación de copia de seguridad.

> [!WARNING]
> Cuando se realice una restauración a partir de una copia de seguridad, se reemplazarán los volúmenes existentes desde la copia de seguridad. Esto puede provocar la pérdida de los datos que se escribieron después de que se realizase la copia de seguridad.


### <a name="to-restore-your-volume"></a>Para restaurar el volumen
1. Vaya al servicio StorSimple Device Manager y, después, haga clic en **Catálogo de copias de seguridad**.

2. Seleccione una copia de seguridad de la siguiente manera:
   
   1. Especifique el intervalo de tiempo.
   2. Seleccione el dispositivo adecuado.
   3. En la lista desplegable, seleccione el volumen o la directiva de copia de seguridad para la copia de seguridad que desea seleccionar.
   4. Haga clic en **Aplicar** para ejecutar esta consulta.

    Las copias de seguridad asociadas al volumen o la directiva de copia de seguridad seleccionados deben aparecer en la lista de conjuntos de copias de seguridad.
   
    ![Lista de conjuntos de copia de seguridad](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expanda el conjunto de copias de seguridad para ver los volúmenes asociados. Estos volúmenes deben desconectarse en el host y en el dispositivo para que pueda restaurarlos. Acceda a los volúmenes de la hoja **Volúmenes** de su dispositivo y luego siga los pasos indicados en [Take a volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) (Desconexión de un volumen) para desconectarlos.
   
   > [!IMPORTANT]
   > Asegúrese de desconectar primero los volúmenes del host y, después, desconectar los volúmenes del dispositivo. Si no establece los volúmenes sin conexión en el host, esto podría causar daños en los datos.
   
4. Vuelva a la pestaña **Catálogo de Backup** y seleccione un conjunto de copias de seguridad. Haga clic con el botón derecho y, en el menú contextual, seleccione **Restaurar**.

    ![Lista de conjuntos de copia de seguridad](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Se le pedirá confirmación. Revise la información de restauración y después seleccione la casilla de confirmación.
   
    ![Página de confirmación](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  Haga clic en **Restaurar**. Esto inicia un trabajo de restauración que se puede ver accediendo a la página **Trabajos**.

    ![Página de confirmación](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Una vez completada la restauración, compruebe que los volúmenes de la copia de seguridad sustituyeron el contenido de los volúmenes.


## <a name="if-the-restore-fails"></a>Si se produce algún error en la restauración

Recibirá una alerta si se produce un error en la operación de restauración por algún motivo. En su caso, actualice la lista de copias de seguridad para verificar que la copia de seguridad sigue siendo válida. Si la copia de seguridad es válida y se restaura desde la nube, el problema puede deberse a errores en la conectividad.

Para completar correctamente la operación de restauración, desconecte el volumen en el host y vuelva a intentar la operación de restauración. Tenga en cuenta que las modificaciones realizadas en los datos del volumen durante el proceso de restauración se perderán.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [Administrar volúmenes de StorSimple](storsimple-8000-manage-volumes-u2.md).
* Aprenda a [usar el servicio StorSimple Device Manager para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

