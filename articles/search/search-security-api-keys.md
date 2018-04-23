---
title: Crear, administrar y proteger las claves de API de consulta para Azure Search | Microsoft Docs
description: Las claves de API controlan el acceso al punto de conexión de servicio. Las claves de administración conceden acceso de escritura. Las claves de consulta se pueden crear para el acceso de solo lectura.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: 83a082eb7a18c65a5824bf272e0397b18883277f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Crear y administrar claves de API para un servicio de Azure Search

Todas las solicitudes que se realizan a un servicio de búsqueda necesitan una clave de API generada de forma específica para el servicio. Dicha clave de API es el único mecanismo para autenticar el acceso del punto de conexión de su servicio de búsqueda. 

Una clave de API es una cadena que se compone de letras y números generados aleatoriamente. A través de [permisos basados en roles](search-security-rbac.md), puede eliminar o leer las claves, pero no se puede reemplazar una clave con una contraseña definida por el usuario ni usar Active Directory como metodología de autenticación principal para acceder a las operaciones de búsqueda. 

Se usan dos tipos de claves para obtener acceso al servicio de búsqueda: administración (lectura y escritura) y consulta (solo lectura).

|Clave|DESCRIPCIÓN|límites|  
|---------|-----------------|------------|  
|Administración|Concede derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indexadores y orígenes de datos.<br /><br /> Cuando se crea el servicio, se generan dos claves de administración, a las que se hace referencia como claves *principal* y *secundaria* en el portal. También se pueden volver a generar individualmente a petición. Tener dos claves le permite la rotación de una clave mientras se usa la segunda clave para un acceso continuado al servicio.<br /><br /> Las claves de administración solo se especifican en los encabezados de la solicitud HTTP. No se puede colocar una clave de API de administración en una dirección URL.|Máximo 2 por servicio|  
|Consultar|Conceden acceso de solo lectura a índices y documentos y, normalmente, se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.<br /><br /> Las claves de consulta se crean bajo petición. Puede crearlas manualmente en el portal o mediante programación con la [API de REST de administración](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Las claves de consulta se pueden especificar en un encabezado de solicitud HTTP para operaciones de búsqueda y sugerencias. Como alternativa, puede pasar una clave de consulta como un parámetro en una dirección URL. En función de cómo formule la solicitud la aplicación cliente, puede resultar más fácil pasar la clave como un parámetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50 por servicio|  

 Visualmente, no hay distinción entre las claves de administración y de consulta. Ambas claves son cadenas formadas por 32 caracteres alfanuméricos generados aleatoriamente. Si necesita saber qué tipo de clave se especifica en la aplicación, puede [comprobar los valores de las claves en el portal](https://portal.azure.com) o usar la [API de REST](https://docs.microsoft.com/rest/api/searchmanagement/) para devolver el valor y el tipo de clave.  

> [!NOTE]  
>  Se considera una práctica poco segura pasar datos confidenciales, como una `api-key` en el identificador URI de la solicitud. Por este motivo, Azure Search solo acepta una clave de consulta, como una `api-key`, en la cadena de consulta y debe evitar hacerlo a menos que el contenido del índice deba estar disponible públicamente. Como regla general, se recomienda pasar la `api-key` como un encabezado de solicitud.  

## <a name="find-api-keys-for-your-service"></a>Buscar claves de API para el servicio

Puede obtener las claves de acceso en el portal o mediante la [API de REST de administración](https://docs.microsoft.com/rest/api/searchmanagement/). Para obtener más información, consulte [Administrar claves de API de administración y consulta](search-security-api-keys.md).

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Obtenga la lista de los [servicios de búsqueda](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de su suscripción.
3. Seleccione el servicio y, en la página de servicio, seleccione **Configuración** >**Claves** para ver las claves de administración y de consulta.

![Página del portal, configuración, sección claves](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Regeneración de claves de administración

Se crean dos claves de administración para cada servicio para que pueda sustituir una clave primaria mediante la clave secundaria para acceso continuado.

Si vuelve a generar claves primarias y secundarias al mismo tiempo, las aplicaciones que usen una de las claves para acceder a operaciones de servicio ya no tendrán acceso al servicio.

1. En la página **Configuración** >**Claves**, copie la clave secundaria.
2. Para todas las aplicaciones, actualice la configuración de la clave de API para usar la clave secundaria.
3. Regenere la clave principal
4. Actualice todas las aplicaciones para usar la nueva clave principal.

## <a name="secure-api-keys"></a>Protección de las claves de API
La seguridad de las claves se garantiza mediante la restricción del acceso a través del portal o de las interfaces de Resource Manager (PowerShell o interfaz de línea de comandos). Como se ha indicado, los administradores de suscripciones pueden ver y volver a generar todas las claves de API. Como medida de precaución, revise las asignaciones de roles para conocer quién tiene acceso a las claves de administración.

+ En el panel de servicio, haga clic en **Control de acceso (IAM)** para ver las asignaciones de roles para su servicio.

Los miembros de los roles siguientes pueden ver y regenerar las claves: Propietario, Colaborador, [Colaboradores de Search Service](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Para tener acceso basado en la identidad a través de los resultados de búsqueda, puede crear filtros de seguridad para recortar los resultados según la identidad y quitar los documentos para los que el solicitante no debe tener acceso. Para obtener más información, consulte [Filtros de seguridad](search-security-trimming-for-azure-search.md) y [Seguridad con Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Otras referencias

+ [Control de acceso basado en rol en Azure Search](search-security-rbac.md)
+ [Administración mediante PowerShell](search-manage-powershell.md) 
+ [Artículo de rendimiento y optimización](search-performance-optimization.md)