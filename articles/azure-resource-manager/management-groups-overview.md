---
title: "Organización de los recursos con grupos de administración de Azure | Microsoft Docs"
description: "Obtenga información sobre los grupos de administración y cómo utilizarlos."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: rithorn
ms.openlocfilehash: 1264bf77b6d922f5beb22177d1ac63efa9386ef2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organización de los recursos con grupos de administración de Azure 

Si tiene varias suscripciones, puede organizarlas en contenedores llamados "grupos de administración" para ayudarle a administrar el acceso, la directiva y el cumplimiento en las suscripciones. Los grupos de administración proporcionan capacidad de administración de nivel empresarial a gran escala con independencia del tipo de suscripciones que tenga.  

La característica de grupo de administración está disponible en versión preliminar pública. Para empezar a usar los grupos de administración, inicie sesión en [Azure Portal](https://portal.azure.com) y busque **Grupos de administración** en la sección **Todos los servicios**. 

La compatibilidad de Azure Policy con los grupos de administración no está disponible todavía en la versión preliminar pública, se lanzará en las próximas semanas.  

A modo de ejemplo, puede aplicar directivas a un grupo de administración que limite las regiones disponibles para la creación de máquinas virtuales. Esta directiva se aplicaría a todos los grupos de administración, las suscripciones y los recursos de ese grupo de administración, al permitir únicamente que se creen máquinas virtuales en esa región.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Jerarquía de los grupos de administración y las suscripciones 

Puede compilar una estructura flexible de grupos de administración y suscripciones para organizar los recursos en una jerarquía para una administración unificada de las directivas y el acceso. El siguiente diagrama muestra un ejemplo de jerarquía que consta de grupos de administración y suscripciones organizados por departamentos.    

![Jerarquía](media/management-groups/MG_overview.png)

Al crear una jerarquía agrupada por departamentos, es posible asignar roles de [control de acceso basado en rol (RBAC) de Azure](../active-directory/role-based-access-control-what-is.md) que *hereden* en los departamentos de ese grupo de administración. Gracias a los grupos de administración, se reduce la carga de trabajo y el riesgo de errores, ya que el rol solo se asigna una vez. 

### <a name="important-facts-about-management-groups"></a>Hechos importantes acerca de los grupos de administración
- Se admiten 10 000 grupos de administración en un único directorio. 
- Un árbol de grupo de administración puede admitir hasta seis niveles de profundidad.
    - Este límite no incluye el nivel raíz o de suscripción.
- Cada grupo de administración admite solo un elemento primario.
- Cada grupo de administración puede tener varios elementos secundarios. 

## <a name="root-management-group-for-each-directory"></a>Un grupo de administración raíz para cada directorio

Cada directorio tiene un grupo de administración de nivel superior único denominado "raíz". Este grupo de administración raíz está integrado en la jerarquía de manera que contiene todos los grupos de administración y suscripciones. Este grupo de administración raíz permite que las directivas globales y las asignaciones de control de acceso basado en rol se apliquen en el nivel de directorio. Los [administradores de directorio necesitan elevar sus privilegios](../active-directory/role-based-access-control-tenant-admin-access.md) para ser inicialmente el propietario del grupo raíz. Una vez que el administrador es el propietario del grupo, puede asignar cualquier control de acceso basado en rol a otros usuarios o grupos del directorio para administrar la jerarquía.  

### <a name="important-facts-about-the-root-management-group"></a>Hechos importantes acerca de los grupos de administración raíz
- El nombre y el identificador del grupo de administración raíz tienen el identificador de Azure Active Directory de forma predeterminada. El nombre para mostrar se puede actualizar en cualquier momento para mostrarse diferente en Azure Portal. 
- Todas las suscripciones y los grupos de administración pueden incluirse en el grupo de administración raíz único del directorio.  
    - Para la administración global se recomienda que todos los elementos del directorio pertenezcan al grupo de administración raíz.  
    - Durante la versión preliminar pública, las suscripciones del directorio no son automáticamente elementos secundarios de la raíz.   
    - Durante la versión preliminar pública, las suscripciones nuevas no pertenecen de manera predeterminada y automática al grupo de administración raíz. 
- El grupo de administración raíz no se puede mover ni eliminar, a diferencia de los demás. 
  
## <a name="management-group-access"></a>Acceso al grupo de administración

Los grupos de administración de Azure admiten el [control de acceso basado en rol (RBAC)](../active-directory/role-based-access-control-what-is.md) para todos los accesos a recursos y las definiciones de roles. Estos permisos se heredan en los recursos secundarios que existen en la jerarquía.   

Aunque cualquier [rol de control de acceso basado en rol integrado](../active-directory/role-based-access-control-what-is.md#built-in-roles) puede asignarse a un grupo de administración, existen cuatro roles que se usan con frecuencia: 
- **propietario** tiene acceso completo a todos los recursos y cuenta con el derecho a delegar este acceso a otros. 
- **Colaborador**: puede crear y administrar todos los tipos de recursos de Azure, pero no puede conceder acceso a otros.
- **Colaborador de la directiva de recursos**: puede crear y administrar las directivas del directorio de los recursos.     
- **lector** solo puede ver los recursos existentes de Azure. 


## <a name="next-steps"></a>Pasos siguientes 
Para más información sobre los grupos de administración, consulte: 
- [Creación de grupos de administración para organizar los recursos de Azure](management-groups-create.md)
- [Cambio, eliminación y administración de los grupos de administración](management-groups-manage.md)
- [Instalación del módulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Revisión de las especificaciones de la API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Instalación de la extensión de la CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

