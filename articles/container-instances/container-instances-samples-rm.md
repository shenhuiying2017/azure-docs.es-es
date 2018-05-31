---
title: 'Plantillas de ejemplo de Azure Resource Manager: Azure Container Instances'
description: Plantillas de ejemplo de Azure Resource Manager para Azure Container Instances
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259742"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Plantillas de Azure Resource Manager para Azure Container Instances

Las siguientes plantillas de ejemplo implementan instancias de contenedor en diversas configuraciones.

Para opciones de implementación, consulte la sección [Implementación](#deployment). Si desea crear sus propias plantillas, la [Referencia de plantillas de Resource Manager][ref] de Azure Container Instances detalla el formato de la plantilla y las propiedades disponibles.

## <a name="sample-templates"></a>Plantillas de ejemplo

| | |
|-|-|
| **Applications** ||
| [Wordpress][app-wp] | Crea un sitio web de WordPress y su base de datos MySQL en una instancia del contenedor. El contenido del sitio de WordPress y la base de datos MySQL se conservan en un recurso compartido de Azure Files. |
| [MS NAV con IIS y SQL Server][app-nav] | Implementa un único contenedor de Windows con un entorno de Dynamics NAV y Dynamics 365 Business Central con las características completas. |
| **Volúmenes** ||
| [emptyDir][vol-emptydir] | Implementa dos contenedores de Linux que comparten un volumen de emptyDir. |
| [gitRepo][vol-gitrepo] | Implementa un contenedor de Linux que clona un repositorio de GitHub y lo monta como un volumen. |
| [secret][vol-secret] | Implementa un contenedor de Linux con un certificado PFX montado como un volumen secreto. |
| **Redes** ||
| [Contenedor con UDP expuesto][net-udp] | Implementa un contenedor de Windows o Linux que expone un puerto UDP. |
| [Contenedor de Linux con dirección IP pública][net-publicip] | Implementa un único contenedor de Linux accesible a través de una dirección IP pública. |
| **Recursos de Azure** ||
| [Creación de cuenta de Azure Storage y recurso compartido de archivos][az-files] | Usa la CLI de Azure en una instancia del contenedor para crear una cuenta de almacenamiento y un recurso compartido de Azure Files.

## <a name="deployment"></a>Implementación

Tiene varias opciones para implementar recursos con plantillas de Resource Manager:

[CLI de Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups