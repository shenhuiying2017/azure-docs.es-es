<properties
   pageTitle="Creación de plantillas con extensiones de VM de Azure | Microsoft Azure"
	description="Más información sobre la creación de plantillas con extensiones"
	services="virtual-machines"
	documentationCenter=""
	authors="kundanap"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="kundanap"/>

# Cree plantillas del Administrador de recursos de Azure con extensiones de VM.

## Información general de las plantillas del Administrador de recursos de Azure

Las plantillas del Administrador de recursos de Azure le permiten especificar mediante declaración la infraestructura IaaS de Azure en el lenguaje Json definiendo las dependencias entre recursos. Para obtener información más detallada de las plantillas del Administrador de recursos de Azure, consulte los siguientes artículos:

<a href="https://azure.microsoft.com/es-ES/documentation/articles/resource-group-overview/" target="_blank">Información general del grupo de recursos</a>. <br/> <a href="https://azure.microsoft.com/es-ES/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Implementación de plantillas con la CLI de Azure</a>. <br/> <a href="https://azure.microsoft.com/es-ES/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Implementación de plantillas con Azure PowerShell</a>.

## Fragmento de plantilla de ejemplo para extensiones de VM.
El fragmento de plantilla para extensiones de implementación tiene el aspecto siguiente:

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

## Identificación de publisher, type y typeHandlerVersion para cualquier extensión.

Tanto Microsoft como publicadores de terceros de confianza publican las extensiones de VM de Azure y cada extensión se identifica de forma exclusiva por publisher, type y typeHandlerVersion. Pueden determinarse de la manera siguiente:

En Azure PowerShell, ejecute el siguiente cmdlet de Azure PowerShell:

      Get-AzureVMAvailableExtension

En la CLI de Azure, ejecute el siguiente cmdlet de Azure PowerShell:

      Azure VM Extension list

Este cmdlet devuelve el nombre de publicador, el nombre de extensión y la versión de la manera siguiente:

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Estas tres propiedades se asignan a "publisher", "type" y "typeHandlerVersion", respectivamente, en el fragmento de plantilla anterior. Nota: siempre se recomienda utilizar la versión más reciente de extensión para obtener la funcionalidad más actualizada.

## Identificación del esquema para los parámetros de configuración de extensión

El paso siguiente en la creación de plantillas de extensión es identificar el formato para proporcionar parámetros de configuración. Cada extensión es compatible con su propio conjunto de parámetros.

Para consultar una configuración de ejemplo de las extensiones de Windows, haga clic en la documentación [Ejemplos de extensiones de Windows](virtual-machines-extensions-configuration-samples-windows.md).

Para consultar una configuración de ejemplo de las extensiones de Linux, haga clic en la documentación [Ejemplos de extensiones de Linux](virtual-machines-extensions-configuration-samples-linux.md).

Consulte lo siguiente en las plantillas de VM para obtener una plantilla totalmente completada con extensiones de VM.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Extensión del script personalizado en una VM de Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Extensión del script personalizado en una VM de Windows</a>.

Después de crear la plantilla, puede implementarla con la CLI de Azure o Azure PowerShell.

<!---HONumber=September15_HO1-->