---
title: "Solución de problemas de implementación de OpenShift en Azure | Microsoft Docs"
description: "Solución de problemas de implementación de OpenShift en Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: ea3664870480f6ed170972a5f52940dc4a852219
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshooting-openshift-deployment-in-azure"></a>Solución de problemas de implementación de OpenShift en Azure

Si el clúster de OpenShift no se implementa correctamente, hay algunas tareas de solución de problemas que se pueden realizar para restringir el problema. Consulte el estado de implementación y compárelo con la siguiente lista de códigos de salida.

- Código de salida 3: el nombre de usuario de suscripción de Red Hat/contraseña o id. de organización/clave de activación es incorrecto.
- Código de salida 4: el id. de grupo de Red Hat es incorrecto o no hay derechos disponibles.
- Código de salida 5: no se puede aprovisionar el volumen de grupo fino de Docker.
- Código de salida 6: error en la instalación del clúster de OpenShift.
- Código de salida 7: la instalación del clúster de OpenShift se realizó correctamente, pero hubo un error de configuración del proveedor de nube de Azure: problema de configuración maestra en nodo maestro.
- Código de salida 8: la instalación del clúster de OpenShift se realizó correctamente, pero hubo un error de configuración del proveedor de nube de Azure: problema de configuración de nodo en nodo maestro.
- Código de salida 9: la instalación del clúster de OpenShift se realizó correctamente, pero hubo un error de configuración del proveedor de nube de Azure: problema de configuración de nodo en nodo de infraestructura o aplicación.
- Código de salida 10: la instalación del clúster de OpenShift se realizó correctamente, pero hubo un error de configuración del proveedor de nube de Azure: se están corrigiendo los nodos maestros o el maestro no se puede definir como no programable.
- Código de salida 11: error al implementar las métricas
- Código de salida 12: error al implementar el registro

En el caso de los códigos de salida del 7 al 10, el clúster de OpenShift se instaló, pero hubo un error en la configuración del proveedor de nube de Azure. Puede usar SSH en el nodo maestro (Origin) o nodo de bastión (Container Platform) y, desde ahí, usar SSH en cada uno de los nodos del clúster y corregir los problemas.

Una causa frecuente de los errores con los códigos de salida del 7 al 9 es que la entidad de servicio no tenía los permisos adecuados para la suscripción o el grupo de recursos. Si el problema es ese, asigne los permisos correctos y vuelva a ejecutar manualmente el script que generó un error y los scripts subsiguientes.
Asegúrese de reiniciar el servicio que generó el error (por ejemplo, ystemctl restart atomic-openshift-node.service) antes de volver a ejecutar los scripts.

Para solucionar otros problemas, use SSH en el nodo principal en el puerto 2200 (Origin) o en el nodo de bastión en el puerto 22 (Container Platform). Debe estar en la raíz (sudo su-) y navegar al directorio siguiente: /var/lib/waagent/custom-script/download.

Debería ver una carpeta denominada '0' y '1'. En cada una de estas carpetas, verá dos archivos: stderr y stdout. Puede examinar estos archivos para determinar dónde se produjo el error.
