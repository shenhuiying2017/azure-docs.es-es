---
title: "Clonación del volumen de la serie 8000 de StorSimple | Microsoft Docs"
description: "Describe los diferentes tipos de clon y cuándo usarlos, y explica cómo se puede usar un conjunto de copia de seguridad para clonar un volumen individual."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 070ac53e-7388-4c48-b8a5-8ed7f9108b2c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/26/2017
ms.author: alkohli
ms.openlocfilehash: 2b627250df62bbe3299869ef3812947afbb8f29f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>Usar el servicio StorSimple Manager para clonar un volumen (Update 2)
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Información general
En la página **Catálogo de copias de seguridad** del servicio StorSimple Manager se muestran todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automatizadas. Puede usar esta página para enumerar todas las copias de seguridad para un volumen o una directiva de copia de seguridad, seleccionar o eliminar las copias de seguridad, o usar una copia de seguridad para restaurar o clonar un volumen.

![Página del catálogo de copias de seguridad](./media/storsimple-clone-volume-u2/backupCatalog.png)  

Este tutorial describe cómo puede usar una copia de seguridad para clonar un volumen individual. También explica la diferencia entre clones *transitorios* y *permanentes*.

> [!NOTE]
> Un volumen anclado localmente se clonará como volumen en capas. Si necesita que el volumen clonado se ancle localmente, puede convertir el clon en un volumen anclado local cuando la operación de clonación se complete correctamente. Para información sobre cómo convertir un volumen en capas en un volumen anclado localmente, vaya a [Cambiar el tipo de volumen](storsimple-manage-volumes-u2.md#change-the-volume-type).
> 
> Si intenta convertir un volumen clonado a anclado localmente desde en capas inmediatamente después de la clonación (cuando todavía es un clon transitorio), la conversión generará el siguiente mensaje de error:
> 
> `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
> 
> Este error solo se recibe si está clonando en un dispositivo diferente. Puede convertir correctamente el volumen a anclado localmente si convierte primero el clon transitorio en un clon permanente. Para convertir el clon transitorio en un clon permanente, tome una instantánea de nube del mismo.
> 
> 

## <a name="create-a-clone-of-a-volume"></a>Crear un clon de un volumen
Puede crear un clon en el mismo dispositivo, en otro dispositivo o incluso en una máquina virtual mediante el uso de una instantánea local o en la nube.

#### <a name="to-clone-a-volume"></a>Para clonar un volumen
1. En la página del servicio de Administrador de StorSimple, haga clic en la pestaña **Catálogo de copia de seguridad** y seleccione un conjunto de copia de seguridad.
2. Expanda el conjunto de copias de seguridad para ver los volúmenes asociados. Haga clic y seleccione un volumen del conjunto de copia de seguridad.
   
     ![Clonar un volumen](./media/storsimple-clone-volume-u2/CloneVol.png) 
3. Haga clic en **Clonar** para empezar a clonar el volumen seleccionado.
4. En el Asistente para clonar volúmenes, en **Especificar el nombre y la ubicación**:
   
   1. Identifique un dispositivo de destino. Esta es la ubicación donde se creará el clon. Puede elegir el mismo dispositivo o especificar otro dispositivo. Si elige un volumen asociado a otros proveedores de servicios en la nube (no Azure), la lista desplegable del dispositivo de destino solo mostrará dispositivos físicos. No se puede clonar un volumen asociado a otros proveedores de servicios en la nube en un dispositivo virtual.
      
      > [!NOTE]
      > Asegúrese de que la capacidad necesaria para el clon es inferior a la capacidad disponible en el dispositivo de destino. 
      > 
      > 
   2. Especifique un nombre de volumen único para el clon. El nombre debe tener entre 3 y 127 caracteres. 
      
      > [!NOTE]
      > El campo **Clonar volumen como** tiene el valor **En capas** aunque esté clonando un volumen anclado localmente. No puede cambiar esta configuración. Sin embargo, si necesita que el volumen clonado se ancle localmente también, puede convertir el clon en un volumen anclado localmente una vez creado correctamente el clon. Para información sobre cómo convertir un volumen en capas en un volumen anclado localmente, vaya a [Cambiar el tipo de volumen](storsimple-manage-volumes-u2.md#change-the-volume-type).
      > 
      > 
      
        ![Asistente para la clonación 1](./media/storsimple-clone-volume-u2/clone1.png) 
   3. Haga clic en el icono de flecha  ![icono de flecha](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) para ir a la página siguiente.
5. En **Especificar los hosts que pueden usar este volumen**:
   
   1. Especifique un registro de control de acceso (ACR) para el clon. Puede agregar un ACR nuevo o elegir uno de la lista.
      
        ![Asistente para la clonación 2](./media/storsimple-clone-volume-u2/clone2.png) 
   2. Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)para completar la operación.
6. Se iniciará un trabajo de clonación y se le notificará cuando se haya creado correctamente el clon. Haga clic en **Ver trabajo** para supervisar el trabajo de clonación en la página **Trabajos**. Cuando finalice el trabajo de clonación, verá el mensaje siguiente:
   
    ![Mensaje de clon](./media/storsimple-clone-volume-u2/CloneMsg.png) 
7. Una vez completado el trabajo de clonación:
   
   1. Vaya a la página **Dispositivos** y seleccione la pestaña **Contenedores de volúmenes**. 
   2. Seleccione el contenedor de volúmenes que está asociado con el volumen de origen que clonó. En la lista de volúmenes, debería ver el clon recién creado.

> [!NOTE]
> La copia de seguridad predeterminada y la supervisión se deshabilitan automáticamente en un volumen clonado.
> 
> 

Los clones que se creen de esta forma son clones transitorios. Para obtener más información acerca de los tipos de clon, consulte [Clones transitorios frente a clones permanentes](#transient-vs-permanent-clones).

Este clon es ahora un volumen normal, y todas las operaciones que sean posibles en un volumen estarán disponibles para el clon. Deberá configurar el volumen para las copias de seguridad.

## <a name="transient-vs-permanent-clones"></a>Clones transitorios frente a clones permanentes
Los clones transitorios solo se crean al clonarse en un dispositivo diferente. Se puede clonar un volumen específico de un conjunto de copia de seguridad a un dispositivo diferente administrado por StorSimple Manager. El clon transitorio tendrá referencias a los datos del volumen original y usará esos datos para leer y escribir localmente en el dispositivo de destino. 

Después de tomar una instantánea en la nube de un clon transitorio, el clon resultante será un clon *permanente* . Durante este proceso, se crea una copia de los datos en la nube y el tiempo para copiar estos datos está determinado por el tamaño de los mismos y las latencias de Azure (es decir, una copia de Azure en Azure). Este proceso puede tardar días o semanas. El clon transitorio se convierte en independiente de esta forma y no tiene ninguna referencia a los datos del volumen original desde los que se clonaron. 

## <a name="scenarios-for-transient-and-permanent-clones"></a>Escenarios para clones transitorios y permanentes
Las secciones siguientes describen situaciones de ejemplo en las que pueden usarse clones transitorios y permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperación de nivel de elemento con un clon transitorio
Necesita recuperar un archivo de presentación de Microsoft PowerPoint de un año de antigüedad. El administrador de TI identifica la copia de seguridad específica de ese período de tiempo y, a continuación, filtra el volumen. El administrador clona el volumen, busca el archivo que necesita y se lo proporciona. En este escenario, se usa un clon transitorio. 

![Vídeo disponible](./media/storsimple-clone-volume-u2/Video_icon.png) **Vídeo disponible**

Para ver un vídeo en el que se muestra cómo puede usar el clon y restaurar las características de StorSimple para recuperar archivos eliminados, haga clic [aquí](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Pruebas en el entorno de producción con un clon permanente
Necesita comprobar un error de prueba en el entorno de producción. Puede crear un clon del volumen en el entorno de producción y, después, tomar una instantánea de nube de este clon para crear un volumen clonado independiente. En este escenario, se usa un clon permanente.  

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [restaurar un volumen de StorSimple de un conjunto de copia de seguridad](storsimple-restore-from-backup-set-u2.md).
* Obtenga información sobre cómo [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

