---
title: "Configuración de una matriz virtual de StorSimple como servidor de archivos | Microsoft Docs"
description: "En este tercer tutorial sobre la implementación de matrices virtuales StorSimple se enseña a configurar un dispositivo virtual como servidor de archivos."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: adc1c942-dd4e-4589-bc10-18ae3f7cbcdc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 37d25c8c7b47bf8ef3b4d5c6d3978b13c52329c3
ms.lasthandoff: 04/13/2017


---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Implementar una matriz virtual de StorSimple: Configurar un servidor de archivos
![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introducción
Este artículo se aplica a la matiz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o dispositivo virtual de StorSimple) que se ejecuta en la versión de disponibilidad general de marzo de 2016. En este artículo se describe cómo realizar la instalación inicial, registrar el servidor de archivos de StorSimple, completar la instalación del dispositivo y crear y conectarse a recursos compartidos de SMB. Este es el último artículo de la serie de tutoriales de implementación necesarios para implementar completamente la matriz virtual como servidor de archivos o servidor iSCSI.

El proceso de instalación y configuración puede tardar unos 10 minutos en completarse.

## <a name="setup-prerequisites"></a>Requisitos previos de instalación
Antes de instalar y configurar el dispositivo virtual StorSimple, asegúrese de que:

* Ha aprovisionado un dispositivo virtual y se ha conectado a él según se detalla en [Implementación de una matriz virtual de StorSimple: aprovisionamiento de una matriz virtual en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o en [Implementar una matriz virtual de StorSimple: Aprovisionar una matriz virtual en VMware](storsimple-ova-deploy2-provision-vmware.md).
* Tiene la clave de registro del servicio de StorSimple Manager que creó para administrar dispositivos virtuales StorSimple. Para más información, consulte [Paso 2: Obtener la clave de registro del servicio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para la matriz virtual de StorSimple.
* Si este es el segundo dispositivo virtual o subsiguiente que registra en un servicio de StorSimple Manager existente, debe tener la clave de cifrado de datos del servicio. Esta clave se generó cuando el primer dispositivo se registró correctamente en este servicio. Si perdió esta clave, consulte la sección [Obtener la clave de cifrado de datos del servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) para la matriz virtual de StorSimple.

## <a name="step-by-step-setup"></a>Configuración paso a paso
Use las siguientes instrucciones paso a paso para instalar y configurar el dispositivo virtual StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Paso 1: Completar la configuración de la interfaz de usuario web local y registrar el dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Para completar la instalación y registrar el dispositivo
1. Abra una ventana del explorador y conéctese a la interfaz de usuario de web local. Escriba:    
   
   `https://<ip-address of network interface>`
   
   Use la dirección URL de conexión que anotó en el paso anterior. Verá un error que indica que hay un problema con el certificado de seguridad del sitio web. Haga clic en **Continue to this webpage**(Continuar a esta página web).
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)
2. Inicie sesión en la interfaz de usuario web del dispositivo virtual como **StorSimpleAdmin**. Escriba la contraseña del administrador de dispositivos que cambió en el paso 3: Iniciar el dispositivo virtual en [Implementación de una matriz virtual de StorSimple: aprovisionamiento de una matriz virtual en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o en [Implementar una matriz virtual de StorSimple: Aprovisionar una matriz virtual en VMware](storsimple-ova-deploy2-provision-vmware.md). El usuario predeterminado es *StorSimpleAdmin* y la contraseña predeterminada es *Password1*.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)
3. Lo llevará a la página de **inicio** . En esta página se describen los distintos parámetros necesarios para configurar y registrar el dispositivo virtual en el servicio StorSimple Manager. Tenga en cuenta que las opciones **Configuración de red**, **Configuración de proxy web** y **Configuración horaria** son opcionales. Los únicos parámetros obligatorios son **Configuración del dispositivo** y **Configuración de la nube**.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)
4. En la página **Configuración de red** en **Interfaces de red**, DATA 0 se configurará automáticamente. Cada interfaz de red se establece de forma predeterminada para obtener la dirección IP automáticamente (DHCP). Por lo tanto, una dirección IP, una subred y una puerta de enlace se asignarán automáticamente (tanto para IPv4 como para IPv6).
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)
   
   Si agrega más de una interfaz de red durante el aprovisionamiento del dispositivo, se pueden configurar aquí. Tenga en cuenta que puede configurar la interfaz de red como IPv4 únicamente o como IPv4 e IPv6. No se admiten las configuraciones de solo IPv6.
5. Los servidores DNS son necesarios porque se utilizan cuando el dispositivo intenta comunicarse con sus proveedores de servicios de almacenamiento en la nube o para resolver el dispositivo por nombre cuando se configura como servidor de archivos. En la página **Configuración de red** en **Servidores DNS**:
   
   1. Se configurarán automáticamente un servidor DNS principal y secundario. Si decide configurar direcciones IP estáticas, puede especificar servidores DNS. Para lograr la alta disponibilidad, se recomienda que configure un servidor DNS principal y uno secundario.
   2. Haga clic en **Apply**. Esto aplicará y validará la configuración de red.
6. En la página **Device settings** (Configuración de dispositivo):
   
   1. Asigne un **nombre** exclusivo al dispositivo. Este nombre puede tener de 1 a 15 caracteres y puede contener letras, números y guiones.
   2. Haga clic en el icono **Servidor de archivos** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) del **tipo** de dispositivo que crea. Un servidor de archivos le permitirá crear carpetas compartidas.
   3. Como el dispositivo es un servidor de archivos, debe unir el dispositivo a un dominio. Escriba un valor en **nombre de dominio**.
   4. Haga clic en **Apply**.
7. Aparece un cuadro de diálogo. Escriba las credenciales del dominio en el formato especificado. Haga clic en el icono de marca de verificación. Se comprobarán las credenciales del dominio. Verá un mensaje de error si las credenciales son incorrectas.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)
8. Haga clic en **Apply**. Esto aplicará y validará la configuración de dispositivo.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Asegúrese de que su matriz virtual está en su propia unidad organizativa (UO) de Active Directory y de que no se le aplica ni hereda ningún objeto de directiva de grupo (GPO). La directiva de grupo puede instalar aplicaciones, como software antivirus, en la matriz virtual de StorSimple. La instalación de software adicional no se admite y puede provocar daños en los datos. 
   > 
   > 
9. Configure el servidor proxy web (de manera opcional). Aunque la configuración del proxy web es opcional, tenga en cuenta que, si usa un proxy web, solo puede configurarlo aquí.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)
   
   En la página **Proxy web** :
   
   1. Proporcione la **URL de proxy web** en este formato:  *http://&lt;dirección o nombre de dominio completo&gt;:Número de puerto*. Tenga en cuenta que no se admiten direcciones URL HTTPS.
   2. Especifique **Autenticación** como **Básica** o **Ninguna**.
   3. Si utiliza autenticación, también debe proporcionar un **Nombre de usuario** y una **Contraseña**.
   4. Haga clic en **Apply**. Esto validará y aplicará los parámetros de proxy web configurados.
10. (Opcionalmente) configure las opciones de hora para el dispositivo, como la zona horaria y los servidores NTP principal y secundario. Se requieren servidores NTP, ya que el dispositivo debe sincronizar la hora para que pueda autenticarse con los proveedores de servicios en la nube.
    
    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)
    
    En la página **Time settings** (Configuración de hora):
    
    1. En la lista desplegable, seleccione la **zona horaria** según la ubicación geográfica en la que se va a implementar el dispositivo. La zona horaria predeterminada para el dispositivo es la hora del Pacífico (PST). El dispositivo usará esta zona horaria para todas las operaciones programadas.
    2. Especifique un **servidor NTP principal** para el dispositivo o acepte el valor predeterminado de time.windows.com. Asegúrese de que su red permite que el tráfico NTP pase del centro de datos a Internet.
    3. Opcionalmente, especifique un **servidor NTP secundario** para el dispositivo.
    4. Haga clic en **Apply**. Esto validará y aplicará los parámetros de hora configurados.
11. Configure las opciones de nube para el dispositivo. En este paso, completa la configuración del dispositivo local y, a continuación, registra el dispositivo en el servicio StorSimple Manager.
    
    1. Escriba la **clave de registro del servicio** que obtuvo en el [Paso 2: Obtener la clave de registro del servicio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para la matriz virtual de StorSimple.
    2. Omita este paso si se trata del primer dispositivo que va a registrar con este servicio y vaya al paso siguiente. Si no es el primer dispositivo que va a registrar en este servicio, debe proporcionar la **clave de cifrado de datos del servicio**. Esta clave se necesita junto con la clave de registro del servicio para registrar dispositivos adicionales en el servicio StorSimple Manager. Para más información, consulte [Obtener la clave de cifrado de los datos del servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) en la interfaz de usuario web local.
    3. Haga clic en **Registrar**. Se reiniciará el dispositivo. Debe esperar de 2 a 3 minutos antes de que el dispositivo se registre correctamente. Una vez que se haya reiniciado el dispositivo, irá a la página de inicio de sesión.
       
       ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
12. Vuelva al Portal de Azure clásico. En la página **Dispositivos** , compruebe que el dispositivo se conectó correctamente al servicio consultando el estado. El estado del dispositivo debe ser **Activo**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Paso 2: Completar la instalación de dispositivos necesarios
Para completar a la configuración del dispositivo StorSimple, debe hacer lo siguiente:

* Seleccione una cuenta de almacenamiento para asociarla a este dispositivo.
* Elija la configuración de cifrado para los datos que se envían a la nube.

Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/) para completar la configuración mínima del dispositivo.

#### <a name="to-complete-the-minimum-device-setup"></a>Para completar la configuración mínima del dispositivo
1. En la página **Dispositivos** , seleccione el dispositivo que acaba de crear. Este dispositivo se debe mostrar como **Activo**. Haga clic en la flecha situada junto al nombre del dispositivo y luego haga clic en **Inicio rápido**.
2. Haga clic en **completar configuración del dispositivo** para iniciar el Asistente para configurar dispositivos.
3. En la página **Configuración básica** del Asistente para configurar dispositivos, haga lo siguiente:
   
   1. Especifique una cuenta de almacenamiento para usarla con el dispositivo. Puede seleccionar una cuenta de almacenamiento existente en esta suscripción en la lista desplegable o especificar **Agregar más** para elegir una cuenta de una suscripción diferente.
   2. Defina la configuración de cifrado para todos los datos en reposo (cifrado de AES) que se enviarán a la nube. Para cifrar los datos, seleccione el cuadro combinado para **habilitar la clave de cifrado de almacenamiento en la nube**. Escriba un cifrado de almacenamiento en la nube que contenga 32 caracteres. Vuelva a escribir la clave para confirmarla. Se usará una clave AES de 256 bits con la clave definida por el usuario para el cifrado.
   3. Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).
      
      ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

Ahora se actualizará la configuración. Después de actualizar la configuración correctamente, el botón para completar la configuración del dispositivo aparecerá atenuado. Volverá a la página **Inicio rápido** del dispositivo.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

> [!NOTE]
> Puede modificar la configuración restante del dispositivo en cualquier momento accediendo a la página **Configurar** .
> 
> 

## <a name="step-3-add-a-share"></a>Paso 3: Agregar un recurso compartido
Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/) para crear un recurso compartido.

#### <a name="to-create-a-share"></a>Para crear un recurso compartido
1. En la página **Inicio rápido** del dispositivo, haga clic en **Agregar un recurso compartido**. Se iniciará el Asistente para agregar un recurso compartido.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)
2. En la página **Configuración básica** haga lo siguiente:
   
   1. Especifique un nombre exclusivo para el recurso compartido. El nombre debe ser una cadena que contenga entre 3 y 127 caracteres.
   2. (Opcional) Proporcione una descripción para el recurso compartido. La descripción ayudará a identificar a los propietarios del recurso compartido.
   3. Seleccione un tipo de uso para el recurso compartido. El tipo de uso puede ser **En capas** o **Anclado localmente**. El tipo en capas es el valor predeterminado. Para las cargas de trabajo que requieren garantías locales, latencias bajas y un rendimiento más alto, seleccione un recurso compartido **Anclado localmente** . Para todos los demás datos, seleccione un recurso compartido **En capas** .
   
   Un recurso compartido anclado localmente se aprovisiona de forma intensa y garantiza que los datos principales del recurso compartido continúen siendo locales en el dispositivo y que no se traspasan a la nube. Por otro lado, un recurso compartido en capas tiene aprovisionamiento reducido. Cuando se crea un recurso compartido en capas, el 10 % del espacio se aprovisiona en la capa local y el 90 % del espacio se aprovisiona en la nube. Por ejemplo, si se aprovisiona un volumen de 1 TB, 100 GB residirían en el espacio local y 900 GB se utilizarían en la nube cuando se apilen los datos. A su vez, esto hace que, si agota todo el espacio local en el dispositivo, no se puede aprovisionar un recurso compartido en capas.
3. Especifique la capacidad aprovisionada para el recurso compartido. Tenga en cuenta que la capacidad especificada debe ser menor que la capacidad disponible. Si usa un recurso compartido en capas, el tamaño del recurso compartido debe estar entre 500 GB y 20 TB. Para un recurso compartido anclado localmente, especifique un tamaño de recurso compartido de 50 GB a 2 TB. Use la capacidad disponible como guía para aprovisionar un recurso compartido. Si la capacidad local disponible es de 0 GB, no podrá aprovisionar recursos compartidos locales o en capas.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)
4. Haga clic en el icono de flecha ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) para ir a la página siguiente.
5. En la página **Configuración adicional** , asigne los permisos al usuario o al grupo que tendrá acceso a este recurso compartido. Especifique el nombre del usuario o del grupo de usuarios en formato *<mailto:john@contoso.com>*. Se recomienda que utilice un grupo de usuarios (en lugar de un único usuario) para otorgar los privilegios de administrador para tener acceso a estos recursos compartidos. Después de haber asignado los permisos aquí, puede utilizar el Explorador de Windows para modificar estos permisos.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)
6. Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Se creará un recurso compartido con la configuración especificada. De forma predeterminada, se habilitarán la supervisión y la copia de seguridad para el recurso compartido.

## <a name="step-4-connect-to-the-share"></a>Paso 4: Conectarse al recurso compartido
Ahora, necesitará conectarse a los recursos compartidos que creó en el paso anterior. Siga estos pasos en el host de Windows Server.

#### <a name="to-connect-to-the-share"></a>Para conectarse al recurso compartido
1. Presione ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + R. En la ventana Ejecutar, especifique el *\\<file server name>* como la ruta de acceso y reemplace *nombre del servidor de archivos* por el nombre del dispositivo que asignó a su servidor de archivos. Haga clic en **OK**.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)
2. Se abrirá el Explorador. Ahora podrá ver los recursos compartidos que creó como carpetas. Seleccione y haga doble clic en un recurso compartido (carpeta) para ver el contenido.
   
   ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)
3. Ahora puede agregar archivos a estos recursos compartidos y realizar una copia de seguridad.

![icono de vídeo](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **Vídeo disponible**

Mire el vídeo para ver cómo puede configurar y registrar una matriz virtual de StorSimple como servidor de archivos.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Configure-a-StorSimple-Virtual-Array/player]
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Aprenda a usar la interfaz de usuario web local para [administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).


