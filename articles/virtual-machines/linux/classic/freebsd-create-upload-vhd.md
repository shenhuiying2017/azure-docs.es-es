---
title: "Creación y carga de una imagen de máquina virtual de FreeBSD | Microsoft Docs"
description: "Aprenda a crear y cargar un disco duro virtual (VHD) que contenga el sistema operativo FreeBSD para crear una máquina virtual de Azure."
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
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
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
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

Esta tarea incluye los cuatro pasos siguientes:

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

## <a name="step-2-prepare-the-connection-to-azure"></a>Paso 2: preparación de la conexión a Azure
Asegúrese de estar utilizando la CLI de Azure en el modelo de implementación clásica (`azure config mode asm`) y luego inicie sesión en su cuenta:

```azurecli
azure login
```


<a id="upload"></a>


## <a name="step-3-upload-the-vhd-file"></a>Paso 3: Cargar el archivo .vhd

Tiene que cargar el archivo VHD a una cuenta de almacenamiento. Puede seleccionar una cuenta de almacenamiento existente o [crear una nueva](../../../storage/common/storage-create-storage-account.md).

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del Almacenamiento de blobs. A continuación se muestran algunos términos que se van a usar al cargar el archivo:

* **URLAlmacenamientoDeBlobs** es la dirección URL de la cuenta de almacenamiento que ha creado en el paso 2.
* **SuCarpetaDeImágenes** es el contenedor de Almacenamiento de blobs en el que desea almacenar las imágenes.
* **VHDName** es la etiqueta que aparece en el Portal de Azure clásico para identificar el disco duro virtual.
* **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd.

Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>Paso 4: Creación de una máquina virtual con el archivo .vhd cargado
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
