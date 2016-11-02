<properties
	pageTitle="Configuración de un Almacén de claves para máquinas virtuales en Azure Resource Manager | Microsoft Azure"
	description="Cómo configurar un Almacén de claves para usarlo con una máquina virtual de Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Configuración de un Almacén de claves para máquinas virtuales en Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica

En la pila de Azure Resource Manager, los certificados o secretos se modelan como recursos que se proporcionan mediante el proveedor de recursos del Almacén de claves. Para más información sobre el Almacén de claves, consulte [¿Qué es el Almacén de claves de Azure?](../key-vault/key-vault-whatis.md)

>[AZURE.NOTE] 
>
>1. Para que un Almacén de claves se utilice con máquinas virtuales de Azure Resource Manager, la propiedad **EnabledForDeployment** del Almacén de claves se debe establecer en true. Puede hacer esto en varios clientes.
>
>2. El Almacén de claves debe crearse en la misma ubicación y suscripción que la máquina virtual.

## Uso de PowerShell para configurar el Almacén de claves
Para crear un almacén de claves usando PowerShell, consulte [Introducción al Almacén de claves de Azure](../key-vault/key-vault-get-started.md#vault).

Para almacenes de claves nuevos, puede usar este cmdlet de PowerShell:

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para almacenes de claves existentes, puede usar este cmdlet de PowerShell:

	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## Uso de la CLI para configurar el Almacén de claves
Para crear un almacén de claves mediante la interfaz de la línea de comandos (CLI), consulte [Administración del Almacén de claves mediante CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Para la CLI, primero debe crear el almacén de claves y luego asignar la directiva de implementación. Para ello, puede usar el siguiente comando:

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Uso de plantillas para configurar el Almacén de claves
Al utilizar plantillas, deberá establecer la propiedad `enabledForDeployment` en `true` para el recurso de Almacén de claves.

	{
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para otras opciones que puede configurar al crear un almacén de claves mediante plantillas, consulte [Create a key vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/) (Creación de un almacén de claves).

<!---HONumber=AcomDC_0824_2016-->