<properties
   pageTitle="Uso de las plantillas del Administrador de recursos de Azure de la extensión de scripts personalizados"
	description="Automatización de las tareas de configuración de máquina virtual de Azure mediante el script personalizado con plantillas de ARM"
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
	ms.date="07/01/2015"
	ms.author="kundanap"/>

# Uso de la extensión de scripts personalizados con plantillas del Administrador de recursos de Azure

Este artículo proporciona información general sobre la escritura de plantillas del Administrador de recursos de Azure con la extensión de scripts personalizados para cargas de trabajo de arranque en una máquina virtual de Linux o Windows.

Para obtener información general sobre la extensión de scripts personalizados, vea el artículo <a href="https://azure.microsoft.com/es-es/documentation/articles/virtual-machines-extensions-customscript/" target="_blank">aquí</a>.

Desde su lanzamiento, la extensión de scripts personalizados se ha usado ampliamente para configurar cargas de trabajo en máquinas virtuales de Windows y Linux. Con la introducción de las plantillas del Administrador de recursos de Azure, los usuarios ahora pueden crear una única plantilla que no solo aprovisiona la máquina virtual sino que también configura sus cargas de trabajo.

## Información general de las plantillas del Administrador de recursos de Azure

Las plantillas del Administrador de recursos de Azure le permiten especificar mediante declaración la infraestructura IaaS de Azure en el lenguaje Json definiendo las dependencias entre recursos. Para obtener información más detallada de las plantillas del Administrador de recursos de Azure, consulte los siguientes artículos:

<a href="https://azure.microsoft.com/es-es/documentation/articles/resource-group-overview/" target="_blank">Información general del grupo de recursos</a>. <br/> <a href="https://azure.microsoft.com/es-es/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Implementación de plantillas con la CLI de Azure</a>. <br/> <a href="https://azure.microsoft.com/es-es/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Implementación de plantillas con Azure PowerShell</a>.

### Requisitos previos para ejecutar la extensión de la secuencia de comandos personalizada

1. Instale los últimos cmdlets de Azure PowerShell <a href="http://azure.microsoft.com/downloads" target="_blank">aquí</a>.
2. Si las secuencias de comandos se van a ejecutar en una máquina virtual existente, asegúrese de que el agente de máquina virtual está habilitado en la máquina virtual; si no lo está, siga las indicaciones de este <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">artículo</a> para instalar uno.
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden proceder de un único contenedor de almacenamiento o de varios.
4. También pueden cargarse los scripts en una cuenta de Github.
5. La secuencia de comandos debe crearse de forma tal que la secuencia de comandos de entrada que inicia la extensión inicie, a su vez, otras secuencias de comandos.

## Información general de uso de la extensión de scripts personalizados con plantillas:

Para realizar la implementación con plantillas, usamos la misma versión de la extensión de scripts personalizados que está disponible para las API de administración de servicios de Azure. La extensión es compatible con los mismos parámetros y escenarios que la carga de archivos en la cuenta de Almacenamiento de Azure o la ubicación de Github. La diferencia clave cuando usa plantillas es que debe especificarse la versión exacta de la extensión, al contrario de la especificación de la versión en el formato majorversion.*.

 ## Fragmento de plantilla para la extensión del script personalizado en una máquina virtual de Linux

Definición del siguiente recurso de extensión en la sección Recurso de la plantilla

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Fragmento de plantilla para la extensión del script personalizado en una máquina virtual de Windows.

Definición del siguiente recurso en la sección Recurso de la plantilla

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

En los ejemplos anteriores, reemplace la dirección URL de archivo y el nombre de archivo con su propia configuración.

Después de crear la plantilla, puede implementarla con la CLI de Azure o Azure PowerShell.

Consulte los ejemplos siguientes para obtener ejemplos completos de configuración de aplicaciones en una máquina virtual con una extensión de script personalizado.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Extensión del script personalizado en una máquina virtual de Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Extensión del script personalizado en una máquina virtual de Windows</a>.

<!---HONumber=August15_HO9-->