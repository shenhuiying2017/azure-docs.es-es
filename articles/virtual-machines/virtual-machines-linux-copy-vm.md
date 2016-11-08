---
title: Creación de una copia de una máquina virtual Linux de Azure | Microsoft Docs
description: Sepa cómo crear una copia de una máquina virtual Linux en el modelo de implementación de Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn

---
# Creación de una copia de una máquina virtual Linux que se ejecuta en Azure
En este artículo se muestra cómo crear una copia de una máquina virtual de Azure con Linux con el modelo de implementación de Resource Manager. Primero copie a través del sistema operativo y los discos de datos en un nuevo contenedor y, después, configure los recursos de red para crear la nueva máquina virtual.

También puede [cargar y crear una máquina virtual desde una imagen de disco personalizada](virtual-machines-linux-upload-vhd.md).

## Antes de empezar
Asegúrese de que se cumplen los siguientes requisitos previos antes de comenzar los pasos:

* Tiene la [CLI de Azure](../xplat-cli-install.md) descargada e instalada en su equipo.
* También necesitará cierta información acerca de la máquina virtual Linux de Azure existente:

| Información de la máquina virtual de origen | Dónde obtenerla |
| --- | --- |
| Nombre de la máquina virtual |`azure vm list` |
| Nombre del grupo de recursos |`azure vm list` |
| Ubicación |`azure vm list` |
| Nombre de la cuenta de almacenamiento |`azure storage account list -g <resourceGroup>` |
| Nombre del contenedor |`azure storage container list -a <sourcestorageaccountname>` |
| Nombre del archivo del VHD de la máquina virtual de origen |`azure storage blob list --container <containerName>` |

* Deberá tomar algunas decisiones acerca de la nueva máquina virtual: <br> -nombre del contenedor <br> - nombre de la máquina virtual <br> -tamaño de la máquina virtual <br> -nombre de red virtual <br> -nombre de la subred <br> -nombre de IP <br> -nombre de NIC

## Inicio de sesión y establecimiento de la suscripción
1. Inicie sesión en la CLI.
   
        azure login
2. Asegúrese de que está en modo de Resource Manager.
   
        azure config mode arm
3. Establezca la suscripción correcta. Puede usar 'azure account list' para ver todas las suscripciones.
   
        azure account set <SubscriptionId>

## Parada de la máquina virtual
Detenga y desasigne la máquina virtual de origen. Puede usar 'azure vm list' para obtener una lista de todas las máquinas virtuales de su suscripción y sus nombres del grupo de recursos.

        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## Copia del VHD
Puede copiar el VHD desde el almacenamiento de origen al de destino con `azure storage blob copy start`. En este ejemplo, vamos a copiar el VHD a la misma cuenta de almacenamiento, pero en un contenedor diferente.

Para copiar el VHD a otro contenedor de la misma cuenta de almacenamiento, escriba:

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>


## Configuración de una red virtual para la nueva máquina virtual
Configure una red virtual y una NIC para la nueva máquina virtual.

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## Creación de la máquina virtual
Ahora puede crear una máquina virtual desde el disco virtual cargado [mediante una plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) o a través de la CLI especificando el identificador URI en el disco copiado escribiendo lo siguiente:

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## Pasos siguientes
Para más información sobre cómo utilizar la CLI de Azure para administrar una máquina virtual nueva, consulte [Comandos de la CLI de Azure para Azure Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0803_2016-->