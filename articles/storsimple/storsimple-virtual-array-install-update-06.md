---
title: "Instalación de Update 0.6 en StorSimple Virtual Array | Microsoft Docs"
description: "Describe cómo usar la IU web de StorSimple Virtual Array para aplicar actualizaciones mediante Azure Portal y el método de revisiones"
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Instalación de Update 0.6 en StorSimple Virtual Array

## <a name="overview"></a>Información general

En este artículo se describen los pasos requeridos para instalar Update 0.6 en StorSimple Virtual Array a través tanto de la interfaz de usuario web local como de Azure Portal. Se aplican las actualizaciones o revisiones de software para mantener actualizada la matriz virtual de StorSimple.

Antes de aplicar una actualización, se recomienda que desconecte primero los volúmenes o recursos compartidos en el host y, luego, el dispositivo. Esto minimizará la posibilidad de daños en los datos. Cuando los volúmenes o recursos compartidos están sin conexión, también debe realizar una copia de seguridad manual del dispositivo.

> [!IMPORTANT]
> - Update 0.6 se corresponde a la versión de software **10.0.10293.0** del dispositivo. Para obtener más información sobre cuáles son las novedades de esta actualización, vaya a las [Notas de la versión de Update 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Si ejecuta Update 0.2, o cualquier versión posterior, se recomienda instalar las actualizaciones a través de Azure Portal. Si está ejecutando Update 0.1 o versiones de software de GA, debe utilizar el método de revisión mediante la interfaz de usuario web local para instalar Update 0.6.
>
> - Tenga en cuenta que al instalar una actualización o revisión, se reiniciará el dispositivo. Dado que la matriz virtual de StorSimple es un dispositivo de nodo único, se interrumpirá cualquier operación de E/S que esté en curso y el dispositivo permanecerá un rato inactivo.

## <a name="use-the-azure-portal"></a>Uso del Portal de Azure

Si se ejecuta Update 0.2, o cualquier versión posterior, se recomienda instalar las actualizaciones mediante Azure Portal. El procedimiento del Portal requiere que el usuario examine, descargue e instale las actualizaciones. Este procedimiento tarda aproximadamente 7 minutos en completarse. Realice los pasos siguientes para instalar la actualización o revisión.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Una vez que la instalación esté completa, vaya a su servicio StorSimple Device Manager. Seleccione **Dispositivos** y, a continuación, seleccione y haga clic en el dispositivo que acaba de actualizar. Vaya a **Configuración > Administrar > Actualizaciones del dispositivo**. La versión de software que aparece debe ser **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Uso de la interfaz de usuario web local

Se pueden seguir dos pasos con la interfaz de usuario web local:

* Descargar la actualización o la revisión
* Instalar la actualización o la revisión

### <a name="download-the-update-or-the-hotfix"></a>Descargar la actualización o la revisión

Realice los pasos siguientes para descargar la actualización de software desde el catálogo de Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para descargar la actualización o la revisión

1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si esta es la primera vez que utiliza el Catálogo de Microsoft Update en este equipo, haga clic en **Instalar** cuando se le solicite que instale el complemento del Catálogo de Microsoft Update.

3. En el cuadro de búsqueda del Catálogo de Microsoft Update, escriba el número de Knowledge Base correspondiente a la revisión que quiera descargar. Escriba **4023268** para Update 0.6 y, luego, haga clic en **Buscar**.
   
    Aparece la lista de revisiones, por ejemplo, **StorSimple Virtual Array Update 0.6**.
   
    ![Búsqueda de catálogo](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Haga clic en **Descargar**.

5. Debería ver cinco archivos para descargar. Descargue cada uno de esos archivos en una carpeta. La carpeta también se puede copiar en un recurso compartido de red que sea accesible desde el dispositivo.

6. Abra la carpeta donde se encuentran los archivos.
    ![Archivos del paquete](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Se ve lo siguiente:
    -  Un archivo de paquete independiente de Microsoft Update `WindowsTH-KB3011067-x64`. Este archivo se utiliza para actualizar el software del dispositivo.
    - Un archivo de paquete del agente de supervisión de Geneva `GenevaMonitoringAgentPackageInstaller`. Este archivo se utiliza para actualizar el agente del servicio de supervisión y diagnóstico (MDS). Haga doble clic en el archivo cab. Aparece un archivo _.msi_. Seleccione el archivo, haga clic con el botón derecho sobre él y, a continuación, **extraiga** el archivo. Se usa el archivo _.msi_ para actualizar el agente.

        ![Extracción del archivo de actualización del agente de MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > No es necesario actualizar al agente MDS si está ejecutando StorSimple Update 0.5 (0.0.10293.0).

    - Tres archivos que contienen actualizaciones críticas de seguridad de Windows: `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64` y `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Instalar la actualización o la revisión

Antes de instalar la actualización o la revisión, asegúrese de que tiene la actualización o la revisión descargada de forma local en el host o que puede acceder a ella a través de un recurso compartido de red.

Utilice este método para instalar actualizaciones en un dispositivo que ejecute las versiones de software Update 0.1 o GA. Este procedimiento tarda aproximadamente doce minutos en completarse. Realice los pasos siguientes para instalar la actualización o revisión.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalar la actualización o la revisión

1. En la interfaz de usuario web local, vaya a **Mantenimiento** > **Actualización de software**. Tome nota de la versión de software que se está ejecutando. Si está ejecutando **10.0.10290.0**, no es necesario actualizar el agente MDS en el paso 6.
   
    ![actualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. En **Update file path**(Ruta de acceso del archivo de actualización), escriba el nombre del archivo de actualización o de revisión. Asimismo, también puede acceder al archivo de instalación de la actualización o de la revisión si está en un recurso compartido de red. Haga clic en **Aplicar**.
   
    ![actualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Se mostrará una advertencia. Dado que la matriz virtual es un dispositivo de nodo único, una vez aplicada la actualización, se reiniciará el dispositivo y habrá un tiempo de inactividad. Haga clic en el icono de marca de verificación.
   
   ![actualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Se inicia la actualización. Una vez que el dispositivo se actualice correctamente, este se reiniciará. La interfaz de usuario local no será accesible durante este tiempo.
   
    ![actualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Una vez completado el reinicio, se le llevará a la página de **inicio de sesión** . Para comprobar que el software del dispositivo se ha actualizado, en la interfaz de usuario de web local, vaya a **Mantenimiento** > **Actualización de software**. La versión de software que aparece debe ser **10.0.0.0.0.10293** para Update 0.6.
   
   > [!NOTE]
   > Las versiones de software se muestran de forma ligeramente distinta en la interfaz de usuario web local y Azure Portal. Por ejemplo, la misma versión aparece como **10.0.0.0.0.10293** en la interfaz de usuario web local y como **10.0.10293.0** en Azure Portal.
   
    ![actualizar dispositivo](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Omita este paso si se estaba ejecutando StorSimple Virtual Array Update 0.5 (**10.0.10290.0**) antes de aplicar esta actualización. Tomó nota de la versión de software en el paso 1 antes de iniciar la actualización. Si estaba ejecutando Update 0.5, el agente MDS ya está actualizado.

    Si está ejecutando una versión de software anterior a Update 0.5, lo siguiente que debe hacer es actualizar el agente MDS. En la página **Actualización de software**, vaya a **Actualizar ruta de acceso al archivo** y vaya al archivo `GenevaMonitoringAgentPackageInstaller.msi`. Repita los pasos del 2 al 4. Después de que se reinicie la matriz virtual, inicie sesión en la interfaz de usuario web local.

7. Repita los pasos del 2 al 4 para instalar las correcciones de seguridad de Windows con los archivos `windows8.1-kb4012213-x64`, `windows8.1-kb3205400-x64` y `windows8.1-kb4019213-x64`. La matriz virtual se reinicia después de cada instalación y debe iniciar sesión en la interfaz de usuario web local.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [administración de la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

