<properties
	pageTitle="Creación de una copia de una máquina virtual de Linux | Microsoft Azure"
	description="Aprenda a crear una copia de una máquina virtual de Azure en que se ejecuta Linux, en el modelo de implementación de Resource Manager, mediante la creación de una *imagen especializada*."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# Creación de una copia de una máquina virtual con Linux en el modelo de implementación de Resource Manager



En este artículo se muestra cómo crear una copia de una máquina virtual (VM) de Azure con Linux, en el modelo de implementación de Resource Manager mediante la CLI de Azure y el Portal de Azure. Muestra cómo crear una imagen **_especializada_** de una máquina virtual de Azure que mantiene las cuentas de usuario y otros datos de estado de la máquina virtual original. Las imágenes especializadas son útiles en escenarios tales como la portabilidad de una máquina virtual con Linux del modelo de implementación clásica al modelo de implementación de Resource Manager, o bien la creación de una copia de seguridad de la máquina virtual con Linux creada en el modelo de implementación de Resource Manager. Puede copiar los discos de datos y del sistema operativa de esta forma y, luego, configurar los recursos de red para crear la nueva máquina virtual.

Si necesita crear implementaciones masivas de máquinas virtuales con Linux similares, necesitará una imagen *generalizada*; para ello, consulte [Capturar una máquina virtual de Linux para usarla como plantilla del Administrador de recursos](virtual-machines-linux-capture-image.md).



## Comprobaciones antes de comenzar

En este artículo se supone que se cumplen los siguientes requisitos previos antes de iniciar estos pasos:

1. Tiene una máquina virtual de Azure con Linux, que se creó mediante el modelo de implementación clásica o de Resource Manager. Ha configurado el sistema operativo y conectado los discos de datos. Además, ha realizado otras personalizaciones como la instalación de las aplicaciones requeridas. Esta máquina virtual se usará para crear la copia; si necesita ayuda para crear la máquina virtual de origen, consulte [Creación de una máquina virtual con Linux en Azure mediante la CLI](virtual-machines-linux-quick-create-cli.md). 

1. Tiene la CLI de Azure descargada e instalada en el equipo y ha iniciado sesión en la suscripción de Azure. Para más información, consulte [Instalación de la CLI de Azure](../xplat-cli-install.md).

1. Tiene un grupo de recursos y una cuenta de almacenamiento, así como un contenedor de blobs creado en el grupo de recursos en el que va a copiar los VHD. Para más información sobre la creación de cuentas de almacenamiento y contenedores de blob mediante la CLI de Azure, consulte [Uso de la CLI de Azure con Almacenamiento de Azure](../storage/storage-azure-cli.md).


> [AZURE.NOTE] Para las máquinas virtuales creadas mediante cualquiera de los dos modelos de implementación se aplican los mismos pasos que a la imagen de origen. Se indicarán las diferencias menores cuando proceda.


## Copia de los VHD a una cuenta de almacenamiento de Resource Manager


1. Primero libere los VHD que ha utilizado la máquina virtual de origen, para lo que tiene cualquiera de las dos opciones siguientes:

	- Si desea **_copiar_** la máquina de origen, **deténgala** y **desasígnela**. En el portal, haga clic en **Examinar** > **Máquinas virtuales** o **Máquinas virtuales (clásico)** > *su máquina virtual* > **Detener**. En el caso de las máquinas virtuales creadas en el modelo de implementación de Resource Manager, también puede utilizar el comando `azure vm stop <yourResourceGroup> <yourVmName>` de la CLI de Azure seguido de `azure vm deallocate <yourResourceGroup> <yourVmName>`. Tenga en cuenta que el *estado* de la máquina virtual en el portal cambia de **En ejecución** a **Detenido (desasignado)**.
	
	- O bien, si desea **_migrar_** la máquina virtual de origen, **elimine** la máquina virtual y use el VHD anterior. **Desplácese** a la máquina virtual en el [portal](https://portal.azure.com) y haga clic en **Eliminar**.
	
1. Busque la clave de acceso de la cuenta de almacenamiento que contiene el VHD de origen. Para más información sobre las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

	- Si la máquina virtual de origen se creó mediante el modelo de implementación clásica, haga clic en **Examinar** > **Cuentas de almacenamiento (clásico)** > *su cuenta de almacenamiento* > **Todas las configuraciones** > **Claves** y copie la clave con la etiqueta **CLAVE DE ACCESO PRINCIPAL**. O bien, en la CLI de Azure, cambie al modo clásico mediante `azure config mode asm` y, luego, utilice `azure storage account keys list <yourSourceStorageAccountName>`.

	- En el caso de una máquina virtual creada mediante el modelo de implementación de Resource Manager, haga clic en **Examinar** > **Cuentas de almacenamiento** > *su cuenta de almacenamiento* > **Todas las configuraciones** > **Claves de acceso** y copie el texto con la etiqueta **clave1**. O bien, en la CLI de Azure, asegúrese de que esté en modo Resource Manager, para lo que debe usar `azure config mode arm` y, luego, `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>`.

1. Copie los archivos del VHD con los [comandos de la CLI de Azure para Almacenamiento de Azure](../storage/storage-azure-cli.md), como se describe en los pasos siguientes. Como alternativa, si prefiere adoptar un enfoque de interfaz de usuario para conseguir los mismos resultados, puede utilizar el [ Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/) en su lugar. </br>
	1. Configure la cadena de conexión de la cuenta de almacenamiento de destino. Dicha cadena de conexión contendrá la clave de acceso de esta cuenta de almacenamiento.
	
			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>
	
	2. Cree una [firma de acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md) para el archivo del VHD en la cuenta de almacenamiento de origen. Anote la salida de **URL de acceso compartido** del comando siguiente.
	
			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>
	
	3. Use el siguiente comando para copiar el VHD del almacenamiento de origen al de destino.
	
			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>
	
	4. El archivo de VHD se copiará de forma asincrónica. Para comprobar el progreso, use el comando siguiente.
	
			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>
		
</br>

>[AZURE.NOTE] Tendrá que copiar los discos de datos y del sistema operativo por separado como se ha descrito anteriormente.


## Creación de una máquina virtual mediante el VHD copiado

Estos pasos muestran cómo utilizar la CLI de Azure para crear una máquina virtual con Linux basada en Resource Manager en una nueva red virtual mediante el VHD copiado en los pasos anteriores. El VHD debe encontrarse en la misma cuenta de almacenamiento que la máquina virtual que se va a crear.


En primer lugar, configure una red virtual y una NIC para la nueva máquina virtual parecida al siguiente script. Use los valores para las variables más apropiados para su aplicación.

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


A continuación, cree la nueva máquina virtual con los VHD copiados, para lo que debe usar el comando siguiente. </br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>

	
Las direcciones URL de los discos de datos y del sistema operativo deben ser similares a la siguiente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Para encontrarlo en el portal, diríjase al contenedor de almacenamiento, haga clic en el VHD de datos o del sistema operativo que se ha copiado y copie el contenido de la **dirección URL**.
	
	
Si el comando se ejecuta correctamente, verá un resultado similar al siguiente:

	$azure vm create -g "testcopyRG" -n "redhatcopy" -f "LinCopyNic" -d https://testcopystore.blob.core.windows.net/testcopyblob/RedHat201631816334.vhd -x https://testcopystore.blob.core.windows.net/testcopyblob/RedHat-data.vhd -e 10 -Y -l "West US" -y Linux -z "Standard_A1" -o "testcopystore" -R "testcopyblob"
	info:    Executing command vm create
	+ Looking up the VM "redhatcopy"
	info:    Using the VM Size "Standard_A1"
	+ Looking up the NIC "LinCopyNic"
	info:    Found an existing NIC "LinCopyNic"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourceGroups/testcopyRG/providers/Microsoft.Network/virtualNetworks/LinCopyVnet/subnets/LinCopySub" in the NIC "LinCopyNic"
	info:    This NIC IP configuration has a public ip already configured "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourcegroups/testcopyrg/providers/microsoft.network/publicipaddresses/lincopyip", any public ip parameters if provided, will be ignored.
	+ Looking up the storage account testcopystore
	info:    The storage URI 'https://testcopystore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
	+ Creating VM "redhatcopy"
	info:    vm create command OK

Debería ver la máquina virtual recién creada en el [Portal de Azure](https://portal.azure.com) en **Examinar** > **Máquinas virtuales**.

Conéctese a la nueva máquina virtual mediante el cliente SSH que prefiera y use las credenciales de la cuenta de la máquina virtual original, por ejemplo, `ssh OldAdminUser@<IPaddressOfYourNewVM>`. Para más información acerca de SSH para la máquina virtual de Linux, consulte [Uso de la CLI de Azure con Almacenamiento de Azure](virtual-machines-linux-ssh-from-linux.md).


## Pasos siguientes

Para obtener información sobre cómo utilizar la CLI de Azure para administrar una máquina virtual nueva, consulte [Comandos de la CLI de Azure en el modo de Azure Resource Manager (ARM)](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0504_2016-->