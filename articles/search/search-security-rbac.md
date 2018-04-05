---
title: Establecer roles RBAC para el acceso administrativo de Azure Search en el portal | Microsoft Docs
description: Control administrativo basado en roles en Azure Portal.
services: search
documentationcenter: ''
author: HeidiSteen
manager: cgronlun
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: a5f6395a0160402b3b0dfe95dc12b866854e70d9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="set-rbac-roles-for-administrative-access"></a>Configuración de roles RBAC para el acceso administrativo

Azure proporciona un [modelo de autorización global basado en roles](../active-directory/role-based-access-control-configure.md) para todos los servicios que se administran del portal o de las API de Resource Manager. Los roles de Propietario, Colaborador y Lector determinan el nivel de *administración de servicio* de los usuarios, grupos y entidades de seguridad de Active Directory asignados a cada rol. 

> [!Note]
> No hay ningún control de acceso basado en roles para proteger partes de un índice ni de un subconjunto de documentos. Para tener acceso basado en la identidad a través de los resultados de búsqueda, puede crear filtros de seguridad para recortar los resultados según la identidad y quitar los documentos para los que el solicitante no debe tener acceso. Para obtener más información, consulte [Filtros de seguridad](search-security-trimming-for-azure-search.md) y [Seguridad con Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Tareas de administración por rol

Para Azure Search, los roles están asociados a los niveles de permisos que admiten las siguientes tareas de administración:

| Rol | Task |
| --- | --- |
| Propietario |Crear o eliminar el servicio o cualquier objeto del servicio, entre los que se incluyen claves de API, índices, indexadores, orígenes de datos de indexadores y programas de indexadores.<p>Ver el estado del servicio, incluidos el tamaño de almacenamiento y los recuentos.<p>Agregar o eliminar miembros del rol (solo un propietario puede administrar la pertenencia a roles).<p>Los administradores de suscripciones y los propietarios de servicios tienen pertenencia automática al rol Propietario. |
| Colaborador |El mismo nivel de acceso que Propietario, menos el administración de roles RBAC. Por ejemplo, un usuario con el rol Colaborador puede crear o eliminar objetos, o ver y regenerar [claves de API](search-security-api-keys.md), pero no puede modificar pertenencias a roles. |
| [Rol integrado Colaborador de Search Service](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#search-service-contributor) | Equivalente al rol Colaborador. |
| Lector |Ver métricas e información esencial del servicio. Los miembros de este rol no pueden ver el índice, el indizador, el origen de datos ni la información de la clave.  |

Los roles no otorgan derechos de acceso al punto de conexión de servicio. Las operaciones del servicio Search, como la administración de índices, el rellenado del índice y las consultas en datos de búsqueda, se controlan mediante claves de API, no a través de roles. Para más información, consulte [Administración de claves de API](search-security-api-keys.md).

## <a name="see-also"></a>Otras referencias

+ [Administración mediante PowerShell](search-manage-powershell.md) 
+ [Consideraciones sobre el rendimiento y la optimización de Azure Search](search-performance-optimization.md)
+ [Introducción al control de acceso basado en roles en Azure Portal](../active-directory/role-based-access-control-what-is.md)