---
title: Versiones de API del proveedor de recursos compatibles con perfiles en Azure Stack | Microsoft Docs
description: Obtenga información sobre la versión de Azure Resource Manager compatible con perfiles en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: db01df21c95ee41197344cec719f1c2ab2dfc2ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Versiones de API del proveedor de recursos compatibles con perfiles en Azure Stack

Un proveedor de recursos de Azure proporciona los recursos que puede implementar y administrar a través de Azure Resource Manager. Cada proveedor ofrece operaciones para trabajar con recursos. Algunos proveedores de recursos comunes incluyen Microsoft.Compute, que suministra las máquinas virtuales, Microsoft.Storage, que suministra los recursos de cuentas de almacenamiento y Microsoft.Web, que suministra recursos relacionados con aplicaciones web. Para más información, consulte [Tipos y proveedores de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

En la tabla siguiente correspondiente a cada proveedor de recursos se indica la versión compatible de la API para Azure Stack cuando se usan los perfiles.

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Puede usar el control de acceso basado en rol para administrar las acciones que los usuarios de su organización pueden realizar en los recursos. Este conjunto de operaciones le permite definir roles, asignar roles a los usuarios o grupos y obtener información sobre los permisos. Para más información, consulte [Autorización](https://docs.microsoft.com/rest/api/authorization/).

| Tipos de recursos | Versiones de API |
|---------------------|--------------------|
| Bloqueos | 2017-04-01 |
| Operaciones | 2015-07-01 |
| Permisos | 2015-07-01 |
| Asignaciones de directiva | 2016-12-01 (2017-06-01-preview) |
| Definiciones de directiva | 2016-12-01 |
| Operaciones del proveedor | 2015-07-01-preview |
| Asignaciones de roles | 2015-07-01 |
| Definiciones de roles | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Tipo de recurso | Versión de API |
|----------------------------------|----------------------|
| Suscripciones de proveedor delegado | 2015-06-01 - preview |
| Agregados de uso delegado | 2015-06-01 - preview |
| Gasto estimado de recursos | 2015-06-01 – preview |
| Operaciones | 2015-06-01 - preview |
| Agregados de uso de suscriptor | 2015-06-01 - preview |
| Agregados de uso | 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

Las API de Azure Compute proporcionan acceso mediante programación a las máquinas virtuales y sus recursos de apoyo. Para más información, consulte [Azure Compute](https://docs.microsoft.com/rest/api/compute/).

| Tipo de recurso | Versión de API |
|---------------------------------------------------------------|-------------|
| Conjuntos de disponibilidad | 2016-03-30 |
| Ubicaciones | 2016-03-30 |
| Ubicaciones/operaciones | 2016-03-30 |
| Ubicaciones/publicadores | 2016-03-30 |
| Ubicaciones/usos | 2016-03-30 |
| Ubicaciones/vmSizes | 2016-03-30 |
| Operaciones | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Virtual Machines/extensiones | 2016-03-30 |
| Conjuntos de escalado de máquina virtual | 2016-03-30 |
| Virtual Machine Scale Sets/extensiones | 2016-03-30 |
| Virtual Machine Scale Sets/Interfaces de red | 2016-03-30 |
| Virtual Machine Scale Sets/Máquinas virtuales | 2016-03-30 |
| Virtual Machines Scale Sets/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Tipo de recurso | Versión de API |
|------------------|-------------|
| Protección | 2015-04-01 |
| Contenido de protección | 2015-04-01 |
| Extracción de protección | 2015-04-01 |
| Elementos de la galería | 2015-04-01 |
| Operaciones | 2015-04-01 |
| Portal | 2015-04-01 |
| Search | 2015-04-01 |
| Sugerir | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Tipos de recursos | Versiones de API |
|--------------------|--------------------|
| Reglas de alertas | 2016-03-01 |
| Categorías de eventos | 2017-03-01-preview |
| Tipos de eventos | 2017-03-01-preview |
| Definiciones de métricas | 2016-03-01 |
| Operaciones | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Administración de los almacenes de claves, así como las claves, los secretos y los certificados dentro de los almacenes de claves. Para más información, consulte el artículo de [referencia de la API REST de Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/).

| Tipos de recursos | Versiones de API |
|-------------------------|--------------|
| Operaciones | 2016-10-01 |
| Almacenes | 2016-10-01 |
| Almacenes/directivas de acceso | 2016-10-01 |
| Almacenes/secretos | 2016-10-01 |

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

Administración de los almacenes de claves, así como las claves, los secretos y los certificados dentro de los almacenes de claves. Para más información, consulte el artículo de [referencia de la API REST de Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/).

| Tipos de recursos | Versiones de API |
|------------------|--------------------|
| Ubicaciones | 2017-02-01-preview |
| Ubicaciones/cuotas | 2017-02-01-preview |

### <a name="microsoftnetwork"></a>Microsoft.Network

El resultado de la llamada a las operaciones es una representación de la lista de operaciones de nube de red disponibles. Para más información, consulte la [API de REST de operación](https://docs.microsoft.com/rest/api/operation/).

| Tipos de recursos | Versiones de API |
|---------------------------|--------------|
| Conexiones | 2015-06-15 |
| Zonas DNS | 2016-04-01 |
| Equilibradores de carga | 2015-06-15 |
| Puerta de enlace de red local | 2015-06-15 |
| Ubicaciones | 2016-04-01 |
| Ubicación/operationResults | 2016-04-01 |
| Ubicaciones/operaciones | 2016-04-01 |
| Ubicaciones/usos | 2016-04-01 |
| Interfaces de red | 2015-06-15 |
| Grupos de seguridad de red | 2015-06-15 |
| Operaciones | 2015-06-15 |
| Dirección IP pública | 2015-06-15 |
| Tablas de ruta | 2015-06-15 |
| Puerta de enlace de red virtual | 2015-06-15 |
| Virtual Networks | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager le permite implementar y administrar la infraestructura para las soluciones de Azure. Puede organizar los recursos relacionados de grupos de recursos e implementar sus recursos con plantillas JSON. Si desea ver una introducción a la implementación y administración de recursos con Resource Manager, consulte [Información general sobre Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Tipos de recursos | Versiones de API |
|-----------------------------------------|-------------------|
| Registros de aplicación | 2015-01-01 |
| Comprobación de nombre de recurso | 2015-012016-09-01 |
| Proveedores delegados | 2015-01-01 |
| Proveedores delegados/ofertas | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Implementaciones | 2016-0209-01 |
| Implementaciones/operaciones | 2016-0209-01 |
| Metadatos de extensiones | 2015-01-01 |
| Vínculos | 2015-012016-09-01 |
| Ubicaciones | 2015-01-01 |
| Ofertas | 2015-01-01 |
| Operaciones | 2015-01-01 |
| Proveedores | 2015-012017-08-01 |
| Grupos de recursos | 2015-012016-09-01 |
| Recursos | 2015-012016-09-01 |
| Suscripciones | 2015-012016-09-01 |
| Suscripciones/ubicación | 2015-012016-09-01 |
| Suscripciones/resultados de operación | 2015-012016-09-01 |
| Suscripciones/proveedores | 2015-012017-08-01 |
| Suscripciones/grupos de recursos | 2015-012016-09-01 |
| Suscripciones/resourceGroups/recursos | 2015-012016-09-01 |
| Suscripciones/recursos | 2015-012016-09-01 |
| Suscripciones/tagNames | 2016-0609-01 |
| Suscripciones/tagNames/tagValues | 2016-0609-01 |
| Inquilinos | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

El proveedor de recursos de almacenamiento (SRP) le permite administrar mediante programación la cuenta de almacenamiento y las claves. Para más información, consulte la [referencia de API de REST de proveedor de recursos de Azure Storage](https://docs.microsoft.com/rest/api/storagerp/).

| Tipos de recursos | Versiones de API |
|-------------------------|--------------|
| Comprueba la disponibilidad del nombre | 2016-01-01 |
| Ubicaciones | 2016-01-01 |
| Ubicaciones/cuotas | 2016-01-01 |
| Operaciones | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Usos | 2016-01-01 |

## <a name="next-steps"></a>Pasos siguientes

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)  
