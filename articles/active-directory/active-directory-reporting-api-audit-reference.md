---
title: "Referencia de la API de auditoría de Azure Active Directory | Microsoft Docs"
description: "Introducción a la API de auditoría de Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/16/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: b1de516d907826d3e6ede0783649f6101b381852
ms.openlocfilehash: 261cce0b8424f73df4c7ca86784a14e95a8336f1


---
# <a name="azure-active-directory-audit-api-reference"></a>Referencia de la API de auditoría de Azure Active Directory
Este tema forma parte de una serie de temas sobre la API de informes de Azure Active Directory.  
La característica de generación de informes de Azure AD proporciona una API que permite acceder a los datos de auditoría mediante el uso de código o herramientas relacionadas.
El objetivo de este tema es ofrecer información de referencia sobre la **API de auditoría**.

Consulte:

* [Registros de auditoría](active-directory-reporting-azure-portal.md#audit-logs) para más información conceptual
* [Introducción a la API de generación de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md) para obtener más información sobre esta API

Para ver preguntas, problemas o comentarios, póngase en contacto con el equipo de [ayuda de informes de AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="who-can-access-the-data"></a>¿Quién puede acceder a los datos?
* Usuarios de los roles de administrador o lector de seguridad
* Administradores globales
* Cualquier aplicación que tenga autorización para acceder a la API (la autorización de aplicaciones solo puede configurarse según los permisos del administrador global).

## <a name="prerequisites"></a>Requisitos previos
Para acceder a este informe a través de la API de generación de informes, debe cumplir los siguientes requisitos:

* Una [edición de Azure Active Directory gratis o superior](active-directory-editions.md)
* Completar los [requisitos previos para acceder a la API de generación de informes de Azure AD](active-directory-reporting-api-prerequisites.md) 

## <a name="accessing-the-api"></a>Acceso a la API
Puede acceder a esta API a través del [Probador de Graph](https://graphexplorer2.cloudapp.net) o mediante programación con, por ejemplo, PowerShell. Para que PowerShell pueda interpretar correctamente la sintaxis de filtro de OData empleada en las llamadas a la API REST Graph de AAD, debe usar el carácter de acento grave para escapar $. El carácter de acento grave actúa como [carácter de escape de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), de modo que PowerShell puede hacer una interpretación literal del carácter $ y evitar que lo confunda con un nombre de variable de PowerShell (por ejemplo, $filter).

Este tema se centra en el Probador de Graph. Para ver un ejemplo de PowerShell, consulte este [script de PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Punto de conexión de API
Puede acceder a esta API con el siguiente URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

No hay ningún límite en el número de registros devueltos por la API de generación de informes de Azure AD (mediante la paginación de OData).
Para ver los límites de retención de los datos de informes, consulte [Directivas de retención de informes](active-directory-reporting-retention.md).

Esta llamada devuelve los datos en lotes. Cada lote tiene un máximo de 1000 registros.  
Para obtener el siguiente lote de registros, haga clic en el vínculo Siguiente. Obtenga la información de skiptoken desde el primer conjunto de registros devueltos. El token de omisión estará al final del conjunto de resultados.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtros admitidos
Puede limitar el número de registros que se devuelven mediante una llamada de API usando un filtro.  
En lo que respecta a los datos relacionados con las API de inicio de sesión, se admiten los siguientes filtros:

* **$top=\<número de registros que se devolverán\>**: limita el número de registros devueltos. Se trata de una operación costosa. No debe utilizar este filtro si quiere devolver miles de objetos.     
* **$filter=\<instrucción de filtro\>**: para especificar, en función de los campos de filtro compatibles, el tipo de registros que le interesan

## <a name="supported-filter-fields-and-operators"></a>Operadores y campos de filtro compatibles
Para especificar el tipo de registros que le interesa, puede crear una instrucción de filtro que puede contener uno o una combinación de los campos de filtro siguientes:

* [activityDate](#activitydate): define una fecha o un intervalo de fechas
* [activityType](#activitytype): define el tipo de una actividad
* [activity](#activity): define la actividad como cadena  
* [actor/name](#actorname): define el actor en el formato de nombre del actor
* [actor/objectid](#actorobjectid) : define el actor en el formato del identificador del actor   
* [actor/upn](#actorupn): define el actor en el formato de nombre principal de usuario (UPN) del actor 
* [target/name](#targetname): define el destino en el formato de nombre del actor
* [target/objectid](#targetobjectid) : define el destino en el formato de identificador del destino  
* [target/upn](#targetupn) : define el actor en el formato de nombre principal de usuario del actor (UPN)   

- - -
### <a name="activitydate"></a>activityDate
**Operadores compatibles**: eq, ge, le, gt y lt

**Ejemplo**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**Notas**:

datetime debe estar en formato UTC.

- - -
### <a name="activitytype"></a>activityType
**Operadores compatibles**: eq

**Ejemplo**:

    $filter=activityType eq 'User'    

**Notas**:

Distingue mayúsculas de minúsculas.

- - -
### <a name="activity"></a>activity
**Operadores compatibles**: eq, contains y startsWith

**Ejemplo**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**Notas**:

Distingue mayúsculas de minúsculas.

- - -
### <a name="actorname"></a>actor/name
**Operadores compatibles**: eq, contains y startsWith

**Ejemplo**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**Notas**:

No distingue mayúsculas de minúsculas.

- - -
### <a name="actorobjectid"></a>actor/objectid
**Operadores compatibles**: eq

**Ejemplo**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

- - -
### <a name="targetname"></a>target/namde
**Operadores compatibles**: eq, contains y startsWith

**Ejemplo**:

    $filter=targets/any(t: t/name eq 'some name')    

**Notas**:

No distingue mayúsculas de minúsculas.

- - -
### <a name="targetupn"></a>target/upn
**Operadores compatibles**: eq y startsWith

**Ejemplo**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Notas**:

* No distingue mayúsculas de minúsculas
* Al consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity es preciso agregar el espacio de nombres completo

- - -
### <a name="targetobjectid"></a>target/objectid
**Operadores compatibles**: eq

**Ejemplo**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>actor/upn
**Operadores compatibles**: eq y startsWith

**Ejemplo**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**Notas**:

* No distingue mayúsculas de minúsculas. 
* Debe agregar el espacio de nombres completo al consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Pasos siguientes
* ¿Quiere ver ejemplos de actividades del sistema filtradas? Consulte los [ejemplos de la API de auditoría de Azure Active Directory](active-directory-reporting-api-audit-samples.md).
* ¿Quiere obtener más información sobre la API de generación de informes de Azure AD? Consulte [Introducción a la API de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md).




<!--HONumber=Jan17_HO1-->


