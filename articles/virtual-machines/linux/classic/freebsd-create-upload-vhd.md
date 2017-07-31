---
title: "Creación y carga de una imagen de máquina virtual de FreeBSD | Microsoft Docs"
description: "Aprenda a crear y cargar un disco duro virtual (VHD) que contenga el sistema operativo FreeBSD para crear una máquina virtual de Azure."
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: kyliel
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 7a92105f9d7be88311f2ecd89b22e35f3ad3bbac
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Creación y carga de un VHD de FreeBSD en Azure
En este artículo se muestra cómo crear y cargar un disco duro virtual (VHD) que contenga el sistema operativo FreeBSD. Después de cargarlo, puede utilizarlo como su propia imagen para crear una máquina virtual (VM) en Azure.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para más información sobre cómo cargar un VHD con el modelo de Resource Manager, consulte [aquí](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene los siguientes elementos:

* **Una suscripción de Azure**: si no tiene una cuenta, puede crear una en un par de minutos. Si tiene una suscripción a MSDN, consulte [Crédito mensual de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). De lo contrario, obtenga información sobre cómo [crear una cuenta de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **Herramientas de Azure PowerShell**: el módulo Azure PowerShell debe estar instalado y configurado para utilizar su suscripción de Azure. Para descargar el módulo, consulte la página de [descargas de Azure](https://azure.microsoft.com/downloads/). Hay disponible aquí un tutorial que describe cómo instalar y configurar el módulo. Use el cmdlet de [descargas de Azure](https://azure.microsoft.com/downloads/) para cargar el VHD.
* **Sistema operativo FreeBSD instalado en un archivo .vhd**: se debe instalar un sistema operativo FreeBSD compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd. Por ejemplo, puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones sobre cómo instalar y utilizar Hyper-V, consulte [Instalar Hyper-V y crear una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD mediante el Administrador de Hyper-V o el cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Además, hay un [tutorial en MSDN sobre cómo utilizar FreeBSD con Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Esta tarea incluye los cinco pasos siguientes:

## <a name="step-1-prepare-the-image-for-upload"></a>Paso 1: Preparación de la imagen que se va a cargar
En la máquina virtual en la que se instaló el sistema operativo FreeBSD, realice los procedimientos siguientes:

1. Habilite DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Habilite SSH.

    Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. De forma predeterminada, se habilita después de la instalación desde el disco FreeBSD. 
3. Configure la consola de serie.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Instale sudo.

    La cuenta raíz está deshabilitada en Azure. Esto significa que deberá usar sudo con un usuario sin privilegios para ejecutar comandos con privilegios elevados.

        # pkg install sudo
   
5. Requisitos previos del agente de Azure.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Instale el agente de Azure.

    Siempre puede encontrar la versión más reciente del agente de Azure en [github](https://github.com/Azure/WALinuxAgent/releases). La versión 2.0.10 + admite oficialmente FreeBSD 10 y 10.1 y la versión 2.1.4 (incluida la 2.2.x) admite oficialmente FreeBSD 10.2 y versiones posteriores.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    Para 2.0, usaremos la 2.0.16 como ejemplo:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Para 2.1, utilizaremos la 2.1.4 como ejemplo:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Después de instalar el agente de Azure, es recomendable comprobar que está ejecutándose:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Desaprovisione el sistema.

    Desaprovisione el sistema para limpiarlo y dejarlo adecuado para el reaprovisionamiento. El comando siguiente también elimina la última cuenta de usuario aprovisionada y los datos asociados:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Ahora ya puede apagar la máquina virtual.

## <a name="step-2-create-a-storage-account-in-azure"></a>Paso 2: Creación de una cuenta de almacenamiento en Azure
Necesita una cuenta de almacenamiento de Azure para cargar un archivo .vhd, que se puede usar para crear una máquina virtual. Puede utilizar el portal clásico de Azure para crear una cuenta de almacenamiento.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la barra de comandos, haga clic en **Nuevo**.
3. Seleccione **Data Services** > **Storage** > **Creación rápida**.

    ![Crear rápidamente una cuenta de almacenamiento](./media/freebsd-create-upload-vhd/Storage-quick-create.png)
4. Rellene los campos de la manera siguiente:

   * En el campo **URL** , escriba un nombre de subdominio que vaya a usar en la URL de la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 números y letras minúsculas. Este nombre se convierte en el nombre del host dentro de la dirección URL que se usó para direccionar el Almacenamiento de blobs de Azure, el Almacenamiento en cola de Azure o los recursos de Almacenamiento de tablas de Azure de la suscripción.
   * En la lista desplegable **Ubicación/grupo de afinidad**, seleccione una **ubicación o grupo de afinidad** para la cuenta de almacenamiento. Un grupo de afinidad le permite colocar sus servicios y almacenamiento en la nube en el mismo centro de datos.
   * En el campo **Replicación**, decida si va a usar la replicación **Redundancia geográfica** para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste, por lo que su almacenamiento conmuta por error a esa ubicación si se produce un error importante en la ubicación principal. La ubicación secundaria se asigna automáticamente y no se puede cambiar. Si necesita más control sobre la ubicación del almacenamiento en la nube debido a requisitos legales o las directivas de su organización, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento. Puede encontrar más información sobre la administración de la replicación geográfica de cuentas de almacenamiento aquí: [Replicación de Azure Storage](../../../storage/storage-redundancy.md).

     ![Escribir los detalles de la cuenta de almacenamiento](./media/freebsd-create-upload-vhd/Storage-create-account.png)
5. Haga clic en **Crear cuenta de almacenamiento**. La cuenta aparece ahora en **Almacenamiento**.

    ![Cuenta de almacenamiento creada correctamente](./media/freebsd-create-upload-vhd/Storagenewaccount.png)
6. Después, cree un contenedor para los archivos .vhd que ha cargado. Seleccione el nombre de la cuenta de almacenamiento y, a continuación, elija **Contenedores**.

    ![Detalles de la cuenta de almacenamiento](./media/freebsd-create-upload-vhd/storageaccount_detail.png)
7. Seleccione **Crear un contenedor**.

    ![Detalles de la cuenta de almacenamiento](./media/freebsd-create-upload-vhd/storageaccount_container.png)
8. En el campo **Nombre** , escriba un nombre para el contenedor. A continuación, en el menú desplegable **Acceso** , seleccione el tipo de directiva de acceso que desee.

    ![Nombre del contenedor](./media/freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > De manera predeterminada, el contenedor es privado y solo puede acceder a él el propietario de la cuenta. Para permitir el acceso de lectura público a los blobs del contenedor, pero no a las propiedades y metadatos del contenedor, use la opción **Blob público** . Para permitir el acceso de lectura público completo para el contenedor y los blobs, utilice la opción **Contenedor público** .
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>Paso 3: Preparación de la conexión a Azure
Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure. Para ello, puede usar el método de Azure Active Directory (Azure AD) o el método del certificado.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Uso del método de Azure AD para cargar un archivo .vhd
1. Abra la consola de Azure PowerShell.
2. Escriba el siguiente comando:   
    `Add-AzureAccount`

    Este comando abre una ventana de inicio de sesión donde podrá iniciar sesión con su cuenta profesional o educativa.

    ![Ventana de PowerShell](./media/freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure autentica y guarda las credenciales. A continuación, cierra la ventana.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Uso del método del certificado para cargar un archivo .vhd
1. Abra la consola de Azure PowerShell.
2. Escriba:  `Get-AzurePublishSettingsFile`.
3. Se abre una ventana del explorador que le solicita que descargue el archivo .publishsettings. Este archivo contiene información y un certificado para su suscripción de Azure.

    ![Página de descarga del explorador](./media/freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. Guarde el archivo .publishsettings.
5. Escriba:  `Import-AzurePublishSettingsFile <PathToFile>`, donde `<PathToFile>` es la ruta completa al archivo .publishsettings.

   Para obtener información, consulte [Get started with Azure cmdlets](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)(Introducción a los cmdlets de Azure).

   Para más información sobre cómo instalar Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).

## <a name="step-4-upload-the-vhd-file"></a>Paso 4: Carga del archivo .vhd
Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del Almacenamiento de blobs. A continuación se muestran algunos términos que se van a usar al cargar el archivo:

* **URLAlmacenamientoDeBlobs** es la dirección URL de la cuenta de almacenamiento que ha creado en el paso 2.
* **SuCarpetaDeImágenes** es el contenedor de Almacenamiento de blobs en el que desea almacenar las imágenes.
* **VHDName** es la etiqueta que aparece en el Portal de Azure clásico para identificar el disco duro virtual.
* **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd.

Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Paso 5: Creación de una máquina virtual con el archivo .vhd cargado
Después de cargar el archivo .vhd, puede agregarlo como imagen a la lista de imágenes personalizadas que están asociadas con su suscripción y crear una máquina virtual con esta imagen personalizada.

1. Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Utilice Linux como tipo de sistema operativo. La versión actual de Azure PowerShell acepta solo "Linux" o "Windows" como parámetro.
   >
   >
2. Tras completar los pasos anteriores, la nueva imagen aparecerá en la lista cuando elija la pestaña **Imágenes** en el Portal de Azure clásico.  

    ![Elija una imagen](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Cree una máquina virtual desde la galería. Esta nueva imagen ahora está disponible en **Mis imágenes**.
4. Seleccione la nueva imagen. Siga las indicaciones para configurar un nombre de host, una contraseña, una clave SSH, etc.

    ![Imagen personalizada](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. Una vez completado el aprovisionamiento, verá que la máquina virtual de FreeBSD se ejecuta en Azure.

    ![Imagen de FreeBSD en Azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)

