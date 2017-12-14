---
title: "Clonación de un volumen de la serie StorSimple 8000 | Microsoft Docs"
description: "Describe los diferentes tipos de clonación y su uso y se explica cómo puede usar un conjunto de copia de seguridad para clonar un volumen individual en un dispositivo de la serie StorSimple 8000."
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
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: d3965c74d51996b3e4144130fad01fae9e265897
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Uso del servicio StorSimple Device Manager de Azure Portal para clonar un volumen

## <a name="overview"></a>Información general

En este tutorial se describe cómo se puede usar un conjunto de copia de seguridad para clonar un volumen individual mediante la hoja **Catálogo de copias de seguridad**. También explica la diferencia entre clones *transitorios* y *permanentes*. Las instrucciones de este tutorial se aplican a todos los dispositivos de la serie StorSimple 8000 con Update 3 o posterior.

En la hoja **Catálogo de copias de seguridad** del servicio StorSimple Device Manager se muestran todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automatizadas. A continuación, puede seleccionar un volumen de un conjunto de copia de seguridad para clonar.

 ![Lista de conjuntos de copia de seguridad](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Consideraciones sobre la clonación de un volumen

Tenga en cuenta la siguiente información al clonar un volumen.

- Un clon se comporta de la misma manera que un volumen normal. Cualquier operación que sea posible en un volumen es posible en el clon.

- La copia de seguridad predeterminada y la supervisión se deshabilitan automáticamente en un volumen clonado. Debe configurar un volumen clonado para cualquier copia de seguridad.

- Un volumen anclado localmente se clona como un volumen en capas. Si necesita que el volumen clonado se ancle localmente, puede convertir el clon en un volumen anclado local cuando la operación de clonación se complete correctamente. Para información sobre cómo convertir un volumen en capas en un volumen anclado localmente, vaya a [Cambiar el tipo de volumen](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Si intenta convertir un volumen clonado en capas a anclado localmente inmediatamente después de la clonación (cuando todavía es un clon transitorio), la conversión generará el siguiente mensaje de error:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Este error solo se recibe si está clonando en un dispositivo diferente. Puede convertir correctamente el volumen a anclado localmente si convierte primero el clon transitorio en un clon permanente. Tome una instantánea de nube del clon transitorio para convertirlo a un clon permanente.

## <a name="create-a-clone-of-a-volume"></a>Crear un clon de un volumen

Puede crear un clon en el mismo dispositivo, en otro dispositivo o incluso en un dispositivo de nube mediante una instantánea local o de nube.

El siguiente procedimiento describe cómo crear un clon desde el catálogo de copias de seguridad.  Un método alternativo para iniciar la clonación es ir a **Volúmenes**, seleccionar un volumen y luego hacer clic con el botón derecho para invocar el menú contextual y seleccionar **Clonar**.

Realice los pasos siguientes para crear un clon del volumen desde el catálogo de copias de seguridad.

#### <a name="to-clone-a-volume"></a>Para clonar un volumen

1. Vaya al servicio StorSimple Device Manager y, después, haga clic en **Catálogo de copias de seguridad**.

2. Seleccione una copia de seguridad de la siguiente manera:
   
   1. Seleccione el dispositivo adecuado.
   2. En la lista desplegable, seleccione el volumen o la directiva de copia de seguridad para la copia de seguridad que desea seleccionar.
   3. Especifique el intervalo de tiempo.
   4. Haga clic en **Aplicar** para ejecutar esta consulta.

    Las copias de seguridad asociadas al volumen o la directiva de copia de seguridad seleccionados deben aparecer en la lista de conjuntos de copias de seguridad.
   
    ![Lista de conjuntos de copia de seguridad](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expanda el conjunto de copia de seguridad para ver el volumen asociado y seleccione un volumen en un conjunto de copia de seguridad. Haga clic con el botón derecho y, en el menú contextual, seleccione **Clonar**.

   ![Lista de conjuntos de copia de seguridad](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. En la hoja **Clonar**, realice los pasos siguientes:
   
    1. Identifique un dispositivo de destino. Esta es la ubicación donde se creará el clon. Puede elegir el mismo dispositivo o especificar otro dispositivo.

      > [!NOTE]
      > Asegúrese de que la capacidad necesaria para el clon es inferior a la capacidad disponible en el dispositivo de destino. 
       
    2. Especifique un nombre de volumen único para el clon. El nombre debe tener entre 3 y 127 caracteres.
      
        > [!NOTE]
        > El campo **Clonar volumen como** tiene el valor **En capas** aunque esté clonando un volumen anclado localmente. No puede cambiar esta configuración. Sin embargo, si necesita que el volumen clonado se ancle localmente también, puede convertir el clon en un volumen anclado localmente una vez creado correctamente el clon. Para información sobre cómo convertir un volumen en capas en un volumen anclado localmente, vaya a [Cambiar el tipo de volumen](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
    3. En **Hosts conectados**, especifique un registro de control de acceso (ACR) para la clonación. Puede agregar un ACR nuevo o elegir uno de la lista. El ACR determina qué hosts pueden acceder a este clon.
      
        ![Lista de conjuntos de copia de seguridad](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

    4. Haga clic en **Clonar** para completar la operación.

4. Se inicia un trabajo de clonación. Cuando se haya creado correctamente el clon, recibirá una notificación. Haga clic en la notificación de trabajo o vaya a la hoja **Trabajos** para supervisar el trabajo de clonación.

    ![Lista de conjuntos de copia de seguridad](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Una vez completado el trabajo de clonación, vaya al dispositivo y luego haga clic en **Volúmenes**. En la lista de volúmenes, debería ver la clonación que acaba de crear en el mismo contenedor de volúmenes que tiene el volumen de origen.

    ![Lista de conjuntos de copia de seguridad](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Los clones que se creen de esta forma son clones transitorios. Para obtener más información acerca de los tipos de clon, consulte [Clones transitorios frente a clones permanentes](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Clones transitorios frente a clones permanentes
Los clones transitorios se crean solo cuando la clonación se realiza en otro dispositivo. Se puede clonar un volumen específico de un conjunto de copia de seguridad a un dispositivo diferente administrado por StorSimple Device Manager. El clon transitorio tiene referencias a los datos del volumen original y usa esos datos para leer y escribir localmente en el dispositivo de destino.

Después de tomar una instantánea de nube de un clon transitorio, el clon resultante será un clon *permanente*. Durante este proceso, se crea una copia de los datos en la nube y el tiempo para copiar estos datos está determinado por el tamaño de los mismos y las latencias de Azure (es decir, una copia de Azure en Azure). Este proceso puede tardar días o semanas. El clon transitorio se convierte en permanente de esta forma y no tiene ninguna referencia a los datos del volumen original desde los que se clonaron.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Escenarios para clones transitorios y permanentes
Las secciones siguientes describen situaciones de ejemplo en las que pueden usarse clones transitorios y permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperación de nivel de elemento con un clon transitorio
Necesita recuperar un archivo de presentación de Microsoft PowerPoint de un año de antigüedad. El administrador de TI identifica la copia de seguridad específica de ese período de tiempo y, a continuación, filtra el volumen. El administrador clona el volumen, busca el archivo que necesita y se lo proporciona. En este escenario, se usa un clon transitorio.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Pruebas en el entorno de producción con un clon permanente
Necesita comprobar un error de prueba en el entorno de producción. Puede crear un clon del volumen en el entorno de producción y, después, tomar una instantánea de nube de este clon para crear un volumen clonado independiente. En este escenario, se usa un clon permanente.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [restaurar un volumen de StorSimple de un conjunto de copia de seguridad](storsimple-8000-restore-from-backup-set-u2.md).
* Aprenda a [usar el servicio StorSimple Device Manager para administrar el dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

