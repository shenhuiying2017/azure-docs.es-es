# Información general
## [Qué es Azure AD Connect](active-directory-aadconnect.md)
## [¿Qué es la sincronización de Azure AD Connect?](active-directory-aadconnectsync-whatis.md)
### [Usuarios y contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Arquitectura](active-directory-aadconnectsync-understanding-architecture.md)
### [Aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [Expresiones declarativas de aprovisionamiento](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [Configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md)
## [¿Qué es Azure AD Connect y la federación?](active-directory-aadconnectfed-whatis.md)


# Introducción
## [Requisitos previos](active-directory-aadconnect-prerequisites.md)
## [Instalación de Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [Configuración rápida](active-directory-aadconnect-get-started-express.md)
### [Configuración personalizada](active-directory-aadconnect-get-started-custom.md)
### [Actualización desde DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [Actualización desde una versión anterior](active-directory-aadconnect-upgrade-previous-version.md)
### [Instalación mediante una base de datos de ADSync ya existente](active-directory-aadconnect-existing-database.md)

# Cómo
## Planeamiento y diseño
### [Conceptos de diseño](active-directory-aadconnect-design-concepts.md)
### [Topologías de Azure AD Connect](active-directory-aadconnect-topologies.md)
### [Active Directory Federation Services en Azure](active-directory-aadconnect-azure-adfs.md)
### [Consideraciones especiales para instancias](active-directory-aadconnect-instances.md)
### [Cuando ya tiene una cuenta de Azure AD](active-directory-aadconnect-existing-tenant.md)
## [Administración de Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [Renovación de certificados para Office 365 y Azure AD](active-directory-aadconnect-o365-certs.md)
### [Actualización del certificado SSL para una granja de Servicios de federación de Active Directory (AD FS)](active-directory-aadconnectfed-ssl-update.md)
### [Habilitación de la escritura diferida](active-directory-aadconnect-feature-device-writeback.md)
### [Opciones de inicio de sesión de usuario](active-directory-aadconnect-user-signin.md)
#### [Inicio de sesión único de conexión directa](active-directory-aadconnect-sso.md)
##### [Inicio rápido](active-directory-aadconnect-sso-quick-start.md)
##### [¿Cómo funciona?](active-directory-aadconnect-sso-how-it-works.md)
##### [Preguntas más frecuentes](active-directory-aadconnect-sso-faq.md)
##### [Solución de problemas](active-directory-aadconnect-troubleshoot-sso.md)
#### [Autenticación de paso a través](active-directory-aadconnect-pass-through-authentication.md)
##### [Inicio rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [Limitaciones actuales](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [¿Cómo funciona?](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [Actualización de versiones preliminares de agentes](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)
##### [Bloqueo inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [Preguntas más frecuentes](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [Solución de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
##### [Análisis a fondo de la seguridad](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)
### [Compatibilidad con varios dominios para la federación](active-directory-aadconnect-multiple-domains.md)
### [Actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md)
### [Uso de un proveedor de identidades (IdP) de SAML 2.0 para un inicio de sesión único](active-directory-aadconnect-federation-saml-idp.md)



## Administración de Azure AD Connect Sync
### [Evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [Sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Cuenta de servicio de Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md)
### [Asistente para la instalación](active-directory-aadconnectsync-installation-wizard.md)
### [Cambio de la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [Configuración del filtrado](active-directory-aadconnectsync-configure-filtering.md)
### [Programador](active-directory-aadconnectsync-feature-scheduler.md)
### [Extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Cambio de la contraseña de la cuenta del servicio Azure AD Sync](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [Cambio de la contraseña de la cuenta de AD DS](active-directory-aadconnectsync-change-addsacct-pass.md)
### [Habilitación de la papelera de reciclaje de AD](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [Operaciones](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Diseñador de metaverso](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Búsqueda de metaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## Administración de servicios de federación
### [Administración y personalización](active-directory-aadconnect-federation-management.md)
### [Federación de varias instancias de Azure AD con una instancia única de AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## Solución de problemas
### [Conectividad](active-directory-aadconnect-troubleshoot-connectivity.md)
### [Errores durante la sincronización](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [El objeto no está sincronizado](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [Sincronización de contraseñas](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [Error LargeObject provocado por userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [Recuperación del límite de 10 GB de LocalDB](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# Referencia
## [Ejemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [Sincronización de identidades y resistencia de atributos duplicados](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [La identidad híbrida requería puertos y protocolos](active-directory-aadconnect-ports.md)
## [Características en versión preliminar](active-directory-aadconnect-feature-preview.md)
## [Historial de versiones](active-directory-aadconnect-version-history.md)
## [Cuentas y permisos](active-directory-aadconnect-accounts-permissions.md)

## Azure AD Connect Sync
### [Atributos sincronizados con Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md)
### [Historial de versiones de conectores](active-directory-aadconnectsync-connector-version-history.md)
### [Referencia de funciones](active-directory-aadconnectsync-functions-reference.md)
### [Tareas y consideraciones operativas](active-directory-aadconnectsync-operations.md)
### [Lista de compatibilidad de federación de AD Azure](active-directory-aadconnect-federation-compatibility.md)
### [Conceptos técnicos](active-directory-aadconnectsync-technical-concepts.md)
### [Características de servicio](active-directory-aadconnectsyncservice-features.md)




# Temas relacionados
## [Supervisión de la infraestructura de identidad local y los servicios de sincronización en la nube](../connect-health/active-directory-aadconnect-health.md)
## [Guía de diseño de identidad híbrida](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=security-identity)
##[Preguntas más frecuentes sobre Azure AD Connect](active-directory-aadconnect-faq.md)
##[Degradación de la sincronización de directorios](active-directory-aadconnect-dirsync-deprecated.md)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)

