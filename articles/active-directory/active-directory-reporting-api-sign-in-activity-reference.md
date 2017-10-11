---
title: "Referencia de la API de informes de actividad de inicio de sesión de Azure Active Directory | Microsoft Docs"
description: "Referencia de la API de informes de actividad de inicio de sesión de Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: d83f1a899ba38dab2c1c1661adede87db6f88c20
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Referencia de la API de informes de actividad de inicio de sesión de Azure Active Directory
Este tema forma parte de una serie de temas sobre la API de informes de Azure Active Directory.  
La característica de generación de informes de Azure AD proporciona una API que permite acceder a los datos de informes de actividades de inicio de sesión mediante el uso de código o herramientas relacionadas.
El objetivo de este tema es ofrecer información de referencia sobre la **API de informes de actividad de inicio de sesión**.

Consulte:

* [Actividades de inicio de sesión](active-directory-reporting-azure-portal.md#activity-reports) para obtener más información
* [Introducción a la API de generación de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md) para obtener más información sobre esta API


## <a name="who-can-access-the-api-data"></a>¿Quién puede acceder a los datos de la API?
* Usuarios y entidades de seguridad de los roles de administrador o lector de seguridad
* Administradores globales
* Cualquier aplicación que tenga autorización para acceder a la API (la autorización de aplicaciones solo puede configurarse según los permisos del administrador global).

Para configurar el acceso a fin de que una aplicación tenga acceso a API de seguridad tales como eventos de inicio de sesión, use el siguiente PowerShell para agregar la entidad de servicio de las aplicaciones al rol de lector de seguridad

```PowerShell
Connect-MsolService
$servicePrincipal = Get-MsolServicePrincipal -AppPrincipalId "<app client id>"
$role = Get-MsolRole | ? Name -eq "Security Reader"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $servicePrincipal.ObjectId
```

## <a name="prerequisites"></a>Requisitos previos
Para acceder a este informe a través de la API de generación de informes, debe cumplir los siguientes requisitos:

* Tener una [edición de Azure Active Directory Premium P1 o P2](active-directory-editions.md)
* Completar los [requisitos previos para acceder a la API de generación de informes de Azure AD](active-directory-reporting-api-prerequisites.md) 

## <a name="accessing-the-api"></a>Acceso a la API
Puede acceder a esta API a través del [Probador de Graph](https://graphexplorer2.cloudapp.net) o mediante programación con, por ejemplo, PowerShell. Para que PowerShell pueda interpretar correctamente la sintaxis de filtro de OData empleada en las llamadas a la API REST Graph de AAD, debe usar el carácter de acento grave para escapar $. El carácter de acento grave actúa como [carácter de escape de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), de modo que PowerShell puede hacer una interpretación literal del carácter $ y evitar que lo confunda con un nombre de variable de PowerShell (por ejemplo, $filter).

Este tema se centra en el Probador de Graph. Para ver un ejemplo de PowerShell, consulte este [script de PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).

## <a name="api-endpoint"></a>Punto de conexión de API
Puede acceder a esta API con el siguiente URI base:  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



Debido al volumen de datos, esta API tiene un límite de un millón de registros devueltos. 

Esta llamada devuelve los datos en lotes. Cada lote tiene un máximo de 1000 registros.  
Para obtener el siguiente lote de registros, haga clic en el vínculo Siguiente. Obtenga la información de [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) desde el primer conjunto de registros devueltos. El token de omisión estará al final del conjunto de resultados.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtros admitidos
Puede limitar el número de registros que se devuelven mediante una llamada de API usando un filtro.  
En lo que respecta a los datos relacionados con las API de inicio de sesión, se admiten los siguientes filtros:

* **$top=\<número de registros que se devolverán\>**: limita el número de registros devueltos. Se trata de una operación costosa. No debe utilizar este filtro si quiere devolver miles de objetos.  
* **$filter=\<instrucción de filtro\>**: para especificar, en función de los campos de filtro compatibles, el tipo de registros que le interesan

## <a name="supported-filter-fields-and-operators"></a>Operadores y campos de filtro compatibles
Para especificar el tipo de registros que le interesa, puede crear una instrucción de filtro que puede contener uno o una combinación de los campos de filtro siguientes:

* [signinDateTime](#signindatetime) : define una fecha o un intervalo de fechas.
* [userId](#userid) : define un determinado usuario según su identificador.
* [userPrincipalName](#userprincipalname) : define un determinado usuario según el nombre principal del usuario (UPN).
* [appId](#appid) : define una determinada aplicación según el identificador de la aplicación.
* [appDisplayName](#appdisplayname) : define una determinada aplicación según el nombre para mostrar de la aplicación.
* [loginStatus](#loginStatus) : define el estado de los inicios de sesión (success o failure).

> [!NOTE]
> Al utilizar el Probador de Graph, tenga en cuenta que los campos de filtro distinguen mayúsculas y minúsculas.
> 
> 

Para limitar el ámbito de los datos devueltos, puede crear combinaciones de los filtros y campos de filtro disponibles. Por ejemplo, la siguiente instrucción devuelve los 10 principales registros entre el 1 y el 6 de julio de 2016:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**Operadores compatibles**: eq, ge, le, gt y lt

**Ejemplo**:

Uso de una fecha concreta

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



Uso de un intervalo de fechas    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Notas**:

El parámetro datetime debe estar en formato UTC 

- - -
### <a name="userid"></a>userId
**Operadores compatibles**: eq

**Ejemplo**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Notas**:

El valor de userId es un valor de cadena.

- - -
### <a name="userprincipalname"></a>userPrincipalName
**Operadores compatibles**: eq

**Ejemplo**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Notas**:

El valor de userPrincipalName es un valor de cadena.

- - -
### <a name="appid"></a>appId
**Operadores compatibles**: eq

**Ejemplo**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Notas**:

El valor de appId es un valor de cadena.

- - -
### <a name="appdisplayname"></a>appDisplayName
**Operadores compatibles**: eq

**Ejemplo**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Notas**:

El valor de appDisplayName es un valor de cadena.

- - -
### <a name="loginstatus"></a>loginStatus
**Operadores compatibles**: eq

**Ejemplo**:

    $filter=loginStatus+eq+'1'  


**Notas**:

Hay dos opciones para loginStatus: 0: success; 1: failure

- - -
## <a name="next-steps"></a>Pasos siguientes
* ¿Quiere ver ejemplos de actividades de inicio de sesión filtradas? Consulte [Azure Active Directory sign-in activity report API samples](active-directory-reporting-api-sign-in-activity-samples.md)(Ejemplos de API de informes de actividad de inicio de sesión de Azure Active Directory).
* ¿Quiere obtener más información sobre la API de generación de informes de Azure AD? Consulte [Introducción a la API de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md).

