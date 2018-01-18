# Información general
## [¿Qué es Azure Active Directory?](active-directory-whatis.md)
## [Acerca de la administración de identidades de Azure](identity-fundamentals.md)
## [Información acerca de las soluciones de identidades de Azure](understand-azure-identity-solutions.md)
## [Elección de una solución de identidad híbrida](choose-hybrid-identity-solution.md)
## [Asociación de suscripciones de Azure](active-directory-how-subscriptions-associated-directory.md)
## [Preguntas más frecuentes](active-directory-faq.md)
## [Novedades](whats-new.md)


# Introducción
## [Introducción a Azure AD](get-started-azure-ad.md)
## [Suscripción a Azure AD Premium](active-directory-get-started-premium.md)
## [Adición de un dominio personalizado](add-custom-domain.md)
## [Configuración de la personalización de marca de la compañía](customize-branding.md)
## [Adición de usuarios a Azure AD](add-users-azure-active-directory.md)
## [Asignación de licencias a usuarios](license-users-groups.md)
## [Configuración del autoservicio de restablecimiento de contraseña](active-directory-passwords-getting-started.md)


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
### [Adición de usuarios invitados de otro directorio (B2B)](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Adición de usuarios de B2B por los administradores](active-directory-b2b-admin-add-users.md)
#### [Adición de usuarios de B2B por los trabajadores de la información](active-directory-b2b-iw-add-users.md)
#### [API y personalización](active-directory-b2b-api.md)
#### [Ejemplos de Code y Azure PowerShell](active-directory-b2b-code-samples.md)
#### [Ejemplo de portal de registro de autoservicio](active-directory-b2b-self-service-portal.md)
#### [Invitación por correo electrónico](active-directory-b2b-invitation-email.md)
#### [Canje de invitación](active-directory-b2b-redemption-experience.md)
#### [Incorporación de usuarios B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
#### [Acceso condicional para B2B](active-directory-b2b-mfa-instructions.md)
#### [Directivas de uso compartido de B2B](active-directory-b2b-delegate-invitations.md)
#### [Adición de un usuario B2B a un rol](active-directory-b2b-add-guest-to-role.md)
#### [Grupos dinámicos y usuarios de B2B](active-directory-b2b-dynamic-groups.md)
#### [Informes y auditoría](active-directory-b2b-auditing-and-reporting.md)
#### [B2B para organizaciones híbridas](active-directory-b2b-hybrid-organizations.md)
#### [Uso compartido externo de B2B y Office 365](active-directory-b2b-o365-external-user.md)
#### [Licencias B2B](active-directory-b2b-licensing.md)
#### [Limitaciones actuales](active-directory-b2b-current-limitations.md)
#### [P+F](active-directory-b2b-faq.md)
#### [Solución de problemas de B2B](active-directory-b2b-troubleshooting.md)
#### [Descripción del usuario de B2B](active-directory-b2b-user-properties.md)
#### [Token de usuario de B2B](active-directory-b2b-user-token.md)
#### [B2B para las aplicaciones integradas de Azure AD](active-directory-b2b-configure-saas-apps.md)
#### [Asignación de notificaciones de usuario de B2B](active-directory-b2b-claims-mapping.md)
#### [Comparación de la colaboración B2B con B2C](active-directory-b2b-compare-b2c.md)
#### [Obtención de soporte técnico para B2B](active-directory-b2b-support.md)

## [Administración de grupos y miembros](active-directory-manage-groups.md)
### Administrar grupos
#### [portal de Azure](active-directory-groups-create-azure-portal.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Administración de miembros del grupo](active-directory-groups-members-azure-portal.md)
### [Administración de propietarios de grupo](active-directory-accessmanagement-managing-group-owners.md)
### [Administración de pertenencia al grupo](active-directory-groups-membership-azure-portal.md)
### [Asignación de licencias mediante grupos](active-directory-licensing-whatis-azure-portal.md)
#### [Asignación de licencias a un grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificación y resolución de problemas de licencias de un grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migración de usuarios individuales con licencia a licencias basadas en grupos](active-directory-licensing-group-migration-azure-portal.md)
#### [Escenarios adicionales para licencias basadas en grupos](active-directory-licensing-group-advanced.md)
#### [Ejemplos de Azure PowerShell para licencias basadas en grupos](active-directory-licensing-ps-examples.md)
#### [Referencia de productos y planes de servicio en Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Configuración de la expiración de los grupos de Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Visualización de todos los grupos](active-directory-groups-view-azure-portal.md)
### [Administración del acceso de grupo a aplicaciones SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Restauración de un grupo eliminado de Office 365](active-directory-groups-restore-azure-portal.md)
### Administración de la configuración de grupo
#### [Azure Portal](active-directory-groups-settings-azure-portal.md)
#### [Cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Creación de reglas avanzadas
#### [Portal de Azure](active-directory-groups-dynamic-membership-azure-portal.md)
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
### Referencia
#### [Retención](active-directory-reporting-retention.md)
#### [Latencias](active-directory-reporting-latencies-azure-portal.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### [Códigos de error de la actividad de inicio de sesión](active-directory-reporting-activity-sign-ins-errors.md)
#### [Multi-Factor Authentication](active-directory-reporting-activity-sign-ins-mfa.md)
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
### [Introducción a las contraseñas](active-directory-passwords-overview.md)
### Documentos de usuario
#### [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md)
#### [Procedimientos recomendados sobre las contraseñas](active-directory-secure-passwords.md)
#### [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md)
### [Vea cómo funciona SSPR](active-directory-passwords-how-it-works.md)
### [Guía de implementación de SSPR](active-directory-passwords-best-practices.md)
### [SSPR y Windows 10](active-directory-passwords-login.md)
### [Directivas de SSPR](active-directory-passwords-policy.md)
### [Personalización de SSPR](active-directory-passwords-customize.md)
### [Requisitos de datos de SSPR](active-directory-passwords-data.md)
### [Informes de SSPR](active-directory-passwords-reporting.md)
### Administradores de TI: Restablecimiento de contraseñas
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
### [Licencia de SSPR](active-directory-passwords-licensing.md)
### [Escritura diferida de contraseñas](active-directory-passwords-writeback.md)
### [Solución de problemas](active-directory-passwords-troubleshoot.md)
### [P+F](active-directory-passwords-faq.md)


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
### [Información general](active-directory-enable-sso-scenario.md)
### [Introducción](active-directory-integrating-applications-getting-started.md)
### [Tutoriales de integración de aplicaciones SaaS](active-directory-saas-tutorial-list.md)
### [Detección de aplicaciones de nube](cloudappdiscovery-get-started.md)
#### [Creación de informes de instantánea](cloudappdiscovery-set-up-snapshots.md)
#### [Configuración de informes continuos](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Uso de un analizador de registros personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
#### Detección basada en agente
##### [¿Qué es Cloud App Discovery?](active-directory-cloudappdiscovery-whatis.md)
##### [Actualización de configuración del registro](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
##### [Descripción de la seguridad y privacidad](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)


### [Acceso remoto a las aplicaciones con el proxy de aplicación](active-directory-application-proxy-get-started.md)
#### Introducción
##### [Habilitación del proxy de aplicación](active-directory-application-proxy-enable.md)
##### [Publicación de aplicaciones](application-proxy-publish-azure-portal.md)
##### [Dominios personalizados](active-directory-application-proxy-custom-domains.md)
#### [Inicio de sesión único](application-proxy-sso-overview.md)
##### [SSO con KCD](active-directory-application-proxy-sso-using-kcd.md)
##### [SSO con encabezados](application-proxy-ping-access.md)
##### [SSO con almacenamiento de contraseñas](application-proxy-sso-azure-portal.md)
#### Conceptos
##### [Conectores](application-proxy-understand-connectors.md)
##### [Seguridad](application-proxy-security-considerations.md)
##### [Redes](application-proxy-network-topology-considerations.md)


##### [Actualización desde TMG o UAG](application-proxy-transition-from-uag-tmg.md)

#### Configuraciones avanzadas
##### [Publicación en redes independientes](active-directory-application-proxy-connectors-azure-portal.md)
##### [Servidores proxy](application-proxy-working-with-proxy-servers.md)
##### [Aplicaciones compatibles con notificaciones](active-directory-application-proxy-claims-aware-apps.md)
##### [Aplicaciones de cliente nativo](active-directory-application-proxy-native-client.md)
##### [Instalación silenciosa](active-directory-application-proxy-silent-installation.md)
##### [Página de inicio personalizada](application-proxy-office365-app-launcher.md)
##### [Traducción de vínculos insertados](application-proxy-link-translation.md)
#### Tutoriales de publicación
##### [Escritorio remoto](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Microsoft Teams](application-proxy-teams.md)
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

### [Administración del acceso a aplicaciones](active-directory-managing-access-to-apps.md)
#### [Acceso con SSO](active-directory-appssoaccess-whatis.md)
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

## [Administración del acceso a Azure](toc.yml)

## Administración del acceso a los recursos
### [Roles de administrador](active-directory-assign-admin-roles-azure-portal.md)
#### [Asignación de roles de administrador](active-directory-users-assign-role-azure-portal.md)
### [Unidades administrativas](active-directory-administrative-units-management.md)
### [Configuración de la vigencia de los tokens](active-directory-configurable-token-lifetimes.md)
### [Administración de cuentas administrativas de acceso de emergencia](active-directory-admin-manage-emergency-access-accounts.md)

## Revisiones de acceso
### [Introducción a las revisiones de acceso](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Finalización de una revisión de acceso](active-directory-azure-ad-controls-complete-access-review.md)
### [Creación de una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md)
### [Revisión de acceso en Privileged Identity Management de Azure AD](active-directory-azure-ad-controls-perform-access-review.md)
### [Revisión del acceso](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Acceso de invitado con revisiones de acceso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Administración del acceso de usuarios con revisiones](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Administración de programas y controles](active-directory-azure-ad-controls-manage-programs-controls.md)


## Protección de las identidades
### [Acceso condicional](active-directory-conditional-access-azure-portal.md)
#### [Controles](active-directory-conditional-access-controls.md)
#### [Condición de ubicación](active-directory-conditional-access-locations.md)
#### [Primeros pasos](active-directory-conditional-access-azure-portal-get-started.md)
#### [procedimientos recomendados](active-directory-conditional-access-best-practices.md)
#### [Descripción de directivas de dispositivo para servicios de Office 365](active-directory-conditional-access-device-policies.md)
#### [Migración de directivas clásicas](active-directory-conditional-access-migration.md)
#### [Herramienta What if](active-directory-conditional-access-whatif.md)
#### Tareas
##### [Migración de directiva de MFA clásica](active-directory-conditional-access-migration-mfa.md)
##### [Configuración del acceso condicional basado en dispositivos](active-directory-conditional-access-policy-connected-applications.md)
##### [Configuración del acceso condicional basado en aplicaciones](active-directory-conditional-access-mam.md)
##### [Proporcionar condiciones de uso para los usuarios y las aplicaciones](active-directory-tou.md)
##### [Configuración de la conectividad VPN](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [Configuración de SharePoint y Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Corrección](active-directory-conditional-access-device-remediation.md)
#### [Referencia técnica](active-directory-conditional-access-technical-reference.md)
#### [Preguntas más frecuentes](active-directory-conditional-faqs.md)

### Windows Hello
#### [Autenticación sin contraseñas](active-directory-azureadjoin-passport.md)
#### [Habilitación de Windows Hello para empresas](active-directory-azureadjoin-passport-deployment.md)
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
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Integración de otros servicios con Azure AD]()
### [Habilitación de la integración con LinkedIn](linkedin-integration.md)

## [Implementación de AD DS en máquinas virtuales de Azure](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory en máquinas virtuales de Azure](active-directory-deploying-ws-ad-guidelines.md)
### [Controlador de dominio de réplica en una red virtual de Azure](active-directory-install-replica-active-directory-domain-controller.md)
### [Nuevo bosque en una red virtual de Azure](active-directory-new-forest-virtual-machine.md)



## [Implementación de AD FS en Azure](active-directory-aadconnect-azure-adfs.md)
### [Alta disponibilidad](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Cambio de algoritmo hash de firma](active-directory-federation-sha256-guidance.md)

## [Solución de problemas](active-directory-troubleshooting-support-howto.md)
### [Solución de problemas: El elemento "Active Directory" falta o no está disponible](active-directory-troubleshooting.md)

## Implementación de la prueba de concepto (PoC) de Azure AD
### [Guía de la prueba de concepto: Introducción](active-directory-playbook-intro.md)
### [Guía de la prueba de concepto: Ingredientes](active-directory-playbook-ingredients.md)
### [Guía de la prueba de concepto: Implementación](active-directory-playbook-implementation.md)
### [Guía de la prueba de concepto: Bloques de creación](active-directory-playbook-building-blocks.md)


# Referencia
## [Ejemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
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
