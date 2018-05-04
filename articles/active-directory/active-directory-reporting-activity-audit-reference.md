---
title: Referencia de la actividad de auditoría de Azure Active Directory (Azure AD) | Microsoft Docs
description: Obtenga una visión general de las actividades de auditoría que se pueden registrar en los registros de auditoría de Azure Active Directory (Azure AD).
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
ms.date: 04/19/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5b94092d1fbcb76210f132d4b08d131533e25397
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ad-audit-activity-reference"></a>Referencia sobre actividades de auditoría de Azure AD

Con los informes de Azure Active Directory (Azure AD), puede obtener toda la información que necesita para determinar cómo marcha el entorno.

La arquitectura de los informes de Azure AD consta de los siguientes componentes:

- **Actividad** 
    - **Actividades de inicio de sesión** : información sobre el uso de las aplicaciones administradas y las actividades de inicio de sesión de usuario
    - Los **registros de auditoría** proporcionan rastreabilidad mediante los registros de todos los cambios realizados por diversas características de Azure AD. Algunos ejemplos de registros de auditoría incluyen los cambios realizados en cualquier recurso de Azure AD como usuarios, aplicaciones, grupos, roles, directivas, autenticaciones etc.
- **Seguridad** 
    - **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. Para más información, consulte Inicios de no seguros.
    - **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. Para más información, consulte la sección Usuarios marcados en riesgo.

En este artículo se enumeran las actividades de auditoría que se pueden registrar en los registros de auditoría.
 


## <a name="access-reviews"></a>Revisiones de acceso

|Categoría de auditoría|Actividad|
|---|---|
|Administración de directorios|Adición de unidad administrativa|
|Administración de directorios|Incorporación de un miembro a una unidad administrativa|
|Administración de directorios|Eliminación de unidad administrativa|
|Administración de directorios|Eliminación de miembro de la unidad administrativa|
|Administración de directorios|Actualización de la unidad administrativa|
|Administración de directorios|Administración|
|User Management|Operación de directorio|
|User Management|Exportación|
|User Management|Importar|
|User Management|Otros|
|User Management|Procesar custodia|
|User Management|Acción de reglas de sincronización|
|User Management|Agregar aplicación|
|User Management|Eliminar aplicación|
|User Management|Actualización de una aplicación|
|User Management|Actualización del modo de inicio de sesión único de la aplicación|
|User Management|Automated Password Rollover (Sustitución automática de contraseña)|
|User Management|Incorporación de permisos de aplicaciones V2|
|User Management|Creación de aplicación V1|
|User Management|Creación de aplicación V2|
|User Management|Eliminación de aplicación V1|
|User Management|Eliminación de aplicación V2|
|User Management|Eliminación de la concesión de permisos de aplicaciones V2|
|User Management|Obtención de aplicaciones V1 y V2|
|User Management|Obtención de aplicación V1|
|User Management|Obtención de aplicaciones V1|
|User Management|Obtención de aplicación V2|
|User Management|Obtención de aplicaciones V2|




## <a name="account-provisioning"></a>Account Provisioning (Aprovisionamiento de cuentas)

|Categoría de auditoría|Actividad|
|---|---|
|Administración de aplicaciones|Recuperación de las concesiones de permisos de aplicaciones V2|
|Administración de aplicaciones|Recuperación de entidades de servicio de aplicaciones V2 en el inquilino actual|
|Administración de aplicaciones|Actualización de aplicación V1|
|Administración de aplicaciones|Actualización de aplicación V2|
|Administración de aplicaciones|Actualización de la concesión de permisos de aplicaciones V2|
|Administración de aplicaciones|Adición de OAuth2PermissionGrant|
|Administración de aplicaciones|Adición de la asignación de roles de aplicación a la entidad de servicio|

## <a name="application-proxy"></a>Proxy de aplicación

|Categoría de auditoría|Actividad|
|---|---|
|Administración de aplicaciones|Agregar aplicación|
|Administración de aplicaciones|Incorporación de un propietario a una aplicación|
|Administración de aplicaciones|Incorporación de un propietario a una entidad de servicio|
|Administración de aplicaciones|Incorporación de directiva a entidad de servicio|
|Administración de directorios|Agregar entidad de servicio|
|Administración de directorios|Agregar credenciales de la entidad de servicio|
|Administración de directorios|Consentimiento a la aplicación|
|Administración de directorios|Eliminar aplicación|
|Administración de directorios|Eliminación permanente de aplicación|
|Administración de directorios|Eliminación de Oauth2PermissionGrant|
|Administración de directorios|Eliminación de la asignación de roles de aplicación de la entidad de servicio|
|Administración de directorios|Eliminación de propietario de la aplicación|
|Recurso|Eliminación de propietario de la entidad de servicio|
|Recurso|Eliminación de directiva de entidad de servicio|
|Recurso|Quitar entidad de servicio|


## <a name="automated-password-rollover"></a>Automated Password Rollover (Sustitución automática de contraseña)

|Categoría de auditoría|Actividad|
|---|---|
|Administración de aplicaciones|Quitar credenciales de la entidad de servicio|


## <a name="b2c"></a>B2C

|Categoría de auditoría|Actividad|
|---|---|
|Administración de aplicaciones|Restauración de aplicación|
|Administración de aplicaciones|Revocación de consentimiento|
|Administración de aplicaciones|Actualización de una aplicación|
|Administración de aplicaciones|Actualizar secretos externos|
|Administración de aplicaciones|Actualización de entidad de servicio|
|Administración de aplicaciones|Emisión de un token de acceso a la aplicación|
|Administración de aplicaciones|Emisión de un código de autorización para la aplicación|
|Administración de aplicaciones|Emisión de un id_token para la aplicación|
|Administración de aplicaciones|Validación de las credenciales de la cuenta local|
|Administración de aplicaciones|Validación de la autenticación del usuario|
|Administración de aplicaciones|Incorporación de permisos de aplicaciones V2|
|Administración de aplicaciones|Incorporación de una clave basada en un secreto ASCII a un contenedor de claves CPIM|
|Administración de aplicaciones|Incorporación de una clave a un contenedor de claves CPIM|
|Administración de aplicaciones|AdminPolicyDatas-SetResources|
|Administración de aplicaciones|AdminUserJourneys-GetResources|
|Administración de aplicaciones|AdminUserJourneys-RemoveResources|
|Autenticación|AdminUserJourneys-SetResources|
|Autenticación|Creación de IdentityProvider|
|Autenticación|Creación de aplicación V1|
|Autenticación|Creación de aplicación V2|
|Autenticación|Creación de un dominio personalizado en el inquilino|
|Autorización|Creación de un nuevo AdminUserJourney|
|Autorización|Creación de un json de recurso localizado|
|Autorización|Creación de un nuevo IDP personalizado|
|Autorización|Creación de nuevo IDP|
|Autorización|Creación o actualización de un recurso del directorio B2C|
|Autorización|Creación de directiva|
|Autorización|Creación de directiva de trustFramework|
|Autorización|Creación de directiva de trustFramework con prefijo configurable|
|Autorización|Creación de atributo de usuario|
|Autorización|CreateTrustFrameworkPolicy|
|Autorización|Crea o actualiza un nuevo AdminUserJourney|
|Autorización|Eliminación de IDP|
|Autorización|Eliminación de IdentityProvider|
|Autorización|Eliminación de aplicación V1|
|Autorización|Eliminación de aplicación V2|
|Autorización|Eliminación de la concesión de permisos de aplicaciones V2|
|Autorización|Eliminación de un recurso del directorio B2C|
|Autorización|Eliminación de un contenedor de claves CPIM|
|Autorización|Eliminación de directiva de trustFramework|
|Autorización|Eliminación de atributo de usuario|
|Autorización|Habilitar característica B2C|
|Autorización|Obtención de recursos de directorio B2C en una suscripción|
|Autorización|Obtención de IDP personalizado|
|Autorización|Obtención de IDP|
|Autorización|Obtención de aplicaciones V1 y V2|
|Autorización|Obtención de aplicación V1|
|Autorización|Obtención de aplicaciones V1|
|Autorización|Obtención de aplicación V2|
|Autorización|Obtención de aplicaciones V2|
|Autorización|Obtención de un recurso del directorio B2C|
|Autorización|Obtención de una lista de dominios personalizados en el inquilino|
|Autorización|Obtención de un recorrido del usuario|
|Autorización|Obtención de notificaciones de aplicación permitidas para el recorrido del usuario|
|Autorización|Obtención de notificaciones autoafirmadas permitidas para el recorrido del usuario|
|Autorización|Obtención de notificaciones autoafirmadas permitidas de directiva|
|Autorización|Obtención de la lista de notificaciones de salida disponibles|
|Autorización|Obtención de definiciones de contenido para el recorrido del usuario|
|Autorización|Obtención de IDP para un flujo de administración específico|
|Autorización|Obtención de metadatos de clave activos del contenedor de claves en JWK|
|Autorización|Obtención de una lista de todos los flujos de administración|
|Autorización|Obtención de lista de etiquetas para todos los flujos de administración de todos los usuarios|
|Autorización|Obtención de la lista de inquilinos de un usuario|
|Autorización|Obtención de notificaciones autoafirmadas de las cuentas locales|
|Autorización|Obtención de un json de recurso localizado|
|Autorización|Obtención de operaciones del proveedor de recursos Microsoft.AzureActiveDirectory|
|Autorización|Obtención de directivas|
|Autorización|Obtención de directiva|
|Autorización|Obtención de propiedades de recurso de un inquilino|
|Autorización|Obtención de una lista de IDP compatible|
|Autorización|Obtención de lista de IDP compatible del recorrido del usuario|
|Autorización|Obtención de información del inquilino|
|Autorización|Obtención de características permitidas del inquilino|
|Autorización|Obtención de una lista de IDP personalizada definida por el inquilino|
|Autorización|Obtención de una lista de IDP definida por el inquilino|
|Autorización|Obtención de una lista de IDP local definida por el inquilino|
|Autorización|Obtención de los detalles de inquilino de un usuario para la creación de recursos|
|Autorización|Obtención de lista de inquilinos|
|Autorización|Obtención de tenantDomains|
|Autorización|Obtención de la referencia cultural predeterminada para CPIM|
|Autorización|Obtención de los detalles de un flujo de administración|
|Autorización|Obtención de la lista de UserJourneys para este inquilino|
|Autorización|Obtención del conjunto de referencias culturales admitidas disponibles para CPIM|
|Autorización|Obtención de directiva de trustFramework|
|Autorización|Obtención de directiva de trustFramework como xml|
|Autorización|Obtención de atributo de usuario|
|Autorización|Obtención de atributos de usuario|
|Autorización|Obtención de lista de recorridos del usuario|
|Autorización|GetIEFPolicies|
|Autorización|GetIdentityProviders|
|Autorización|GetTrustFrameworkPolicy|
|Autorización|Obtiene un contenedor de claves CPIM en formato jwk|
|Autorización|Obtiene una lista de contenedores de claves del inquilino|
|Autorización|Obtiene el tipo de inquilino|
|Autorización|MigrateTenantMetadata|
|Autorización|Revisión de IdentityProvider|
|Autorización|PutTrustFrameworkPolicy|
|Autorización|PutTrustFrameworkpolicy|
|Autorización|Eliminación de un recorrido del usuario|
|Autorización|Restauración de una copia de seguridad del contenedor de claves CPIM|
|Autorización|Recuperación de las concesiones de permisos de aplicaciones V2|
|Autorización|Recuperación de entidades de servicio de aplicaciones V2 en el inquilino actual|
|Autorización|Actualización de IDP personalizado|
|Autorización|Actualización de IDP|
|Autorización|Actualización de IDP local|
|Autorización|Actualización de aplicación V1|
|Autorización|Actualización de aplicación V2|
|Autorización|Actualización de la concesión de permisos de aplicaciones V2|
|Autorización|Actualización de directiva|
|Autorización|Actualización de atributo de usuario|
|Autorización|Carga de una clave cifrada CPIM|
|Autorización|Autorización de usuario: la API está deshabilitada para el conjunto de características del inquilino|
|Autorización|Autorización del usuario: se ha concedido acceso al usuario como "Administrador del inquilino"|
|Autorización|Autorización del usuario: se han concedido al usuario derechos de acceso de "Usuarios autenticados"|
|Autorización|Comprobación de si está habilitada la característica B2C|
|Autorización|Comprobación de si está habilitada la característica|
|Autorización|Creación de programa|
|Autorización|Eliminación de programa|
|Autorización|Vinculación al control del programa|
|Autorización|Incorporación a las revisiones de acceso de Azure AD|
|Autorización|Desvinculación al control del programa|
|Autorización|Actualización de programa|
|Autorización|Deshabilitar SSO de escritorio|
|Autorización|Deshabilitar SSO de escritorio para un dominio específico|
|Autorización|Deshabilitar el proxy de aplicación|
|Autorización|Deshabilitar la autenticación de paso a través|
|Autorización|Habilitar SSO de escritorio|
|Administración de directorios|Habilitar SSO de escritorio para un dominio específico|
|Administración de directorios|Habilitar proxy de aplicación|
|Administración de directorios|Habilitar la autenticación de paso a través|
|Administración de directorios|Creación de un dominio personalizado en el inquilino|
|Administración de directorios|Habilitar característica B2C|
|Administración de directorios|Obtención de una lista de dominios personalizados en el inquilino|
|Administración de directorios|Obtención de propiedades de recurso de un inquilino|
|Administración de directorios|Obtención de información del inquilino|
|Administración de directorios|Obtención de características permitidas del inquilino|
|Administración de directorios|Obtención de tenantDomains|
|Clave|Obtiene el tipo de inquilino|
|Clave|Comprobación de si está habilitada la característica B2C|
|Clave|Comprobación de si está habilitada la característica|
|Clave|Agregar asociado a la compañía|
|Clave|Incorporación de dominio sin comprobar|
|Clave|Incorporación de dominio comprobado|
|Clave|Creación de compañía|
|Clave|Crear configuración de compañía.|
|Clave|Eliminar configuración de compañía.|
|Clave|Disminución del nivel de asociado|
|Clave|Se ha eliminado el directorio|
|Otros|Se ha eliminado el directorio de forma permanente|
|Otros|Directorio programado para eliminación|
|Recurso|Promoción de la compañía al asociado|
|Recurso|Purgar propiedades de administración de derechos|
|Recurso|Eliminación de asociado de la compañía|
|Recurso|Eliminación de dominio sin comprobar|
|Recurso|Eliminación de dominio comprobado|
|Recurso|Establecer información sobre la compañía|
|Recurso|Establecimiento de la característica DirSync|
|Recurso|Establecimiento de la marca DirSyncEnabled|
|Recurso|Establecer asociación|
|Recurso|Establecimiento de umbral de eliminación accidental|
|Recurso|Establecimiento de la ubicación de datos permitida de empresa|
|Recurso|Establecimiento de característica de empresa multinacional habilitada|
|Recurso|Establecimiento de característica de directorio en inquilino|
|Recurso|Establecer la autenticación de dominio|
|Recurso|Establecer la configuración de la federación en el dominio|
|Recurso|Establecimiento de directiva de contraseñas|
|Recurso|Establecimiento de propiedades de administración de derechos|
|Recurso|Actualización de compañía|
|Recurso|Actualización de configuración de compañía|
|Recurso| Actualizar dominio|
|Recurso|Comprobar dominio|
|Recurso|Comprobar dominio verificado por correo electrónico|
|Recurso|Incorporación|
|Recurso|Actualización de la configuración de alertas|
|Recurso|Actualización de la configuración de la ingesta semanal|
|Recurso|Deshabilitar la reescritura de contraseñas para el directorio|
|Recurso|Habilitar la reescritura de contraseñas para el directorio|
|Recurso|Adición de la asignación de roles de aplicación al grupo|
|Recurso|Agregar grupo|
|Recurso|Agregar miembro a grupo|
|Recurso|Incorporación de un propietario a un grupo|
|Recurso|Creación de configuración de grupo|
|Recurso|Eliminar grupo|
|Recurso|Eliminación de la configuración de grupo|
|Recurso|Finalización de la aplicación de licencias basadas en grupos a los usuarios|
|Recurso|Eliminación permanente de grupo|
|Recurso|Eliminación de la asignación de roles de aplicación de grupo|
|Recurso|Quitar miembro de grupo|
|Recurso|Eliminación de propietario del grupo|
|Recurso|Restauración de grupo|
|Recurso|Establecimiento de licencia de grupo|
|Recurso|Establecer grupo que va a administrar el usuario.|
|Recurso|Empezar a aplicar licencias basadas en grupo a los usuarios|
|Recurso|Desencadenamiento del nuevo cálculo de licencias de grupo|
|Recurso|Actualizar grupo|
|Recurso|Actualización de la configuración de grupo|
|Recurso|Agregar miembro|
|Recurso|Crear grupo|
|Recurso|Eliminar grupo|
|Recurso|Quitar miembro|
|Recurso|Actualizar grupo|
|Recurso|Aprobación de una solicitud pendiente para unirse a un grupo|
|Recurso|Cancelación de una solicitud pendiente para unirse a un grupo|
|Recurso|Creación de directiva de administración del ciclo de vida|
|Recurso|Eliminación de una solicitud pendiente para unirse a un grupo|
|Recurso|Rechazo de una solicitud pendiente para unirse a un grupo|
|Recurso|Renovación de grupo|
|Recurso|Solicitud de unión a un grupo|
|Recurso|Establecimiento de propiedades de grupo dinámico|
|Recurso|Actualización de directiva de administración del ciclo de vida|
|Recurso|Incorporación de una clave basada en un secreto ASCII a un contenedor de claves CPIM|
|Recurso|Incorporación de una clave a un contenedor de claves CPIM|
|Recurso|Eliminación de un contenedor de claves CPIM|
|Recurso|Eliminación de un contenedor de claves|
|Recurso|Obtención de metadatos de clave activos del contenedor de claves en JWK|
|Recurso|Obtención de metadatos del contenedor de claves|
|Recurso|Obtiene un contenedor de claves CPIM en formato jwk|
|Recurso|Obtiene una lista de contenedores de claves del inquilino|
|Recurso|Restauración de una copia de seguridad del contenedor de claves CPIM|
|Recurso|Guardar un contenedor de claves|
|Recurso|Carga de una clave cifrada CPIM|
|Recurso|Emisión de un código de autorización para la aplicación|
|Recurso|Emisión de un id_token para la aplicación|


## <a name="core-directory"></a>Core Directory (Directorio principal)

|Categoría de auditoría|Actividad|
|---|---|
|Administración de unidades administrativas|Descarga de un tipo de evento de riesgo único|
|Administración de unidades administrativas|Descarga de administradores y del estado de las notificaciones de ingesta semanales|
|Administración de unidades administrativas|Descarga de todos los tipos de eventos de riesgo|
|Administración de unidades administrativas|Descarga de los eventos de riesgo de usuarios gratuitos|
|Administración de unidades administrativas|Descarga de usuarios marcados en riesgo|
|Administración de aplicaciones|Invitaciones por lotes procesadas|
|Administración de aplicaciones|Invitaciones por lotes cargadas|
|Administración de aplicaciones|Incorporación de un propietario a una directiva|
|Administración de aplicaciones|Add policy|
|Administración de aplicaciones|Eliminación de directiva|
|Administración de aplicaciones|Eliminación de credenciales de directiva|
|Administración de aplicaciones|Actualización de directiva|
|Administración de aplicaciones|Establecimiento de la directiva de registro MFA|
|Administración de aplicaciones|Establecimiento de la directiva de riesgo de inicio de sesión|
|Administración de aplicaciones|Establecimiento de la directiva de riesgo de usuario|
|Administración de aplicaciones|Aceptación de términos de uso|
|Administración de aplicaciones|Creación de términos de uso|
|Administración de aplicaciones|Rechazo de términos de uso|
|Administración de aplicaciones|Eliminación de términos de uso|
|Administración de aplicaciones|Edición de términos de uso|
|Administración de aplicaciones|Publicación de los términos de uso|
|Administración de aplicaciones|Cancelación de la publicación de los términos de uso|
|Administración de aplicaciones|Incorporación de certificado SSL de la aplicación|
|Administración de aplicaciones|Eliminación del enlace SSL|
|Administración de aplicaciones|Registro del conector|
|Administración de aplicaciones|AdminPolicyDatas-RemoveResources|
|Administración de aplicaciones|AdminPolicyDatas-SetResources|
|Administración de aplicaciones|AdminUserJourneys-GetResources|
|Administración de directorios|AdminUserJourneys-RemoveResources|
|Administración de directorios|AdminUserJourneys-SetResources|
|Administración de directorios|Creación de IdentityProvider|
|Administración de directorios|Creación de un nuevo AdminUserJourney|
|Administración de directorios|Creación de un json de recurso localizado|
|Administración de directorios|Creación de un nuevo IDP personalizado|
|Administración de directorios|Creación de nuevo IDP|
|Administración de directorios|Creación o actualización de un recurso del directorio B2C|
|Administración de directorios|Creación de directiva|
|Administración de directorios|Creación de directiva de trustFramework|
|Administración de directorios|Creación de directiva de trustFramework con prefijo configurable|
|Administración de directorios|Creación de atributo de usuario|
|Administración de directorios|CreateTrustFrameworkPolicy|
|Administración de directorios|Eliminación de IDP|
|Administración de directorios|Eliminación de IdentityProvider|
|Administración de directorios|Eliminación de un recurso del directorio B2C|
|Administración de directorios|Eliminación de directiva de trustFramework|
|Administración de directorios|Eliminación de atributo de usuario|
|Administración de directorios|Obtención de recursos de directorio B2C en un grupo de recursos|
|Administración de directorios|Obtención de recursos de directorio B2C en una suscripción|
|Administración de directorios|Obtención de IDP personalizado|
|Administración de directorios|Obtención de IDP|
|Administración de directorios|Obtención de un recurso del directorio B2C|
|Administración de directorios|Obtención de un recorrido del usuario|
|Administración de directorios|Obtención de notificaciones de aplicación permitidas para el recorrido del usuario|
|Administración de directorios|Obtención de notificaciones autoafirmadas permitidas para el recorrido del usuario|
|Administración de directorios|Obtención de notificaciones autoafirmadas permitidas de directiva|
|Administración de directorios|Obtención de la lista de notificaciones de salida disponibles|
|Administración de directorios|Obtención de definiciones de contenido para el recorrido del usuario|
|Administración de directorios|Obtención de IDP para un flujo de administración específico|
|Administración de directorios|Obtención de una lista de todos los flujos de administración|
|Administración de directorios|Obtención de lista de etiquetas para todos los flujos de administración de todos los usuarios|
|Administración de grupos|Obtención de la lista de inquilinos de un usuario|
|Administración de grupos|Obtención de notificaciones autoafirmadas de las cuentas locales|
|Administración de grupos|Obtención de un json de recurso localizado|
|Administración de grupos|Obtención de operaciones del proveedor de recursos Microsoft.AzureActiveDirectory|
|Administración de grupos|Obtención de directivas|
|Administración de grupos|Obtención de directiva|
|Administración de grupos|Obtención de una lista de IDP compatible|
|Administración de grupos|Obtención de lista de IDP compatible del recorrido del usuario|
|Administración de grupos|Obtención de una lista de IDP personalizada definida por el inquilino|
|Administración de grupos|Obtención de una lista de IDP definida por el inquilino|
|Administración de grupos|Obtención de una lista de IDP local definida por el inquilino|
|Administración de grupos|Obtención de los detalles de inquilino de un usuario para la creación de recursos|
|Administración de grupos|Obtención de la referencia cultural predeterminada para CPIM|
|Administración de grupos|Obtención de los detalles de un flujo de administración|
|Administración de grupos|Obtención de la lista de UserJourneys para este inquilino|
|Administración de grupos|Obtención del conjunto de referencias culturales admitidas disponibles para CPIM|
|Administración de grupos|Obtención de directiva de trustFramework|
|Administración de grupos|Obtención de directiva de trustFramework como xml|
|Administración de grupos|Obtención de atributo de usuario|
|Administración de directivas|Obtención de atributos de usuario|
|Administración de directivas|Obtención de lista de recorridos del usuario|
|Administración de directivas|GetIEFPolicies|
|Administración de directivas|GetIdentityProviders|
|Administración de directivas|GetTrustFrameworkPolicy|
|Recurso|MigrateTenantMetadata|
|Recurso|Traslado de recursos|
|Recurso|Revisión de IdentityProvider|
|Recurso|PutTrustFrameworkPolicy|
|Recurso|PutTrustFrameworkpolicy|
|Recurso|Eliminación de un recorrido del usuario|
|Recurso|Actualización de IDP personalizado|
|Recurso|Actualización de IDP|
|Recurso|Actualización de IDP local|
|Recurso|Actualización de un recurso del directorio B2C|
|Recurso|Actualización de directiva|
|Recurso|Actualización del estado de la suscripción|
|Administración de roles|Actualización de atributo de usuario|
|Administración de roles|Validación de los recursos en movimiento|
|Administración de roles|Incorporación de un dispositivo|
|Administración de roles|Adición de la configuración de dispositivo|
|Administración de roles|Incorporación de propietario registrado a dispositivo|
|Administración de roles|Incorporación de usuarios registrados a dispositivo|
|Administración de roles|Eliminar un dispositivo|
|Administración de roles|Eliminación de la configuración del dispositivo|
|Administración de roles|El dispositivo ya no es compatible|
|Administración de roles|El dispositivo ya no está administrado|
|User Management|Eliminación de propietario registrado del dispositivo|
|User Management|Eliminación de usuarios registrados del dispositivo|
|User Management|Actualización de dispositivo|
|User Management|Actualización de configuración de dispositivo|
|User Management|Incorporación de un miembro apto al rol|
|User Management|Incorporación de un miembro a un rol|
|User Management|Incorporación de asignación de roles a definición de roles|
|User Management|Incorporación de rol desde plantilla|
|User Management|Incorporación de miembro con ámbito agregado a rol|
|User Management|Eliminación de miembros aptos del rol|
|User Management|Eliminación de miembro del rol|
|User Management|Eliminación de la asignación de roles de la definición de roles|
|User Management|Eliminación de miembro con ámbito de rol|
|User Management|Actualización de rol|
|User Management|AccessReview_Review|
|User Management|AccessReview_Update|
|User Management|ActivationAborted|
|User Management|ActivationApproved|
|User Management|ActivationCanceled|
|User Management|ActivationRequested|
|User Management|Se agregó|
|User Management|Assign|


## <a name="identity-protection"></a>Protección de identidad

|Categoría de auditoría|Actividad|
|---|---|
|Administración de directorios|Elevar|
|Administración de directorios|Quitado|
|Administración de directorios|Cambios en la configuración de roles|
|Otros|ScanAlertsNow|
|Otros|Signup|
|Otros|Reducir los privilegios|
|Otros|UpdateAlertSettings|
|Otros|UpdateCurrentState|
|Administración de directivas|Revisión de acceso finalizada|
|Administración de directivas|Incorporación de aprobador para solicitar la aprobación|
|Administración de directivas|Incorporación de revisor para acceder a la revisión|
|User Management|Aplicación de la revisión de acceso|
|User Management|Creación de revisión de acceso|


## <a name="invited-users"></a>Invited Users (Usuarios invitados)

|Categoría de auditoría|Actividad|
|---|---|
|Otros|Creación de aprobación de solicitud|
|Otros|Eliminación de revisión de acceso|
|User Management|Eliminación del revisor en la revisión de acceso|
|User Management|Solicitud de la aplicación de los resultados de la revisión|
|User Management|Solicitud de detención de la revisión|
|User Management|Revisión de la asignación de aplicaciones|
|User Management|Revisión de la pertenencia al grupo|
|User Management|Revisión de la pertenencia al rol Rbac|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Categoría de auditoría|Actividad|
|---|---|
|Administración de grupos|Revisión de la solicitud de aprobación de la solicitud|
|Administración de grupos|Actualización de la revisión de acceso|
|Administración de grupos|Actualización de la configuración de las notificaciones por correo de revisión de acceso|
|Administración de grupos|Actualización de la configuración del número de repeticiones de la revisión de acceso|
|Administración de grupos|Actualización de la configuración de la duración en días de la repetición de la revisión de acceso|
|User Management|Actualización de la configuración del tipo de finalización de las repeticiones de la revisión de acceso|
|User Management|Actualización de la configuración del tipo de repeticiones de la revisión de acceso|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Categoría de auditoría|Actividad|
|---|---|
|Administración de roles|Actualización de la configuración de los recordatorios de revisión de acceso|
|Administración de roles|Actualización de la aprobación de solicitud|
|Administración de roles|Adición de la asignación de roles de aplicación al usuario|
|Administración de roles|Agregar usuario|
|Administración de roles|Incorporación de información de aplicación de teléfono para la autenticación sólida de usuarios|
|Administración de roles|Cambiar la licencia de usuario|
|Administración de roles|Cambiar la contraseña de usuario|
|Administración de roles|Conversión de usuario federado en administrado|
|Administración de roles|Creación de contraseña de aplicaciones para usuario|
|Administración de roles|Eliminación de contraseña de aplicaciones para usuario|
|Administración de roles|Eliminación de usuario|
|Administración de roles|Deshabilitar cuenta|
|Administración de roles|Habilitar la autenticación sólida|
|Administración de roles|Eliminación permanente de usuario|
|Administración de roles|Eliminación de la asignación de roles de aplicación del usuario|
|Administración de roles|Eliminación de información de aplicación de teléfono para la autenticación sólida de usuarios|



## <a name="self-service-group-management"></a>Self-service Group Management (Administración de grupos de autoservicio)

|Categoría de auditoría|Actividad|
|---|---|
|Administración de grupos|Restablecer contraseña de usuario|
|Administración de grupos|Restauración de usuario|
|Administración de grupos|Establecer el cambio forzado de la contraseña de usuario|
|Administración de grupos|Establecimiento del administrador de usuarios|
|Administración de grupos|Establecimiento de metadatos de token Oath de usuarios habilitado|
|Administración de grupos|Actualización de la marca de tiempo StsRefreshTokenValidFrom|
|Administración de grupos|Actualizar secretos externos|
|Administración de grupos|Actualizar usuario|
|Administración de grupos|El administrador genera una contraseña temporal|


## <a name="self-service-password-management"></a>Self-service Password Management (Administración de contraseñas de autorservicio)

|Categoría de auditoría|Actividad|
|---|---|
|Administración de directorios|El administrador solicita al usuario que restablezca su contraseña|
|Administración de directorios|Asignación de usuarios externos a la aplicación|
|User Management|El correo electrónico no se ha enviado, el usuario canceló la suscripción|
|User Management|Invitación a usuario externo|
|User Management|Canje de invitación a usuario externo|
|User Management|Creación de inquilino viral|
|User Management|Creación de usuario viral|
|User Management|Registro de contraseña de usuario|
|User Management|Restablecimiento de contraseña de usuario|
|User Management|Bloqueado para el restablecimiento de contraseña de autoservicio|


## <a name="terms-of-use"></a>Términos de uso

|Categoría de auditoría|Actividad|
|---|---|
|Administración de directivas|Cambio de contraseña (autoservicio)|
|Administración de directivas|Restablecimiento de contraseña (por parte del administrador)|
|Administración de directivas|Restablecimiento de contraseña (autoservicio)|
|Administración de directivas|Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio|
|Administración de directivas|Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio|
|Administración de directivas|Desbloqueo de la cuenta de usuario (autoservicio)|
|Administración de directivas|Usuario registrado para el restablecimiento de contraseña de autoservicio|




## <a name="next-steps"></a>Pasos siguientes

Para ver una introducción a:

- Los informes, consulte la [guía de informes de Azure Active Directory](active-directory-reporting-azure-portal.md).

- Los informes de actividad de auditoría, consulte [Informes de actividad de auditoría en el portal de Azure Active Directory](active-directory-reporting-activity-audit-logs.md). 

