---
title: "Extensión de máquina virtual de Azure y OMS para Linux | Microsoft Docs"
description: "Implemente el agente de OMS en la máquina virtual Linux con una extensión de máquina virtual."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 138fc8c98ea6f409b28407b20851c96ecc618b09
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="oms-virtual-machine-extension-for-linux"></a>Extensión de máquina virtual de OMS para Linux

## <a name="overview"></a>Información general

Operations Management Suite (OMS) proporciona funcionalidades de corrección de alertas, supervisión y envío de alertas a todos los recursos locales y en la nube. Microsoft, como editor de la extensión de máquina virtual del agente de OMS para Linux, es quien presta los servicios de soporte técnico para esta solución. La extensión instala al agente de OMS en máquinas virtuales de Azure e inscribe dichas máquinas virtuales en un área de trabajo de OMS. En este documento se especifican las plataformas compatibles, configuraciones y opciones de implementación de la extensión de máquina virtual de OMS para Linux.

## <a name="prerequisites"></a>Requisitos previos

### <a name="operating-system"></a>Sistema operativos

La extensión del agente de OMS puede ejecutarse en estas distribuciones de Linux.

| Distribución | Versión |
|---|---|
| CentOS Linux | 5, 6 y 7 |
| Oracle Linux | 5, 6 y 7 |
| Red Hat Enterprise Linux Server | 5, 6 y 7 |
| Debian GNU/Linux | 6, 7 y 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 y 12 |

### <a name="internet-connectivity"></a>Conectividad de Internet

La extensión del agente de OMS para Linux requiere que la máquina virtual de destino esté conectada a Internet. 

## <a name="extension-schema"></a>Esquema de extensión

El siguiente JSON muestra el esquema para la extensión del agente de OMS. La extensión requiere el identificador y la clave del área de trabajo de OMS de destino, valores que se pueden encontrar en el portal de OMS. Como la clave del área de trabajo debe tratarse como datos confidenciales, debe almacenarse en una configuración protegida. Los datos de configuración protegida de la extensión de VM de Azure están cifrados y solo se descifran en la máquina virtual de destino. Tenga en cuenta que **workspaceId** y **workspaceKey** distinguen mayúsculas de minúsculas.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>Valores de propiedad

| Nombre | Valor / ejemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| workspaceId (p.ej) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (p. ej.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. Las plantillas resultan ideales al implementar una o varias máquinas virtuales que requieren configurarse tras la implementación, por ejemplo, para incorporarse a OMS. Puede encontrar una plantilla de Resource Manager de ejemplo que incluye la extensión de VM del agente de OMS en la [Galería de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

La configuración JSON de una extensión de máquina virtual puede estar anidada en el recurso de máquina virtual o colocada en la raíz o nivel superior de una plantilla JSON de Resource Manager. La colocación de la configuración JSON afecta al valor del nombre y tipo del recurso. Para obtener más información, consulte el artículo sobre cómo [establecer el nombre y el tipo de recursos secundarios](../../azure-resource-manager/resource-manager-template-child-resource.md). 

En el siguiente ejemplo se da por supuesto que la extensión OMS está anidada dentro de los recursos de máquina virtual. Cuando se anidan los recursos de extensión, la plantilla JSON se coloca en el objeto `"resources": []` de la máquina virtual.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Al colocar la plantilla JSON de la extensión en la raíz de la plantilla, el nombre de recurso incluye una referencia a la máquina virtual principal, y el tipo refleja la configuración anidada.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Implementación de la CLI de Azure

La CLI de Azure puede utilizarse para implementar la extensión de máquina virtual del agente de OMS en una máquina virtual. Reemplace la clave OMS y el identificador de OMS por los de su área de trabajo OMS. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante la CLI de Azure. Para ver el estado de implementación de las extensiones de una máquina virtual determinada, ejecute el comando siguiente con la CLI de Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

El resultado de la ejecución de las extensiones se registra en el archivo siguiente:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Códigos de error y su significado

| Código de error | Significado | Acción posible |
| :---: | --- | --- |
| 10 | La máquina virtual ya está conectada a un área de trabajo de OMS | Para conectar la máquina virtual al área de trabajo especificada en el esquema de extensión, establezca stopOnMultipleConnections en false en la configuración pública o quite esta propiedad. Esta máquina virtual se factura una vez por cada área de trabajo a la que se conecta. |
| 11 | Configuración no válida proporcionada a la extensión | Siga los ejemplos anteriores para configurar todos los valores de propiedad necesarios para la implementación. |
| 12 | El administrador de paquetes de dpkg está bloqueado | Asegúrese de que todas las operaciones de actualización de dpkg en el equipo han finalizado e intente de nuevo. |
| 20 | | Habilitar llamado antes de tiempo | [Actualice el agente de Linux de Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) a la versión más reciente disponible. |
| 51 | Esta extensión no se admite en el sistema operativo de la máquina virtual | |
| 55 | No se puede conectar con el servicio Microsoft Operations Management Suite | Compruebe que el sistema tiene acceso a Internet o que se ha proporcionado un servidor proxy HTTP válido. Además, compruebe la validez del identificador del área de trabajo. |

Puede encontrar información adicional de solución de problemas en la [Guía de solución de problemas del agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#).

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/en-us/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
