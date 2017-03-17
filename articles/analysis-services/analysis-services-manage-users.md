---
title: "Administración de usuarios en Azure Analysis Services | Microsoft Docs"
description: Aprenda a administrar usuarios en un servidor de Analysis Services en Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 039ed6f4be9f3e0f6b92e5a9f11e12392912df9d
ms.lasthandoff: 02/16/2017


---
# <a name="manage-users-in-azure-analysis-services"></a>Administración de usuarios en Azure Analysis Services
En Azure Analysis Services hay dos tipos de usuarios: los administradores del servidor y los usuarios de la base de datos. 

## <a name="server-administrators"></a>Administradores de servidor
Puede usar **Administradores de Analysis Services** en la hoja de control del servidor en Azure Portal o Propiedades del servidor en SSMS para administrar los administradores de servidor. Los administradores de Analysis Services son los administradores del servidor de bases de datos con derechos para tareas comunes de administración de bases de datos, como agregar y quitar bases de datos y administrar usuarios. De forma predeterminada, el usuario que crea el servidor en Azure Portal se agrega automáticamente como administrador de Analysis Services.

![Administradores del servidor en Azure Portal](./media/analysis-services-manage-users/aas-manage-users-admins.png)

También debe saber que:

* Windows Live ID no es un tipo de identidad compatible con Azure Analysis Services.  
* Los administradores de Analysis Services deben ser usuarios válidos de Azure Active Directory.
* Si se crea un servidor de Azure Analysis Services mediante plantillas de Azure Resource Manager, Administradores de Analysis Services toma una matriz JSON de los usuarios que deben agregarse como administradores.

Los administradores de Analysis Services pueden no ser los mismos que administradores de recursos de Azure, que pueden administrar los recursos de las suscripciones de Azure. Así se mantiene la compatibilidad con los XMLA y TMSL existentes para administrar los comportamientos en Analysis Services y para permitir segregar las obligaciones entre la administración de recursos de Azure y la administración de base de datos de Analysis Services. Para ver todos los roles y tipos de acceso de su recurso de Azure Analysis Services, utilice Access Control (IAM) en la hoja de control.

### <a name="to-add-administrators-using-azure-portal"></a>Incorporación de administradores mediante Azure Portal
1. En la hoja de control del servidor, haga clic en **Administradores de Analysis Services**.
2. En la hoja **\<nombreDeServidor > Administradores de Analysis Services**, haga clic en **Agregar**.
3. En la hoja **Agregar administradores de servidor**, seleccione las cuentas de los usuarios que desea agregar.

## <a name="database-users"></a>Usuarios de base de datos
Los usuarios de la base de datos se deben agregar a los roles de base de datos. Los roles definen los usuarios y grupos que tienen los mismos permisos para una base de datos. De forma predeterminada, las bases de datos de modelo tabular tienen un rol Usuarios de forma predeterminada con permisos de lectura. Para más información, consulte [Roles (SSAS tabular)](https://msdn.microsoft.com/library/hh213165.aspx).

Los usuarios de la base de datos modelo de Azure Analysis Services *deben estar en Azure Active Directory*. Los nombres de usuario especificados deben ser por dirección de correo electrónico de organización o UPN. Esto es diferente de las bases de datos locales de modelo tabular que admiten usuarios por nombres de usuario de dominio de Windows. 

Puede crear roles de base de datos, agregar usuarios y grupos a roles y configurar la seguridad de nivel de fila en SQL Server Data Tools (SSDT) o en SQL Server Management Studio (SSMS). También puede agregar o quitar usuarios a roles mediante los [cmdlets de Analysis Services PowerShell](https://msdn.microsoft.com/library/hh758425.aspx) o mediante [Tabular Model Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL).

**Script TMSL de ejemplo**

En este ejemplo, un usuario y un grupo se agregan al rol Usuarios de la base de datos de SalesBI.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en rol (RBAC)

Los administradores de suscripciones pueden utilizar **Access Control** en la hoja de control para configurar los roles. No pasa lo mismo con los administradores de servidor o los usuarios de base de datos, que, como se ha descrito anteriormente, se configuran en el nivel de base de datos o de servidor. 

![Control de acceso en Azure Portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Los roles se aplican a los usuarios o cuentas que necesitan realizar tareas que se pueden completar en el portal o mediante plantillas de Azure Resource Manager. Para más información, consulte el artículo sobre el [control de acceso basado en roles](../active-directory/role-based-access-control-what-is.md).

## <a name="next-steps"></a>Pasos siguientes
Si aún no ha implementado un modelo tabular en el servidor, ahora es buen momento para hacerlo. Para más información, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementación en Azure Analysis Services).

Si ha implementado un modelo en un servidor, está listo para conectarse a él mediante un cliente o un explorador. Para más información, consulte [Get data from Azure Analysis Services](analysis-services-connect.md) (Obtención de datos del servidor de Azure Analysis Services).


