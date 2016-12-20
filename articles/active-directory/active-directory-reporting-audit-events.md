---
title: "Eventos del informe de auditoría de Azure Active Directory | Microsoft Docs"
description: "Eventos auditados que están disponibles para ver y descargar desde Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: mbaldwin
editor: 
ms.assetid: 307eedf7-05bc-448d-a84d-bead5a4c5770
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 23da68e26865a8c3525cce3cb2b3a4db447fcc4c


---
# <a name="azure-active-directory-audit-report-events"></a>Eventos del informe de auditoría de Azure Active Directory
*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*

El Informe de auditoría de Azure Active Directory ayuda a los clientes a identificar las acciones con privilegios que se produjeron en su Azure Active Directory. Las acciones con privilegios incluyen cambios de elevación (por ejemplo, creación de roles o restablecimientos de contraseña), cambios de configuraciones de directiva (por ejemplo, directivas de contraseña) o cambios de configuración de directorio (por ejemplo, cambios en la configuración de la federación de dominio). Los informes proporcionan el registro de auditoría para el nombre del evento, el actor que realizó la acción, el recurso de destino que se ve afectado por el cambio y la fecha y hora (en UTC). Los clientes pueden recuperar la lista de eventos de auditoría de Azure Active Directory desde [Azure Portal](https://portal.azure.com/), como se describe en [Informes de Azure Active Directory - versión preliminar](active-directory-reporting-azure-portal.md).

## <a name="list-of-audit-report-events"></a>Lista de eventos del informe de auditoría
<!--- audit event descriptions should be in the past tense --->

| Eventos | Descripción del evento |
| --- | --- |
| **Eventos de usuario** | |
| Agregar usuario |Agregó un usuario al directorio. |
| Eliminar usuario |Eliminó un usuario del directorio. |
| Establecer propiedades de la licencia |Estableció las propiedades de la licencia para un usuario en el directorio. |
| Restablecer contraseña de usuario |Restableció la contraseña de un usuario en el directorio. |
| Cambiar la contraseña de usuario |Cambió la contraseña de un usuario en el directorio. |
| Cambiar la licencia de usuario |Cambió la licencia asignada a un usuario en el directorio. Para comprobar qué licencias se actualizaron, examine las siguientes propiedades de [Actualizar usuario](#update-user-attributes) . |
| Actualizar usuario |Actualizó un usuario en el directorio. [Consulte a continuación](#update-user-attributes) los atributos que se pueden actualizar. |
| Establecer el cambio forzado de la contraseña de usuario |Estableció la propiedad que fuerza a un usuario a cambiar su contraseña en el inicio de sesión. |
| Actualizar credenciales de usuario |El usuario cambió la contraseña |
| **Eventos de grupo** | |
| Agregar grupo |Se crea un grupo en el directorio. |
| Actualizar grupo |Se actualiza un grupo del directorio. Para ver las propiedades del grupo que se actualizaron, consulte [Propiedades de grupo auditadas](#update-group-attributes) en la sección siguiente |
| Eliminar grupo |Se elimina un grupo del directorio. |
| Agregar miembro a grupo |Se agrega un miembro a un grupo del directorio. |
| Quitar miembro de grupo |Se quita un miembro de un grupo del directorio. |
| CreateGroupSettings |Configuración de grupo creada. |
| UpdateGroupSettings |Configuración de grupo actualizada. Para ver las opciones de la configuración del grupo que se actualizaron, consulte [Propiedades de grupo auditadas](#update-group-attributes) en la sección siguiente. |
| DeleteGroupSettings |Configuración de grupo eliminada. |
| SetGroupLicense |Establecer licencia de grupo. |
| SetGroupManagedBy |Establecer grupo que va a administrar el usuario. |
| AddGroupMember |Miembro agregado a grupo. |
| RemoveGroupMember |Quitar miembro de grupo |
| AddGroupOwner |Propietario agregado a grupo. |
| RemoveGroupOwner |Propietario quitado de grupo. |
| **Eventos de aplicación** | |
| Agregar entidad de servicio |Agregó una entidad de servicio al directorio. |
| Quitar entidad de servicio |Quitó una entidad de servicio del directorio. |
| Agregar credenciales de la entidad de servicio |Agregó credenciales a una entidad de servicio. |
| Quitar credenciales de la entidad de servicio |Quitó las credenciales de una entidad de servicio. |
| Agregar entrada de delegación |Se crea [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) en el directorio. |
| Establecer entrada de delegación |Se actualiza [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) en el directorio. |
| Quitar entrada de delegación |Se elimina [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) en el directorio. |
| **Eventos de rol** | |
| Agregar miembro de rol a Rol |Agregó un usuario a un rol del directorio. |
| Quitar miembro de rol de Rol |Quitó un usuario de un rol del directorio. |
| Establecer la información de contacto de la compañía |Establecer preferencias de contacto a nivel de compañía. Incluye direcciones de correo electrónico para marketing, así como  notificaciones técnicas sobre Microsoft Online Services. |
| Agregar entrada de delegación |Se crea [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio. |
| Establecer entrada de delegación |Se actualiza [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio. |
| Quitar entrada de delegación |Se elimina [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio. |
| AddSevicePrincipalOwner |Propietario agregado a entidad de servicio. |
| RemoveSevicePrincipalOwner |Propietario quitado de entidad de servicio. |
| AddApplication |Agregar aplicación. |
| UpdateApplication |Actualizar aplicación. Para ver las opciones de la configuración de la aplicación que se actualizaron, consulte [Propiedades de aplicación auditadas](#update-application-attributes) en la sección siguiente |
| DeleteApplication |Eliminar aplicación. |
| RestoreApplication |Restaurar aplicación. |
| AddApplicationOwner |Agregar propietario a aplicación. |
| RemoveApplicationOwner |Quitar propietario de aplicación. |
| **Eventos de rol** | |
| Agregar miembro de rol a Rol |Agregó un usuario a un rol del directorio. |
| Quitar miembro de rol de Rol |Quitó un usuario de un rol del directorio. |
| AddRoleDefinition |Definición de rol agregada. |
| UpdateRoleDefinition |Definición de rol actualizada. Para ver las opciones de la configuración del rol que se actualizaron, consulte [Propiedades de definición de rol auditadas](#update-role-definition-attributes) en la sección siguiente |
| DeleteRoleDefinition |Definición de rol eliminada. |
| AddRoleAssignmentToRoleDefinition |Asignación de rol agregada a definición de rol. |
| RemoveRoleAssignmentFromRoleDefinition |Asignación de rol quitada de definición de rol. |
| AddRoleFromTemplate |Rola agregado desde plantilla. |
| UpdateRole |Rol actualizado. |
| AddRoleScopeMemberToRole |Miembro con ámbito agregado a rol. |
| RemoveRoleScopedMemberFromRole |Miembro con ámbito quitado de rol. |
| **Eventos de dispositivo (todos los eventos nuevos)** | |
| AddDevice |Dispositivo agregado. |
| UpdateDevice |Dispositivo actualizado. Para ver las propiedades del dispositivo que se actualizaron, consulte [Propiedades de dispositivo auditadas](#update-device-attributes) en la sección siguiente |
| DeleteDevice |Dispositivo eliminado. |
| AddDeviceConfiguration |Configuración de dispositivo agregada. |
| UpdateDeviceConfiguration |Configuración de dispositivo actualizada. Para ver las propiedades de la configuración del dispositivo que se actualizaron, consulte [Propiedades de configuración de dispositivo auditadas](#update-device-configuration-attributes) en la sección siguiente |
| DeleteDeviceConfiguration |Configuración de dispositivo eliminada. |
| AddRegisteredOwner |Propietario registrado agregado a dispositivo. |
| AddRegisteredUsers |Usuarios registrados agregados a dispositivo. |
| RemoveRegisteredOwner |Quitar propietario registrado de dispositivo. |
| RemoveRegisteredUsers |Quitar usuarios registrados de dispositivo. |
| RemoveDeviceCredentials |Quitar credenciales de dispositivo. |
| **Eventos B2B** | |
| Invitaciones por lotes cargadas. |Un administrador ha cargado un archivo que contiene invitaciones para enviar a usuarios asociados. |
| Invitaciones por lotes procesadas. |Se ha procesado un archivo que contiene invitaciones a usuarios asociados. |
| Invitar a usuario externo. |Se ha sido invitado a un usuario externo al directorio. |
| Canjear invitación a usuario externo. |Un usuario externo ha canjeado su invitación al directorio. |
| Agregar usuario externo al grupo. |Se ha asignado una suscripción a un usuario externo a un grupo del directorio. |
| Asignar usuario externo a la aplicación. |Se ha asignado a un usuario externo acceso directo a una aplicación. |
| Creación de inquilinos viral. |Se ha creado un nuevo inquilino en Azure AD mediante el canje de una invitación. |
| Creación de usuarios viral. |Se ha creado un usuario en un inquilino existente en Azure AD mediante el canje de una invitación. |
| **Unidades administrativas (todos los eventos nuevos)** | |
| AddAdministrativeUnit |Agregar unidad administrativa. |
| UpdateAdministrativeUnit |Actualizar unidad administrativa. Para ver las propiedades de una unidad administrativa que se actualizaron, consulte [Propiedades de unidad administrativa auditadas](#update-administrative-unit-attributes) en la sección siguiente |
| DeleteAdministrativeUnit |Eliminar unidad administrativa. |
| AddMemberToAdministrativeUnit |Agregar miembro a unidad administrativa. |
| RemoveMemberFromAdministrativeUnit |Quitar miembro de unidad administrativa. |
| **Eventos de directorio** | |
| Agregar asociado a la compañía |Agregó un asociado al directorio. |
| Quitar asociado de la compañía |Quitó un asociado del directorio. |
| DemotePartner |Disminuir nivel de asociado. |
| Agregar dominio a la compañía |Agregó un dominio al directorio. |
| Quitar dominio de la compañía |Quitó un dominio del directorio. |
|   Actualizar dominio |Actualizó un dominio en el directorio. Para ver las propiedades del dominio que se actualizaron, consulte [Propiedades de dominio auditadas](#update-domain-attributes) en la sección siguiente |
| Establecer la autenticación de dominio |Se cambió la configuración de dominio predeterminada para la compañía. |
| Establecer la información de contacto de la compañía |Establecer preferencias de contacto a nivel de compañía. Incluye direcciones de correo electrónico para marketing, así como  notificaciones técnicas sobre Microsoft Online Services. |
| Establecer la configuración de la federación en el dominio |Actualizó la configuración de la federación para un dominio. |
| Comprobar dominio |Comprobó un dominio en el directorio. |
| Comprobar dominio verificado por correo electrónico |Comprobó un dominio en el directorio mediante la verificación por correo electrónico. |
| Establecer la marca DirSyncEnabled en la compañía |Estableció la propiedad que habilita un directorio para Sincronización de Azure AD. |
| Establecer directiva de contraseñas |Estableció las restricciones de longitud y caracteres para las contraseñas de usuario. |
| Establecer información sobre la compañía |Actualizó la información a nivel de la compañía. Consulte el cmdlet [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) de PowerShell para obtener más información. |
| SetCompanyAllowedDataLocation |Establecer ubicación de datos permitida de empresa. |
| SetCompanyDirSyncEnabled |Establecer marca DirSyncEnabled. |
| SetCompanyDirSyncFeature |Establecer característica DirSync. |
| SetCompanyInformation |Establecer información sobre compañía. |
| SetCompanyMultiNationalEnabled |Establecer característica de empresa multinacional habilitada. |
| SetDirectoryFeatureOnTenant |Establecer característica de directorio en inquilino. |
| SetTenantLicenseProperties |Establecer propiedades de licencia de inquilino. |
| CreateCompanySettings |Crear configuración de compañía. |
| UpdateCompanySettings |Actualizar configuración de compañía. Para ver las propiedades de la compañía que se actualizaron, consulte [Propiedades de compañía auditadas](#update-company-attributes) en la sección siguiente |
| DeleteCompanySettings |Eliminar configuración de compañía. |
| SetAccidentalDeletionThreshold |Establecer umbral de eliminación accidental. |
| SetRightsManagementProperties |Establecer propiedades de administración de derechos. |
| PurgeRightsManagementProperties |Purgar propiedades de administración de derechos. |
| UpdateExternalSecrets |Actualizar secretos externos |
| **Eventos de directiva (todos los eventos nuevos)** | |
| AddPolicy |Agregar directiva. |
| UpdatePolicy |Actualizar directiva. |
| DeletePolicy |Eliminar directiva. |
| AddDefaultPolicyApplication |Agregar directiva a aplicación. |
| AddDefaultPolicyServicePrincipal |Agregar directiva a entidad de servicio. |
| RemoveDefaultPolicyApplication |Quitar directiva de aplicación. |
| RemoveDefaultPolicyServicePrincipal |Quita directiva de entidad de servicio. |
| RemovePolicyCredentials |Quitar credenciales de directiva. |

## <a name="audit-report-retention"></a>Retención de informes de auditoría
Los eventos del informe de auditoría de Azure AD se conservan durante 180 días. Para obtener más información acerca de la retención de los informes, consulte [Directivas de retención de informes de Azure Active Directory](active-directory-reporting-retention.md).

Para los clientes interesados en almacenar eventos de auditoría durante períodos de retención más largos, se puede utilizar la API de informes para extraer periódicamente los eventos de auditoría en un almacén de datos independiente. Consulte [Introducción a la API de informes](active-directory-reporting-api-getting-started.md) para obtener más información.

## <a name="properties-included-with-each-audit-event"></a>Propiedades que se incluyen con cada evento de auditoría
| Propiedad | Description |
| --- | --- |
| Fecha y hora |La fecha y la hora en que ocurrió el evento de auditoría |
| Actor |El usuario o la entidad de servicio que realizó la acción |
| Acción |La acción que se realizó |
| Destino |El usuario o la entidad de servicio en que se realizó la acción |

## <a name="update-user-attributes"></a>Atributos de "Actualizar usuario"
El evento de auditoría "Actualizar usuario" incluye información adicional sobre los atributos de usuario que se actualizaron. Para cada atributo, se incluye el valor anterior y el valor nuevo.

| Atributo | Description |
| --- | --- |
| AccountEnabled |El usuario puede iniciar sesión. |
| AssignedLicense |Todas las licencias que se han asignado al usuario. |
| AssignedPlan |Planes de servicio que se derivan de las licencias asignadas al usuario. |
| LicenseAssignmentDetail |Detalles sobre las licencias asignadas al usuario. Por ejemplo, si se incluyeran licencias basadas en un grupo, esto incluiría a todo el grupo que concedió la licencia. |
| Móvil |Teléfono móvil del usuario. |
| OtherMail |Dirección de correo electrónico alternativa del usuario. |
| OtherMobile |Teléfono móvil alternativo del usuario. |
| StrongAuthenticationMethod |Una lista de métodos de comprobación configurados por el usuario para  Multi-Factor Authentication, como la llamada de voz, los SMS o el código de comprobación de una aplicación móvil. |
| StrongAuthenticationRequirement |Si Multi-Factor Authentication se exige, habilita o deshabilita para este usuario. |
| StrongAuthenticationUserDetails |Número de teléfono, número de teléfono alternativo y dirección de correo electrónico del usuario que se usan para Multi-Factor Authentication y para la comprobación de restablecimiento de contraseña. |
| StrongAuthenticationPhoneAppDetail |Detalles de aplicaciones telefónicas registradas para realizar el inicio de sesión 2FA |
| TelephoneNumber |Número de teléfono del usuario. |
| AlternativeSecurityId |Un identificador de seguridad alternativo para el objeto. |
| CreationType |Método de creación del usuario (ya sea por invitación o viral). |
| InviteTicket |Lista de los vales de invitación del usuario. |
| InviteReplyUrl |Lista de direcciones URL para responder tras la aceptación de una invitación. |
| InviteResources |Lista de recursos a los que el usuario ha sido invitado. |
| LastDirSyncTime |Última vez que se actualizó el objeto debido a la sincronización del directorio relevante (cliente, local). |
| MSExchRemoteRecipientType |Se asigna a tipos de destinatarios MSO. Consulte [tipos de destinatarios de MSO] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx para ver los tipos de destinatarios. |
| PreferredDataLocation |La ubicación preferida para los datos del usuario, grupo, contacto, carpeta pública o dispositivo. |
| ProxyAddresses |La dirección por la que se reconoce un objeto de destinatario de Exchange Server en un sistema de correo externo. |
| StsRefreshTokensValidFrom |Lleva información de revocación del token de actualización (se considera que los tokens de actualización de STS emitidos antes este momento han expirado). |
| UserPrincipalName |El UPN que es un nombre de inicio de sesión de estilo Internet para un usuario. |
| UserState |Estado de usuario PendingApproval/PendingAcceptance/Accepted/PendingVerification. |
| UserStateChangedOn |Marca de tiempo del último cambio en UserState. Se utiliza para desencadenar los flujos de trabajo del ciclo de vida. |
| UserType |Tipo de usuario. Miembro (0), Invitado (1), Viral (2). |

## <a name="update-group-attributes"></a>Atributos de "Actualizar grupo"
| Atributo | Description |
| --- | --- |
| Clasificación |La clasificación de un grupo unificado (HBI, MBI, etc.). |
| Description |Frases descriptivas en lenguaje natural acerca del objeto. |
| DisplayName |Nombre para mostrar de un objeto |
| DirSyncEnabled |Indica si la sincronización se produce desde un directorio relevante (cliente, local). |
| GroupLicenseAssignment |Asignación de licencias de un grupo |
| GroupType |Tipo de grupo, unificado (0) |
| IsMembershipRuleLocked |Indica que la propiedad MembershipRule la establece el servicio de administración de grupos de autoservicio y los usuarios no la pueden cambiar. Aplicable únicamente a grupos en los que GroupType incluye GroupType.DynamicMembership |
| IsPublic |Marca que indica si el grupo es público o privado. |
| LastDirSyncTime |Última vez que se actualizó el objeto como consecuencia de la sincronización del directorio relevante (cliente, local). |
| Mail |Dirección de correo electrónico principal |
| MailEnabled |Indica si este grupo tiene funcionalidad de correo electrónico. |
| MailNickname |Moniker para un objeto de la libreta de direcciones, normalmente la parte de su nombre de correo electrónico anterior al símbolo "@". |
| MembershipRule |Una cadena que expresa los criterios que usa por el servicio de administración de grupos de autoservicio para determinar los miembros que deben pertenecer a este grupo. Consulte también IsMembershipRuleLocked. Aplicable únicamente a grupos en los que GroupType incluye GroupType.DynamicMembership. |
| MembershipRuleProcessingState |Un valor enum definido por el servicio de administración de grupos de autoservicio y que define el estado del procesamiento de la pertenencia de este grupo. Aplicable únicamente a grupos en los que GroupType incluye GroupType.DynamicMembership. |
| ProxyAddresses |La dirección por la que se reconoce un objeto de destinatario de Exchange Server en un sistema de correo externo. |
| RenewedDateTime |Registro de marca de tiempo de cuándo fue la última vez que se renovó un grupo. |
| SecurityEnabled |Indica si la pertenencia al grupo puede influir en las decisiones de autorización. |
| WellKnownObject |Etiqueta un objeto de directorio, que lo designa como uno de un conjunto predefinido. |

## <a name="update-device-attributes"></a>Atributos de "Actualizar dispositivo"
| Atributo | Description |
| --- | --- |
| AccountEnabled |Indica si se puede autenticar una entidad de seguridad. |
| CloudAccountEnabled |Indica si se puede autenticar una entidad de seguridad. Escrito por InTune cuando el dispositivo se usa de manera local. |
| CloudDeviceOSType |Tipo del dispositivo basado en el sistema operativo, por ejemplo, Windows RT, iOS. Cuando lo establece un servicio en la nube (como Intune), este atributo se convierte en autoritativo en el directorio para DeviceOSType. |
| CloudDeviceOSVersion |Versión del sistema operativo. Cuando lo establece un servicio en la nube (como Intune), este atributo se convierte en autoritativo en el directorio para DeviceOSVersion. |
| CloudDisplayName |Valor del atributo LDAP displayName. Cuando lo establece un servicio en la nube (como Intune), este atributo se convierte en autoritativo en el directorio para displayName. |
| CloudCreated |Indica si los servicios en la nube crearon el objeto. |
| CompliantUntil |Hasta qué momento el dispositivo se considera compatible. |
| DeviceMetadata |Metadatos personalizados para el dispositivo |
| DeviceObjectVersion |Este atributo se utiliza para identificar la versión del esquema del dispositivo. |
| DeviceOSType |Tipo del dispositivo basado en el sistema operativo, por ejemplo, Windows RT, iOS. Escrito por el servicio de registro y cuya actualización está previsto que la realice el servicio de administración de MDM o el servicio de administración de STS. |
| DeviceOSVersion |Versión del sistema operativo. Escrito por el servicio de registro y cuya actualización está previsto que la realice el servicio de administración de MDM o el servicio de administración de STS. |
| DevicePhysicalIds |Atributo multivalor destinado a almacenar identificadores del dispositivo físico. Esto puede incluir identificadores de BIOS, huellas digitales de TPM, identificadores específicos del hardware, etc. |
| DirSyncEnabled |Indica si la sincronización se produce desde un directorio relevante (cliente, local). |
| DisplayName |Nombre para mostrar de un objeto |
| IsCompliant |Este atributo se utiliza para administrar el estado de administración de dispositivos móviles del dispositivo. |
| IsManaged |Este atributo se utiliza para indicar que una MDM en la nube administra el dispositivo. |
| LastDirSyncTime |Última vez que se actualizó el objeto debido a la sincronización del directorio relevante (cliente, local). |

## <a name="update-device-configuration-attributes"></a>Atributos de "Actualizar configuración de dispositivo"
| Atributo | Description |
| --- | --- |
| MaximumRegistrationInactivityPeriod |El número máximo de días que un dispositivo puede estar inactivo antes de que se considere para su eliminación. |
| RegistrationQuota |Directiva que se utiliza para limitar el número de registros de dispositivos que se permiten para un único usuario. |

## <a name="update-service-principal-configuration-attributes"></a>Atributos de "Actualizar configuración de entidad de servicio"
| Atributo | Description |
| --- | --- |
| AccountEnabled |Indica si se puede autenticar una entidad de seguridad. |
| AppPrincipalId |Identidad externa y definida por la aplicación para una entidad de seguridad. |
| DisplayName |Nombre para mostrar de un objeto |
| ServicePrincipalName |Nombre de entidad de servicio, que contiene "name o authority", donde name especifica un valor de clase de aplicación y authority contiene al menos nombre de host[:puerto] o "name" que especifica un identificador para la entidad de servicio. |

## <a name="update-app-attributes"></a>Atributos de "Actualizar aplicación"
| Atributo | Description |
| --- | --- |
| AppAddress |El conjunto de direcciones (direcciones URL de redireccionamiento) asignadas a una entidad de servicio. |
| AppId |Identificador de la aplicación |
| AppIdentifierUri |Identificador URI, que identifica la aplicación.  Normalmente, es la dirección URL de acceso de la aplicación. |
| AppLogoUrl |La dirección URL de la imagen del logotipo de la aplicación almacenada en una red CDN. |
| AvailableToOtherTenants |Si el valor es true. la aplicación es multiempresa (es decir, la pueden utilizar otros inquilinos). |
| DisplayName |El nombre para mostrar de un nombre de aplicación |
| Entitlement |Lista de derechos de la aplicación. |
| ExternalUserAccountDelegationsAllowed |Marca que indica si una aplicación de recursos es da confianza y puede crear entradas de delegación para cuentas de usuario externas. |
| GroupMembershipClaims |Directiva de notificaciones de pertenencia a grupo. |
| PublicClient |El valor es True si el cliente no puede mantener el secreto (es decir, el cliente no es confidencial en OAuth2.0) |
| RecordConsentConditions |Tipos de condiciones de consentimiento, tal como definen acuerdo con los términos del contrato: No (0), SilentConsentForPartnerManagedApp(1). Este valor se expondrá en el esquema de API Graph y solo pueden establecerlo o cambiarlo administradores de inquilinos. |
| RequiredResourceAccess |Contenido XML de un valor de la propiedad RequiredResourceAccess. |
| WebApp |Si es true, indica que esta aplicación es una aplicación web. |
| WwwHomepage |La página web principal. |

## <a name="update-role-attributes"></a>Atributos de "Actualizar rol"
| Atributo | Description |
| --- | --- |
| AppAddress |El conjunto de direcciones (direcciones URL de redireccionamiento) asignadas a una entidad de servicio. |
| BelongsToFirstLoginObjectSet |Si el valor es true, indica que este objeto pertenece al conjunto de objetos requeridos para habilitar el inicio de sesión del primer administrador de un inquilino nuevo. |
| Builtin |Indica si la duración de un objeto es propiedad del sistema. |
| Description |Frases descriptivas en lenguaje natural acerca del objeto. |
| DisplayName |Nombre para mostrar de un objeto |
| MailNickname |Moniker para un objeto de la libreta de direcciones, normalmente la parte de su nombre de correo electrónico anterior al símbolo "@". |
| RoleDisabled |Indica si el rol se debe ignorar para las comprobaciones de acceso. |
| RoleTemplateId |Identidad de la plantilla de rol. |
| ServiceInfo |Información de aprovisionamiento específica del servicio que pueden consumir MOAC u otras instancias del servicio (de los mismos tipos de servicio o de otros). |
| TaskSetScopeReference |Identifica una instancia de TaskSet y un conjunto de ámbitos asociados a un rol o RoleTemplate. |
| ValidationError |Información publicada por un servicio federado que describe un error no transitorio específico del servicio con respecto a las propiedades o el vínculo de una acción del administrador de objetos que se va a resolver. |
| WellKnownObject |Etiqueta un objeto de directorio, que lo designa como uno de un conjunto predefinido. |

## <a name="update-role-definition-attributes"></a>Atributos de "Actualizar definición de rol"
| Atributo | Description |
| --- | --- |
| Ámbitos asignables |Colección de ámbitos de autorización a los que se puede hacer referencia al asignar esta propiedad RoleDefinition a una entidad de seguridad. |
| DisplayName |Nombre para mostrar de un objeto |
| GrantedPermissions |Permisos otorgados por esta propiedad RoleDefinition. |

## <a name="update-administrative-unit-attributes"></a>Atributos de "Actualizar unidad administrativa"
| Atributo | Description |
| --- | --- |
| Description |Esta propiedad se actualiza cuando se cambia la descripción de una unidad administrativa. |
| DisplayName |Esta propiedad se actualiza cuando se cambia el nombre de una unidad administrativa. |

## <a name="update-company-attributes"></a>Atributos de "Actualizar empresa"
| Atributo | Description |
| --- | --- |
| AllowedDataLocation |Una ubicación en la que se permite realizar aprovisionamientos a los usuarios de la compañía. |
| AuthorizedServiceInstance |Nombres de instancias de servicio en las que se puede implementar un plan. |
| DirSyncEnabled |Indica si la sincronización se produce desde un directorio relevante (cliente, local). |
| DirSyncStatus |Indica si la sincronización de objetos de la libreta de direcciones en el contexto de este inquilino se produce desde un directorio relevante (cliente, de manera local); una expansión de la propiedad DirSyncEnabled en objetos de la empresa. |
| DirSyncFeatures |Indicador de bits para realizar un seguimiento del conjunto de características de dirsync habilitadas y deshabilitadas para el inquilino. |
| DirectoryFeatures |Características del directorio habilitadas o deshabilitadas. |
| DirSyncConfiguration |Contiene toda la configuración de DirSync específica del inquilino actual. |
| DisplayName |Nombre para mostrar de un objeto |
| IsMnc |Un indicador booleano establecido en "true" si la compañía está habilitada para la característica de empresa multinacional. |
| ObjectSettings |Una colección de valores aplicables al ámbito del objeto. |
| PartnerCommerceUrl |Dirección URL del sitio de comercio del asociado. |
| PartnerHelpUrl |Dirección URL del sitio de ayuda del asociado. |
| PartnerSupportEmail |Dirección URL del correo electrónico de soporte técnico del asociado. |
| PartnerSupportTelephone |Dirección URL del teléfono de soporte técnico del asociado. |
| PartnerSupportUrl |Dirección URL del sitio de soporte técnico del asociado. |
| StrongAuthenticationDetails |Detalles relacionados con StrongAuthentication. |
| StrongAuthenticationPolicy |Directiva de autenticación sólida para la compañía. |
| TechnicalNotificationMail |Dirección de correo electrónico para notificar problemas técnicos relacionados con una compañía. |
| TelephoneNumber |Números de teléfono que cumplen la recomendación E.123 de ITU. |
| TenantType |El tipo de un inquilino. Si no se especifica este valor, el inquilino es una compañía. De lo contrario, los posibles valores son: MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5). |
| VerifiedDomain |Conjunto de nombres de dominio DNS enlazados a una compañía. |

## <a name="update-domain-attributes"></a>Atributos de "Actualizar dominio"
| Atributo | Description |
| --- | --- |
| Capacidades |Indicadores de bits que describen las capacidades del dominio, si existen. |
| Valor predeterminado |Indica si el dominio es el valor predeterminado; por ejemplo, el sufijo UserPrincipalName predeterminado cuando un administrador crea un nuevo usuario en MOAC. |
| Inicial |Indica si el dominio es el dominio inicial de la compañía, tal como lo asigna OCP. El dominio inicial es un subdominio único de un dominio de Microsoft Online; p.ej., contoso3.microsoftonline.com. |
| LiveType |Tipo del espacio de nombres de Windows Live correspondiente, si existe. |
| Name |Identificador del punto de conexión. |
| PasswordNotificationWindowDays |El número de días con que se notifica al usuario antes de que una contraseña expire. |
| PasswordValidityPeriodDays |El número de días que una contraseña es apropiada antes de que deba cambiarse. |

Los registros de auditoría son un control necesario para muchas regulaciones de conformidad. Para que los clientes que usan el informe de auditoría de Azure Active Directory cumplan las regulaciones de conformidad, se recomienda que el cliente envíe una copia de este tema de ayuda con la copia del informe de auditoría exportado del cliente para ayudar a explicar los detalles del informe. Si el auditor desea conocer las regulaciones de conformidad que cumple actualmente Azure, diríjalo a la [página Conformidad](https://azure.microsoft.com/support/trust-center/compliance/) del Centro de confianza de Microsoft Azure.




<!--HONumber=Nov16_HO3-->


