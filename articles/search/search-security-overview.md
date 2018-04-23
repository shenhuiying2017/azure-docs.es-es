---
title: Protección de datos y operaciones en Azure Search | Microsoft Docs
description: La seguridad de Azure Search se basa en el cumplimiento de SOC 2, el cifrado, la autenticación y el acceso a identidades mediante identificadores de seguridad de usuarios y grupos en los filtros de Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: heidist
ms.openlocfilehash: 7db1b6c6f72f3cea7446b5f96dac7cd6e9b4252d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="security-and-controlled-access-in-azure-search"></a>Seguridad y acceso controlado en Azure Search

Azure Search es [compatible con SOC 2](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), con una arquitectura de seguridad completa que incluye seguridad física, transmisiones cifradas, almacenamiento cifrado y medidas de seguridad de software en toda la plataforma. Azure Search solo acepta solicitudes autenticadas en sus operaciones. De modo opcional, puede agregar controles de acceso por usuario en el contenido. En este artículo se trata la seguridad en cada capa, pero se centra principalmente en cómo se protegen los datos y las operaciones en Azure Search.

![Diagrama de bloques de las capas de seguridad](media/search-security-overview/azsearch-security-diagram.png)

## <a name="physical-security"></a>Seguridad física

Los centros de datos de Microsoft proporcionan una seguridad física líder en la industria y son compatibles con una amplia gama de normas y reglas. Para más información, vaya a la página [Centros de datos globales](https://www.microsoft.com/cloud-platform/global-datacenters) o vea un vídeo corto sobre la seguridad de los centros de datos.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Transmisión y almacenamiento cifrados

El cifrado se extiende a lo largo de la canalización de indización: desde las conexiones, pasando por la transmisión y hasta los datos indizados almacenados en Azure Search.

| Nivel de seguridad | DESCRIPCIÓN |
|----------------|-------------|
| Cifrado en tránsito | Azure Search escucha en el puerto HTTPS 443. Las conexiones a los servicios de Azure están cifradas en toda la plataforma. |
| Cifrado en reposo | El cifrado se internaliza totalmente en el proceso de indexación, sin impacto cuantificable a la hora de indexar el tiempo que tarda en completarse ni el tamaño de indexación. Se produce automáticamente en todas las indexaciones, incluidas las actualizaciones incrementales a un índice que no esté totalmente cifrado (creado antes de enero de 2018).<br><br>Internamente, el cifrado se basa en el [cifrado del servicio de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), que usa [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits.|
| [Cumplimiento normativo de SOC 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) | Todos los servicios de búsqueda de los centros que proporcionan Azure Search cumplen con la normativa AICPA SOC 2. Para revisar el informe completo, vaya a [Azure - and Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) (Informe de SOC 2 de tipo II de Azure y Azure Government). |

El cifrado es interno para Azure Search, con certificados y claves de cifrado que Microsoft administra internamente, y se aplica universalmente. No se puede activar ni desactivar el cifrado, administrar ni sustituir sus claves, ni ver la configuración de cifrado en el portal o mediante programación. 

El cifrado en reposo se anunció el 24 de enero de 2018 y se aplica a todos los niveles de servicio, incluidos los servicios (gratis) compartidos, y en todas las regiones. Para el cifrado completo, los índices creados antes de esa fecha deben quitarse y volver a generarse para que se produzca el cifrado. En caso contrario, solo se cifrarán los datos nuevos a partir del 24 de enero.

## <a name="azure-wide-logical-security"></a>Seguridad lógica en todo Azure

Hay disponibles varios mecanismos de seguridad en Azure Stack que, por tanto, están disponibles automáticamente para los recursos de Azure Search que cree.

+ [Bloqueos en el nivel de suscripción y recurso para evitar la eliminación](../azure-resource-manager/resource-group-lock-resources.md)
+ [Control de acceso basado en roles (RBAC) para controlar el acceso a la información y las operaciones administrativas](../role-based-access-control/overview.md)

Todos los servicios de Azure admiten controles de acceso basado en roles (RBAC) para establecer niveles de acceso de forma coherente en todos los servicios. Por ejemplo, ver información confidencial como la clave de administración está restringido a los roles de Colaborador y Propietario, mientras que la visualización del estado del servicio está disponible para los miembros de cualquier rol. RBAC proporciona los roles de Propietario, Colaborador y Lector. De forma predeterminada, todos los administradores de servicios son miembros del rol de propietario.

## <a name="service-access-and-authentication"></a>Autenticación y acceso al servicio

Mientras Azure Search hereda las medidas de seguridad de la plataforma Azure, también proporciona su propia autenticación basada en claves. Una clave de API es una cadena que se compone de letras y números generados aleatoriamente. El tipo de clave (administrador o consulta) determina el nivel de acceso. El envío de una clave válida se considera una prueba de que la solicitud se origina desde una entidad de confianza. Se usan dos tipos de claves para obtener acceso a su servicio de búsqueda:

* Administración (válida para cualquier operación de lectura y escritura en el servicio)
* Consulta (válida para operaciones de solo lectura, como las consultas en un índice)

Las claves de administración se crean cuando se aprovisiona el servicio. Hay dos claves de administración, designadas como *principal* y *secundaria*, pero en realidad son intercambiables. Todos los servicios tienen dos claves de administración, con el fin de que se pueda dejar de usar una de ellas sin perder el acceso al servicio. Puede volver a generar cualquiera de las claves de administración, pero no puede agregarla al recuento total de claves de administración. Hay un máximo de dos claves de administración por servicio de búsqueda.

Las claves de consulta se crean a medida que son necesarias y están diseñadas para las aplicaciones cliente que llaman directamente a Azure Search. Puede crear hasta 50 claves de consulta. En el código de la aplicación, especifique la dirección URL de búsqueda y una clave de API de consulta para permitir el acceso de solo lectura al servicio. El código de aplicación también especifica el índice que utiliza la aplicación. Juntos, el punto de conexión, una clave de API para el acceso de solo lectura y un índice de destino definen el nivel de acceso y ámbito de la conexión desde la aplicación cliente.

Se requiere autenticación en cada solicitud, y cada solicitud se compone de una clave obligatoria, una operación y un objeto. Cuando se encadenan, los dos niveles de permisos (completo y de solo lectura) y el contexto (por ejemplo, una operación de consulta en un índice) son suficientes para proporcionar seguridad en la gama completa de las operaciones del servicio. Para más información acerca de las claves, vea [Create and manage api-keys](search-security-api-keys.md) (Creación y administración de claves de API).

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
| Administrar claves de consulta |  Clave de administración, RBAC propietario o colaborador en el recurso  |


## <a name="see-also"></a>Otras referencias

+ [Introducción a .NET (se muestra el uso de una clave de administración para crear un índice)](search-create-index-dotnet.md)
+ [Introducción a REST (se muestra el uso de una clave de administración para crear un índice)](search-create-index-rest-api.md)
+ [Control de acceso basado en identidades mediante filtros de Azure Search](search-security-trimming-for-azure-search.md)
+ [Control de acceso basado en identidades de Active Directory mediante filtros de Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros de Azure Search](search-filters.md)