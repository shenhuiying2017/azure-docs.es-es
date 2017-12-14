---
title: "Actualización de recursos en las aplicaciones administradas de Azure | Microsoft Docs"
description: "Describe cómo trabajar con los recursos del grupo de recursos administrado de una aplicación administrada de Azure."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: d3c955d7be0e7e6d45751c0e685bad498e524d94
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Trabajo con los recursos del grupo de recursos administrado de una aplicación administrada de Azure

En este artículo se describe cómo actualizar los recursos que se implementan como parte de una aplicación administrada. Como editor de una aplicación administrada, tiene acceso a los recursos del grupo de recursos administrado. Para actualizar estos recursos, debe encontrar el grupo de recursos administrados asociado a una aplicación administrada y acceder a los recursos de ese grupo.

En este artículo se da por supuesto que ha implementado la aplicación administrada del proyecto de ejemplo de [Managed Web Application (IaaS) with Azure management services](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app)(Aplicación web administrada (IaaS) con los servicios de administración de Azure). Esa aplicación administrada incluye una máquina virtual **Standard_D1_v2**. Si no ha implementado esa aplicación administrada, puede usar igualmente este artículo para familiarizarse con los pasos para actualizar un grupo de recursos administrados.

La siguiente imagen muestra la aplicación administrada implementada.

![Aplicación administrada implementada](./media/update-managed-resources/deployed.png)

En este artículo, va a usar la CLI de Azure para:

* Identificar la aplicación administrada
* Identificar el grupo de recursos administrado
* Identificar los recursos de máquina virtual del grupo de recursos administrados
* Cambiar el tamaño de la máquina virtual (ya sea a un tamaño más pequeño si no la utiliza o uno mayor para admitir más carga)
* Asignar una directiva al grupo de recursos administrados que especifica las ubicaciones permitidas

## <a name="get-managed-application-and-managed-resource-group"></a>Obtención de la aplicación administrada y del grupo de recursos administrados

Para obtener las aplicaciones administradas en un grupo de recursos, use:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Para obtener el identificador del grupo de recursos administrado, use:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Cambio de tamaño de las máquinas virtuales en un grupo de recursos administrado

Para ver las máquinas virtuales en el grupo de recursos administrados, proporcione el nombre de ese grupo.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Para actualizar el tamaño de las máquinas virtuales, use:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Una vez completada la operación, compruebe que la aplicación se está ejecutando en Standard D2 v2.

![Aplicación administrada que usa Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Aplicación de directivas a un grupo de recursos administrado

Obtenga el grupo de recursos administrado y asigne una directiva en ese ámbito. La directiva **e56962a6-4747-49cd-b67b-bf8b01975c4c** es una directiva incorporada para especificar ubicaciones permitidas.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Para ver las ubicaciones permitidas, use:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

La asignación de directivas aparece en el portal.

![Visualización de la asignación de directiva](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte [Introducción a las aplicaciones administradas](overview.md).
* Para ver ejemplos de proyectos, consulte [Sample projects for Azure managed applications](sample-projects.md) (Ejemplos de proyectos para aplicaciones administradas de Azure).
