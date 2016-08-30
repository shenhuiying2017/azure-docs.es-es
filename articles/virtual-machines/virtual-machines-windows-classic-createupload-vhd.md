<properties
	pageTitle="Creación y carga de una imagen de máquina virtual mediante Powershell | Microsoft Azure"
	description="Aprenda a crear y cargar una imagen de Windows Server generalizada (VHD) mediante el modelo de implementación clásica y Azure Powershell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Crear y cargar un VHD de Windows Server a Azure

En este artículo se muestra cómo puede cargar su propia imagen de VM generalizada como un disco duro virtual (VHD) que podrá utilizar para crear máquinas virtuales. Para obtener más detalles sobre discos y VHD en Microsoft Azure, consulte [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. También puede [capturar](virtual-machines-windows-capture-image.md) y [cargar](virtual-machines-windows-upload-image.md) una máquina virtual mediante el modelo Resource Manager.

## Requisitos previos

En este artículo se supone que ya dispones de:

- **Una suscripción a Azure**: si no tiene una, puede [abrir una cuenta de Azure gratis](/pricing/free-trial/?WT.mc_id=A261C142F).

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)**: tiene el módulo Microsoft Azure PowerShell instalado y configurado para usar su suscripción.

- **Un archivo .VHD**: sistema operativo Windows compatible almacenado en un archivo .vhd y conectado a una máquina virtual. También debe comprobar que los roles de servidor que se ejecutan en el disco duro virtual sean compatibles con sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Compatibilidad de Sysprep con roles de servidor).

> [AZURE.IMPORTANT] El formato VHDX no se admite en Microsoft Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Para obtener más información, consulta esta [publicación de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## Paso 1: Preparar el VHD 

Antes de cargar el VHD en Azure, tiene que generalizarse mediante la herramienta Sysprep. Esto prepara el VHD para usarlo como una imagen. Para obtener más información sobre Sysprep, consulta [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

Desde la máquina virtual en la que se instaló el sistema operativo, realice el procedimiento siguiente:

1. Inicie sesión en el sistema operativo.

2. Abra una ventana del símbolo del sistema como administrador. Cambie el directorio a **%windir%\\system32\\sysprep** y, a continuación, ejecute `sysprep.exe`.

	![Abrir una ventana de símbolo del sistema](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	Aparecerá el cuadro de diálogo **Herramienta de preparación del sistema**.

	![Iniciar Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  En la **Herramienta de preparación del sistema**, selecciona **Iniciar la Configuración rápida (OOBE) del sistema** y asegúrate de que la casilla **Generalizar** está seleccionada.

5.  En **Opciones de apagado**, seleccione **Apagar**.

6.  Haga clic en **Aceptar**.

## Paso 2: Crear una cuenta de almacenamiento y un contenedor

Necesitas una cuenta de almacenamiento de Azure para tener un sitio para cargar el archivo .vhd. Este paso te muestra cómo crear una cuenta u obtener la información que necesitas de una cuenta existente. Reemplace las variables entre &lsaquo; corchetes &rsaquo; por su propia información.

1. Inicio de sesión

		Add-AzureAccount

1. Establezca su suscripción a Azure.

    	Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Cree una cuenta de almacenamiento nueva. El nombre de la cuenta de almacenamiento debe ser único y debe tener entre 3 y 24 caracteres. El nombre puede ser cualquier combinación de letras y números. También necesita especificar una ubicación como "Este de EE. UU. "
    	
		New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Establezca la nueva cuenta de almacenamiento como la predeterminada.
    	
		Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Cree un contenedor nuevo.

    	New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## Paso 3: Cargar el archivo .vhd

Use [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) para cargar el VHD.

Desde la ventana de Azure PowerShell que utilizó en el paso anterior, escriba el siguiente comando y reemplace las variables entre &lsaquo; corchetes &rsaquo; por su propia información.

		Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## Paso 4: Agregar la imagen a la lista de imágenes personalizadas

Use el cmdlet [Add-AzureVMImage]) (https://msdn.microsoft.com/library/mt589167.aspx) para agregar la imagen a la lista de sus imágenes personalizadas.

		Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## Pasos siguientes

Ahora puede [crear una máquina virtual personalizada](virtual-machines-windows-classic-createportal.md) mediante la imagen que cargó.

<!---HONumber=AcomDC_0817_2016-->