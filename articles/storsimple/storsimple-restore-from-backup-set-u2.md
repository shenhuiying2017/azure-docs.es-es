---
title: Restaurar un volumen de StorSimple desde una copia de seguridad | Microsoft Docs
description: "Explica cómo usar la página del catálogo de Backup del Administrador de StorSimple para restaurar un volumen de StorSimple desde un conjunto de copias de seguridad."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 76fa3dd8fa2f9775dc166686e699a8dd66399aff
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restaurar un volumen de StorSimple de un conjunto de copia de seguridad (Update 2)
> [!NOTE]
> El portal clásico para StorSimple está en desuso. Los administradores de dispositivos StorSimple realizarán la transición automáticamente al nuevo Azure Portal según la programación de puesta en desuso. Recibirá un correo electrónico y una notificación del portal en los que se avisa de este paso. Este documento también se retirará pronto. Para ver la versión de este artículo para el nuevo Azure Portal, vaya a [Restaurar un volumen de StorSimple de un conjunto de copia de seguridad (Update 2)](storsimple-8000-restore-from-backup-set-u2.md). Si tiene alguna pregunta sobre este paso, vea las [preguntas frecuentes de la migración a Azure Portal](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Información general
La página **Catálogo de Backup** muestra todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automatizadas. Utilice esta página para mostrar y administrar copias de seguridad, ejecutar restauraciones a partir de un conjunto de copia de seguridad o clonar un volumen.

 ![Página del catálogo de Backup](./media/storsimple-restore-from-backup-set-u2/restore.png)

Este tutorial explica cómo usar la página **Catálogo de Backup** para restaurar el dispositivo desde un conjunto de copia de seguridad.

Puede restaurar un volumen a partir de una copia de seguridad local o en la nube. En cualquier caso, la operación de restauración pone el volumen en línea inmediatamente mientras los datos se descargan en segundo plano. 

## <a name="before-you-restore"></a>Antes de restaurar
Antes de iniciar una operación de restauración, debe tener en cuenta lo siguiente:

* **Desconecte el volumen**: desconecte el volumen en el host y en el dispositivo antes de iniciar la operación de restauración. Aunque la operación de restauración pone el volumen automáticamente en línea en el dispositivo, debe poner el dispositivo en línea manualmente en el host. Puede poner el volumen en línea en el host en cuanto el volumen esté conectado en el dispositivo. (No es necesario esperar a que termine la operación de restauración). Para conocer los procedimientos, vaya a [Desconectar un volumen](storsimple-manage-volumes-u2.md#take-a-volume-offline).
* **El tipo de volumen después de la restauración**: los volúmenes eliminados se restauran en función del tipo de instantánea; es decir, los volúmenes anclados localmente se restauran como volúmenes anclados localmente y los volúmenes en capas, como volúmenes en capas.
  
    Para los volúmenes existentes, el tipo de uso actual del volumen reemplaza el tipo que se almacena en la instantánea. Por ejemplo, si restaura un volumen a partir de una instantánea realizada cuando el tipo de volumen se ha almacenado en capas y el tipo de volumen ahora está anclado localmente (debido a una operación de conversión), el volumen se restaurará como un volumen anclado localmente. De forma similar, si un volumen anclado localmente existente se ha expandido y posteriormente se restaura a partir de una instantánea más antigua que se realiza cuando el volumen es más pequeño, el volumen restaurado conservará el tamaño expandido actual.
  
    No se puede convertir un volumen de un volumen en capas a uno anclado localmente o _viceversa_, mientras se está restaurando el volumen. Espere hasta que finalice la operación de restauración y después puede convertir el volumen a otro tipo. Para obtener información sobre cómo convertir un volumen, vaya a [Cambiar el tipo de volumen](storsimple-manage-volumes-u2.md#change-the-volume-type). 
* **El tamaño del volumen se reflejará en el volumen restaurado**: esta es una consideración importante si va a restaurar un volumen anclado localmente que se ha eliminado (porque los volúmenes anclados localmente se aprovisionan totalmente). Asegúrese de que dispone de suficiente espacio antes de intentar restaurar un volumen anclado localmente que se haya eliminado anteriormente. 
* **No se puede expandir un volumen mientras se está restaurando** : espere a que termine la operación de restauración antes de intentar expandir el volumen. Para obtener información sobre la expansión de un volumen, vaya a [Modificar un volumen](storsimple-manage-volumes-u2.md#modify-a-volume).
* **Puede realizar una copia de seguridad mientras se restaura un volumen local** : para conocer los procedimientos, vaya a [Usar el servicio de Administrador de StorSimple para administrar directivas de copia de seguridad](storsimple-manage-backup-policies.md).
* **Puede cancelar una operación de restauración**: si se cancela el trabajo de restauración, el volumen se revertirá al estado en que estaba antes de iniciar la restauración. Para conocer los procedimientos, vaya a [Cancelar un trabajo](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Cómo funciona la restauración
Para los dispositivos que ejecutan Update 4 o una versión posterior, se implementa una restauración basada en el mapa térmico. Como las solicitudes del host para acceder a los datos llegan al dispositivo, se realiza el seguimiento de estas solicitudes y se crea un mapa térmico. Una velocidad de solicitudes alta da como resultado fragmentos de datos con mayor nivel de actividad, mientras que una velocidad de solicitudes inferior se traduce en fragmentos con menos nivel de actividad. Debe acceder a los datos dos veces como mínimo para marcarse como _activo_. Un archivo que se modifica también se marca como _activo_. Una vez que se inicia la restauración, se realiza la hidratación proactiva de los datos en función del mapa térmico. Para las versiones anteriores a Update 4, los datos se descargan durante la restauración únicamente en función del acceso. 

El seguimiento basado en el mapa térmico solo se habilita para los volúmenes en capa; los volúmenes anclados localmente no son compatibles. Tampoco se puede realizar la restauración basada en el mapa térmico al clonar un volumen en otro dispositivo. Si hay una restauración en contexto y una instantánea local para el volumen que se restaurará en el dispositivo, no se realizará la rehidratación (porque los datos ya están disponibles localmente). De forma predeterminada, cuando se restaura, se inician los trabajos de rehidratación que rehidratan los datos de forma proactiva en función del mapa térmico. En Update 4, los cmdlets de Windows PowerShell se puede utilizar para consultar los trabajos de rehidratación en ejecución, cancelar un trabajo de rehidratación y obtener el estado del trabajo de rehidratación.

* `Get-HcsRehydrationJob`: este cmdlet obtiene el estado del trabajo de rehidratación. Se desencadena un solo trabajo de rehidratación para un volumen.
* `Set-HcsRehydrationJob`: este cmdlet permite interrumpir, detener o reanudar el trabajo de rehidratación, cuando la rehidratación está en curso. 

Para obtener más información sobre los cmdlets de rehidratación, vaya al artículo de [referencia de cmdlet de Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Normalmente, con la rehidratación automática, se espera que el rendimiento de lectura transitorio sea superior. La magnitud real de mejoras depende de varios factores, como el patrón de acceso, la renovación de datos y el tipo de datos. Para cancelar un trabajo de rehidratación, puede usar el cmdlet de PowerShell. Si desea desactivar permanentemente los trabajos de rehidratación para todas las restauraciones futuras, póngase en contacto con el equipo de soporte técnico de Microsoft.

## <a name="how-to-use-the-backup-catalog"></a>Cómo usar el catálogo de copias de seguridad
La página **Catálogo de Backup** proporciona una consulta que permite limitar la selección de conjuntos de copias de seguridad. Puede filtrar los conjuntos de copias de seguridad que se recuperan en función de los parámetros siguientes:

* **Dispositivo** : dispositivo en el que se creó el conjunto de copias de seguridad.
* **Directiva de copia de seguridad** o **volumen**: directiva de copia de seguridad o volumen asociado a este conjunto de copia de seguridad.
* **De** y **A**: el intervalo de fecha y hora en el que se creó el conjunto de copia de seguridad.

A continuación, los conjuntos de copias de seguridad filtrados se presentan en forma de tabla en función de los siguientes atributos:

* **Nombre** : nombre de la directiva de copias de seguridad o del volumen asociado al conjunto de copias de seguridad.
* **Tamaño** : tamaño real del conjunto de copias de seguridad.
* **Creado en** : fecha y hora en que se crearon las copias de seguridad. 
* **Tipo** : los conjuntos de copias de seguridad pueden ser instantáneas locales o instantáneas en la nube. Una instantánea local es una copia de seguridad de todos los datos del volumen almacenados localmente en el dispositivo. Una instantánea de nube hace referencia a la copia de seguridad de datos del volumen que reside en la nube. Las instantáneas locales proporcionan un acceso más rápido, mientras que las instantáneas en la nube son preferibles para la resistencia de los datos.
* **Iniciada por**: las copias de seguridad se pueden iniciar automáticamente en función de una programación o manualmente por el usuario. (Puede usar una directiva de copia de seguridad para programar copias de seguridad. Como alternativa, puede usar la opción **Realizar copia de seguridad** para realizar una copia de seguridad interactiva).

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Cómo restaurar un volumen de StorSimple de una copia de seguridad.
Puede usar la página **Catálogo de Backup** para restaurar el volumen StorSimple a partir de una copia de seguridad específica. Sin embargo, debe tener en cuenta que, cuando se restaura un volumen, el volumen volverá al estado en el que se encontraba cuando se realizó la copia de seguridad. Se perderán todos los datos que se agregaron después de la operación de copia de seguridad.

> [!WARNING]
> Cuando se realice una restauración a partir de una copia de seguridad, se reemplazarán los volúmenes existentes desde la copia de seguridad. Esto puede provocar la pérdida de los datos que se escribieron después de que se realizase la copia de seguridad.
> 
> 

### <a name="to-restore-your-volume"></a>Para restaurar el volumen
1. En la página del servicio de Administrador de StorSimple, haga clic en la pestaña **Catálogo de Backup**.
   
    ![Catálogo de Backup](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. Seleccione una copia de seguridad de la siguiente manera:
   
   1. Seleccione el dispositivo adecuado.
   2. En la lista desplegable, seleccione el volumen o la directiva de copia de seguridad para la copia de seguridad que desea seleccionar.
   3. Especifique el intervalo de tiempo.
   4. Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) para ejecutar esta consulta.
      
      Las copias de seguridad asociadas al volumen o la directiva de copia de seguridad seleccionados deben aparecer en la lista de conjuntos de copias de seguridad.
3. Expanda el conjunto de copias de seguridad para ver los volúmenes asociados. Estos volúmenes deben desconectarse en el host y en el dispositivo para que pueda restaurarlos. Acceda a los volúmenes de la página **Contenedores de volúmenes** y, a continuación, siga los pasos indicados en [Desconectar un volumen](storsimple-manage-volumes-u2.md#take-a-volume-offline) para desconectarlos.
   
   > [!IMPORTANT]
   > Asegúrese de desconectar primero los volúmenes del host y, después, desconectar los volúmenes del dispositivo. Si no establece los volúmenes sin conexión en el host, esto podría causar daños en los datos.
   > 
   > 
4. Vuelva a la pestaña **Catálogo de Backup** y seleccione un conjunto de copias de seguridad.
5. Haga clic en **Restaurar** en la parte inferior de la página.
6. Se le pedirá confirmación. Revise la información de restauración y después seleccione la casilla de confirmación.
   
    ![Página de confirmación](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Se inicia un trabajo de restauración. Puede verlo accediendo a la página **Trabajos**. 
8. Una vez completada la restauración, puede comprobar que los volúmenes de la copia de seguridad sustituyeron el contenido de los volúmenes.

![Vídeo disponible](./media/storsimple-restore-from-backup-set-u2/Video_icon.png)**Vídeo disponible**

Para ver un vídeo en que se muestra cómo usar las características de clonación y restauración en StorSimple para recuperar archivos eliminados, haga clic [aquí](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Si se produce algún error en la restauración
Recibirá una alerta si se produce un error en la operación de restauración por algún motivo. En su caso, actualice la lista de copias de seguridad para verificar que la copia de seguridad sigue siendo válida. Si la copia de seguridad es válida y se restaura desde la nube, el problema puede deberse a errores en la conectividad. 

Para completar correctamente la operación de restauración, desconecte el volumen en el host y vuelva a intentar la operación de restauración. Las modificaciones realizadas en los datos del volumen durante el proceso de restauración se perderán.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [Administrar volúmenes de StorSimple](storsimple-manage-volumes-u2.md).
* Obtenga información sobre cómo [usar el servicio del administrador de StorSimple para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

