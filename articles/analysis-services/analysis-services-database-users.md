---
title: "Administración de usuarios y roles de base de datos en Azure Analysis Services | Microsoft Docs"
description: "Obtenga información sobre cómo administrar usuarios y roles de base de datos en un servidor de Analysis Services en Azure."
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
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: d0bc7d7514f111b4bbde33bd60ae21264bd797fc
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="manage-database-roles-and-users"></a>Administración de usuarios y roles de base de datos

En el nivel de base de datos modelo, todos los usuarios deben pertenecer a un rol. Los roles definen a los usuarios con permisos concretos para la base de datos modelo. Cualquier usuario o grupo de seguridad que se agregue a un rol debe tener una cuenta en un inquilino de Azure AD en la misma suscripción que el servidor.

La forma en que se definen los roles es distinta en función de la herramienta que se usa, pero el efecto es el mismo.

Los permisos de los roles incluyen:
*  **Administrador**: usuarios con permisos totales para la base de datos. Los roles de base de datos con permisos de administrador son distintos de los administradores de servidor.
*  **Proceso**: usuarios que se pueden conectar a la base de datos y realizan operaciones de proceso en ella, además de analizar los datos de base de datos modelo.
*  **Lectura**: usuarios que pueden usar una aplicación cliente para conectarse a los datos de una base de datos modelo y analizarlo.

Cuando se crea un proyecto de modelo tabular, crea roles y agrega usuarios o grupos a esos roles mediante el Administrador de roles de SSDT. Cuando se implementa en un servidor, se usa SSMS, [cmdlets de PowerShell para Analysis Services](https://msdn.microsoft.com/library/hh758425.aspx) o [Tabular Model Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) para agregar o quitar roles de miembros de usuario.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Para agregar o administrar roles y usuarios en SSDT  
  
1.  En SSDT > **Explorador de modelos tabulares**, haga clic con el botón derecho en **Roles**.  
  
2.  En **Administrador de roles**, haga clic en **Nuevo**.  
  
3.  Escriba un nombre para el rol.  
  
     De manera predeterminada, el nombre del rol predeterminado se enumera de manera incremental para cada rol nuevo. Se recomienda que escriba un nombre que identifique de forma clara el tipo de miembro, por ejemplo, Administrador financiero o Especialistas en recursos humanos.  
  
4.  Seleccione uno de estos permisos:  
  
    |Permiso|Descripción|  
    |----------------|-----------------|  
    |**None**|Los miembros no pueden modificar el esquema de modelo ni tampoco consultar datos.|  
    |**Lectura**|Los miembros pueden consultar datos (según los filtros de fila), pero no pueden modificar el esquema de modelo.|  
    |**Lectura y proceso**|Los miembros pueden consultar datos (según los filtros de nivel de fila) y ejecutar las operaciones Procesar y Procesar todo, pero no pueden modificar el esquema de modelo.|  
    |**Proceso**|Los modelos pueden ejecutar las operaciones Procesar y Procesar todo. No pueden modificar el esquema de modelo ni pueden consultar datos.|  
    |**Administrador**|Los miembros pueden modificar el esquema de modelo y consultar todos los datos.|   
  
5.  Si el rol que crea tiene permiso de Lectura o Lectura y proceso, puede agregar filtros de fila mediante una fórmula DAX. Haga clic en la pestaña **Filtros de fila**, seleccione una tabla y, luego, haga clic en el campo **Filtro DAX** y escriba una fórmula DAX.
  
6.  Haga clic en **Miembros** > **Agregar externo**.  
  
8.  En **Agregar miembro externo**, escriba usuarios o grupos en Azure AD del inquilino por dirección de correo electrónico. Una vez que hace clic en Aceptar y cierra el Administrador de roles, los roles y los miembros de rol aparecen en el Explorador de modelos tabulares. 
 
     ![Roles y usuarios en el Explorador de modelos tabulares](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implemente el servidor de Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Para agregar o administrar roles y usuarios en SSMS
Para agregar roles y usuarios a una base de datos modelo implementada, debe estar conectado al servidor como administrador del servidor y ya debe tener un rol de base de datos con permisos de administrador.

1. En el Explorador de objetos, haga clic con el botón derecho en **Rol** > **Nuevo rol**.

2. En **Crear rol**, escriba un nombre de rol y su descripción.

3. Seleccione un permiso.
   |Permiso|Descripción|  
   |----------------|-----------------|  
   |**Control total (administrador)**|Los miembros pueden modificar el esquema modelo, el proceso y pueden consultar todos los datos.| 
   |**Proceso de una base de datos**|Los modelos pueden ejecutar las operaciones Procesar y Procesar todo. No pueden modificar el esquema de modelo ni pueden consultar datos.|  
   |**Lectura**|Los miembros pueden consultar datos (según los filtros de fila), pero no pueden modificar el esquema de modelo.|  
  
4. Haga clic en **Pertenencia** y, luego, escriba un usuario o grupo en Azure AD del inquilino por dirección de correo electrónico.

     ![Agregar usuario](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Si el rol que crea tiene permiso de lectura, puede agregar filtros de fila mediante una fórmula DAX. Haga clic en **Filtros de fila**, seleccione una tabla y, luego, escriba una fórmula DAX en el campo **Filtro DAX**. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Para agregar roles y usuarios mediante un script de TMSL
Puede ejecutar un script de TMSL en la ventana XMLA en SSMS o mediante PowerShell. Use el comando [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) y el objeto [Roles](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl).

**Script TMSL de ejemplo**

En este ejemplo, se agrega un grupo y un usuario externo de B2B al rol de analista con permisos de lectura para la base de datos de SalesBI. Tanto el usuario externo como el grupo deben encontrarse en el mismo Azure AD de inquilino.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
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
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Para agregar roles y usuarios mediante PowerShell
El módulo [SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) proporciona cmdlets de administración de base de datos específicos de la tarea y el cmdlet Invoke-ASCmd de uso general que acepta un script o una consulta de Tabular Model Scripting Language (TMSL). Los cmdlets siguientes se usan para administrar usuarios y roles de base de datos.
  
|Cmdlet|Descripción|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Agrega un miembro a un rol de base de datos.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Quita un miembro de un rol de base de datos.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Ejecuta un script de TMSL.|

## <a name="row-filters"></a>Filtros de fila  
Los filtros de fila definen las filas de una tabla que los miembros de un rol determinado pueden consultar. Los filtros de fila están definidos para cada tabla en un modelo mediante fórmulas DAX.  
  
Los filtros de fila solo se pueden definir para los roles con permisos de lectura y lectura y proceso. De manera predeterminada, si no hay definido un filtro de fila para una tabla determinada, los miembros pueden consultar todas las filas de la tabla a menos que el filtrado cruzado se aplique desde otra tabla.
  
 Los filtros de fila requieren una fórmula DAX, que se debe evaluar con un valor TRUE o FALSE, para definir las filas a las que pueden consultar los miembros de ese rol determinado. No es posible consultar filas que no están incluidas en la fórmula DAX. Por ejemplo, en la tabla Cliente con la siguiente expresión de filtros de fila siguiente, *=Customers [Country] = "USA"*, los miembros del rol Ventas solo pueden ver los clientes en los Estados Unidos.  
  
Los filtros de fila se aplican a las rilas especificadas y a las filas relacionadas. Cuando una tabla tiene varias relaciones, los filtros aplican seguridad para la relación activa. Los filtros de fila forman una intersección con otros filtros de fila definidos para las tablas relacionadas, por ejemplo:  
  
|Tabla|Expresión DAX|  
|-----------|--------------------|  
|Region|=Region[Country]="EE. UU."|  
|ProductCategory|=ProductCategory[Name]="Bicicletas"|  
|Transacciones|=Transactions[Year]=2016|  
  
 El efecto neto es que los miembros pueden consultar filas de datos cuando el cliente se encuentra en EE. UU., la categoría de producto es "bicicletas" y el año es 2016. Los usuarios no pueden consultar transacciones fuera de los EE. UU., transacciones que no sean bicicletas o transacciones que no se hayan realizado el 2016, a menos que sean miembro de otro rol que conceda estos permisos.
  
 Puede usar el filtro, *=FALSE()*, para denegar el acceso a todas las filas de una tabla completa.

## <a name="next-steps"></a>Pasos siguientes
  [Administración de administradores del servidor](analysis-services-server-admins.md)   
  [Administración de Azure Analysis Services con PowerShell](analysis-services-powershell.md)  
  [Referencia Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

