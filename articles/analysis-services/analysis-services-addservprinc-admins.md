---
title: Incorporación de una entidad de seguridad de servicio al rol de administrador del servidor de Azure Analysis Services | Microsoft Docs
description: Aprenda a agregar una entidad de seguridad de servicio de automatización al rol de administrador del servidor
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
ms.assetid: ''
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: owend
ms.openlocfilehash: 8e51b80e184b2b1ff24b1051b55088fbc54c271c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>Incorporación de una entidad de servicio al rol de administrador del servidor 

 Para automatizar las tareas de PowerShell desatendidas, una entidad de seguridad de servicio debe tener privilegios de **administrador del servidor** en el servidor de Analysis Services que se está administrando. En este artículo se describe cómo agregar una entidad de seguridad de servicio al rol de administrador de servidor en un servidor Azure AS.

## <a name="before-you-begin"></a>Antes de empezar
Antes de completar esta tarea, debe tener una entidad de seguridad de servicio registrada en Azure Active Directory.

[Creación de una entidad de servicio: Azure Portal](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Creación de entidad de servicio: PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Permisos necesarios
Para completar esta tarea, debe tener permisos de [administrador del servidor](analysis-services-server-admins.md) en el servidor Azure AS. 

## <a name="add-service-principle-to-server-administrators-role"></a>Incorporación de una entidad de servicio al rol de administradores de servidor

1. En SSMS, conéctese a su servidor Azure AS.
2. En **Propiedades del servidor** > **Seguridad**, haga clic en **Agregar**.
3. En **Seleccione un usuario o grupo**, busque la aplicación registrada por su nombre, selecciónela y, a continuación, haga clic en **Agregar**.

    ![Búsqueda de la cuenta de entidad de seguridad de servicio](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique el identificador de la cuenta de entidad de seguridad de servicio y, a continuación, haga clic en **Aceptar**.
    
    ![Búsqueda de la cuenta de entidad de seguridad de servicio](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Para las operaciones del servidor que usan cmdlets de AzureRm, la entidad de seguridad del servicio que ejecuta el programador también debe pertenecer al rol **Propietario** para el recurso en [Control de acceso basado en rol (RBAC) de Azure](../active-directory/role-based-access-control-what-is.md). 

## <a name="related-information"></a>Información relacionada

* [Descarga del módulo de PowerShell de SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Descarga de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


