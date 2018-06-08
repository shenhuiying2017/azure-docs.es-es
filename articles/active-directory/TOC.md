# [Documentación de Azure Active Directory](index.md)

# Información general
## [¿Qué es Azure Active Directory?](active-directory-whatis.md)
## [Acerca de la administración de identidades de Azure](identity-fundamentals.md)
## [Información acerca de las soluciones de identidades de Azure](understand-azure-identity-solutions.md)
## [Elección de una solución de identidad híbrida](choose-hybrid-identity-solution.md)
## [Asociación de suscripciones de Azure](active-directory-how-subscriptions-associated-directory.md)
## [Consideraciones sobre residencia y datos](active-directory-data-storage-eu.md)
## [Preguntas más frecuentes](active-directory-faq.md)
## [Novedades](whats-new.md)


# Introducción
## [Introducción a Azure AD](get-started-azure-ad.md)
## [Suscripción a Azure AD Premium](active-directory-get-started-premium.md)
## [Adición de un dominio personalizado](add-custom-domain.md)
## [Configuración de la personalización de marca de la compañía](customize-branding.md)
## [Adición de usuarios a Azure AD](add-users-azure-active-directory.md)
## [Asignación de licencias a usuarios](license-users-groups.md)
## [Configuración del autoservicio de restablecimiento de contraseña](authentication/quickstart-sspr.md)
## [Incorporación de información de privacidad de su organización en Azure AD](active-directory-properties-area.md)


# Procedimientos
## Planeamiento y diseño
### [Descripción de la arquitectura de Azure AD](active-directory-architecture.md)
### [Asignación de notificaciones en Azure Active Directory](active-directory-claims-mapping.md)
### [Implementación de una solución de identidad híbrida](active-directory-hybrid-identity-design-considerations-overview.md)
#### Determinación de requisitos
##### [Identidad](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Sincronización de directorios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Multi-factor Authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Identificación de la estrategia de ciclo de vida](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planeamiento de la seguridad de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Protección de datos](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Administración de contenido](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Control de acceso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Respuesta a los incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planeamiento del ciclo de vida de identidad
##### [Tareas](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Estrategia de adopción](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Pasos siguientes](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Comparación de herramientas](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Administrar usuarios
### [Adición de nuevos usuarios a Azure AD](add-users-azure-active-directory.md)
### [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
### [Uso compartido de cuentas](active-directory-sharing-accounts.md)
### [Asignación de usuarios a roles de administración](active-directory-users-assign-role-azure-portal.md)
### [Restaurar un usuario eliminado](active-directory-users-restore.md)
### [Adición de usuarios invitados de otro directorio (B2B)](b2b/what-is-b2b.md)
#### [Adición de usuarios de B2B por los administradores](b2b/add-users-administrator.md)
#### [Adición de usuarios de B2B por los trabajadores de la información](b2b/add-users-information-worker.md)
#### [API y personalización](b2b/customize-invitation-api.md)
#### [Ejemplos de Code y Azure PowerShell](b2b/code-samples.md)
#### [Ejemplo de portal de registro de autoservicio](b2b/self-service-portal.md)
#### [Invitación por correo electrónico](b2b/invitation-email-elements.md)
#### [Canje de invitación](b2b/redemption-experience.md)
#### [Incorporación de usuarios B2B sin invitación](b2b/add-user-without-invite.md)
#### [Permiso o bloqueo de invitaciones](b2b/allow-deny-list.md)
#### [Acceso condicional para B2B](b2b/conditional-access.md)
#### [Directivas de uso compartido de B2B](b2b/delegate-invitations.md)
#### [Adición de un usuario B2B a un rol](b2b/add-guest-to-role.md)
#### [Grupos dinámicos y usuarios de B2B](b2b/use-dynamic-groups.md)
#### [Salida de una organización](b2b/leave-the-organization.md)
#### [Informes y auditoría](b2b/auditing-and-reporting.md)
#### [B2B para organizaciones híbridas](b2b/hybrid-organizations.md)
##### [Concesión a los usuarios B2B de acceso a las aplicaciones locales](b2b/hybrid-cloud-to-on-premises.md)
##### [Concesión a los usuarios locales de acceso a las aplicaciones en la nube](b2b/hybrid-on-premises-to-cloud.md)
#### [Uso compartido externo de B2B y Office 365](b2b/o365-external-user.md)
#### [Licencias B2B](b2b/licensing-guidance.md)
#### [Limitaciones actuales](b2b/current-limitations.md)
#### [P+F](b2b/faq.md)
#### [Solución de problemas de B2B](b2b/troubleshoot.md)
#### [Descripción del usuario de B2B](b2b/user-properties.md)
#### [Token de usuario de B2B](b2b/user-token.md)
#### [B2B para las aplicaciones integradas de Azure AD](b2b/configure-saas-apps.md)
#### [Asignación de notificaciones de usuario de B2B](b2b/claims-mapping.md)
#### [Comparación de la colaboración B2B con B2C](b2b/compare-with-b2c.md)
#### [Obtención de soporte técnico para B2B](b2b/get-support.md)

## [Administración de grupos y miembros](active-directory-manage-groups.md)
### Administrar grupos
#### [Azure Portal](active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell for Graph (v2)](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](active-directory-accessmanagement-groups-settings-cmdlets.md)
### [Administración de miembros del grupo](active-directory-groups-members-azure-portal.md)
### [Administración de propietarios de grupo](active-directory-accessmanagement-managing-group-owners.md)
### [Administración de pertenencia al grupo](active-directory-groups-membership-azure-portal.md)
### [Asignación de licencias mediante grupos](active-directory-licensing-whatis-azure-portal.md)
#### [Asignación de licencias a un grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificación y resolución de problemas de licencias de un grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migración de usuarios individuales con licencia a licencias basadas en grupos](active-directory-licensing-group-migration-azure-portal.md)
#### [Migración de usuarios entre licencias de productos](active-directory-licensing-group-product-migration.md)
#### [Escenarios adicionales para licencias basadas en grupos](active-directory-licensing-group-advanced.md)
#### [Ejemplos de Azure PowerShell para licencias basadas en grupos](active-directory-licensing-ps-examples.md)
#### [Referencia de productos y planes de servicio en Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Configuración de la expiración de los grupos de Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Aplicación una directiva de nomenclatura para grupos](groups-naming-policy.md)
### [Visualización de todos los grupos](active-directory-groups-view-azure-portal.md)
### [Administración del acceso de grupo a aplicaciones SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Restauración de un grupo eliminado de Office 365](active-directory-groups-restore-azure-portal.md)
### [Administración de la configuración de grupo](active-directory-groups-settings-azure-portal.md) 
### Creación de reglas avanzadas
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
### [Configuración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md)
### [Solución de problemas](active-directory-accessmanagement-troubleshooting.md)

## [Administración de informes](active-directory-reporting-azure-portal.md)
### [Actividad de inicios de sesión](active-directory-reporting-activity-sign-ins.md)
### [Actividad de auditoría](active-directory-reporting-activity-audit-logs.md)
### [Usuarios en riesgo](active-directory-reporting-security-user-at-risk.md)
### [Inicios de sesión no seguros](active-directory-reporting-security-risky-sign-ins.md)
### [Eventos de riesgo](active-directory-reporting-risk-events.md)
### [P+F](active-directory-reporting-faq.md)
### Tareas
#### [Configuración de ubicaciones con nombre](active-directory-named-locations.md)
#### [Búsqueda de informes de actividad](active-directory-reporting-migration.md)
#### [Uso del paquete de contenido de Power BI de Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Corrección de usuarios marcados en riesgo](active-directory-report-security-user-at-risk-remediation.md)
### Referencia
#### [Retención](active-directory-reporting-retention.md)
#### [Latencias](active-directory-reporting-latencies-azure-portal.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### [Referencia sobre actividades de auditoría](active-directory-reporting-activity-audit-reference.md)
#### [Códigos de error de la actividad de inicio de sesión](active-directory-reporting-activity-sign-ins-errors.md)
#### [Autenticación multifactor](active-directory-reporting-activity-sign-ins-mfa.md



### Solución de problemas
#### [Datos de auditoría que faltan](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Datos que faltan en las descargas](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Errores de los paquetes de contenido de los registros de actividad de Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
### [Acceso mediante programación](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Referencia de auditoría](active-directory-reporting-api-audit-reference.md)
#### [Referencia de inicio de sesión](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Requisitos previos](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Ejemplos de auditoría](active-directory-reporting-api-audit-samples.md)
#### [Ejemplos de inicio de sesión](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Uso de certificados](active-directory-reporting-api-with-certificates.md)

## Administrar contraseñas
### [Introducción a las contraseñas](authentication/active-directory-passwords-overview.md)
### Documentos de usuario
#### [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md)
#### [Procedimientos recomendados sobre las contraseñas](active-directory-secure-passwords.md)
#### [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md)
### [Vea cómo funciona SSPR](authentication/concept-sspr-howitworks.md)
### [Guía de implementación de SSPR](authentication/howto-sspr-deployment.md)
### [SSPR y Windows 10](authentication/tutorial-sspr-windows.md)
### [Directivas de SSPR](authentication/concept-sspr-policy.md)
### [Personalización de SSPR](authentication/concept-sspr-customization.md)
### [Requisitos de datos de SSPR](authentication/howto-sspr-authenticationdata.md)
### [Informes de SSPR](authentication/howto-sspr-reporting.md)
### Administradores de TI: Restablecimiento de contraseñas
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
### [Licencia de SSPR](authentication/concept-sspr-licensing.md)
### [Escritura diferida de contraseñas](authentication/howto-sspr-writeback.md)
### [Solución de problemas](authentication/active-directory-passwords-troubleshoot.md)
### [P+F](authentication/active-directory-passwords-faq.md)


## Administración de dispositivos
### [Introducción](device-management-introduction.md)
### [Uso de Azure Portal](device-management-azure-portal.md)
### [Planear Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Preguntas más frecuentes](device-management-faq.md)
### Tareas
#### [Configuración de dispositivos con Windows 10 registrados en Azure AD](device-management-azuread-registered-devices-windows10-setup.md)
#### [Configuración de dispositivos unidos a Azure AD](device-management-azuread-joined-devices-setup.md)
#### [Configuración de dispositivos híbridos unidos a Azure AD](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Implementación local](active-directory-device-registration-on-premises-setup.md)
#### [Combinación de Azure AD durante la primera ejecución de Windows 10](device-management-azuread-joined-devices-frx.md)
### Solución de problemas
#### [Dispositivos combinados híbridos de Azure AD para Windows 10 y Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Dispositivos combinados híbridos de Azure AD para Windows heredado](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Administración de aplicaciones
### [Información general](manage-apps/what-is-application-management.md)
### [Introducción](manage-apps/plan-an-application-integration.md)
### [Tutoriales de integración de aplicaciones SaaS](active-directory-saas-tutorial-list.md)
### [Detección de aplicaciones de nube](manage-apps/cloud-app-discovery.md)
#### [Creación de informes de instantánea](manage-apps/cloud-app-discovery-create-snapshot-reports.md)
#### [Configuración de informes continuos](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Uso de un analizador de registros personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser)

### [Aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS](active-directory-saas-app-provisioning.md) 
#### [Tutoriales de integración de aplicaciones](active-directory-saas-tutorial-list.md) 
#### [Automatización del aprovisionamiento para aplicaciones habilitadas con SCIM](active-directory-scim-provisioning.md) 
#### [Personalización de asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md) 
#### [Escritura de expresiones para la asignación de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Uso de filtros de ámbito](active-directory-saas-scoping-filters.md) 
#### [Notificación del aprovisionamiento automático de usuarios](active-directory-saas-provisioning-reporting.md) 
#### [Solución de problemas de aprovisionamiento de usuarios](active-directory-application-provisioning-content-map.md) 

### [Acceso remoto a las aplicaciones con el proxy de aplicación](manage-apps/application-proxy.md)
#### Introducción
##### [Habilitación del proxy de aplicación](manage-apps/application-proxy-enable.md)
##### [Publicación de aplicaciones](manage-apps/application-proxy-publish-azure-portal.md)
##### [Dominios personalizados](manage-apps/application-proxy-configure-custom-domain.md)
#### [Inicio de sesión único](manage-apps/application-proxy-single-sign-on.md)
##### [SSO con KCD](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [SSO con encabezados](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [SSO con almacenamiento de contraseñas](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Conceptos
##### [Conectores](manage-apps/application-proxy-connectors.md)
##### [Seguridad](manage-apps/application-proxy-security.md)
##### [Redes](manage-apps/application-proxy-network-topology.md)


##### [Actualización desde TMG o UAG](manage-apps/application-proxy-migration.md)

#### Configuraciones avanzadas
##### [Publicación en redes independientes](manage-apps/application-proxy-connector-groups.md)
##### [Servidores proxy](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Aplicaciones compatibles con notificaciones](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Aplicaciones de cliente nativo](manage-apps/application-proxy-configure-native-client-application.md)
##### [Instalación silenciosa](manage-apps/application-proxy-register-connector-powershell.md)
##### [Página de inicio personalizada](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Traducción de vínculos insertados](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Caracteres comodín](active-directory-application-proxy-wildcard.md)
##### [Eliminación de datos personales](manage-apps/application-proxy-remove-personal-data.md)


#### Tutoriales de publicación
##### [Escritorio remoto](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](application-proxy-teams.md)
##### [Tableau](active-directory-application-proxy-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Solución de problemas](active-directory-application-proxy-troubleshoot.md)

### Administración de aplicaciones empresariales
#### [Asignación de usuarios](active-directory-coreapps-assign-user-azure-portal.md)
#### [Personalización de la información de marca](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [¿Cómo puedo deshabilitar los inicios de sesión de usuario?](active-directory-coreapps-disable-app-azure-portal.md)
#### [Eliminación de usuarios](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Visualización de todas mis aplicaciones](active-directory-coreapps-view-azure-portal.md)
#### [Administración del aprovisionamiento de cuentas de usuario](active-directory-enterprise-apps-manage-provisioning.md)
#### [Administración de inicio de sesión único para aplicaciones empresariales](active-directory-enterprise-apps-manage-sso.md)
#### [Firma avanzada de certificados para aplicaciones SAML](active-directory-enterprise-apps-advance-certificate-options.md)
#### [Ocultar una aplicación de una experiencia de usuario](active-directory-coreapps-hide-third-party-app.md)
### [Configuración de la aceleración automática del inicio de sesión mediante la directiva de HRD](active-directory-auto-acceleration-using-hrd.md)
### [Migración de aplicaciones de AD FS a Azure AD](migrate-adfs-apps-to-azure.md) 
### [Administración del acceso a aplicaciones](active-directory-managing-access-to-apps.md)
#### [Acceso con SSO](manage-apps/what-is-single-sign-on.md)
#### [Certificados para SSO](active-directory-sso-certs.md)
#### [Restricciones de inquilino](active-directory-tenant-restrictions.md)
#### [Uso de usuarios de aprovisionamiento de SCIM](active-directory-scim-provisioning.md)

### [Solución de problemas](active-directory-application-troubleshoot-content-map.md)
#### [Desarrollo de aplicaciones](active-directory-application-dev-troubleshoot-content-map.md)
##### [Configuración y registro](active-directory-application-dev-config-content-map.md)
##### [Desarrollo](active-directory-application-dev-development-content-map.md)
#### [Administración de aplicaciones](active-directory-application-management-troubleshoot-content-map.md)
##### [Configuración](active-directory-application-config-content-map.md)
##### [Inicio de sesión](active-directory-application-sign-in-content-map.md)
##### [Aprovisionamiento](active-directory-application-provisioning-content-map.md)

###### [Comprobación de si un usuario está aprovisionado](application-provisioning-when-will-provisioning-finish-specific-user.md) 
###### [Aprovisionamiento que tarda mucho tiempo](application-provisioning-when-will-provisioning-finish.md) 
###### [Configuración del aprovisionamiento de usuarios](application-provisioning-config-how-to.md) 
###### [Problema con la configuración de aprovisionamiento](application-provisioning-config-problem.md) 
###### [Problema al guardar las credenciales del administrador](application-provisioning-config-problem-storage-limit.md) 
###### [No se está aprovisionando ningún usuario](application-provisioning-config-problem-no-users-provisioned.md) 
###### [Se está aprovisionando a los usuarios equivocados](application-provisioning-config-problem-wrong-users-provisioned.md) 

##### [Administración del acceso](active-directory-application-access-content-map.md)
##### [Panel de acceso](active-directory-application-access-panel-content-map.md)
##### [Proxy de aplicación](active-directory-application-proxy-content-map.md)
##### [Acceso condicional](active-directory-application-conditional-access-content-map.md)
### [Desarrollo de aplicaciones](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Biblioteca de documentos](active-directory-apps-index.md)

## Administración del directorio
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nombres de dominio personalizados
#### [Guía de inicio rápido](add-custom-domain.md)
#### [Agregar nombres de dominio personalizados](active-directory-domains-manage-azure-portal.md)
### [Administración de directorios](active-directory-administer.md)
### [Varios directorios](active-directory-licensing-directory-independence.md)
### [Suscripción de autoservicio](active-directory-self-service-signup.md)
### [Asumir el control de un directorio no administrado](domains-admin-takeover.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Habilitación](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Configuración de directiva de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Configuración de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Preguntas más frecuentes](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Solución de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integración de identidades locales con Azure AD Connect](./connect/active-directory-aadconnect.md)

## [Administración del acceso a Azure](../role-based-access-control/toc.yml)

## Administración del acceso a los recursos
### [Roles de administrador](active-directory-assign-admin-roles-azure-portal.md)
#### [Asignación de un rol de administrador a un usuario](active-directory-users-assign-role-azure-portal.md) 
#### [Comparación de permisos para miembros y para usuarios invitados](users-default-permissions.md) 
### [Protección del acceso con privilegios](admin-roles-best-practices.md)  
### [Creación de cuentas administrativas de acceso de emergencia](active-directory-admin-manage-emergency-access-accounts.md) 


#### [Permisos predeterminados de usuario](users-default-permissions.md)
### [Unidades administrativas](active-directory-administrative-units-management.md)
### [Configuración de la vigencia de los tokens](active-directory-configurable-token-lifetimes.md)
### [Protección de los roles con privilegios](admin-roles-best-practices.md)

## Revisiones de acceso
### [Introducción a las revisiones de acceso](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Finalización de una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md)
### [Creación de una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md)
### [Revisión de acceso en Privileged Identity Management de Azure AD](active-directory-azure-ad-controls-perform-access-review.md)
### [Revisión del acceso](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Acceso de invitado con revisiones de acceso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Administración del acceso de usuarios con revisiones](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Administración de programas y controles](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Recuperación de los resultados de revisión de acceso](active-directory-azure-ad-controls-retrieve-access-review.md)

## Protección de las identidades
### [Acceso condicional](active-directory-conditional-access-azure-portal.md)
#### [Condiciones](active-directory-conditional-access-conditions.md)
#### [Condiciones de ubicación](active-directory-conditional-access-locations.md)
#### [Controles](active-directory-conditional-access-controls.md)
#### [Primeros pasos](active-directory-conditional-access-azure-portal-get-started.md)
#### [procedimientos recomendados](active-directory-conditional-access-best-practices.md)
#### [Descripción de directivas de dispositivo para servicios de Office 365](active-directory-conditional-access-device-policies.md)
#### [Migración de directivas clásicas](active-directory-conditional-access-migration.md)
#### [Herramienta What if](active-directory-conditional-access-whatif.md)
#### Guía de inicio rápido
##### [Configuración de Multi-Factor Authentication para aplicaciones por cada nube](active-directory-conditional-access-app-based-mfa.md)
#### Tareas
##### [Migración de directiva de MFA clásica](active-directory-conditional-access-migration-mfa.md)
##### [Configuración del acceso condicional basado en dispositivos](active-directory-conditional-access-policy-connected-applications.md)
##### [Configuración del acceso condicional basado en aplicaciones](active-directory-conditional-access-mam.md)
##### [Proporcionar condiciones de uso para los usuarios y las aplicaciones](active-directory-tou.md)
##### [Configuración de la conectividad VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Configuración de SharePoint y Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Corrección](active-directory-conditional-access-device-remediation.md)
#### [Referencia técnica](active-directory-conditional-access-technical-reference.md)
#### [Preguntas más frecuentes](active-directory-conditional-faqs.md)


### Autenticación basada en certificados
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Primeros pasos](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Habilitación](active-directory-identityprotection-enable.md)
#### [Detección de vulnerabilidades](active-directory-identityprotection-vulnerabilities.md)
#### [Eventos de riesgo](active-directory-identity-protection-risk-events.md)
#### [Notificaciones](active-directory-identityprotection-notifications.md)
#### [Experiencia de inicio de sesión](active-directory-identityprotection-flows.md)
#### [Simulación de eventos de riesgo](active-directory-identityprotection-playbook.md)
#### [Desbloqueo de usuarios](active-directory-identityprotection-unblock-howto.md)
#### [Preguntas más frecuentes](active-directory-identity-protection-faqs.md)
#### [Glosario](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## Integración de otros servicios con Azure AD 
### [Integración de LinkedIn con Azure AD](linkedin-integration.md)

## [Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md)
### [Alta disponibilidad](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Cambio de algoritmo hash de firma](active-directory-federation-sha256-guidance.md)

## [Solución de problemas](active-directory-troubleshooting-support-howto.md)

## Implementación de la prueba de concepto (PoC) de Azure AD
### [Guía de la prueba de concepto: Introducción](active-directory-playbook-intro.md)
### [Guía de la prueba de concepto: Ingredientes](active-directory-playbook-ingredients.md)
### [Guía de la prueba de concepto: Implementación](active-directory-playbook-implementation.md)
### [Guía de la prueba de concepto: Bloques de creación](active-directory-playbook-building-blocks.md)


# Referencia
## [Ejemplos de código](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [cmdlets de Azure PowerShell](/powershell/azure/overview)
## [Referencia de la API de Java](/java/api)
## [API de .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Restricciones y límites del servicio](active-directory-service-limits-restrictions.md)

# Temas relacionados
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD para desarrolladores](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Recursos
## [Foro de comentarios de Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Precios](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=active-directory)
## [Desbordamiento de la pila](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
