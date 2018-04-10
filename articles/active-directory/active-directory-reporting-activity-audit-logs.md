---
title: Informes de actividad de auditoría en el portal de Azure Active Directory | Microsoft Docs
description: Introducción a los informes de actividad de auditoría en el portal de Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Informes de actividad de auditoría en el portal de Azure Active Directory 

Con los informes de Azure Active Directory (Azure AD), puede obtener toda la información que necesita para determinar cómo marcha el entorno.

La arquitectura de los informes de Azure AD consta de los siguientes componentes:

- **Actividad** 
    - **Actividades de inicio de sesión** : información sobre el uso de las aplicaciones administradas y las actividades de inicio de sesión de usuario
    - **Registros de auditoría**: información de la actividad del sistema sobre los usuarios y la administración de grupos, sus aplicaciones administradas y actividades de directorio.
- **Seguridad** 
    - **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. Para más información, consulte Inicios de no seguros.
    - **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. Para más información, consulte la sección Usuarios marcados en riesgo.

Este tema ofrece una visión general de las actividades de auditoría.
 
## <a name="who-can-access-the-data"></a>¿Quién puede acceder a los datos?
* Usuarios de los roles de administrador o lector de seguridad
* Administradores globales
* Los usuarios individuales (no administradores) pueden ver sus propias actividades


## <a name="audit-logs"></a>Registros de auditoría

Los registros de auditoría de Azure Active Directory proporcionan registros de las actividades del sistema de cara al cumplimiento.  
El primer punto de entrada a todos los datos de auditoría es **Registros de auditoría** en la sección **Actividad** de **Azure Active Directory**.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/61.png "Registros de auditoría")

Un registro de auditoría tiene una vista de lista predeterminada que muestra:

- la fecha y hora de la repetición
- el iniciador/actor (*quién*) de una actividad 
- la actividad (*qué*) 
- el destino

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/18.png "Registros de auditoría")

Puede personalizar la vista de lista, haga clic en **Columnas** en la barra de herramientas.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/19.png "Registros de auditoría")

Esto le permite mostrar los campos adicionales o quitar los campos que ya se están mostrando.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/21.png "Registros de auditoría")


Si hace clic en un elemento de la vista de lista, puede obtener todos los detalles disponibles sobre él.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/22.png "Registros de auditoría")


## <a name="filtering-audit-logs"></a>Filtrado de registros de auditoría

Para restringir los datos del informe a un nivel que se adapte a sus necesidades, puede filtrar los datos de auditoría con los siguientes campos:

- Intervalo de fechas
- Iniciado por (actor)
- Categoría
- Tipo de recurso de actividad
- Actividad

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/23.png "Registros de auditoría")


El filtro **Intervalo de fechas** permite definir un período de tiempo para los datos devueltos.  
Los valores posibles son:

- 1 mes
- 7 días
- 24 horas
- Personalizado

Cuando se selecciona un intervalo de tiempo personalizado, puede configurar una hora de inicio y una hora de finalización.

El filtro **Iniciado por** le permite definir el nombre de un actor o su nombre principal universal (UPN).

El filtro **Categoría** le permite seleccionar uno de los filtros siguientes:

- Todo
- Core category (Categoría principal)
- Core Directory (Directorio principal)
- Self-service Password Management (Administración de contraseñas de autorservicio)
- Self-service group management (Administración de grupos de autoservicio)
- Account provisioning- Automated password rollover (Aprovisionamiento de cuentas: sustitución automática de contraseñas)
- Invited users (Usuarios invitados)
- MIM service (Servicio MIM)
- Protección de identidad
- B2C

El filtro **Tipo de recurso de actividad** le permite seleccionar uno de los filtros siguientes:

- Todo 
- Grupo
- Directorio
- Usuario
- Application
- Directiva
- Dispositivo
- Otros

Cuando se selecciona **Grupo** como **Tipo de recurso de actividad**, obtendrá una categoría de filtro adicional que le permite proporcionar también un **Origen**:

- Azure AD
- O365


El filtro **Actividad** se basa en la selección de categoría y de tipo de recurso de actividad que realice. Puede seleccionar la actividad específica que desea ver o elegir todas. 

Para obtener la lista de todas las actividades de auditoría, use Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, donde $tenantdomain = el nombre de dominio, o bien consulte el artículo sobre [eventos del informe de auditoría](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Métodos abreviados de los registros de auditoría

Además de **Azure Active Directory**, Azure Portal proporciona dos puntos de entrada adicionales para auditar datos:

- Usuarios y grupos
- Aplicaciones empresariales

### <a name="users-and-groups-audit-logs"></a>Registros de auditoría de los usuarios y grupos

Con los informes de auditoría basadas en grupos y usuarios, puede obtener respuestas a preguntas como:

- ¿Qué tipos de actualizaciones se han aplicado a los usuarios?

- ¿Cuántos usuarios han cambiado?

- ¿Cuántas contraseñas han cambiado?

- ¿Qué ha hecho un administrador en un directorio?

- ¿Cuáles son los grupos que se han agregado?

- ¿Hay grupos con cambios de pertenencia?

- ¿Se han cambiado los propietarios del grupo?

- ¿Qué licencias se han asignado a un grupo o un usuario?

Si desea revisar los datos de auditoría relacionados con usuarios y grupos, puede buscar una vista filtrada en **Registros de auditoría** en la sección **Actividad** de **Usuarios y grupos**. Este punto de entrada tiene **Usuarios y grupos** como **Tipo de recurso de actividad** preseleccionado.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/93.png "Registros de auditoría")

### <a name="enterprise-applications-audit-logs"></a>Registros de auditoría de aplicaciones empresariales

Con los informes de auditoría basadas en aplicaciones, puede obtener respuestas a preguntas tales como:

* ¿Cuáles son las aplicaciones que se han agregado o actualizado?
* ¿Cuáles son las aplicaciones que se han quitado?
* ¿Ha cambiado el principal de servicio para una aplicación?
* ¿Se han cambiado los nombres de las aplicaciones?
* ¿Quién dio el consentimiento a una aplicación?

Si desea revisar los datos de auditoría relacionados con las aplicaciones, puede buscar una vista filtrada en **Registros de auditoría** en la sección **Actividad** de la hoja **Aplicaciones empresariales**. Este punto de entrada tiene **Aplicaciones empresariales** como **Tipo de recurso de actividad** preseleccionado.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/134.png "Registros de auditoría")

Puede filtrar aún más esta vista hasta simplemente **grupos** o simplemente **usuarios**.

![Registros de auditoría](./media/active-directory-reporting-activity-audit-logs/25.png "Registros de auditoría")



## <a name="azure-ad-audit-activity-list"></a>Lista de actividades de auditoría de Azure AD

En esta sección se proporciona una lista de todas las actividades que se pueden registrar. 


|Nombre de servicio|Categoría de auditoría|Tipo de recurso de actividad|Actividad|
|---|---|---|---|
|Account Provisioning (Aprovisionamiento de cuentas)|Administración de aplicaciones|Application|Administración|
|Account Provisioning (Aprovisionamiento de cuentas)|Administración de aplicaciones|Application|Operación de directorio|
|Account Provisioning (Aprovisionamiento de cuentas)|Administración de aplicaciones|Application|Exportación|
|Account Provisioning (Aprovisionamiento de cuentas)|Administración de aplicaciones|Application|Importar|
|Account Provisioning (Aprovisionamiento de cuentas)|Administración de aplicaciones|Application|Otros|
|Account Provisioning (Aprovisionamiento de cuentas)|Administración de aplicaciones|Application|Procesar custodia|
|Account Provisioning (Aprovisionamiento de cuentas)|Administración de aplicaciones|Application|Acción de reglas de sincronización|
|Proxy de aplicación|Administración de aplicaciones|Application|Agregar aplicación|
|Proxy de aplicación|Recurso|Recurso|Incorporación de certificado SSL de la aplicación|
|Proxy de aplicación|Recurso|Recurso|Eliminación del enlace SSL|
|Proxy de aplicación|Administración de aplicaciones|Application|Eliminar aplicación|
|Proxy de aplicación|Administración de directorios|Directorio|Deshabilitar SSO de escritorio|
|Proxy de aplicación|Administración de directorios|Directorio|Deshabilitar SSO de escritorio para un dominio específico|
|Proxy de aplicación|Administración de directorios|Directorio|Deshabilitar el proxy de aplicación|
|Proxy de aplicación|Administración de directorios|Directorio|Deshabilitar la autenticación de paso a través|
|Proxy de aplicación|Administración de directorios|Directorio|Habilitar SSO de escritorio|
|Proxy de aplicación|Administración de directorios|Directorio|Habilitar SSO de escritorio para un dominio específico|
|Proxy de aplicación|Administración de directorios|Directorio|Habilitar proxy de aplicación|
|Proxy de aplicación|Administración de directorios|Directorio|Habilitar la autenticación de paso a través|
|Proxy de aplicación|Recurso|Recurso|Registro del conector|
|Proxy de aplicación|Administración de aplicaciones|Application|Actualización de una aplicación|
|Proxy de aplicación|Administración de aplicaciones|Application|Actualización del modo de inicio de sesión único de la aplicación|
|Automated Password Rollover (Sustitución automática de contraseña)|Administración de aplicaciones|Application|Automated Password Rollover (Sustitución automática de contraseña)|
|B2C|Administración de aplicaciones|Application|Incorporación de permisos de aplicaciones V2|
|B2C|Autorización|Autorización|Incorporación de permisos de aplicaciones V2|
|B2C|Clave|Clave|Incorporación de una clave basada en un secreto ASCII a un contenedor de claves CPIM|
|B2C|Autorización|Autorización|Incorporación de una clave basada en un secreto ASCII a un contenedor de claves CPIM|
|B2C|Clave|Clave|Incorporación de una clave a un contenedor de claves CPIM|
|B2C|Autorización|Autorización|Incorporación de una clave a un contenedor de claves CPIM|
|B2C|Recurso|Recurso|AdminPolicyDatas-RemoveResources|
|B2C|Autorización|Autorización|AdminPolicyDatas-SetResources|
|B2C|Recurso|Recurso|AdminPolicyDatas-SetResources|
|B2C|Recurso|Recurso|AdminUserJourneys-GetResources|
|B2C|Autorización|Autorización|AdminUserJourneys-GetResources|
|B2C|Autorización|Autorización|AdminUserJourneys-RemoveResources|
|B2C|Recurso|Recurso|AdminUserJourneys-RemoveResources|
|B2C|Recurso|Recurso|AdminUserJourneys-SetResources|
|B2C|Autorización|Autorización|AdminUserJourneys-SetResources|
|B2C|Autorización|Autorización|Creación de IdentityProvider|
|B2C|Recurso|Recurso|Creación de IdentityProvider|
|B2C|Autorización|Autorización|Creación de aplicación V1|
|B2C|Administración de aplicaciones|Application|Creación de aplicación V1|
|B2C|Administración de aplicaciones|Application|Creación de aplicación V2|
|B2C|Autorización|Autorización|Creación de aplicación V2|
|B2C|Administración de directorios|Directorio|Creación de un dominio personalizado en el inquilino|
|B2C|Autorización|Autorización|Creación de un dominio personalizado en el inquilino|
|B2C|Autorización|Autorización|Creación de un nuevo AdminUserJourney|
|B2C|Recurso|Recurso|Creación de un nuevo AdminUserJourney|
|B2C|Recurso|Recurso|Creación de un json de recurso localizado|
|B2C|Autorización|Autorización|Creación de un json de recurso localizado|
|B2C|Autorización|Autorización|Creación de un nuevo IDP personalizado|
|B2C|Recurso|Recurso|Creación de un nuevo IDP personalizado|
|B2C|Recurso|Recurso|Creación de nuevo IDP|
|B2C|Autorización|Autorización|Creación de nuevo IDP|
|B2C|Autorización|Autorización|Creación o actualización de un recurso del directorio B2C|
|B2C|Recurso|Recurso|Creación o actualización de un recurso del directorio B2C|
|B2C|Recurso|Recurso|Creación de directiva|
|B2C|Autorización|Autorización|Creación de directiva|
|B2C|Autorización|Autorización|Creación de directiva de trustFramework|
|B2C|Recurso|Recurso|Creación de directiva de trustFramework|
|B2C|Autorización|Autorización|Creación de directiva de trustFramework con prefijo configurable|
|B2C|Recurso|Recurso|Creación de directiva de trustFramework con prefijo configurable|
|B2C|Recurso|Recurso|Creación de atributo de usuario|
|B2C|Autorización|Autorización|Creación de atributo de usuario|
|B2C|Autorización|Autorización|CreateTrustFrameworkPolicy|
|B2C|Recurso|Recurso|CreateTrustFrameworkPolicy|
|B2C|Autorización|Autorización|Crea o actualiza un nuevo AdminUserJourney|
|B2C|Recurso|Recurso|Eliminación de IDP|
|B2C|Autorización|Autorización|Eliminación de IDP|
|B2C|Recurso|Recurso|Eliminación de IdentityProvider|
|B2C|Autorización|Autorización|Eliminación de IdentityProvider|
|B2C|Administración de aplicaciones|Application|Eliminación de aplicación V1|
|B2C|Autorización|Autorización|Eliminación de aplicación V1|
|B2C|Administración de aplicaciones|Application|Eliminación de aplicación V2|
|B2C|Autorización|Autorización|Eliminación de aplicación V2|
|B2C|Autorización|Autorización|Eliminación de la concesión de permisos de aplicaciones V2|
|B2C|Administración de aplicaciones|Application|Eliminación de la concesión de permisos de aplicaciones V2|
|B2C|Recurso|Recurso|Eliminación de un recurso del directorio B2C|
|B2C|Autorización|Autorización|Eliminación de un recurso del directorio B2C|
|B2C|Clave|Clave|Eliminación de un contenedor de claves CPIM|
|B2C|Autorización|Autorización|Eliminación de un contenedor de claves CPIM|
|B2C|Clave|Clave|Eliminación de un contenedor de claves|
|B2C|Recurso|Recurso|Eliminación de directiva de trustFramework|
|B2C|Autorización|Autorización|Eliminación de directiva de trustFramework|
|B2C|Recurso|Recurso|Eliminación de atributo de usuario|
|B2C|Autorización|Autorización|Eliminación de atributo de usuario|
|B2C|Autorización|Autorización|Habilitar característica B2C|
|B2C|Administración de directorios|Directorio|Habilitar característica B2C|
|B2C|Recurso|Recurso|Obtención de recursos de directorio B2C en un grupo de recursos|
|B2C|Recurso|Recurso|Obtención de recursos de directorio B2C en una suscripción|
|B2C|Autorización|Autorización|Obtención de recursos de directorio B2C en una suscripción|
|B2C|Autorización|Autorización|Obtención de IDP personalizado|
|B2C|Recurso|Recurso|Obtención de IDP personalizado|
|B2C|Recurso|Recurso|Obtención de IDP|
|B2C|Autorización|Autorización|Obtención de IDP|
|B2C|Autorización|Autorización|Obtención de aplicaciones V1 y V2|
|B2C|Administración de aplicaciones|Application|Obtención de aplicaciones V1 y V2|
|B2C|Autorización|Autorización|Obtención de aplicación V1|
|B2C|Administración de aplicaciones|Application|Obtención de aplicación V1|
|B2C|Autorización|Autorización|Obtención de aplicaciones V1|
|B2C|Administración de aplicaciones|Application|Obtención de aplicaciones V1|
|B2C|Administración de aplicaciones|Application|Obtención de aplicación V2|
|B2C|Autorización|Autorización|Obtención de aplicación V2|
|B2C|Administración de aplicaciones|Application|Obtención de aplicaciones V2|
|B2C|Autorización|Autorización|Obtención de aplicaciones V2|
|B2C|Recurso|Recurso|Obtención de un recurso del directorio B2C|
|B2C|Autorización|Autorización|Obtención de un recurso del directorio B2C|
|B2C|Autorización|Autorización|Obtención de una lista de dominios personalizados en el inquilino|
|B2C|Administración de directorios|Directorio|Obtención de una lista de dominios personalizados en el inquilino|
|B2C|Autorización|Autorización|Obtención de un recorrido del usuario|
|B2C|Recurso|Recurso|Obtención de un recorrido del usuario|
|B2C|Recurso|Recurso|Obtención de notificaciones de aplicación permitidas para el recorrido del usuario|
|B2C|Autorización|Autorización|Obtención de notificaciones de aplicación permitidas para el recorrido del usuario|
|B2C|Recurso|Recurso|Obtención de notificaciones autoafirmadas permitidas para el recorrido del usuario|
|B2C|Autorización|Autorización|Obtención de notificaciones autoafirmadas permitidas para el recorrido del usuario|
|B2C|Recurso|Recurso|Obtención de notificaciones autoafirmadas permitidas de directiva|
|B2C|Autorización|Autorización|Obtención de notificaciones autoafirmadas permitidas de directiva|
|B2C|Recurso|Recurso|Obtención de la lista de notificaciones de salida disponibles|
|B2C|Autorización|Autorización|Obtención de la lista de notificaciones de salida disponibles|
|B2C|Recurso|Recurso|Obtención de definiciones de contenido para el recorrido del usuario|
|B2C|Autorización|Autorización|Obtención de definiciones de contenido para el recorrido del usuario|
|B2C|Autorización|Autorización|Obtención de IDP para un flujo de administración específico|
|B2C|Recurso|Recurso|Obtención de IDP para un flujo de administración específico|
|B2C|Clave|Clave|Obtención de metadatos de clave activos del contenedor de claves en JWK|
|B2C|Autorización|Autorización|Obtención de metadatos de clave activos del contenedor de claves en JWK|
|B2C|Clave|Clave|Obtención de metadatos del contenedor de claves|
|B2C|Recurso|Recurso|Obtención de una lista de todos los flujos de administración|
|B2C|Autorización|Autorización|Obtención de una lista de todos los flujos de administración|
|B2C|Autorización|Autorización|Obtención de lista de etiquetas para todos los flujos de administración de todos los usuarios|
|B2C|Recurso|Recurso|Obtención de lista de etiquetas para todos los flujos de administración de todos los usuarios|
|B2C|Recurso|Recurso|Obtención de la lista de inquilinos de un usuario|
|B2C|Autorización|Autorización|Obtención de la lista de inquilinos de un usuario|
|B2C|Recurso|Recurso|Obtención de notificaciones autoafirmadas de las cuentas locales|
|B2C|Autorización|Autorización|Obtención de notificaciones autoafirmadas de las cuentas locales|
|B2C|Recurso|Recurso|Obtención de un json de recurso localizado|
|B2C|Autorización|Autorización|Obtención de un json de recurso localizado|
|B2C|Autorización|Autorización|Obtención de operaciones del proveedor de recursos Microsoft.AzureActiveDirectory|
|B2C|Recurso|Recurso|Obtención de operaciones del proveedor de recursos Microsoft.AzureActiveDirectory|
|B2C|Recurso|Recurso|Obtención de directivas|
|B2C|Autorización|Autorización|Obtención de directivas|
|B2C|Recurso|Recurso|Obtención de directiva|
|B2C|Autorización|Autorización|Obtención de directiva|
|B2C|Administración de directorios|Directorio|Obtención de propiedades de recurso de un inquilino|
|B2C|Autorización|Autorización|Obtención de propiedades de recurso de un inquilino|
|B2C|Recurso|Recurso|Obtención de una lista de IDP compatible|
|B2C|Autorización|Autorización|Obtención de una lista de IDP compatible|
|B2C|Recurso|Recurso|Obtención de lista de IDP compatible del recorrido del usuario|
|B2C|Autorización|Autorización|Obtención de lista de IDP compatible del recorrido del usuario|
|B2C|Administración de directorios|Directorio|Obtención de información del inquilino|
|B2C|Autorización|Autorización|Obtención de información del inquilino|
|B2C|Administración de directorios|Directorio|Obtención de características permitidas del inquilino|
|B2C|Autorización|Autorización|Obtención de características permitidas del inquilino|
|B2C|Autorización|Autorización|Obtención de una lista de IDP personalizada definida por el inquilino|
|B2C|Recurso|Recurso|Obtención de una lista de IDP personalizada definida por el inquilino|
|B2C|Recurso|Recurso|Obtención de una lista de IDP definida por el inquilino|
|B2C|Autorización|Autorización|Obtención de una lista de IDP definida por el inquilino|
|B2C|Recurso|Recurso|Obtención de una lista de IDP local definida por el inquilino|
|B2C|Autorización|Autorización|Obtención de una lista de IDP local definida por el inquilino|
|B2C|Recurso|Recurso|Obtención de los detalles de inquilino de un usuario para la creación de recursos|
|B2C|Autorización|Autorización|Obtención de los detalles de inquilino de un usuario para la creación de recursos|
|B2C|Autorización|Autorización|Obtención de lista de inquilinos|
|B2C|Autorización|Autorización|Obtención de tenantDomains|
|B2C|Administración de directorios|Directorio|Obtención de tenantDomains|
|B2C|Recurso|Recurso|Obtención de la referencia cultural predeterminada para CPIM|
|B2C|Autorización|Autorización|Obtención de la referencia cultural predeterminada para CPIM|
|B2C|Recurso|Recurso|Obtención de los detalles de un flujo de administración|
|B2C|Autorización|Autorización|Obtención de los detalles de un flujo de administración|
|B2C|Autorización|Autorización|Obtención de la lista de UserJourneys para este inquilino|
|B2C|Recurso|Recurso|Obtención de la lista de UserJourneys para este inquilino|
|B2C|Autorización|Autorización|Obtención del conjunto de referencias culturales admitidas disponibles para CPIM|
|B2C|Recurso|Recurso|Obtención del conjunto de referencias culturales admitidas disponibles para CPIM|
|B2C|Autorización|Autorización|Obtención de directiva de trustFramework|
|B2C|Recurso|Recurso|Obtención de directiva de trustFramework|
|B2C|Autorización|Autorización|Obtención de directiva de trustFramework como xml|
|B2C|Recurso|Recurso|Obtención de directiva de trustFramework como xml|
|B2C|Recurso|Recurso|Obtención de atributo de usuario|
|B2C|Autorización|Autorización|Obtención de atributo de usuario|
|B2C|Autorización|Autorización|Obtención de atributos de usuario|
|B2C|Recurso|Recurso|Obtención de atributos de usuario|
|B2C|Autorización|Autorización|Obtención de lista de recorridos del usuario|
|B2C|Recurso|Recurso|Obtención de lista de recorridos del usuario|
|B2C|Autorización|Autorización|GetIEFPolicies|
|B2C|Recurso|Recurso|GetIEFPolicies|
|B2C|Autorización|Autorización|GetIdentityProviders|
|B2C|Recurso|Recurso|GetIdentityProviders|
|B2C|Recurso|Recurso|GetTrustFrameworkPolicy|
|B2C|Autorización|Autorización|GetTrustFrameworkPolicy|
|B2C|Clave|Clave|Obtiene un contenedor de claves CPIM en formato jwk|
|B2C|Autorización|Autorización|Obtiene un contenedor de claves CPIM en formato jwk|
|B2C|Clave|Clave|Obtiene una lista de contenedores de claves del inquilino|
|B2C|Autorización|Autorización|Obtiene una lista de contenedores de claves del inquilino|
|B2C|Autorización|Autorización|Obtiene el tipo de inquilino|
|B2C|Administración de directorios|Directorio|Obtiene el tipo de inquilino|
|B2C|Autenticación|Autenticación|Emisión de un token de acceso a la aplicación|
|B2C|Autenticación|Autenticación|Emisión de un código de autorización para la aplicación|
|B2C|Otros|Otros|Emisión de un código de autorización para la aplicación|
|B2C|Autenticación|Autenticación|Emisión de un id_token para la aplicación|
|B2C|Otros|Otros|Emisión de un id_token para la aplicación|
|B2C|Autorización|Autorización|MigrateTenantMetadata|
|B2C|Recurso|Recurso|MigrateTenantMetadata|
|B2C|Recurso|Recurso|Traslado de recursos|
|B2C|Autorización|Autorización|Revisión de IdentityProvider|
|B2C|Recurso|Recurso|Revisión de IdentityProvider|
|B2C|Recurso|Recurso|PutTrustFrameworkPolicy|
|B2C|Autorización|Autorización|PutTrustFrameworkPolicy|
|B2C|Autorización|Autorización|PutTrustFrameworkpolicy|
|B2C|Recurso|Recurso|PutTrustFrameworkpolicy|
|B2C|Recurso|Recurso|Eliminación de un recorrido del usuario|
|B2C|Autorización|Autorización|Eliminación de un recorrido del usuario|
|B2C|Autorización|Autorización|Restauración de una copia de seguridad del contenedor de claves CPIM|
|B2C|Clave|Clave|Restauración de una copia de seguridad del contenedor de claves CPIM|
|B2C|Administración de aplicaciones|Application|Recuperación de las concesiones de permisos de aplicaciones V2|
|B2C|Autorización|Autorización|Recuperación de las concesiones de permisos de aplicaciones V2|
|B2C|Administración de aplicaciones|Application|Recuperación de entidades de servicio de aplicaciones V2 en el inquilino actual|
|B2C|Autorización|Autorización|Recuperación de entidades de servicio de aplicaciones V2 en el inquilino actual|
|B2C|Clave|Clave|Guardar un contenedor de claves|
|B2C|Autorización|Autorización|Actualización de IDP personalizado|
|B2C|Recurso|Recurso|Actualización de IDP personalizado|
|B2C|Recurso|Recurso|Actualización de IDP|
|B2C|Autorización|Autorización|Actualización de IDP|
|B2C|Recurso|Recurso|Actualización de IDP local|
|B2C|Autorización|Autorización|Actualización de IDP local|
|B2C|Administración de aplicaciones|Application|Actualización de aplicación V1|
|B2C|Autorización|Autorización|Actualización de aplicación V1|
|B2C|Administración de aplicaciones|Application|Actualización de aplicación V2|
|B2C|Autorización|Autorización|Actualización de aplicación V2|
|B2C|Administración de aplicaciones|Application|Actualización de la concesión de permisos de aplicaciones V2|
|B2C|Autorización|Autorización|Actualización de la concesión de permisos de aplicaciones V2|
|B2C|Recurso|Recurso|Actualización de un recurso del directorio B2C|
|B2C|Recurso|Recurso|Actualización de directiva|
|B2C|Autorización|Autorización|Actualización de directiva|
|B2C|Recurso|Recurso|Actualización del estado de la suscripción|
|B2C|Recurso|Recurso|Actualización de atributo de usuario|
|B2C|Autorización|Autorización|Actualización de atributo de usuario|
|B2C|Clave|Clave|Carga de una clave cifrada CPIM|
|B2C|Autorización|Autorización|Carga de una clave cifrada CPIM|
|B2C|Autorización|Autorización|Autorización de usuario: la API está deshabilitada para el conjunto de características del inquilino|
|B2C|Autorización|Autorización|Autorización del usuario: se ha concedido acceso al usuario como "Administrador del inquilino"|
|B2C|Autorización|Autorización|Autorización del usuario: se han concedido al usuario derechos de acceso de "Usuarios autenticados"|
|B2C|Autenticación|Autenticación|Validación de las credenciales de la cuenta local|
|B2C|Recurso|Recurso|Validación de los recursos en movimiento|
|B2C|Autenticación|Autenticación|Validación de la autenticación del usuario|
|B2C|Administración de directorios|Directorio|Comprobación de si está habilitada la característica B2C|
|B2C|Autorización|Autorización|Comprobación de si está habilitada la característica B2C|
|B2C|Autorización|Autorización|Comprobación de si está habilitada la característica|
|B2C|Administración de directorios|Directorio|Comprobación de si está habilitada la característica|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Adición de OAuth2PermissionGrant|
|Core Directory (Directorio principal)|Administración de unidades administrativas|AdministrativeUnit|Adición de unidad administrativa|
|Core Directory (Directorio principal)|User Management|Usuario|Adición de la asignación de roles de aplicación al usuario|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Adición de la asignación de roles de aplicación al grupo|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Adición de la asignación de roles de aplicación a la entidad de servicio|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Agregar aplicación|
|Core Directory (Directorio principal)|Recurso|Recurso|Incorporación de un dispositivo|
|Core Directory (Directorio principal)|Recurso|Recurso|Adición de la configuración de dispositivo|
|Core Directory (Directorio principal)|Administración de roles|Rol|Incorporación de un miembro apto al rol|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Agregar grupo|
|Core Directory (Directorio principal)|Administración de unidades administrativas|AdministrativeUnit|Incorporación de un miembro a una unidad administrativa|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Agregar miembro a grupo|
|Core Directory (Directorio principal)|Administración de roles|Rol|Incorporación de un miembro a un rol|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Incorporación de un propietario a una aplicación|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Incorporación de un propietario a un grupo|
|Core Directory (Directorio principal)|Administración de directivas|Directiva|Incorporación de un propietario a una directiva|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Incorporación de un propietario a una entidad de servicio|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Agregar asociado a la compañía|
|Core Directory (Directorio principal)|Administración de directivas|Directiva|Add policy|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Incorporación de directiva a entidad de servicio|
|Core Directory (Directorio principal)|Recurso|Recurso|Incorporación de propietario registrado a dispositivo|
|Core Directory (Directorio principal)|Recurso|Recurso|Incorporación de usuarios registrados a dispositivo|
|Core Directory (Directorio principal)|Administración de roles|Rol|Incorporación de asignación de roles a definición de roles|
|Core Directory (Directorio principal)|Administración de roles|Rol|Incorporación de rol desde plantilla|
|Core Directory (Directorio principal)|Administración de roles|Rol|Incorporación de miembro con ámbito agregado a rol|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Agregar entidad de servicio|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Agregar credenciales de la entidad de servicio|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Incorporación de dominio sin comprobar|
|Core Directory (Directorio principal)|User Management|Usuario|Agregar usuario|
|Core Directory (Directorio principal)|User Management|Usuario|Incorporación de información de aplicación de teléfono para la autenticación sólida de usuarios|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Incorporación de dominio comprobado|
|Core Directory (Directorio principal)|User Management|Usuario|Cambiar la licencia de usuario|
|Core Directory (Directorio principal)|User Management|Usuario|Cambiar la contraseña de usuario|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Consentimiento a la aplicación|
|Core Directory (Directorio principal)|User Management|Usuario|Conversión de usuario federado en administrado|
|Core Directory (Directorio principal)|User Management|Usuario|Creación de contraseña de aplicaciones para usuario|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Creación de compañía|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Crear configuración de compañía.|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Creación de configuración de grupo|
|Core Directory (Directorio principal)|Administración de unidades administrativas|AdministrativeUnit|Eliminación de unidad administrativa|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Eliminar aplicación|
|Core Directory (Directorio principal)|User Management|Usuario|Eliminación de contraseña de aplicaciones para usuario|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Eliminar configuración de compañía.|
|Core Directory (Directorio principal)|Recurso|Recurso|Eliminar un dispositivo|
|Core Directory (Directorio principal)|Recurso|Recurso|Eliminación de la configuración del dispositivo|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Eliminar grupo|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Eliminación de la configuración de grupo|
|Core Directory (Directorio principal)|Administración de directivas|Directiva|Eliminación de directiva|
|Core Directory (Directorio principal)|User Management|Usuario|Eliminación de usuario|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Disminución del nivel de asociado|
|Core Directory (Directorio principal)|Recurso|Recurso|El dispositivo ya no es compatible|
|Core Directory (Directorio principal)|Recurso|Recurso|El dispositivo ya no está administrado|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Se ha eliminado el directorio|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Se ha eliminado el directorio de forma permanente|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Directorio programado para eliminación|
|Core Directory (Directorio principal)|User Management|Usuario|Deshabilitar cuenta|
|Core Directory (Directorio principal)|User Management|Usuario|Habilitar la autenticación sólida|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Finalización de la aplicación de licencias basadas en grupos a los usuarios|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Eliminación permanente de aplicación|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Eliminación permanente de grupo|
|Core Directory (Directorio principal)|User Management|Usuario|Eliminación permanente de usuario|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Promoción de la compañía al asociado|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Purgar propiedades de administración de derechos|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Eliminación de Oauth2PermissionGrant|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Eliminación de la asignación de roles de aplicación de grupo|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Eliminación de la asignación de roles de aplicación de la entidad de servicio|
|Core Directory (Directorio principal)|User Management|Usuario|Eliminación de la asignación de roles de aplicación del usuario|
|Core Directory (Directorio principal)|Administración de roles|Rol|Eliminación de miembros aptos del rol|
|Core Directory (Directorio principal)|Administración de unidades administrativas|AdministrativeUnit|Eliminación de miembro de la unidad administrativa|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Quitar miembro de grupo|
|Core Directory (Directorio principal)|Administración de roles|Rol|Eliminación de miembro del rol|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Eliminación de propietario de la aplicación|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Eliminación de propietario del grupo|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Eliminación de propietario de la entidad de servicio|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Eliminación de asociado de la compañía|
|Core Directory (Directorio principal)|Administración de directivas|Directiva|Eliminación de credenciales de directiva|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Eliminación de directiva de entidad de servicio|
|Core Directory (Directorio principal)|Recurso|Recurso|Eliminación de propietario registrado del dispositivo|
|Core Directory (Directorio principal)|Recurso|Recurso|Eliminación de usuarios registrados del dispositivo|
|Core Directory (Directorio principal)|Administración de roles|Rol|Eliminación de la asignación de roles de la definición de roles|
|Core Directory (Directorio principal)|Administración de roles|Rol|Eliminación de miembro con ámbito de rol|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Quitar entidad de servicio|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Quitar credenciales de la entidad de servicio|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Eliminación de dominio sin comprobar|
|Core Directory (Directorio principal)|User Management|Usuario|Eliminación de información de aplicación de teléfono para la autenticación sólida de usuarios|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Eliminación de dominio comprobado|
|Core Directory (Directorio principal)|User Management|Usuario|Restablecer contraseña de usuario|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Restauración de grupo|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Restauración de aplicación|
|Core Directory (Directorio principal)|User Management|Usuario|Restauración de usuario|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Revocación de consentimiento|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecer información sobre la compañía|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecimiento de la característica DirSync|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecimiento de la marca DirSyncEnabled|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecer asociación|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecimiento de umbral de eliminación accidental|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecimiento de la ubicación de datos permitida de empresa|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecimiento de característica de empresa multinacional habilitada|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecimiento de característica de directorio en inquilino|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecer la autenticación de dominio|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecer la configuración de la federación en el dominio|
|Core Directory (Directorio principal)|User Management|Usuario|Establecer el cambio forzado de la contraseña de usuario|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Establecimiento de licencia de grupo|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Establecer grupo que va a administrar el usuario.|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecimiento de directiva de contraseñas|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Establecimiento de propiedades de administración de derechos|
|Core Directory (Directorio principal)|User Management|Usuario|Establecimiento del administrador de usuarios|
|Core Directory (Directorio principal)|User Management|Usuario|Establecimiento de metadatos de token Oath de usuarios habilitado|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Empezar a aplicar licencias basadas en grupo a los usuarios|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Desencadenamiento del nuevo cálculo de licencias de grupo|
|Core Directory (Directorio principal)|User Management|Usuario|Actualización de la marca de tiempo StsRefreshTokenValidFrom|
|Core Directory (Directorio principal)|Administración de unidades administrativas|AdministrativeUnit|Actualización de la unidad administrativa|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Actualización de una aplicación|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Actualización de compañía|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Actualización de configuración de compañía|
|Core Directory (Directorio principal)|Recurso|Recurso|Actualización de dispositivo|
|Core Directory (Directorio principal)|Recurso|Recurso|Actualización de configuración de dispositivo|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Actualizar dominio|
|Core Directory (Directorio principal)|User Management|Usuario|Actualizar secretos externos|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Actualizar secretos externos|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Actualizar grupo|
|Core Directory (Directorio principal)|Administración de grupos|Grupo|Actualización de la configuración de grupo|
|Core Directory (Directorio principal)|Administración de directivas|Directiva|Actualización de directiva|
|Core Directory (Directorio principal)|Administración de roles|Rol|Actualización de rol|
|Core Directory (Directorio principal)|Administración de aplicaciones|Application|Actualización de entidad de servicio|
|Core Directory (Directorio principal)|User Management|Usuario|Actualizar usuario|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Comprobar dominio|
|Core Directory (Directorio principal)|Administración de directorios|Directorio|Comprobar dominio verificado por correo electrónico|
|Protección de identidad|User Management|Usuario|El administrador genera una contraseña temporal|
|Protección de identidad|User Management|Usuario|El administrador solicita al usuario que restablezca su contraseña|
|Protección de identidad|Otros|Otros|Descarga de un tipo de evento de riesgo único|
|Protección de identidad|Otros|Otros|Descarga de administradores y del estado de las notificaciones de ingesta semanales|
|Protección de identidad|Otros|Otros|Descarga de todos los tipos de eventos de riesgo|
|Protección de identidad|Otros|Otros|Descarga de los eventos de riesgo de usuarios gratuitos|
|Protección de identidad|Otros|Otros|Descarga de usuarios marcados en riesgo|
|Protección de identidad|Administración de directorios|Directorio|Incorporación|
|Protección de identidad|Administración de directivas|Directiva|Establecimiento de la directiva de registro MFA|
|Protección de identidad|Administración de directivas|Directiva|Establecimiento de la directiva de riesgo de inicio de sesión|
|Protección de identidad|Administración de directivas|Directiva|Establecimiento de la directiva de riesgo de usuario|
|Protección de identidad|Administración de directorios|Directorio|Actualización de la configuración de alertas|
|Protección de identidad|Administración de directorios|Directorio|Actualización de la configuración de la ingesta semanal|
|Invited Users (Usuarios invitados)|User Management|Usuario|Asignación de usuarios externos a la aplicación|
|Invited Users (Usuarios invitados)|Otros|Otros|Invitaciones por lotes procesadas|
|Invited Users (Usuarios invitados)|Otros|Otros|Invitaciones por lotes cargadas|
|Invited Users (Usuarios invitados)|User Management|Usuario|El correo electrónico no se ha enviado, el usuario canceló la suscripción|
|Invited Users (Usuarios invitados)|User Management|Usuario|Invitación a usuario externo|
|Invited Users (Usuarios invitados)|User Management|Usuario|Canje de invitación a usuario externo|
|Invited Users (Usuarios invitados)|User Management|Usuario|Creación de inquilino viral|
|Invited Users (Usuarios invitados)|User Management|Usuario|Creación de usuario viral|
|Microsoft Identity Manager (MIM)|Administración de grupos|Grupo|Agregar miembro|
|Microsoft Identity Manager (MIM)|Administración de grupos|Grupo|Crear grupo|
|Microsoft Identity Manager (MIM)|Administración de grupos|Grupo|Eliminar grupo|
|Microsoft Identity Manager (MIM)|Administración de grupos|Grupo|Quitar miembro|
|Microsoft Identity Manager (MIM)|Administración de grupos|Grupo|Actualizar grupo|
|Microsoft Identity Manager (MIM)|User Management|Usuario|Registro de contraseña de usuario|
|Microsoft Identity Manager (MIM)|User Management|Usuario|Restablecimiento de contraseña de usuario|
|Privileged Identity Management|Administración de roles|Rol|AccessReview_Review|
|Privileged Identity Management|Administración de roles|Rol|AccessReview_Update|
|Privileged Identity Management|Administración de roles|Rol|ActivationAborted|
|Privileged Identity Management|Administración de roles|Rol|ActivationApproved|
|Privileged Identity Management|Administración de roles|Rol|ActivationCanceled|
|Privileged Identity Management|Administración de roles|Rol|ActivationRequested|
|Privileged Identity Management|Administración de roles|Rol|Se agregó|
|Privileged Identity Management|Administración de roles|Rol|Assign|
|Privileged Identity Management|Administración de roles|Rol|Elevar|
|Privileged Identity Management|Administración de roles|Rol|Quitado|
|Privileged Identity Management|Administración de roles|Rol|Cambios en la configuración de roles|
|Privileged Identity Management|Administración de roles|Rol|ScanAlertsNow|
|Privileged Identity Management|Administración de roles|Rol|Signup|
|Privileged Identity Management|Administración de roles|Rol|Reducir los privilegios|
|Privileged Identity Management|Administración de roles|Rol|UpdateAlertSettings|
|Privileged Identity Management|Administración de roles|Rol|UpdateCurrentState|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Aprobación de una solicitud pendiente para unirse a un grupo|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Cancelación de una solicitud pendiente para unirse a un grupo|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Creación de directiva de administración del ciclo de vida|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Eliminación de una solicitud pendiente para unirse a un grupo|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Rechazo de una solicitud pendiente para unirse a un grupo|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Renovación de grupo|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Solicitud de unión a un grupo|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Establecimiento de propiedades de grupo dinámico|
|Self-service Group Management (Administración de grupos de autoservicio)|Administración de grupos|Grupo|Actualización de directiva de administración del ciclo de vida|
|Self-service Password Management (Administración de contraseñas de autorservicio)|User Management|Usuario|Bloqueado para el restablecimiento de contraseña de autoservicio|
|Self-service Password Management (Administración de contraseñas de autorservicio)|User Management|Usuario|Cambio de contraseña (autoservicio)|
|Self-service Password Management (Administración de contraseñas de autorservicio)|Administración de directorios|Directorio|Deshabilitar la reescritura de contraseñas para el directorio|
|Self-service Password Management (Administración de contraseñas de autorservicio)|Administración de directorios|Directorio|Habilitar la reescritura de contraseñas para el directorio|
|Self-service Password Management (Administración de contraseñas de autorservicio)|User Management|Usuario|Restablecimiento de contraseña (por parte del administrador)|
|Self-service Password Management (Administración de contraseñas de autorservicio)|User Management|Usuario|Restablecimiento de contraseña (autoservicio)|
|Self-service Password Management (Administración de contraseñas de autorservicio)|User Management|Usuario|Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio|
|Self-service Password Management (Administración de contraseñas de autorservicio)|User Management|Usuario|Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio|
|Self-service Password Management (Administración de contraseñas de autorservicio)|User Management|Usuario|Desbloqueo de la cuenta de usuario (autoservicio)|
|Self-service Password Management (Administración de contraseñas de autorservicio)|User Management|Usuario|Usuario registrado para el restablecimiento de contraseña de autoservicio|
|Términos de uso|Administración de directivas|Directiva|Aceptación de términos de uso|
|Términos de uso|Administración de directivas|Directiva|Creación de términos de uso|
|Términos de uso|Administración de directivas|Directiva|Rechazo de términos de uso|
|Términos de uso|Administración de directivas|Directiva|Eliminación de términos de uso|
|Términos de uso|Administración de directivas|Directiva|Edición de términos de uso|
|Términos de uso|Administración de directivas|Directiva|Publicación de los términos de uso|
|Términos de uso|Administración de directivas|Directiva|Cancelación de la publicación de los términos de uso|




## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre los informes, consulte [Informes de Azure Active Directory](active-directory-reporting-azure-portal.md).

