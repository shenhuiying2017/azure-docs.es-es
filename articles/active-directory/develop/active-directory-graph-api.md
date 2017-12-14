---
title: API Graph de Azure Active Directory | Microsoft Docs
description: "Una guía de información general e inicio rápido para la API Graph que permite el acceso mediante programación a Azure AD a través de los extremos de la API de REST."
services: active-directory
documentationcenter: 
author: viv-liu
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: 815b9f75864ba3a08f623af12417391fba430f7d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-graph-api"></a>API Graph de Azure Active Directory
> [!IMPORTANT]
> Se recomienda encarecidamente que utilice [Microsoft Graph](https://graph.microsoft.io/) en lugar de la API de Azure AD Graph para tener acceso a recursos de Azure Active Directory. Nuestros esfuerzos de desarrollo ahora se centran en Microsoft Graph y no están previstas realizar mejoras adicionales para la API de Azure AD Graph. Hay un número muy limitado de escenarios para los que Azure AD Graph API todavía podría ser adecuado. Para obtener más información, consulte la entrada de blog [Microsoft Graph o Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) del centro de desarrollo de Office.
> 
> 

La API Graph de Azure Active Directory proporciona acceso mediante programación a Azure AD a través de los extremos de la API de REST. Las aplicaciones pueden usar la API Graph para ejecutar operaciones de creación, lectura, actualización y eliminación (CRUD) en objetos y datos de directorio. Por ejemplo, la API Graph admite las siguientes operaciones comunes en un objeto de usuario:

* Crear un usuario nuevo en un directorio
* Obtener las propiedades detalladas de un usuario, como sus grupos
* Actualizar las propiedades de un usuario, como su ubicación y número de teléfono, o cambiar su contraseña
* Consultar la pertenencia a grupos de un usuario para el acceso basado en roles
* Deshabilitar la cuenta de un usuario o eliminarla por completo

Además de en objetos de usuario, puede realizar las mismas operaciones en otros objetos, como grupos y aplicaciones. Para llamar a la API Graph en un directorio, la aplicación debe estar registrada con Azure AD y configurada para que permita el acceso al directorio. Esto se logra normalmente a través de un flujo de consentimiento de usuario o administrador.

Para empezar a usar la API Graph de Azure Active Directory, consulte la [Guía de inicio rápido de la API Graph](active-directory-graph-api-quickstart.md) o la [documentación interactiva de referencia sobre API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Características
La API Graph ofrece las siguientes características:

* **Extremos de la API de REST**: la API Graph es un servicio RESTful que se compone de extremos a los que se accede con solicitudes HTTP estándar. La API Graph admite tipos de contenido XML o notación de objetos JavaScript  (JSON) en solicitudes y respuestas. Para obtener más información, consulte [Referencia de la API Graph de REST de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autenticación con Azure AD**: todas las solicitudes enviadas a la API Graph se deben autenticar anexando un token web JSON (JWT) al encabezado Authorization de la solicitud. Para conseguir este token, es necesario enviar una solicitud al extremo del token de Azure AD y facilitar unas credenciales válidas. Puede usar el flujo de credenciales de cliente OAuth 2.0 o el flujo de concesión de código de autorización para conseguir un token con el que llamar a Graph. Para obtener más información, vea [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autorización basada en roles (RBAC)**: en la API Graph se usan grupos de seguridad para llevar a cabo la RBAC. Por ejemplo, si quiere saber si un usuario tiene acceso a un recurso determinado, la aplicación puede llamar a la operación [Comprobar pertenencia a grupos (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , que devuelve true o false.
* **Consulta diferencial**: si quiere ver los cambios hechos en un directorio entre dos períodos de tiempo sin tener que hacer consultas frecuentes a la API Graph, puede hacer una solicitud de consulta diferencial. Este tipo de solicitud solo devolverá los cambios hechos entre la solicitud de consulta diferencial anterior y la solicitud actual. Para más información, vea [Consulta diferencial de la API Graph de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Extensiones de directorio**: si va a desarrollar una aplicación que necesite leer o escribir propiedades únicas para objetos de directorio, puede registrar y usar valores de extensión con la API Graph. Por ejemplo, si su aplicación necesita una propiedad de usuario de Skype para cada usuario, puede registrar la nueva propiedad en el directorio para que esté disponible en todos los objetos de usuario. Para obtener más información, consulte [Extensiones de esquema de directorio de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protección con ámbitos de permiso**: API Graph de AAD expone ámbitos de permiso que permiten el acceso protegido o consentido a datos de AAD y admiten diversos tipos de aplicación de cliente, incluidos los siguientes:
  
  * Aquellos con una interfaz de usuario que reciben acceso delegado a los datos a través de una autorización del usuario con sesión iniciada (delegado).
  * Aquellos que usan el control de acceso basado en roles definido por las aplicaciones, como clientes de servicio o demonio (roles de aplicación).
    
    Tanto los ámbitos de permiso delegados como los de roles de aplicación representan un privilegio que se expone mediante API Graph, y las aplicaciones cliente pueden solicitarlos a través de las [características de permisos de registro de aplicación en Azure Portal](https://portal.azure.com). Los clientes pueden comprobar los ámbitos de permiso que se les han concedido inspeccionando la notificación de ámbito ("scp") recibida en el token de acceso para los permisos delegados y la notificación de roles ("roles") para los permisos de roles de aplicación. Obtenga más información sobre los [ámbitos de permiso de API Graph de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).

## <a name="scenarios"></a>Escenarios
La API Graph admite muchos escenarios de aplicación. Los siguientes escenarios son los más comunes:

* **Aplicación de línea de negocio (un solo inquilino)**: en este escenario, un desarrollador empresarial trabaja para una organización que tiene una suscripción a Office 365. El desarrollador compila una aplicación web que interactúa con Azure AD para hacer tareas tales como la asignación de una licencia a un usuario. Esta tarea requiere acceso a la API Graph, por eso el desarrollador registra la aplicación de inquilino único en Azure AD y configura los permisos de lectura y escritura para la API Graph. Luego, la aplicación se configura para que use sus propias credenciales o las del usuario que tenga iniciada la sesión para conseguir un token con el que llamar a la API Graph.
* **Software como aplicación de servicio (multiempresa)**: en este escenario, un fabricante de software independiente (ISV) desarrolla una aplicación web multiempresa hospedada que ofrece características de administración de usuarios para otras organizaciones que usan Azure AD. Estas características requieren acceso a objetos de directorio, por lo que la aplicación necesita llamar a la API Graph. El desarrollador registra la aplicación en Azure AD, la configura para requerir permisos de lectura y escritura para la API Graph, y habilita el acceso externo para que las demás organizaciones puedan permitir usar la aplicación en su directorio. Cuando se autentica un usuario de otra organización en la aplicación por primera vez, se le muestra un cuadro de diálogo de consentimiento con los permisos que solicita la aplicación.  La concesión de consentimiento proporciona a la aplicación los permisos solicitados para la API Graph en el directorio del usuario. Para obtener más información acerca del marco de consentimiento, consulte [Información general sobre el marco de consentimiento](active-directory-integrating-applications.md).

## <a name="see-also"></a>Otras referencias
[Inicio rápido para la API de Azure AD Graph](active-directory-graph-api-quickstart.md)

[Documentación de AD Graph de REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

