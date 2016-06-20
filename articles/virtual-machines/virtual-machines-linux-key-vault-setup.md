<properties
	pageTitle="Configuración de un almacén de claves para máquinas virtuales en Azure Resource Manager | Microsoft Azure"
	description="Cómo configurar un almacén de claves para su uso con una máquina virtual de Azure Resource Manager"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Configuración de un almacén de claves para máquinas virtuales en Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica

En la pila de Azure Resource Manager, los certificados o secretos se modelan como recursos que se proporcionan mediante el proveedor de recursos del almacén de claves. Para más información sobre los almacenes de claves, consulte [¿Qué es el Almacén de claves de Azure?](../key-vault/key-vault-whatis.md)

## Configuración
Para que un almacén de claves se utilice con máquinas virtuales de Azure Resource Manager, la propiedad *EnabledForDeployment* del almacén de claves se debe establecer en true. Puede hacer esto en los diversos clientes, como se muestra a continuación.

## CLI
Para crear el almacén de claves mediante la CLI, consulte [Administración del Almacén de claves mediante CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Para la CLI, primero tendrá que crear el almacén de claves, y luego habilitar la directiva de implementación. Para ello, puede usar el siguiente comando:

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## Plantillas
Al utilizar plantillas, deberá establecer la propiedad `enabledForDeployment` en `true` para el recurso del almacén de claves.

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

Para conocer otras opciones que puede configurar durante la creación de un almacén de claves mediante plantillas, consulte [aquí](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

<!---HONumber=AcomDC_0608_2016-->