<properties
   pageTitle="Creación de plantillas con extensiones de máquina virtual de Linux | Microsoft Azure"
   description="Obtenga información sobre cómo crear plantillas de Azure Resource Manager con extensiones para máquinas virtuales de Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Creación de plantillas de Azure Resource Manager con extensiones de máquina virtual de Linux

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

En la CLI de Azure, ejecute el siguiente comando:

      Azure VM extension list

Este comando devuelve el nombre del editor y el nombre y la versión de la extensión del siguiente modo:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Estas tres propiedades se asignan a "publisher", "type" y "typeHandlerVersion", respectivamente, en el fragmento de plantilla anterior.

>[AZURE.NOTE]Siempre se recomienda usar la versión más reciente de la extensión para obtener la funcionalidad más actualizada.

## Identificación del esquema de los parámetros de configuración de la extensión

El siguiente paso en la creación de una plantilla de extensión consiste en identificar el formato para proporcionar parámetros de configuración. Cada extensión es compatible con su propio conjunto de parámetros.

Para consultar configuraciones de ejemplo de para las extensiones de Linux, haga clic en la documentación [Linux eExtensions samples](virtual-machines-linux-extensions-configuration-samples.md) (Ejemplos de extensiones de Linux).

Consulte el siguiente artículo para obtener una plantilla totalmente completa con extensiones de máquina virtual.

[Custom script extension on a Linux VM (Extensión del script personalizado en una máquina virtual de Linux)](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Una vez creada la plantilla, puede implementarla con la CLI de Azure.

<!---HONumber=AcomDC_0601_2016-->