---
title: "Creación de plantillas con extensiones de máquina virtual Linux | Microsoft Docs"
description: "Obtenga información sobre cómo crear plantillas de Azure Resource Manager con extensiones para máquinas virtuales de Linux."
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 322f8f0b-6697-4acb-b5f3-b3f58d28358b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 42c2a12551db35f4cb3994179ba2e277145f2d9b


---
# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>Creación de plantillas de Azure Resource Manager con extensiones de máquina virtual de Linux
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

En la CLI de Azure, ejecute el siguiente comando:

      Azure VM extension list

Este comando devuelve el nombre del editor y el nombre y la versión de la extensión del siguiente modo:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Estas tres propiedades se asignan a "publisher", "type" y "typeHandlerVersion", respectivamente, en el fragmento de plantilla anterior.

> [!NOTE]
> Siempre se recomienda usar la versión más reciente de la extensión para obtener la funcionalidad más actualizada.
> 
> 

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificación del esquema de los parámetros de configuración de la extensión
El siguiente paso en la creación de una plantilla de extensión consiste en identificar el formato para proporcionar parámetros de configuración. Cada extensión es compatible con su propio conjunto de parámetros.

Para consultar configuraciones de ejemplo para extensiones de Linux, haga clic en la documentación [Ejemplos de extensiones de Linux](virtual-machines-linux-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte el siguiente artículo para obtener una plantilla totalmente completa con extensiones de máquina virtual.

[Custom script extension on a Linux VM (Extensión del script personalizado en una máquina virtual de Linux)](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Una vez creada la plantilla, puede implementarla con la CLI de Azure.




<!--HONumber=Nov16_HO3-->


