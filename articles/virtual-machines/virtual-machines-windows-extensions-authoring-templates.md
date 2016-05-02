<properties
   pageTitle="Creación de plantillas con extensiones de máquina virtual de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear plantillas del Administrador de recursos de Azure con extensiones para máquinas virtuales de Windows."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Creación de plantillas del Administrador de recursos de Azure con extensiones de máquina virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

En Azure PowerShell, ejecute el siguiente cmdlet de Azure PowerShell:

      Get-AzureVMAvailableExtension


Este cmdlet devuelve el nombre de publicador, el nombre de extensión y la versión de la manera siguiente:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Estas tres propiedades se asignan a "publisher", "type" y "typeHandlerVersion", respectivamente, en el fragmento de plantilla anterior.

>[AZURE.NOTE]Siempre se recomienda usar la versión más reciente de la extensión para obtener la funcionalidad más actualizada.

## Identificación del esquema de los parámetros de configuración de la extensión

El siguiente paso en la creación de una plantilla de extensión consiste en identificar el formato para proporcionar parámetros de configuración. Cada extensión es compatible con su propio conjunto de parámetros.

Para ver configuraciones de ejemplo de las extensiones de Windows, consulte [Ejemplos de extensiones de Windows](virtual-machines-windows-extensions-configuration-samples.md).


Consulte el siguiente artículo para obtener una plantilla totalmente completa con extensiones de máquina virtual.

[Extensión de script personalizada en una máquina virtual de Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Una vez creada la plantilla, puede implementarla con Azure PowerShell.

<!---HONumber=AcomDC_0420_2016-->