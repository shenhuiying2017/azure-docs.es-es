---
title: "Protección de datos y operaciones en Azure Search | Microsoft Docs"
description: "La seguridad de Azure Search se basa en el cumplimiento de SOC 2, el cifrado, la autenticación y el acceso a identidades mediante identificadores de seguridad de usuarios y grupos en los filtros de Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/14/2017
ms.author: heidist
ms.openlocfilehash: 23616c70a5fd336b743f5acfad2601a6c3e23fc4
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="data-security-and-controlled-access-to-azure-search-operations"></a>Seguridad de datos y acceso controlado en las operaciones de Azure Search

Azure Search es [compatible con SOC 2](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), con una arquitectura de seguridad completa que incluye seguridad física, transmisiones cifradas, almacenamiento cifrado y medidas de seguridad de software en toda la plataforma. Azure Search solo acepta solicitudes autenticadas en sus operaciones. De modo opcional, puede agregar controles de acceso por usuario en el contenido. En este artículo se trata la seguridad en cada capa, pero se centra principalmente en cómo se protegen los datos y las operaciones en Azure Search.

![Diagrama de bloques de las capas de seguridad](media/search-security-overview/azsearch-security-diagram.png)

Aunque Azure Search hereda las protecciones y medidas de seguridad de la plataforma de Azure, el mecanismo principal usado por el propio servicio es la autenticación basada en claves, donde el tipo de clave determina el nivel de acceso. Una clave puede ser una clave de administración o una clave de consulta para el acceso de solo lectura.

El acceso al servicio se basa en una sección transversal de los permisos concedidos por la clave (completos o solo de lectura), además de un contexto que define un ámbito de las operaciones. Todas las solicitudes se componen de una clave obligatoria, una operación y un objeto. Cuando se encadenan, los dos niveles de permisos y el contexto son suficientes para proporcionar seguridad en la gama completa de las operaciones del servicio. 

## <a name="physical-security"></a>Seguridad física

Los centros de datos de Microsoft proporcionan una seguridad física líder en la industria y son compatibles con una amplia gama de normas y reglas. Para más información, vaya a la página [Centros de datos globales](https://www.microsoft.com/cloud-platform/global-datacenters) o vea un vídeo corto sobre la seguridad de los centros de datos.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Transmisión y almacenamiento cifrados

Azure Search escucha en el puerto HTTPS 443. Las conexiones a los servicios de Azure están cifradas en toda la plataforma. 

En el almacenamiento de back-end utilizado para índices y otras construcciones, Azure Search aprovecha las funcionalidades de cifrado de estas plataformas. El [cumplimiento de SOC 2 de AICPA](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) completo está disponible para todos los servicios de búsqueda (nuevos y existentes) en todos los centros de datos que ofrecen Azure Search. Para revisar el informe completo, vaya a [Azure - and Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) (Informe de SOC 2 de tipo II de Azure y Azure Government).

El cifrado es transparente, con claves de cifrado administradas internamente y aplicadas universalmente. No se puede desactivar para servicios de búsqueda o índices específicos, ni administrar las claves directamente, ni proporcionar sus propias claves. 

## <a name="azure-wide-logical-security"></a>Seguridad lógica en todo Azure

Hay disponibles varios mecanismos de seguridad en Azure Stack que, por tanto, están disponibles automáticamente para los recursos de Azure Search que cree.

+ [Bloqueos en el nivel de suscripción y recurso para evitar la eliminación](../azure-resource-manager/resource-group-lock-resources.md)
+ [Control de acceso basado en roles (RBAC) para controlar el acceso a la información y las operaciones administrativas](../active-directory/role-based-access-control-what-is.md)

Todos los servicios de Azure admiten controles de acceso basado en roles (RBAC) para establecer niveles de acceso de forma coherente en todos los servicios. Por ejemplo, ver información confidencial como la clave de administración está restringido a los roles de Colaborador y Propietario, mientras que la visualización del estado del servicio está disponible para los miembros de cualquier rol. RBAC proporciona los roles de Propietario, Colaborador y Lector. De forma predeterminada, todos los administradores de servicios son miembros del rol de propietario.

## <a name="service-authentication"></a>Autenticación del servicio

Azure Search proporciona su propia metodología de autenticación. La autenticación se produce en cada solicitud y se basa en una clave de acceso que determina el ámbito de las operaciones. Una clave de acceso válida se considera una prueba de que la solicitud se origina desde una entidad de confianza. 

La autenticación por servicio existe en dos niveles: derechos completos y solo consulta. El tipo de clave determina el nivel de acceso que está en vigor.

|Clave|DESCRIPCIÓN|límites|  
|---------|-----------------|------------|  
|Administración|Concede derechos completos para todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar **índices**, **indexadores** y **orígenes de datos**.<br /><br /> Dos **claves de api** de administración, conocidas como claves *principal* y *secundaria* en el portal, que se generan cuando se crea el servicio y pueden volver a generarse individualmente a petición. Tener dos claves le permite la rotación de una clave mientras se usa la segunda clave para un acceso continuado al servicio.<br /><br /> Las claves de administración solo se especifican en los encabezados de la solicitud HTTP. No se puede colocar una **clave de api** de administración en una dirección URL.|Máximo 2 por servicio|  
|Consultar|Conceden acceso de solo lectura a índices y documentos y, normalmente, se distribuyen entre las aplicaciones cliente que emiten solicitudes de búsqueda.<br /><br /> Las claves de consulta se crean bajo petición. Puede crearlas manualmente en el portal o mediante programación con la [API de REST de administración](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Las claves de consulta se pueden especificar en un encabezado de solicitud HTTP para operaciones de búsqueda y sugerencias. Como alternativa, puede pasar una clave de consulta como un parámetro en una dirección URL. En función de cómo formule la solicitud la aplicación cliente, puede resultar más fácil pasar la clave como un parámetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50 por servicio|  

 Visualmente, no hay distinción entre las claves de administración y de consulta. Ambas claves son cadenas formadas por 32 caracteres alfanuméricos generados aleatoriamente. Si necesita saber qué tipo de clave se especifica en la aplicación, puede [comprobar los valores de las claves en el portal](https://portal.azure.com) o usar la [API de REST](https://docs.microsoft.com/rest/api/searchmanagement/) para devolver el valor y el tipo de clave.  

> [!NOTE]  
>  Se considera una práctica poco segura pasar datos confidenciales, como una `api-key` en el identificador URI de la solicitud. Por este motivo, Azure Search solo acepta una clave de consulta, como una `api-key`, en la cadena de consulta y debe evitar hacerlo a menos que el contenido del índice deba estar disponible públicamente. Como regla general, se recomienda pasar la `api-key` como un encabezado de solicitud.  

### <a name="how-to-find-the-access-keys-for-your-service"></a>Cómo buscar las claves de acceso del servicio

Puede obtener las claves de acceso en el portal o mediante la [API de REST de administración](https://docs.microsoft.com/rest/api/searchmanagement/). Para más información, consulte [Administración de claves](search-manage.md#manage-api-keys).

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Obtenga la lista de los [servicios de búsqueda](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de su suscripción.
3. Seleccione el servicio y, en la página de servicio, seleccione **Configuración** >**Claves** para ver las claves de administración y de consulta.

![Página del portal, configuración, sección claves](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>Acceso a los índices

En Azure Search, un índice individual no es un objeto protegible. En su lugar, el acceso a un índice se determina en la capa de servicio (acceso de lectura o escritura), junto con el contexto de una operación.

En el caso del acceso del usuario final, puede estructurar las solicitudes de consulta en la aplicación para conectarse con una clave de consulta, lo que hace que cualquier solicitud sea de solo lectura e incluir el índice específico utilizado por la aplicación. En una solicitud de consulta, no existe el concepto de combinación de índices ni de acceso simultáneo a varios índices para que todas las solicitudes tengan un único índice de destino por definición. Por lo tanto, la estructura de la solicitud de consulta en si misma (una clave y un índice único de destino) define el límite de seguridad.

Los accesos de administrador y de desarrollador a los índices no se diferencian: ambos necesitan tener acceso de escritura para crear, eliminar y actualizar objetos administrados por el servicio. Cualquier persona con una clave de administración del servicio puede leer, modificar o eliminar cualquier índice en el mismo servicio. Para la protección contra la eliminación accidental o malintencionada de índices, su control de código fuente interno para los recursos de código es la solución para revertir una eliminación o modificación de índices no deseada. Azure Search tiene conmutación por error dentro del clúster para garantizar la disponibilidad, pero no almacena ni ejecuta el código propietario utilizado para crear o cargar los índices.

Para soluciones multiinquilino que requieren límites de seguridad en el nivel de índice, estas soluciones suelen incluir un nivel intermedio que los clientes utilizan para controlar el aislamiento de índices. Para más información sobre casos de uso para varios inquilinos, consulte [Modelos de diseño de aplicaciones SaaS para varios inquilinos y Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Acceso de administración desde aplicaciones de cliente

La API de REST de administración de Azure Search es una extensión de Azure Resource Manager y comparte sus dependencias. Por lo tanto, Active Directory es un requisito previo para la administración del servicio Azure Search. Todas las solicitudes administrativas desde el código de cliente deben autenticarse con Azure Active Directory antes de que la solicitud llegue al administrador de recursos.

Las solicitudes de datos al punto de conexión del servicio Azure Search, como Create Index (API de REST del servicio Azure Search) o Search Documents (API de REST del servicio Azure Search), usan una clave de api en el encabezado de la solicitud.

Si el código de la aplicación controla las operaciones de administración del servicio, así como las operaciones de datos en documentos o índices de búsqueda, implemente dos métodos de autenticación en el código: la clave de acceso nativa para Azure Search y la metodología de autenticación de Active Directory requerida por el administrador de recursos. 

Para obtener información sobre la estructura de una solicitud de Azure Search, consulte [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/). Para más información acerca de los requisitos de autenticación del administrador de recursos, consulte [Uso de la API de autenticación del administrador de recursos para acceder a las suscripciones](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Acceso de usuario al contenido del índice

El acceso por usuario a los contenidos de un índice se implementa mediante filtros de seguridad en las consultas, que devuelven documentos asociados con una identidad de seguridad determinada. En lugar de funciones predefinidas y asignaciones de roles, el control de acceso basado en identidades se implementa como un filtro que recorta los resultados de búsqueda de documentos y contenido en función de las identidades. La tabla siguiente describe dos enfoques para recortar el contenido no autorizado de los resultados de la búsqueda.

| Enfoque | DESCRIPCIÓN |
|----------|-------------|
|[Recorte de seguridad basado en filtros de identidad](search-security-trimming-for-azure-search.md)  | Documenta el flujo de trabajo básico para implementar el control de acceso de identidades de usuario. Trata la incorporación de identificadores de seguridad a un índice y, a continuación, se explica el filtrado por ese campo para no incluir el contenido prohibido en los resultados. |
|[Recorte de seguridad basado en identidades de Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | En este artículo se amplía el artículo anterior y se proporcionan los pasos necesarios para recuperar identidades de Azure Active Directory (AAD), uno de los [servicios gratuitos](https://azure.microsoft.com/free/) de la plataforma de nube de Azure. |

## <a name="table-permissioned-operations"></a>Tabla: Operaciones con permisos

En la tabla siguiente se resumen las operaciones permitidas en Azure Search y la clave que desbloquea el acceso a una operación determinada.

| Operación | Permisos |
|-----------|-------------------------|
| Crear un servicio | Titular de la suscripción de Azure|
| Escalar un servicio | Clave de administración, RBAC propietario o colaborador en el recurso  |
| Eliminar un servicio | Clave de administración, RBAC propietario o colaborador en el recurso |
| Crear, modificar y eliminar objetos en el servicio: <br>Índices y partes de componentes (incluidas las definiciones del analizador, los perfiles de puntuación y las opciones de CORS), indexadores, orígenes de datos, sinónimos, proveedores de sugerencias. | Clave de administración, RBAC propietario o colaborador en el recurso  |
| Consultar un índice | Clave de administrador o de consulta (RBAC no es aplicable) |
| Consultar la información del sistema, como devolver estadísticas, recuentos y listas de objetos. | Clave de administración, RBAC en el recurso (propietario, colaborador y lector) |
| Administrar claves de administración | Clave de administración, RBAC propietario o colaborador en el recurso |
| Administrar claves de consulta |  Clave de administración, RBAC propietario o colaborador en el recurso Un lector de RBAC puede ver las claves de consulta. |


## <a name="see-also"></a>Otras referencias

+ [Introducción a .NET (se muestra el uso de una clave de administración para crear un índice)](search-create-index-dotnet.md)
+ [Introducción a REST (se muestra el uso de una clave de administración para crear un índice)](search-create-index-rest-api.md)
+ [Control de acceso basado en identidades mediante filtros de Azure Search](search-security-trimming-for-azure-search.md)
+ [Control de acceso basado en identidades de Active Directory mediante filtros de Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros de Azure Search](search-filters.md)