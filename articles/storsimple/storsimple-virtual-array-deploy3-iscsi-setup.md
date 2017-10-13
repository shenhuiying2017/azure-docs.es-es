---
title: "Configuración del servidor iSCSI de Microsoft Azure StorSimple Virtual Array | Microsoft Docs"
description: "Describe cómo realizar la configuración inicial, registrar el servidor iSCSI de StorSimple y completar la configuración del dispositivo."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 076df176d7cd40c009aea27004fe0f4415999c80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Implementación de una matriz virtual de StorSimple: configurar como un servidor iSCSI mediante Azure Portal

![flujo del proceso de configuración de iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Información general

Este tutorial de implementación se aplica a Microsoft Azure StorSimple Virtual Array. En este tutorial se describe cómo realizar la configuración inicial, registrar un servidor iSCSI de StorSimple, completar la configuración del dispositivo y, después, crear, montar, inicializar y formatear volúmenes en StorSimple Virtual Array configurados como servidor iSCSI. 

Los procedimientos descritos aquí demoran aproximadamente de 30 minutos a 1 hora en completarse. La información publicada en este artículo solo se aplica a las matrices virtual de StorSimple.

## <a name="setup-prerequisites"></a>Requisitos previos de instalación

Antes de instalar y configurar StorSimple Virtual Array, asegúrese de que:

* Ha aprovisionado una matriz virtual y se ha conectado a ella como se describe en [Implementación de una matriz virtual de StorSimple: aprovisionar una matriz virtual en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o en [Implementación de una matriz virtual de StorSimple: aprovisionar una matriz virtual en VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Tiene la clave de registro del servicio StorSimple Device Manager que creó para administrar instancias de StorSimple Virtual Array. Para más información, consulte **Paso 2: Obtener la clave de registro del servicio** en [Implementar una matriz virtual de StorSimple: Preparar el portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Si este es la segunda o subsiguiente matriz virtual que registra en un servicio StorSimple Device Manager existente, debe tener la clave de cifrado de datos del servicio. Esta clave se generó cuando el primer dispositivo se registró correctamente en este servicio. Si perdió esta clave, consulte la sección **Obtener la clave de cifrado de datos del servicio** en [Utilizar la interfaz de usuario web para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Configuración paso a paso

Use las siguientes instrucciones detalladas para instalar y configurar StorSimple Virtual Array:

* [Paso 1: Completar la configuración de la interfaz de usuario web local y registrar el dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [Paso 2: Completar la instalación de dispositivos necesarios](#step-2-complete-the-required-device-setup)
* [Paso 3: Agregar un volumen](#step-3-add-a-volume)
* [Paso 4: Montar, inicializar y formatear un volumen](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Paso 1: Completar la configuración de la interfaz de usuario web local y registrar el dispositivo

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para completar la instalación y registrar el dispositivo

1. Abra una ventana del explorador. Para conectarse a la interfaz de usuario web, escriba:
   
    `https://<ip-address of network interface>`
   
    Use la dirección URL de conexión que anotó en el paso anterior. Verá un error que le indica que hay un problema con el certificado de seguridad del sitio web. Haga clic en **Continue to this web page**(Continuar a esta página web).
   
    ![error de certificado de seguridad](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Inicie sesión en la interfaz de usuario web del dispositivo virtual como **StorSimpleAdmin**. Escriba la contraseña del administrador de dispositivos que cambió en Paso 3: Iniciar el dispositivo virtual en [Implementación de una matriz virtual de StorSimple: aprovisionamiento de una matriz virtual en Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) o en [Implementar una matriz virtual de StorSimple: Aprovisionar una matriz virtual en VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Página de inicio de sesión](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Lo llevará a la página de **inicio** . En esta página se describen los distintos parámetros requeridos para configurar y registrar el dispositivo virtual en el servicio StorSimple Device Manager. Tenga en cuenta que las opciones **Configuración de red**, **Configuración de proxy web** y **Configuración horaria** son opcionales. Los únicos parámetros obligatorios son **Configuración del dispositivo** y **Configuración de la nube**.
   
    ![Página de inicio](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. En la página **Configuración de red** de **Interfaces de red**, se configurará automáticamente DATA 0. Cada interfaz de red se establece de forma predeterminada para obtener la dirección IP automáticamente (DHCP). Por lo tanto, una dirección IP, una subred y una puerta de enlace se asignarán automáticamente (tanto para IPv4 como para IPv6).
   
    Mientras planea implementar el dispositivo como servidor iSCSI (para aprovisionar el almacenamiento en bloque), se recomienda que deshabilite la opción **Get IP address automatically** (Obtener una dirección IP automáticamente) y configure direcciones IP estáticas.
   
    ![Página de configuración de red](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Si agrega más de una interfaz de red durante el aprovisionamiento del dispositivo, se pueden configurar aquí. Tenga en cuenta que puede configurar la interfaz de red como IPv4 únicamente o como IPv4 e IPv6. No se admiten las configuraciones de solo IPv6.
5. Los servidores DNS son necesarios porque se utilizan cuando el dispositivo intenta comunicarse con sus proveedores de servicios de almacenamiento en la nube o para resolver el dispositivo por nombre si se configura como servidor de archivos. En la página **Configuración de red** de **Servidores DNS**:
   
   1. Se configurarán automáticamente un servidor DNS principal y secundario. Si decide configurar direcciones IP estáticas, puede especificar servidores DNS. Para lograr la alta disponibilidad, se recomienda que configure un servidor DNS principal y uno secundario.
   2. Haga clic en **Apply**. Esto aplicará y validará la configuración de red.
6. En la página **Device settings** (Configuración de dispositivo):
   
   1. Asigne un **nombre** exclusivo al dispositivo. Este nombre puede tener de 1 a 15 caracteres y puede contener letras, números y guiones.
   2. Haga clic en el icono de **servidor iSCSI** ![icono de servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) del **tipo** de dispositivo que va a crear. Un servidor iSCSI le permitirá aprovisionar el almacenamiento en bloque.
   3. Especifique si desea que este dispositivo esté unido al dominio. Si el dispositivo es un servidor iSCSI, la unión al dominio es opcional. Si decide no unir el servidor iSCSI a un dominio, haga clic en **Apply**(Aplicar), espere a que la configuración se aplique y, a continuación, vaya al paso siguiente.
      
       Si desea unir el dispositivo a un dominio. Escriba un **nombre de dominio** y, después, haga clic en **Aplicar**.
      
      > [!NOTE]
      > Si une el servidor iSCSI a un dominio, asegúrese de que su matriz virtual esté en su propia unidad organizativa (UO) de Microsoft Azure Active Directory y de que no se le aplica ningún objeto de directiva de grupo (GPO).
      > 
      > 
   4. Aparece un cuadro de diálogo. Escriba las credenciales del dominio en el formato especificado. Haga clic en el icono de marca de verificación  ![icono de marca de verificación](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Se comprobarán las credenciales del dominio. Verá un mensaje de error si las credenciales son incorrectas.
      
       ![credentials](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Haga clic en **Apply**. Esto aplicará y validará la configuración de dispositivo.
7. Configure el servidor proxy web (de manera opcional). Aunque la configuración del proxy web es opcional, tenga en cuenta que, si usa un proxy web, solo puede configurarlo aquí.
   
    ![configurar el proxy web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    En la página **Proxy web** :
   
   1. Escriba la **URL de proxy web** en este formato: *http://dirección ip-host* o *FDQN:Número de puerto*. Tenga en cuenta que no se admiten direcciones URL HTTPS.
   2. Especifique **Autenticación** como **Básica** o **Ninguna**.
   3. Si utiliza autenticación, también debe escribir un **Nombre de usuario** y una **Contraseña**.
   4. Haga clic en **Apply**. Esto validará y aplicará los parámetros de proxy web configurados.
8. (Opcionalmente) configure las opciones de hora para el dispositivo, como la zona horaria y los servidores NTP principal y secundario. Se requieren servidores NTP, ya que el dispositivo debe sincronizar la hora para que pueda autenticarse con los proveedores de servicios en la nube.
   
    ![Time settings](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    En la página **Configuración horaria** :
   
   1. En la lista desplegable, seleccione la **zona horaria** según la ubicación geográfica en la que se va a implementar el dispositivo. La zona horaria predeterminada para el dispositivo es la hora del Pacífico (PST). El dispositivo usará esta zona horaria para todas las operaciones programadas.
   2. Especifique un **servidor NTP principal** para el dispositivo o acepte el valor predeterminado de time.windows.com. Asegúrese de que su red permite que el tráfico NTP pase del centro de datos a Internet.
   3. Opcionalmente, especifique un **servidor NTP secundario** para el dispositivo.
   4. Haga clic en **Apply**. Esto validará y aplicará los parámetros de hora configurados.
9. Configure las opciones de nube para el dispositivo. En este paso, completará la configuración del dispositivo local y, después, registrará el dispositivo en el servicio StorSimple Device Manager.
   
   1. Escriba la **clave de registro del servicio** que obtuvo en **Paso 2: Obtener la clave de registro del servicio** en [Implementación de la matriz virtual de StorSimple: preparación del portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Si no es el primer dispositivo que va a registrar en este servicio, debe proporcionar la **clave de cifrado de datos del servicio**. ya que se necesita, junto con la clave de registro del servicio, para registrar dispositivos adicionales en el servicio StorSimple Device Manager. Para más información, consulte [Obtener la clave de cifrado de los datos del servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) en la interfaz de usuario web local.
   3. Haga clic en **Registrar**. Se reiniciará el dispositivo. Debe esperar de 2 a 3 minutos antes de que el dispositivo se registre correctamente. Una vez que se haya reiniciado el dispositivo, irá a la página de inicio de sesión.
      
      ![Registrar dispositivo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Vuelva a Azure Portal.
11. Navegue hasta la hoja **Dispositivos** del servicio. Si tiene muchos de recursos, haga clic en **Todos los recursos**, haga clic en el nombre del servicio (búsquelo si fuera necesario) y, después, en **Dispositivos**.
12. En la hoja **Dispositivos**, compruebe que el dispositivo se ha conectado correctamente al servicio, para lo que debe consultar su estado. El estado del dispositivo debe ser **Listo para configurar**.
    
    ![Registrar dispositivo](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Paso 2: Configurar el dispositivo como servidor iSCSI

Siga estos pasos en Azure Portal para completar la configuración requerida del dispositivo.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Para configurar el dispositivo como servidor iSCSI

1. Vaya al servicio StorSimple Device Manager y, después, a **Administración > Dispositivos**. En la hoja **Dispositivos**, seleccione el dispositivo que acaba de crear. Este dispositivo aparecerá como **Listo para configurar**.
   
    ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Haga clic en el dispositivo y verá un mensaje del banner que indica que el dispositivo está listo para configurar.
   
    ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Haga clic en **Configurar** en la barra de comandos del dispositivo. Se abrirá la hoja **Configurar**. En **dicha hoja**, realice estas acciones:
   
   * El nombre del servidor iSCSI se rellena automáticamente.
   * Asegúrese de que el cifrado del almacenamiento en la nube está establecido en **Habilitado**. Esto garantiza que los datos enviados desde el dispositivo a la nube están cifrados.
   * Especifique una clave de cifrado de 32 caracteres y regístrela en una aplicación de administración de claves para futuras referencias.
   * Seleccione la cuenta de almacenamiento que se va a usar con el dispositivo. En esta suscripción, puede seleccionar una cuenta de almacenamiento existente o puede hacer clic en **Agregar** para elegir una cuenta de otra suscripción.
     
     ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Haga clic en **Configurar** para completar la configuración del servidor iSCSI.
   
    ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Se le notificará que la creación del servidor iSCSI está en curso. Una vez que el servidor iSCSI se crea correctamente, se actualiza la hoja **Dispositivos** y el estado del dispositivo correspondiente es **En línea**.
   
    ![Configurar dispositivo como servidor iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Paso 3: Agregar un volumen

1. En la hoja **Dispositivos**, seleccione el dispositivo que acaba de configurar como servidor iSCSI. Haga clic **...** (o bien haga clic con el botón derecho en esta fila) y, en el menú contextual, seleccione **Agregar volumen**. También puede hacer clic en **+ Agregar volumen** en la barra de comandos. Con ello, se abrirá la hoja **Agregar volumen**.
   
    ![Agregar un volumen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. En la hoja **Agregar volumen**, haga lo siguiente:
   
   * En el campo **Nombre del volumen**, escriba un nombre único para el volumen. El nombre debe ser una cadena que contenga entre 3 y 127 caracteres.
   * En la lista desplegable **Tipo**, especifique si desea crear un volumen **en capas** o **anclado localmente**. Para las cargas de trabajo que requieren garantías locales, latencias bajas y un rendimiento más alto, seleccione **Volumen anclado** **localmente**. Para todos los demás datos, seleccione **Volumen** **en capas**.
   * En el campo **Capacidad**, especifique el tamaño del volumen. Un volumen en capas debe tener entre 500 GB y 5 TB, mientras que un volumen anclado localmente debe oscilar entre 50 GB y 500 GB.
     
     Un volumen anclado localmente se aprovisiona de forma intensa y garantiza que los datos principales del volumen se mantengan en el dispositivo y que no se traspasan a la nube.
     
     Por otro lado, un volumen en capas tiene un aprovisionamiento reducido. Cuando se crea un volumen en capas, el 10 % del espacio se aprovisiona en la capa local y el 90 % del espacio se aprovisiona en la nube. Por ejemplo, si se aprovisiona un volumen de 1 TB, 100 GB residirían en el espacio local y 900 GB se utilizarían en la nube cuando se apilen los datos. A su vez, esto hace que, si agota todo el espacio local en el dispositivo, no se puede aprovisionar un recurso compartido en capas (porque el 10 % no estará disponible).
     
     ![Agregar un volumen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Haga clic en **Hosts conectados**, seleccione un registro de control de acceso (ACR) correspondiente al iniciador iSCSI que desea conectar a este volumen y, a continuación, haga clic en **Seleccionar**. <br><br> 
3. Para agregar un nuevo host conectado, haga clic en **Agregar nuevo**, escriba un nombre para el host y su nombre calificado iSCSI (IQN) y, a continuación, haga clic en **Agregar**. Si no tiene el IQN, vaya a [Apéndice A: Obtener el IQN de un host de Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Agregar un volumen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Cuando haya terminado de configurar el volumen, haga clic en **Aceptar**. Se creará un volumen con la configuración especificada y verá una notificación. De forma predeterminada, se habilitarán la supervisión y la copia de seguridad para el volumen.
   
     ![Agregar un volumen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Para confirmar que el volumen se creó correctamente, vaya a la hoja **Volúmenes**. Debería ver el volumen mostrado.
   
   ![Agregar un volumen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Paso 4: Montar, inicializar y formatear un volumen

Realice los pasos siguientes para montar, inicializar y formatear los volúmenes StorSimple en un host de Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar y formatear un volumen

1. Abra la aplicación del **iniciador iSCSI** en el servidor apropiado.
2. En la ventana **Propiedades del iniciador iSCSI**, en la pestaña **Detección**, haga clic en **Detectar portal**.
   
    ![Detectar portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. En el cuadro de diálogo **Detectar portal de destino**, proporcione la dirección IP de la interfaz de red habilitada para iSCSI y, a continuación, haga clic en **Aceptar**.
   
    ![Dirección IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. En la ventana **Propiedades del iniciador iSCSI**, en la pestaña **Destinos**, busque los **Destinos detectados**. (Cada volumen será un destino detectado). El estado del dispositivo debe aparecer como **Inactivo**.
   
    ![Destinos detectados](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Seleccione un dispositivo de destino y luego haga clic en **Conectar**. Después de conectar el dispositivo, el estado debería cambiar a **Conectado**. (Para obtener más información sobre el uso del iniciador iSCSI de Microsoft, consulte [Installing and Configuring Microsoft iSCSI Initiator ][1] [Instalación y configuración del iniciador iSCSI de Microsoft]).
   
    ![seleccionar dispositivo de destino](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. En el host de Windows, presione la tecla del logotipo de Windows + X y, a continuación, haga clic en **Ejecutar**.
7. En el cuadro de diálogo **Ejecutar**, escriba **Diskmgmt.msc**. Haga clic en **Aceptar**, y aparecerá el cuadro de diálogo **Administración de discos**. El panel derecho mostrará los volúmenes del host.
8. En la ventana **Administración de discos** , aparecerán los volúmenes montados, tal como se muestra en la siguiente ilustración. Haga clic con el botón derecho en el volumen detectado (haga clic en el nombre del disco) y, a continuación, haga clic en **Conectado**.
   
    ![Administración de discos](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Haga clic con el botón derecho y seleccione **Inicializar disco**.
   
    ![inicializar disco 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. En el cuadro de diálogo, seleccione los discos que desea inicializar y luego haga clic en **Aceptar**.
    
    ![inicializar disco 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Se inicia el Asistente para nuevo volumen simple. Seleccione un tamaño de disco y luego haga clic en **Siguiente**.
    
    ![asistente para nuevo volumen 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Asigne una letra de unidad al volumen y luego haga clic en **Siguiente**.
    
    ![asistente para nuevo volumen 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Especifique los parámetros para formatear el volumen. **En Windows Server, solo se admite NTFS.** Establezca el tamaño de la unidad de asignación en 64 KB. Proporcione una etiqueta para el volumen. Es una práctica recomendada que este nombre sea idéntico al nombre de volumen proporcionado en StorSimple Virtual Array. Haga clic en **Siguiente**.
    
    ![asistente para nuevo volumen 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Compruebe los valores para el volumen y luego haga clic en **Finalizar**.
    
    ![asistente para nuevo volumen 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Los volúmenes aparecerán como **Conectado** on the **Administración de discos** .
    
    ![volúmenes en línea](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo usar la interfaz de usuario web local para [administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apéndice A: Obtener el IQN de un host de Windows Server

Siga estos pasos para obtener el nombre completo del iSCSI (IQN) de un host de Windows que ejecute Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obtener el IQN de un host de Windows

1. Inicie el iniciador iSCSI de Microsoft en el host de Windows.
2. En la ventana **Propiedades del iniciador iSCSI**, en la pestaña **Configuración**, seleccione y copie la cadena desde el campo **Nombre de iniciador**.
   
    ![Propiedades del iniciador iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Guarde esta cadena.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



