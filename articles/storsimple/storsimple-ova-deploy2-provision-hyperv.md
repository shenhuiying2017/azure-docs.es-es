---
title: 'Implementar una matriz virtual de StorSimple: Aprovisionamiento en Hyper-V'
description: "En este segundo tutorial de implementación de matrices virtuales de StorSimple, se trata el aprovisionamiento de un dispositivo virtual en Hyper-V."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 38c440e7-81e9-4689-9ec3-c231a499c43e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/11/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4d8de51377c8aad73ebf71f83b44edb094635f32


---
# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>Implementación de una matriz virtual de StorSimple: aprovisionamiento de una matriz virtual en Hyper-V
![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Información general
Este tutorial de aprovisionamiento se aplica a las matrices virtuales de Microsoft Azure StorSimple (también conocidas como dispositivos virtuales locales de StorSimple o dispositivos virtuales de StorSimple) que se ejecutan en la versión de disponibilidad general de marzo de 2016. En este tutorial se describe cómo aprovisionar una matriz virtual de StorSimple en un sistema host que ejecuta Hyper-V en Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. Este artículo se aplica a la implementación de matrices virtuales de StorSimple en el Portal de Azure clásico, así como en Microsoft Azure Government Cloud.

Se necesitan privilegios de administrador para aprovisionar y configurar un dispositivo virtual. El aprovisionamiento y la instalación inicial pueden tardar unos 10 minutos en completarse.

## <a name="provisioning-prerequisites"></a>Requisitos previos de aprovisionamiento
Aquí encontrará los requisitos previos para aprovisionar un dispositivo virtual en un sistema host que ejecuta Hyper-V en Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

### <a name="for-the-storsimple-manager-service"></a>Para el servicio de Administrador de StorSimple
Antes de comenzar, asegúrese de que:

* Ha completado todos los pasos de [Preparar el portal para la matriz virtual de StorSimple](storsimple-ova-deploy1-portal-prep.md).
* Ha descargado la imagen del dispositivo virtual para Hyper-V desde el Portal de Azure. Para más información, consulte [Paso 3: Descargar la imagen del dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).
  
  > [!IMPORTANT]
  > El software que se ejecuta en la matriz virtual de StorSimple solo puede usarse junto con el servicio StorSimple Manager.
  > 
  > 

### <a name="for-the-storsimple-virtual-device"></a>Para el dispositivo virtual StorSimple
Antes de implementar un dispositivo virtual, asegúrese de que:

* Tiene acceso a un sistema host que ejecuta Hyper-V en Windows Server 2008 R2 o superior que puede utilizarse para aprovisionar un dispositivo.
* El sistema host es capaz de utilizar los recursos siguientes para aprovisionar el dispositivo virtual:
  
  * Un mínimo de 4 núcleos.
  * Al menos 8 GB de RAM.
  * Una interfaz de red.
  * Un disco virtual de 500 GB para datos del sistema.

### <a name="for-the-network-in-the-datacenter"></a>Para la red del centro de datos
Antes de comenzar, revise los requisitos de red para implementar un dispositivo virtual de StorSimple y configurar correctamente la red del centro de datos. Para más información, consulte [Requisitos del sistema de la matriz virtual de StorSimple](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Aprovisionamiento paso a paso
Para aprovisionar y conectarse a un dispositivo virtual, debe realizar los pasos siguientes:

1. Asegúrese de que el sistema host tiene recursos suficientes para cumplir los requisitos mínimos del dispositivo virtual.
2. Aprovisione un dispositivo virtual en el hipervisor.
3. Inicie el dispositivo virtual y obtenga la dirección IP.

En las siguientes secciones se explican cada uno de estos pasos.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>Paso 1: Asegurarse de que el sistema host cumple los requisitos mínimos del dispositivo virtual
Para crear un dispositivo virtual, necesitará:

* El rol de Hyper-V instalado en Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.
* Administrador de Hyper-V de Microsoft en un cliente Microsoft Windows conectado al host.

Asegúrese de que el hardware subyacente (sistema de host) en el que está creando el dispositivo virtual pueda dedicar los siguientes recursos al dispositivo virtual:

* Un mínimo de 4 núcleos.
* Al menos 8 GB de RAM.
* Una interfaz de red.
* Un disco virtual de 500 GB para datos del sistema.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Paso 2: Aprovisionar un dispositivo virtual en el hipervisor
Realice los pasos siguientes para aprovisionar un dispositivo en el hipervisor.

#### <a name="to-provision-a-virtual-device"></a>Para aprovisionar un dispositivo virtual
1. En el host de Windows Server, copie la imagen del dispositivo virtual en una unidad local. Esta es la imagen (VHD o VHDX) que ha descargado mediante el Portal de Azure. Anote la ubicación donde haya copiado la imagen, ya que la va a utilizar más adelante en el procedimiento.
2. Abra el **Administrador del servidor**. En la esquina superior derecha, haga clic en **Herramientas** y seleccione **Administrador de Hyper-V**.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)
   
   Si está ejecutando Windows Server 2008 R2, abra el Administrador de Hyper-V. En el Administrador de servidores, haga clic en **Roles > Hyper-V > Administrador de Hyper-V**.
3. En el **Administrador de Hyper-V**, en el panel de ámbito, haga clic con el botón derecho en el nodo del sistema para abrir el menú contextual y luego haga clic en **Nuevo** > **Máquina virtual**.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)
4. En la página **Antes de comenzar** del Asistente para nueva máquina virtual, haga clic en **Siguiente**.
5. En la página **Especificar nombre** y ubicación, proporcione un **nombre** para el dispositivo virtual. Haga clic en **Siguiente**.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)
6. En la página **Especificar generación**, elija el tipo de imagen de dispositivo y luego haga clic en **Siguiente**. Esta página no aparece si está utilizando Windows Server 2008 R2.
   
   * Elija **Generación 2** si descargó una imagen .vhdx para Windows Server 2012 o versiones posteriores.
   * Elija **Generación 1** si descargó una imagen .vhd para Windows Server 2008 R2 o versiones posteriores.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)
7. En la página **Asignar memoria**, especifique una **memoria de inicio** de al menos **8192 MB**, no habilite la memoria dinámica y luego haga clic en **Siguiente**.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)
8. En la página **Configurar funciones de red**, especifique el conmutador virtual que está conectado a Internet y luego haga clic en **Siguiente**.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)
9. En la página **Conectar disco duro virtual**, elija **Utilizar un disco duro virtual existente**, especifique la ubicación de la imagen de dispositivo virtual (.vhdx o .vhd) y luego haga clic en **Siguiente**.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)
10. Revise la sección **Resumen** y luego haga clic en **Finalizar** para crear la máquina virtual. Pero no se adelante aún: todavía tiene que agregar algunos núcleos de CPU y una segunda unidad. 
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)
11. Para cumplir los requisitos mínimos, necesitará 4 núcleos. Para agregar procesadores virtuales, con el sistema host seleccionado en la ventana **Administrador de Hyper-V**, en el panel de la derecha bajo la lista de **Máquinas virtuales**, ubique la máquina virtual que acaba de crear. Seleccione el nombre del equipo, haga clic con el botón derecho y seleccione **Configuración**.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)
12. En la página **Configuración**, en el panel izquierdo, haga clic en **Procesador**. En el panel derecho, establezca **Número de procesadores virtuales** en 4 (o más). Haga clic en **Apply**.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)
13. Para cumplir los requisitos mínimos, también debe agregar un disco virtual de datos de 500 GB. En la página **Configuración** :
    
    1. En el panel izquierdo, seleccione **Controlador SCSI**.
    2. En el panel derecho, seleccione **Unidad de disco duro** y haga clic en **Agregar**.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)
14. En la página **Unidad de disco duro**, seleccione la opción **Disco duro virtual** y haga clic en **Nuevo**. Se iniciará el **Asistente para crear nuevo disco duro virtual**.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)
15. En la página **Antes de comenzar** del Asistente para crear nuevo disco virtual, haga clic en **Siguiente**.
16. En la página **Elegir formato de disco**, acepte la opción predeterminada de formato **VHDX**. Haga clic en **Next**. Si ejecuta Windows Server 2012 R2 o Windows Server 2008 R2, no podrá ver esta pantalla.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)
17. En la página **Elegir tipo de disco**, establezca el tipo de disco duro virtual como **Expansión dinámica** (recomendado). Si elige un disco de **Tamaño fijo** , también funcionará, pero puede que necesite esperar mucho tiempo. Se recomienda que no utilice la opción **Diferenciación** . Haga clic en **Siguiente**. Tenga en cuenta que **Expansión dinámica** es el valor predeterminado en Windows Server 2012 R2 y Windows Server 2012. En Windows Server 2008 R2, el valor predeterminado es **Tamaño fijo**.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)
18. En la página **Especificar nombre y ubicación**, proporcione un **nombre**, así como una **ubicación** (puede dirigirse a una) para el disco de datos. Haga clic en **Next**.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)
19. En la página **Configurar disco**, seleccione la opción **Crear un nuevo disco duro virtual en blanco** y especifique el tamaño como **500 GB** (o más). Aunque el requisito mínimo son 500 GB, siempre puede aprovisionar un disco más grande. Tenga en cuenta que no se puede expandir o reducir el disco una vez aprovisionado. Para más información sobre el tamaño del disco para aprovisionar, revise la sección de ajuste de tamaño en el documento de [procedimientos recomendados](storsimple-ova-best-practices.md#configuration-best-practices). Haga clic en **Next**.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)
20. En la página **Resumen**, revise los detalles del disco de datos virtual y, si está satisfecho, haga clic en **Finalizar** para crear el disco. El asistente se cerrará y se agregará un disco duro virtual a su equipo.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)
21. Volverá a la página **Configuración** . Haga clic en **Aceptar** para cerrar la página **Configuración** y volver a la ventana Administrador de Hyper-V.
    
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Paso 3: Iniciar el dispositivo virtual y obtener la dirección IP
Realice los pasos siguientes para iniciar el dispositivo virtual y conectarse a él.

#### <a name="to-start-the-virtual-device"></a>Para iniciar el dispositivo virtual
1. Inicie el dispositivo virtual.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)
2. Cuando el dispositivo se esté ejecutando, seleccione el dispositivo, haga clic con el botón derecho y seleccione **Conectar**.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)
3. Es posible que tenga que esperar entre 5 y 10 minutos para que el dispositivo esté listo. En la consola se muestra un mensaje de estado para indicar el progreso. Cuando el dispositivo esté listo, vaya a **Acción**. Presione `Ctrl + Alt + Delete` para iniciar sesión en el dispositivo virtual. El usuario predeterminado es *StorSimpleAdmin* y la contraseña predeterminada es *Password1*.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)
4. Por motivos de seguridad, la contraseña del administrador de dispositivos expirará en el primer inicio de sesión. Se le pedirá que cambie la contraseña.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)
   
   Escriba una contraseña que contenga al menos 8 caracteres. La contraseña debe cumplir al menos 3 de los siguientes 4 requisitos: caracteres en mayúsculas, minúsculas, numéricos y especiales. Vuelva a escribir la contraseña para confirmarla. Se le notificará que la contraseña ha cambiado.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)
5. Tras cambiar la contraseña correctamente, puede reiniciar el dispositivo virtual. Espere a que el dispositivo se inicie.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)
   
    La consola de Windows PowerShell del dispositivo se mostrará junto a una barra de progreso.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)
6. Los pasos 6 a 8 solo se aplican durante el arranque en un entorno sin DHCP. Si se encuentra en un entorno de DHCP, omita estos pasos y vaya al paso 9. Si arranca el dispositivo en un entorno sin DHCP, verá la siguiente pantalla.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)
   
    Ahora debe configurar la red.
7. Utilice el comando `Get-HcsIpAddress` para enumerar las interfaces de red habilitadas en el dispositivo virtual. Si el dispositivo tiene una única interfaz de red habilitada, el nombre predeterminado asignado a esta interfaz es `Ethernet`.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)
8. Utilice el cmdlet `Set-HcsIpAddress` para configurar la red. A continuación se muestra un ejemplo:
   
    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`
   
    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)
9. Una vez que haya finalizado la instalación inicial y el dispositivo haya arrancado, verá el texto de titular del dispositivo. Anote la dirección IP y la dirección URL que se muestran en el texto del titular para administrar el dispositivo. Utilizará esta dirección IP para conectarse a la interfaz de usuario web del dispositivo virtual y completar la instalación local y el registro.
   
   ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)
10. (Opcional) Realice este paso únicamente si va a implementar el dispositivo en Government Cloud. Ahora podrá habilitar el modo FIPS (Estándar federal de procesamiento de información) de los Estados Unidos en el dispositivo. El estándar FIPS 140 define algoritmos criptográficos aprobados para su uso por parte de los sistemas informáticos del Gobierno federal de los Estados Unidos a fin de garantizar la protección de los datos confidenciales.
    
    1. Para habilitar el modo FIPS, ejecute el siguiente cmdlet:
       
        `Enter-HcsFIPSMode`
    2. Reinicie el dispositivo una vez que haya habilitado el modo FIPS para que las validaciones criptográficas surtan efecto.
       
       > [!NOTE]
       > Puede habilitar o deshabilitar el modo FIPS en su dispositivo. No es posible alternar entre el modo FIPS y no FIPS en el dispositivo.
       > 
       > 

Si el dispositivo no cumple los requisitos mínimos de configuración, verá un error en el texto del titular (se muestra a continuación). Debe modificar la configuración del dispositivo para que tenga los recursos adecuados para cumplir los requisitos mínimos. A continuación, puede reiniciar y conectarse al dispositivo. Consulte los requisitos mínimos de configuración de [Paso 1: Asegurarse de que el sistema host cumple los requisitos mínimos del dispositivo virtual](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Si encuentra cualquier otro error durante la configuración inicial mediante la interfaz de usuario web local, consulte los siguientes flujos de trabajo en el artículo sobre cómo [Usar la interfaz de usuario web para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

* Ejecute pruebas de diagnóstico para [solucionar problemas de configuración de la interfaz de usuario web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Genere el paquete de registro y vea los archivos del registro](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![icono de vídeo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **Vídeo disponible**

Mire el vídeo para ver cómo puede aprovisionar una matriz virtual de StorSimple en Hyper-V.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Create-a-StorSimple-Virtual-Array/player]
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Configurar la matriz virtual de StorSimple como servidor de archivos](storsimple-ova-deploy3-fs-setup.md)
* [Configurar la matriz virtual de StorSimple como servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)




<!--HONumber=Nov16_HO3-->


