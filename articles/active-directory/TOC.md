# Información general
## [¿Qué es Azure Active Directory?](active-directory-whatis.md)
## [Elección de la edición](active-directory-editions.md)
## [Acerca de la administración de identidades de Azure](fundamentals-identity.md)
## [Vista previa de la experiencia del portal de Azure AD](active-directory-preview-explainer.md)


# Primeros pasos
## [Obtención de un inquilino de Azure AD](active-directory-howto-tenant.md)
## [Suscripción a Azure AD Premium](active-directory-get-started-premium.md)
## [Asociación de suscripciones de Azure](active-directory-how-subscriptions-associated-directory.md)
## Administración de licencias de Azure AD
### [Portal de Azure](active-directory-licensing-get-started-azure-portal.md)
### [Portal clásico](active-directory-licensing-what-is.md)
## [Obtención de Azure para su organización](sign-up-organization.md)
## [Preguntas más frecuentes](active-directory-faq.md)
## [Tutoriales de aplicaciones SaaS](active-directory-saas-tutorial-list.md)

# Procedimientos
## Planeamiento y diseño
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
### Agregar usuarios
#### [Azure Portal](active-directory-users-create-azure-portal.md)
#### [Portal clásico](active-directory-create-users.md)

### [Adición de usuarios de otros directorios (portal clásico)](active-directory-create-users-external.md)
### [Eliminación de usuarios](active-directory-users-delete-user-azure-portal.md)
### [Administrar perfiles de usuario](active-directory-users-profile-azure-portal.md)
### [Restablecimiento de una contraseña](active-directory-users-reset-password-azure-portal.md)
### [Administración de la información de trabajo de usuario](active-directory-users-work-info-azure-portal.md)
### [Uso compartido de cuentas](active-directory-sharing-accounts.md)

## [Administración de grupos y miembros](active-directory-manage-groups.md)
### Administrar grupos
#### [Azure Portal](active-directory-groups-create-azure-portal.md)
#### [Portal clásico](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Administración de miembros del grupo](active-directory-groups-members-azure-portal.md)
### [Administración de propietarios de grupo](active-directory-accessmanagement-managing-group-owners.md)
### [Administración de pertenencia al grupo](active-directory-groups-membership-azure-portal.md)
### [Visualización de todos los grupos](active-directory-groups-view-azure-portal.md)
### [Habilitación de grupos dedicados](active-directory-accessmanagement-dedicated-groups.md)
### [Administración del acceso de grupo a aplicaciones SaaS](active-directory-accessmanagement-group-saasapps.md)
### Administración de la configuración de grupo
#### [Azure Portal](active-directory-groups-settings-azure-portal.md)
#### [Cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Creación de reglas avanzadas
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Portal clásico](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Licencias basadas en grupos](active-directory-licensing-whatis-azure-portal.md)
#### [Asignación de licencias a un grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificación y resolución de problemas de licencias de un grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migración de usuarios individuales con licencia a licencias basadas en grupos](active-directory-licensing-group-migration-azure-portal.md)
#### [Escenarios adicionales para licencias basadas en grupos](active-directory-licensing-group-advanced.md)
### [Configuración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md)
### [Solución de problemas](active-directory-accessmanagement-troubleshooting.md)

## [Administración de informes](active-directory-reporting-azure-portal.md)
### [Actividad de inicios de sesión](active-directory-reporting-activity-sign-ins.md)
### [Actividad de auditoría](active-directory-reporting-activity-audit-logs.md)
### [Usuarios en riesgo](active-directory-reporting-security-user-at-risk.md)
### [Inicios de sesión no seguros](active-directory-reporting-security-risky-sign-ins.md)
### [Eventos de riesgo](active-directory-reporting-risk-events.md)
### [Redes con nombre](active-directory-known-networks-azure-portal.md)
### [Migración de informes](active-directory-reporting-migration.md)
### [Retención](active-directory-reporting-retention.md)
### [Preguntas más frecuentes](active-directory-reporting-faq.md)
### Solución de problemas
#### [Datos de auditoría que faltan](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Datos que faltan en las descargas](active-directory-reporting-troubleshoot-missing-data-download.md)
###    Acceso mediante programación
#### [Referencia de auditoría](active-directory-reporting-api-audit-reference.md)
#### [Ejemplos de auditoría](active-directory-reporting-api-audit-samples.md)
#### [Requisitos previos](active-directory-reporting-api-prerequisites.md)
#### [Referencia de inicio de sesión](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Ejemplos de inicio de sesión](active-directory-reporting-api-sign-in-activity-samples.md)
### [Portal clásico](active-directory-view-access-usage-reports.md)
#### [Informes de Azure AD](active-directory-reporting-getting-started.md)
#### [Guía de creación de informes](active-directory-reporting-guide.md)
#### [Redes conocidas](active-directory-known-networks.md)
#### [API](active-directory-reporting-api-getting-started.md)
#### [Eventos de auditoría](active-directory-reporting-audit-events.md)
#### [Latencias](active-directory-reporting-latencies.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### Comprensión de informes
##### [Inicio de sesión irregular](active-directory-reporting-irregular-sign-in-activity.md)
##### [Varios errores](active-directory-reporting-sign-ins-after-multiple-failures.md)
##### [Inicios de sesión desde direcciones IP sospechosas](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
##### [Varias zonas geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md)
##### [Dispositivos posiblemente infectados](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)
##### [Orígenes desconocidos](active-directory-reporting-sign-ins-from-unknown-sources.md)
##### [Inicios de sesión anómalos](active-directory-reporting-users-with-anomalous-sign-in-activity.md)

## [Administrar contraseñas](active-directory-manage-passwords.md)
### [Actualización de la propia contraseña](active-directory-passwords-update-your-own-password.md)
### [Configuración del restablecimiento de contraseña de autoservicio](active-directory-passwords.md)
### [Descripción de la administración de contraseñas](active-directory-passwords-how-it-works.md)
### [Descripción de directivas y contraseñas](active-directory-passwords-policy.md)
### Restablecimiento de contraseñas
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
#### [Portal clásico](active-directory-create-users-reset-password.md)
### [Establecimiento de directivas de expiración](active-directory-passwords-set-expiration-policy.md)
### Habilitación de la administración de contraseñas
#### [Primeros pasos](active-directory-passwords-getting-started.md)
#### [Implementación](active-directory-passwords-best-practices.md)
#### [Personalización](active-directory-passwords-customize.md)
#### [Visualización de informes](active-directory-passwords-get-insights.md)
#### [Más información](active-directory-passwords-learn-more.md)
#### [Preguntas más frecuentes](active-directory-passwords-faq.md)
#### [Solución de problemas](active-directory-passwords-troubleshoot.md)

## Administración de dispositivos
### [Registro de dispositivos](active-directory-device-registration-overview.md)
#### [Administración del registro automático](active-directory-device-registration.md)
#### [Configuración](active-directory-conditional-access-automatic-device-registration-setup.md)
#### [Implementación local](active-directory-device-registration-on-premises-setup.md)
#### [Preguntas más frecuentes](active-directory-device-registration-faq.md)
#### Solución de problemas
##### [Solución de problemas de Windows 10 y Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md)
##### [Solución de problemas de clientes de nivel inferior de Windows](active-directory-device-registration-troubleshoot-windows-legacy.md)
### [Azure AD Join](active-directory-azureadjoin-overview.md)
#### [Plan](active-directory-azureadjoin-deployment-aadjoindirect.md)
#### [Configuración del registro de dispositivos](active-directory-azureadjoin-setup.md)
#### [Registro de nuevos dispositivos](active-directory-azureadjoin-user-frx.md)
#### [Implementación](active-directory-azureadjoin-devices-group-policy.md)
#### [Descripción de la integración de Windows 10](active-directory-azureadjoin-windows10-devices-overview.md)
#### [Uso de dispositivos de Windows 10](active-directory-azureadjoin-windows10-devices.md)
#### [Conexión del dispositivo](active-directory-azureadjoin-personal-device.md)
#### [Conexión a un dispositivo con Windows 10](active-directory-azureadjoin-user-upgrade.md)

## Administración de aplicaciones
### [Información general](active-directory-enable-sso-scenario.md)
### [Introducción](active-directory-integrating-applications-getting-started.md)

### [Detección de aplicaciones de nube](active-directory-cloudappdiscovery-whatis.md)
#### [Actualización de configuración del registro](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
#### [Descripción de la seguridad y privacidad](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [Concesión de acceso remoto a las aplicaciones](active-directory-application-proxy-get-started.md)
#### [Habilitación del proxy de aplicación](active-directory-application-proxy-enable.md)
#### [Descripción de los conectores](application-proxy-understand-connectors.md)

#### Publicación de aplicaciones
##### [Portal de Azure](application-proxy-publish-azure-portal.md)
##### [Portal clásico](active-directory-application-proxy-publish.md)

#### [PingAccess para Azure AD](application-proxy-ping-access.md)
#### [Seguridad](application-proxy-security-considerations.md)
#### [Redes](application-proxy-network-topology-considerations.md)
#### [Escritorio remoto](application-proxy-publish-remote-desktop.md)
#### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)

#### Publicación en redes independientes
##### [Azure Portal](active-directory-application-proxy-connectors-azure-portal.md)
##### [Portal clásico](active-directory-application-proxy-connectors.md)
#### [Servidores proxy](application-proxy-working-with-proxy-servers.md)
#### [Dominios personalizados](active-directory-application-proxy-custom-domains.md)
#### [Acceso a aplicaciones](active-directory-appssoaccess-whatis.md)
##### [Portal de Azure](application-proxy-sso-azure-portal.md)
#### [Inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
#### [Aplicaciones compatibles con notificaciones](active-directory-application-proxy-claims-aware-apps.md)
#### [Aplicaciones de cliente nativo](active-directory-application-proxy-native-client.md)
#### [Página de inicio personalizada](application-proxy-office365-app-launcher.md)
#### [Acceso condicional](active-directory-application-proxy-conditional-access.md)
#### [Instalación silenciosa](active-directory-application-proxy-silent-installation.md)
#### [Microsoft Forefront](application-proxy-transition-from-uag-tmg.md)
#### [Solución de problemas](active-directory-application-proxy-troubleshoot.md)

### Administración de aplicaciones empresariales
#### [Asignación de usuarios](active-directory-coreapps-assign-user-azure-portal.md)
#### [Personalización de la información de marca](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [¿Cómo puedo deshabilitar los inicios de sesión de usuario?](active-directory-coreapps-disable-app-azure-portal.md)
#### [Eliminación de usuarios](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Visualización de todas mis aplicaciones](active-directory-coreapps-view-azure-portal.md)
#### [Administración del aprovisionamiento de cuentas de usuario](active-directory-enterprise-apps-manage-provisioning.md)

### Desarrollo
#### [Asignación de usuarios](active-directory-applications-guiding-developers-assigning-users.md)
#### [Asignación de grupos](active-directory-applications-guiding-developers-assigning-groups.md)
#### [Requerir asignación](active-directory-applications-guiding-developers-requiring-user-assignment.md)
#### [Desarrollo de aplicaciones de LoB](active-directory-applications-guiding-developers-for-lob-applications.md)

### [Administración del acceso a aplicaciones](active-directory-managing-access-to-apps.md)
#### [Acceso de autoservicio](active-directory-self-service-application-access.md)
#### [Certificados para SSO](active-directory-sso-certs.md)
#### [Restricciones de inquilino](active-directory-tenant-restrictions.md)

### [Uso de usuarios de aprovisionamiento de SCIM](active-directory-scim-provisioning.md)
### [Biblioteca de documentos](active-directory-apps-index.md)

## Administración del directorio
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nombres de dominio personalizados
#### [Información general](active-directory-add-domain-concepts.md)
#### Incorporación del nombre de dominio
##### [Azure Portal](active-directory-domains-add-azure-portal.md)
##### [Portal clásico](active-directory-add-domain.md)
##### [Con AD FS](active-directory-add-domain-federated.md)
#### [Asignación de usuarios](active-directory-add-domain-add-users.md)
#### Administración de nombres de dominio
##### [Azure Portal](active-directory-domains-manage-azure-portal.md)
##### [Portal clásico](active-directory-add-manage-domain-names.md)
### Personalización de la página de inicio de sesión
#### [Azure Portal](active-directory-branding-custom-signon-azure-portal.md)
#### [Específico por idioma](active-directory-branding-localize-azure-portal.md)
#### [Portal clásico](active-directory-add-company-branding.md)
### [Administración de directorios](active-directory-administer.md)
### [Varios directorios](active-directory-licensing-directory-independence.md)
### [Directorios de O365](active-directory-manage-o365-subscription.md)
### [Suscripción de autoservicio](active-directory-self-service-signup.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Habilitación](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Configuración de directiva de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Configuración de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Preguntas más frecuentes](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Solución de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [Integración de asociados con B2B de Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Adición de usuarios de B2B por los administradores](active-directory-b2b-admin-add-users.md)
#### [Adición de usuarios de B2B por los trabajadores de la información](active-directory-b2b-iw-add-users.md)
#### [Invitación por correo electrónico](active-directory-b2b-invitation-email.md)
#### [Canje de invitación](active-directory-b2b-redemption-experience.md)
#### [Propiedades de usuario B2B](active-directory-b2b-user-properties.md)
#### [Adición de un usuario invitado a un rol](active-directory-b2b-add-guest-to-role.md)
#### [Informes y auditoría](active-directory-b2b-auditing-and-reporting.md)
#### [API y personalización](active-directory-b2b-api.md)
#### [Delegación de invitaciones](active-directory-b2b-delegate-invitations.md)
#### [Grupos dinámicos y B2B](active-directory-b2b-dynamic-groups.md)
#### [Multi-Factor Authentication para B2B](active-directory-b2b-mfa-instructions.md)
#### [Tokens de usuario de B2B](active-directory-b2b-user-token.md)
#### [Asignación de notificaciones de usuario de B2B](active-directory-b2b-claims-mapping.md)
#### [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
#### [Configuración de aplicaciones SaaS para B2B](active-directory-b2b-configure-saas-apps.md)
#### [Ejemplos de código y de PowerShell](active-directory-b2b-code-samples.md)
#### [Limitaciones actuales](active-directory-b2b-current-limitations.md)
#### [Licencias](active-directory-b2b-licensing.md)
#### [Solución de problemas de B2B](active-directory-b2b-troubleshooting.md)
#### [Comparación de la colaboración B2B con B2C](active-directory-b2b-compare-b2c.md)
#### [Obtención de soporte técnico para B2B](active-directory-b2b-support.md)
#### [Preguntas más frecuentes](active-directory-b2b-faq.md)
### [Integración de identidades locales con Azure AD Connect](./connect/active-directory-aadconnect.md)


## Administración del acceso a los recursos
### [Roles de administrador](active-directory-assign-admin-roles.md)
#### [Asignación de roles de administrador](active-directory-users-assign-role-azure-portal.md)
### [Unidades administrativas](active-directory-administrative-units-management.md)
### [Acceso a los recursos de Azure](active-directory-understanding-resource-access.md)
### [Control de acceso basado en roles](role-based-access-control-what-is.md)
#### Administración de asignaciones de acceso
##### [Por usuario](role-based-access-control-manage-assignments.md)
##### [Por recurso](role-based-access-control-configure.md)
#### [Roles integrados](role-based-access-built-in-roles.md)
#### [Roles personalizados](role-based-access-control-custom-roles.md)
#### [Informes](role-based-access-control-access-change-history-report.md)
#### Más formas de administrar roles
##### [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
##### [PowerShell](role-based-access-control-manage-access-powershell.md)
##### [REST](role-based-access-control-manage-access-rest.md)
#### [Solución de problemas](role-based-access-control-troubleshooting.md)
### [Configuración de la vigencia de los tokens](active-directory-configurable-token-lifetimes.md)

## Protección de las identidades
### [Acceso condicional](active-directory-conditional-access.md)
#### [Primeros pasos](active-directory-conditional-access-azuread-connected-apps.md)
#### [Aplicaciones admitidas](active-directory-conditional-access-supported-apps.md)
#### [Descripción de directivas de dispositivo](active-directory-conditional-access-device-policies.md)
#### [Configuración de acceso a aplicaciones conectadas](active-directory-conditional-access-policy-connected-applications.md)
#### [Preguntas más frecuentes](active-directory-conditional-faqs.md)
#### [Solución de problemas](active-directory-conditional-access-device-remediation.md)
#### [Referencia](active-directory-conditional-access-technical-reference.md)
### Windows Hello
#### [Autenticación sin contraseñas](active-directory-azureadjoin-passport.md)
#### [Habilitación de Windows Hello para empresas](active-directory-azureadjoin-passport-deployment.md)
### Autenticación basada en certificados
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Habilitación](active-directory-identityprotection-enable.md)
#### [Detección de vulnerabilidades](active-directory-identityprotection-vulnerabilities.md)
#### [Eventos de riesgo](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [Experiencia de inicio de sesión](active-directory-identityprotection-flows.md)
#### [Simulación de eventos de riesgo](active-directory-identityprotection-playbook.md)
#### [Desbloqueo de usuarios](active-directory-identityprotection-unblock-howto.md)
#### [Glosario](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Implementación de AD DS en máquinas virtuales de Azure](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory en máquinas virtuales de Azure](active-directory-deploying-ws-ad-guidelines.md)
### [Controlador de dominio de réplica en una red virtual de Azure](active-directory-install-replica-active-directory-domain-controller.md)
### [Nuevo bosque en una red virtual de Azure](active-directory-new-forest-virtual-machine.md)



## [Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md)
### [Alta disponibilidad](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Cambio de algoritmo hash de firma](active-directory-federation-sha256-guidance.md)

## [Solución de problemas](active-directory-troubleshooting.md)


# Referencia
## [Cmdlets de PowerShell](/powershell/ )
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
## [Precios](https://azure.microsoft.com/pricing/details/active-directory/)
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=active-directory)
## [Foro de comentarios de Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
