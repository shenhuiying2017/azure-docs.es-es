---
title: "Guía de prueba de concepto de Azure Active Directory: bloques de creación | Documentos de Microsoft"
description: "Exploración e implementación rápida de escenarios de administración de identidades y acceso"
services: active-directory
keywords: "azure active directory, guía, prueba de concepto, PoC"
documentationcenter: 
author: dstefanMSFT
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: bdbdebe069b3150bed4aa26f1f6e677a66f75f32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Guía de prueba de concepto de Azure Active Directory: bloques de creación

## <a name="catalog-of-roles"></a>Catálogo de roles

| Rol | Descripción | Responsabilidad de la prueba de concepto (POC) |
| --- | --- | --- |
| **Equipo de arquitectura de identidad/desarrollo** | Normalmente, este equipo es el que se diseña la solución, implementa prototipos, controla las aprobaciones y finalmente entrega al equipo de operaciones. | Proporciona los entornos y es el que va a evaluar los diferentes escenarios desde la perspectiva de la facilidad de uso. |
| **Equipo de operaciones de identidad locales** | Administra los diferentes orígenes de identidad locales: bosques de Active Directory, directorios LDAP, sistemas de recursos humanos y proveedores de identidades de federación. | Proporciona acceso a los recursos necesarios locales necesarios para los escenarios de prueba de concepto.<br/>Debe estar tan poco implicado como sea posible.|
| **Propietarios técnicos de aplicaciones** | Los propietarios técnicos de las diferentes aplicaciones en la nube y servicios que se integrarán con Azure AD. | Proporcionan información detallada sobre las aplicaciones de SaaS (potencialmente instancias de prueba). |
| **Administrador global de Azure AD** | Administra la configuración de Azure AD. | Proporciona las credenciales para configurar el servicio de sincronización. Normalmente, el mismo equipo que el de Arquitectura de identidad durante la prueba de concepto, pero independiente durante la fase de operaciones.|
| **Equipo de base de datos** | Propietarios de la infraestructura de la base de datos. | Proporciona acceso al entorno SQL (AD FS o Azure AD Connect) para preparaciones de escenarios concretos.<br/>Debe estar tan poco implicado como sea posible. |
| **Equipo de red** | Propietarios de la infraestructura de red | Proporciona el acceso necesario en el nivel de red para que los servidores de sincronización accedan correctamente a los orígenes de datos y los servicios en la nube (reglas de firewall, puertos abiertos, reglas de IPSec etc.). |
| **Equipo de seguridad** | Define la estrategia de seguridad, analiza los informes de seguridad de varios orígenes y completan los hallazgos. | Proporciona escenarios de evaluación de la seguridad del destino. |

## <a name="common-prerequisites-for-all-building-blocks"></a>Requisitos previos comunes para todos los bloques de creación.

A continuación se proporcionan algunos requisitos previos necesarios para cualquier prueba de concepto con Azure AD Premium.

| Requisito previo | Recursos |
| --- | --- |
| Inquilino de Azure AD definido con una suscripción válida a Azure | [Obtención de un inquilino de Azure Active Directory](active-directory-howto-tenant.md)<br/>**Nota:** Si ya tiene un entorno con licencias de Azure AD Premium, puede obtener una suscripción de límite cero, vaya a https://aka.ms/accessaad. <br/>Más información en https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ and https://technet.microsoft.com/library/dn832618.aspx. |
| Dominios definidos y verificados | [Incorporación de un nombre de dominio personalizado a Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Nota:** Algunas cargas de trabajo como Power BI podrían haber aprovisionado un inquilino de Azure AD en segundo plano. Para comprobar si un determinado dominio está asociado a un inquilino, vaya a https://login.microsoftonline.com/{dominio}/v2.0/.well-known/openid-configuration. Si obtiene una respuesta correcta, el dominio ya está asignado a un inquilino y puede ser necesario adquirir el control. En ese caso, póngase en contacto con Microsoft para más información. Aprenda más sobre las opciones de adquisición en [¿Qué es la suscripción de autoservicio de Azure?](active-directory-self-service-signup.md) |
| Prueba de Azure AD Premium o EMS habilitada | [Azure Active Directory Premium gratis durante un mes](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Tener asignadas licencias de Azure AD Premium o EMS para usuarios de prueba de concepto | [Obtención de una licencia para usted y sus usuarios en Azure Active Directory.](active-directory-licensing-get-started-azure-portal.md) |
| Credenciales de administrador global de Azure AD | [Asignación de roles de administrador en Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Opcional pero muy recomendado: entorno de laboratorio paralelo como reserva | [Requisitos previos de Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Sincronización de directorios: Sincronización de hash de contraseñas (PHS) y nueva instalación

Tiempo aproximado para completarlo: una hora para menos de 1000 usuarios de prueba de concepto

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Servidor para ejecutar Azure AD Connect | [Requisitos previos de Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |
| Usuarios de prueba de concepto de destino, en el mismo dominio y parte de un grupo de seguridad, y unidad organizativa | [Instalación personalizada de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Identificar las características de Azure AD Connect necesarias para la prueba de concepto | [Conexión de Active Directory con Azure Active Directory: Configuración de características de sincronización](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Tener las credenciales necesarias para entornos locales y en la nube  | [Azure AD Connect: cuentas y permisos](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Descargue la versión más reciente de Azure AD Connect. | [Descarga de Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Instale Azure AD Connect con la ruta de acceso más sencilla: rápido. <br/>1. Filtre a la unidad organizativa de destino para minimizar el tiempo del ciclo de sincronización.<br/>2. Elija el conjunto de usuarios de destino en el grupo local.<br/>3. Implemente las características necesarias por los demás temas de la prueba de concepto. | [Azure AD Connect: Instalación personalizada: Filtrado por dominio y unidad organizativa](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Instalación personalizada: Filtrado de sincronización basado en grupos](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integración de las identidades locales con Azure Active Directory: Configuración de características de sincronización](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Apertura de la UI de Azure AD Connect y visualización de los perfiles en ejecución completados (importación, sincronización y exportación) | [Azure AD Connect Sync: Programador](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Abra el [Portal de administración de Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), vaya a la hoja "Todos los usuarios", agregue la columna "Origen de autoridad" y vea los usuarios que aparecen, marcados correctamente como procedentes de "Windows Server AD". | [Portal de administración de Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Consideraciones

1. Examine las consideraciones de seguridad de la sincronización de hash de contraseñas [aquí](./connect/active-directory-aadconnectsync-implement-password-synchronization.md).  Si la sincronización de hash de contraseñas para los usuarios de producción piloto no es una opción, tenga en cuenta las alternativas siguientes:
   * Crear usuarios de prueba en el dominio de producción. Asegúrese de que no sincronizar ninguna otra cuenta.
   * Trasladarse a un entorno UAT.
2.  Si desea lograr la federación, merece la pena entender los costos asociados a una solución federada con el proveedor de identidades local más allá de la prueba de concepto y medirlo en relación a las ventajas que busca:
    * Está en la ruta crítica, por lo que tiene que diseñarlo para lograr una alta disponibilidad.
    * Es un servicio local que necesita para el plan de capacidad.
    * Es un servicio local que necesita para la supervisión el mantenimiento o la revisión.

Más información: [Información acerca de la identidad de Office 365 y Azure Active Directory: Identidad federada](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Personalización de marca

Tiempo aproximado para completarlo: 15 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Recursos (imágenes, logotipos, etc.); para una mejor visualización, asegúrese de los recursos tienen los tamaños recomendados. | [Adición de personalización de marca de empresa a la página de inicio de sesión en la versión preliminar de Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Opcional: Si el entorno tiene un servidor ADFS, debe tener acceso al servidor para personalizar el tema web. | [Personalización de inicio de sesión del usuario de FS AD](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Equipo cliente para realizar la experiencia de inicio de sesión del usuario final. |  |
| Opcional: Dispositivos móviles para validar la experiencia. |  |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Vaya al Portal de administración de Azure AD. | [Portal de administración de Azure AD: Personalización de marca de empresa](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Cargue los recursos de la página de inicio de sesión (logotipo prominente o pequeño, etiquetas, etc.). Opcionalmente, si tiene AD FS, alinee los mismos recursos con páginas de inicio de sesión ADFS. | [Incorporación de personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso: Elementos personalizables](active-directory-add-company-branding.md) |
| Espere unos minutos para que surta efecto el cambio. |  |
| Inicie sesión con las credenciales de usuario de prueba de concepto para https://myapps.microsoft.com. |  |
| Confirme la apariencia en el explorador. | [Incorporación de personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md) |
| Si lo desea, confirme la apariencia en otros dispositivos. |  |

### <a name="considerations"></a>Consideraciones

Si la apariencia anterior permanece después de la personalización, vacíe la memoria caché del cliente del explorador y vuelva a intentar la operación.

## <a name="group-based-licensing"></a>Licencias basadas en grupos

Tiempo aproximado para completarlo: 10 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Todos los usuarios de prueba de concepto forman parte de un grupo de seguridad (en la nube o local) | [Creación de un grupo y adición de miembros en Azure Active Directory](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Vaya a la hoja de licencias en el Portal de administración de Azure AD. | [Portal de administración de Azure AD: Licencias](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Asigne las licencias al grupo de seguridad con usuarios de prueba de concepto. | [Asignación de licencias a un grupo de usuarios en Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Consideraciones

Si tiene algún problema, vaya a [Escenarios, limitaciones y problemas conocidos con el uso de grupos para administrar las licencias en Azure Active Directory](active-directory-licensing-group-advanced.md).

## <a name="saas-federated-sso-configuration"></a>Configuración de SSO federado en SaaS

Tiempo aproximado para completarlo: 60 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Entorno de prueba de la aplicación de SaaS disponible. En esta guía, se utiliza ServiceNow como ejemplo.<br/>Se recomienda encarecidamente usar una instancia de prueba para minimizar la fricción al desplazarse por las asignaciones y la calidad de los datos existentes. | Vaya a https://developer.servicenow.com/app.do#!/home para iniciar el proceso de obtención de una instancia de prueba. |
| Acceso de administrador a la consola de administración de ServiceNow. | [Tutorial: Integración de Azure Active Directory con ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Conjuntos de usuarios de destino a los que asignar la aplicación. Se recomienda un grupo de seguridad que contenga los usuarios de prueba de concepto. <br/>Si no es posible crear el grupo, asigne los usuarios directamente a la aplicación para la prueba de concepto. | [Asignación de un usuario o un grupo a una aplicación empresarial en la versión preliminar de Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Comparta el tutorial con todos los actores desde Microsoft Documentation.  | [Tutorial: Integración de Azure Active Directory con ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Convoque una reunión de trabajo y siga los pasos del tutorial con cada actor. | [Tutorial: Integración de Azure Active Directory con ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Asigne la aplicación al grupo identificado en los requisitos previos. Si la prueba de concepto tiene acceso condicional en el ámbito, puede volver a visitarlo que más adelante y agregarle MFA y similares. <br/>Tenga en cuenta que esto activará en el proceso de aprovisionamiento (si está configurado). |  [Asignación de un usuario o un grupo a una aplicación empresarial en la versión preliminar de Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Creación de un grupo y adición de miembros en Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Utilice el Portal de administración de Azure AD para agregar la aplicación ServiceNow desde la galería.| [Portal de administración de AD Azure: Aplicaciones empresariales](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Novedades sobre la administración de aplicaciones empresariales en Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| En la hoja "Inicio de sesión único" de la aplicación ServiceNow, habilite "Inicio de sesión basado en SAML". |  |
| Rellene los campos "URL de inicio de sesión" e "Identificador" con la dirección URL de ServiceNow.<br/>Active la casilla "Make new certificate active" (Activar nuevo certificado)<br/>y haga clic en Guardar configuración. |  |
| Abra la hoja "Configurar ServiceNow" en la parte inferior del panel para ver instrucciones personalizadas para configurar ServiceNow. |  |
| Siga las instrucciones para configurar ServiceNow. |  |
| En la hoja "Aprovisionamiento" de la aplicación ServiceNow, habilite el aprovisionamiento "Automático". | [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales en el nuevo Azure Portal](active-directory-enterprise-apps-manage-provisioning.md) |
| Espere unos minutos mientras se completa el aprovisionamiento.  Mientras tanto, puede consultar los informes de aprovisionamiento. |  |
| Inicie sesión en https://myapps.microsoft.com/ como un usuario de prueba que tiene acceso. | [¿Qué es el Panel de acceso?](active-directory-saas-access-panel-introduction.md) |
| Haga clic en el icono de la aplicación que acaba de crear. Confirme el acceso |  |
| Si lo desea, puede consultar los informes de uso de la aplicación. Tenga en cuenta que hay cierta latencia, por lo que es necesario esperar un poco para ver el tráfico en los informes. | [Informes de actividad de inicio de sesión en el portal de Azure Active Directory: Uso de las aplicaciones administradas](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Directivas de retención de informes de Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Consideraciones

1. El anterior [tutorial](active-directory-saas-servicenow-tutorial.md) hace referencia a una experiencia de administración de Azure AD antigua. Pero la prueba de concepto se basa en la experiencia [Inicio rápido](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away).
2. Si la aplicación de destino no está en la galería, puede usar "Traiga su propia aplicación". Más información: [Novedades sobre la administración de aplicaciones empresariales en Azure Active Directory: Incorporación de aplicaciones personalizadas desde un solo lugar](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>Configuración de SSO con contraseña en SaaS

Tiempo aproximado para completarlo: 15 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Entorno de prueba para las aplicaciones SaaS. HipChat y Twitter son ejemplos de SSO de contraseña. Para cualquier otra aplicación, necesita la dirección URL exacta de la página con el formulario de inicio de sesión de html. | [Twitter en Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat en Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Cuentas de prueba para las aplicaciones. | [Registro en Twitter](https://twitter.com/signup?lang=en)<br/>[Registro gratuito: HipChat](https://www.hipchat.com/sign_up) |
| Conjuntos de usuarios de destino a los que asignar la aplicación. Se recomienda un grupo de seguridad que contenga los usuarios. | [Asignación de un usuario o un grupo a una aplicación empresarial en la versión preliminar de Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Acceso de administrador local a un equipo para implementar la extensión Access Panel para Internet Explorer, Chrome o Firefox. | [Extensión Access Panel para IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensión Access Panel para Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensión Access Panel para Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Instale la extensión del explorador. | [Extensión Access Panel para IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensión Access Panel para Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensión Access Panel para Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configuración de una aplicación de la galería | [Novedades de la administración de aplicaciones de empresa en Azure Active Directory: La nueva y mejorada galería de aplicaciones](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configure la contraseña de SSO. | [Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal: Inicio de sesión con contraseña](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Asigne la aplicación al grupo identificado en los requisitos previos. | [Asignación de un usuario o un grupo a una aplicación empresarial en la versión preliminar de Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Inicie sesión en https://myapps.microsoft.com/ como un usuario de prueba que tiene acceso. |  |
| Haga clic en el icono de la aplicación que acaba de crear. | [¿Qué es el Panel de acceso?: SSO basado en contraseña sin aprovisionamiento de identidad](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Proporcione la credencial de la aplicación. | [¿Qué es el Panel de acceso?: SSO basado en contraseña sin aprovisionamiento de identidad](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Cierre el explorador y repita el inicio de sesión. Esta vez, alrededor del usuario debería ver un acceso ininterrumpido a la aplicación. |  |
| Si lo desea, puede consultar los informes de uso de la aplicación. Tenga en cuenta que hay cierta latencia, por lo que es necesario esperar un poco para ver el tráfico en los informes. | [Informes de actividad de inicio de sesión en el portal de Azure Active Directory: Uso de las aplicaciones administradas](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Directivas de retención de informes de Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Consideraciones

Si la aplicación de destino no está en la galería, puede usar "Traiga su propia aplicación". Más información: [Novedades sobre la administración de aplicaciones empresariales en Azure Active Directory: Incorporación de aplicaciones personalizadas desde un solo lugar](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tenga en cuenta los siguientes requisitos:
   * La aplicación debe tener una dirección URL de inicio de sesión conocida.
   * La página de inicio de sesión debe contener un formulario HTML con uno o varios campos de texto que las extensiones del explorador pueden rellenar de forma automática. Como mínimo, debe contener el nombre de usuario y la contraseña.

## <a name="saas-shared-accounts-configuration"></a>Configuración de cuentas compartidas de SaaS

Tiempo aproximado para completarlo: 30 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| La lista de las aplicaciones de destino y las URL de inicio de sesión exactas anteriores. Por ejemplo, puede usar Twitter. | [Twitter en Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Registro en Twitter](https://twitter.com/signup?lang=en) |
| Credenciales compartidas para esta aplicación SaaS. | [Uso compartido de cuentas con Azure AD](active-directory-sharing-accounts.md)<br/>[Azure AD automated password roll-over for Facebook, Twitter and LinkedIn now in preview! Enterprise Mobility and Security Blog (Versión preliminar de la sustitución automatizada de contraseñas de Azure AD para Facebook, Twitter y LinkedIn: Blog de seguridad y movilidad empresarial)] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Credenciales para al menos dos miembros del equipo que accederán a la misma cuenta. Deben formar parte de un grupo de seguridad. | [Asignación de un usuario o un grupo a una aplicación empresarial en la versión preliminar de Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Acceso de administrador local a un equipo para implementar la extensión Access Panel para Internet Explorer, Chrome o Firefox. | [Extensión Access Panel para IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensión Access Panel para Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensión Access Panel para Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Instale la extensión del explorador. | [Extensión Access Panel para IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extensión Access Panel para Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extensión Access Panel para Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configuración de una aplicación de la galería | [Novedades de la administración de aplicaciones de empresa en Azure Active Directory: La nueva y mejorada galería de aplicaciones](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configure la contraseña de SSO. | [Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal: Inicio de sesión con contraseña](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Asigne la aplicación al grupo identificado en los requisitos previos durante la asignación de credenciales. | [Asignación de un usuario o un grupo a una aplicación empresarial en la versión preliminar de Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Inicie sesión como distintos usuarios que acceden a esa aplicación con la **misma cuenta compartida**.  |  |
| Si lo desea, puede consultar los informes de uso de la aplicación. Tenga en cuenta que hay cierta latencia, por lo que es necesario esperar un poco para ver el tráfico en los informes. | [Informes de actividad de inicio de sesión en el portal de Azure Active Directory: Uso de las aplicaciones administradas](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Directivas de retención de informes de Azure Active Directory](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Consideraciones

Si la aplicación de destino no está en la galería, puede usar "Traiga su propia aplicación". Más información: [Novedades sobre la administración de aplicaciones empresariales en Azure Active Directory: Incorporación de aplicaciones personalizadas desde un solo lugar](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tenga en cuenta los siguientes requisitos:
   * La aplicación debe tener una dirección URL de inicio de sesión conocida.
   * La página de inicio de sesión debe contener un formulario HTML con uno o varios campos de texto que las extensiones del explorador pueden rellenar de forma automática. Como mínimo, debe contener el nombre de usuario y la contraseña.

## <a name="app-proxy-configuration"></a>Configuración de proxy de aplicación

Tiempo aproximado para completarlo: 20 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Una suscripción Basic o Premium a Microsoft Azure AD y un directorio de Azure AD del que sea administrador global. | [Ediciones de Azure Active Directory](active-directory-editions.md) |
| Una aplicación web hospedada localmente que desea configurar para el acceso remoto. |  |
| Un servidor en el que se ejecute Windows Server 2012 R2 o Windows 8.1 o superior, y en el que se pueda instalar el conector del proxy de la aplicación. | [Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md) |
| Si hay un firewall en la ruta de acceso, es preciso asegurarse de que está abierto con el fin de que el conector pueda realizar solicitudes HTTPS (TCP) al proxy de la aplicación. | [Habilitación del proxy de aplicación en Azure Portal: requisitos previos del proxy de aplicación](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| Si su organización usa servidores proxy para conectarse a Internet, eche un vistazo a la entrada de blog Working with existing on-premises proxy servers (Trabajo con servidores proxy locales existentes) para más información sobre cómo configurarlos. | [Trabajo con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Instale un conector en el servidor. | [Habilitación del Proxy de aplicación en Azure Portal: Instalación y registro de un conector](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| Publique la aplicación local en Azure AD como una aplicación de proxy de aplicación | [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](application-proxy-publish-azure-portal.md) |
| Asigne usuarios de prueba. | [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD: Adición de un usuario de prueba](application-proxy-publish-azure-portal.md#add-a-test-user) |
| Si lo desea, configure una experiencia de inicio de sesión único para los usuarios. | [Provisión de inicio de sesión único mediante el proxy de aplicación de Azure AD](application-proxy-sso-azure-portal.md) |
| Pruebe la aplicación al iniciar sesión en el portal de MyApps como usuario asignado. | https://myapps.microsoft.com |

### <a name="considerations"></a>Consideraciones

1. Aunque se recomienda colocar el conector en su red corporativa, hay casos en los que verá un mejor rendimiento colocándolo en la nube. Más información: [Consideraciones sobre la topología de red al utilizar el Proxy de aplicación de Azure Active Directory](application-proxy-network-topology-considerations.md)
2. Para más detalles de seguridad y cómo esto proporciona una solución de acceso remoto especialmente seguro manteniendo únicamente las conexiones salientes, consulte [Consideraciones de seguridad al obtener acceso a aplicaciones de forma remota con el Proxy de aplicación de Azure AD](application-proxy-security-considerations.md).

## <a name="generic-ldap-connector-configuration"></a>Configuración genérica del conector LDAP

Tiempo aproximado para completarlo: 60 minutos

> [!IMPORTANT]
> Se trata de una configuración avanzada que requiere cierta familiaridad con FIM o MIM. Si se usa en producción, se recomienda plantear las preguntas sobre esta configuración a través del [soporte técnico Premier](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Azure AD Connect instalado y configurado | Bloque de creación: [Sincronización de directorios: sincronización de hash de contraseñas](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Cumplir los requisitos de la instancia de ADLDS | [Referencia técnica del conector de LDAP genérico: Información general sobre el conector de LDAP genérico](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| Lista de las cargas de trabajo que los usuarios usan y atributos asociados a ellas. | [Azure AD Connect Sync: atributos sincronizados con Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Incorporación del conector LDAP genérico | [Referencia técnica del conector de LDAP genérico: Creación de un nuevo conector](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| Cree perfiles de ejecución para el conector creado (importación completa, importación diferencial, sincronización completa, sincronización diferencial y exportación). | [Create a Management Agent Run Profile](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx) (Creación de un perfil de ejecución del agente de administración)<br/> [Uso de conectores con el administrador de servicios de Azure AD Connect Sync](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Ejecute el perfil de importación completa y verifique que hay objetos en el espacio del conector. | [Search for a Connector Space Object](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx) (Búsqueda de un objeto en el espacio del conector)<br/>[Uso de conectores con el administrador de servicios de Azure AD Connect Sync: Espacio del conector de búsqueda](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Cree reglas de sincronización para que los objetos del metaverso tengan los atributos necesarios para las cargas de trabajo. | [Azure AD Connect Sync: procedimientos recomendados para cambiar la configuración predeterminada: Cambios en las reglas de sincronización](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Azure AD Connect Sync: conocimiento del aprovisionamiento declarativo](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Azure AD Connect Sync: conocimiento de expresiones de aprovisionamiento declarativo](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Inicie el ciclo de sincronización completa. | [Azure AD Connect Sync: Programador: Inicio del programador](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| En caso de problemas, soluciónelos. | [Solución de problemas de un objeto que no se sincroniza con Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Compruebe que el usuario de LDAP puede iniciar sesión y acceda a la aplicación. | https://myapps.microsoft.com |

### <a name="considerations"></a>Consideraciones

> [!IMPORTANT]
> Se trata de una configuración avanzada que requiere cierta familiaridad con FIM o MIM. Si se usa en producción, se recomienda plantear las preguntas sobre esta configuración a través del [soporte técnico Premier](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Grupos: propiedad delegada

Tiempo aproximado para completarlo: 10 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Aplicación de SaaS (SSO federado o SSO de contraseña) ya configurado | Bloque de creación: [Configuración de SSO federado en SaaS](#saas-federated-sso-configuration) |
| El grupo en la nube al que se ha asignado acceso a la aplicación en el paso 1 está identificado. | Bloque de creación: [Configuración de SSO federado en SaaS](#saas-federated-sso-configuration) <br/>[Creación de un grupo y adición de miembros en Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Las credenciales del propietario del grupo están disponibles. | [Administración del acceso a recursos con grupos de Azure Active Directory](active-directory-manage-groups.md) |
| Las credenciales del trabajador de información que acceden a las aplicaciones estén identificadas. | [¿Qué es el Panel de acceso?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Identifique el grupo al que se ha concedido acceso a la aplicación y configure el propietario de un determinado grupo.| [Administración de la configuración de un grupo en la versión preliminar de Azure Active Directory](active-directory-groups-settings-azure-portal.md) |
| Inicie sesión como propietario del grupo, consulte la pertenencia al grupo en la pestaña de grupos del panel de acceso. | [Página de administración de grupos de Azure Active Directory](https://account.activedirectory.windowsazure.com/r/#/groups) |
| Agregue el trabajador de información que desea probar. |  |
| Inicie sesión como trabajador de información, confirme que el icono está disponible. | [¿Qué es el Panel de acceso?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Consideraciones

Si la aplicación tiene habilitado el aprovisionamiento, puede ser necesario esperar unos minutos para que se complete el aprovisionamiento antes de acceder a la aplicación como trabajador de información.

## <a name="saas-and-identity-lifecycle"></a>Ciclo de vida de identidad y SaaS

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Aplicación de SaaS (SSO federado o SSO de contraseña) ya configurado | Bloque de creación: [Configuración de SSO federado en SaaS](#saas-federated-sso-configuration) |
| El grupo en la nube al que se ha asignado acceso a la aplicación en el paso 1 está identificado. | Bloque de creación: [Configuración de SSO federado en SaaS](#saas-federated-sso-configuration) <br/>[Creación de un grupo y adición de miembros en Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Las credenciales del trabajador de información que acceden a las aplicaciones estén identificadas. | [¿Qué es el Panel de acceso?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Quite el usuario del grupo al que está asignada la aplicación. | [Administración de la pertenencia a grupos de los usuarios del inquilino de Azure Active Directory](active-directory-groups-members-azure-portal.md) |
| Espere unos minutos para la cancelación del aprovisionamiento. | [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory: ¿Cómo funciona el aprovisionamiento automático?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| En una sesión del explorador independiente, inicie sesión como trabajador de información en el portal de aplicaciones y confirme si falta el icono. | http://myapps.microsoft.com |


### <a name="considerations"></a>Consideraciones

Extrapole el escenario de prueba de concepto a escenarios de abandonos o bajas por ausencia. Si el usuario se deshabilita en AD local o se quita, ya no hay una manera de iniciar sesión en la aplicación SaaS.

## <a name="self-service-access-to-application-management"></a>Acceso de autoservicio para administración de aplicaciones

Tiempo aproximado para completarlo: 10 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Identificar a los usuarios de prueba de concepto que solicitan acceso a las aplicaciones, como parte del grupo de seguridad. | Bloque de creación: [Configuración de SSO federado en SaaS](#saas-federated-sso-configuration) |
| Aplicación de destino implementada. | Bloque de creación: [Configuración de SSO federado en SaaS](#saas-federated-sso-configuration) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Vaya a la hoja Aplicaciones empresariales en el Portal de administración de Azure AD. | [Portal de administración de AD Azure: Aplicaciones empresariales](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Configure la aplicación a partir de los requisitos previos con autoservicio. | [Novedades de la administración de aplicaciones de empresa en Azure Active Directory: Configuración del acceso a aplicaciones en modo autoservicio](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Inicie sesión como trabajador de información en el portal de aplicaciones. | http://myapps.microsoft.com |
| Observe el botón "+Agregar aplicación" en la parte superior de la página. Úselo para acceder a la aplicación. |  |

### <a name="considerations"></a>Consideraciones

Las aplicaciones elegidas podrían tener requisitos de aprovisionamiento, por lo que ir inmediatamente a la aplicación, podría causar algunos errores. Si la aplicación elegida admite el aprovisionamiento con Azure AD y está configurado, puede utilizarlo como una oportunidad para mostrar el flujo de trabajo de principio a fin. Consulte el bloque de creación de [Configuración de SSO federado en SaaS](#saas-federated-sso-configuration) para ver recomendaciones adicionales.

## <a name="self-service-password-reset"></a>Restablecimiento de contraseñas de autoservicio

Tiempo aproximado para completarlo: 15 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Habilitar la administración de contraseñas de autoservicio en el inquilino. | [Restablecimiento de contraseña de Azure Active Directory para administradores de TI](active-directory-passwords.md) |
| Habilitar la reescritura de contraseñas para administrar contraseñas localmente. Tenga en cuenta que esto requiere versiones específicas de Azure AD Connect | [Requisitos previos de escritura diferida de contraseñas](active-directory-passwords-writeback.md) |
| Identificar a los usuarios de prueba de concepto que usarán esta funcionalidad y asegurarse de que son miembros de un grupo de seguridad. Los usuarios no deben ser administradores para mostrar completamente la funcionalidad. | [Personalización de la administración de contraseñas de Azure AD: Restringir acceso para restablecer la contraseña](active-directory-passwords-writeback.md) |


### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Vaya al Portal de administración de Azure AD: restablecimiento de contraseñas. | [Portal de administración de Azure AD: Restablecimiento de contraseñas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Determine la directiva de restablecimiento de contraseñas. Para la prueba de concepto, puede utilizar la llamada de teléfono y las preguntas y respuestas. Se recomienda habilitar el registro para que sea necesario iniciar sesión para acceder al panel de acceso. |  |
| Cierre sesión e iníciela como un trabajador de información. |  |
| Proporcione los datos de autoservicio de restablecimiento de contraseñas configurados en el paso 2. | http://aka.ms/ssprsetup |
| Cierre el explorador. |  |
| Empiece el proceso de inicio de sesión como el trabajador de información que utilizó en el paso 4. |  |
| Restablezca la contraseña. | [Actualización de su propia contraseña: restablecimiento de mi contraseña](active-directory-passwords-update-your-own-password.md) |
| Intente iniciar sesión en Azure AD con la nueva contraseña, así como en los recursos locales. |  |

### <a name="considerations"></a>Consideraciones

1. Si la actualización de Azure AD Connect va a producir fricción, considere usarlo con cuentas en la nube o realizar una demostración en un entorno independiente.
2. Los administradores tienen otra directiva y usar la cuenta de administrador para restablecer la contraseña podría alterar la prueba de concepto y generar confusión. Asegúrese de usar una cuenta de usuario normal para probar las operaciones de restablecimiento.


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Azure Multi-Factor Authentication con llamadas de teléfono

Tiempo aproximado para completarlo: 10 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Identificar a los usuarios de prueba de concepto que usarán MFA  |  |
| Teléfono con una buena cobertura de el desafío de MFA  | [¿Qué es Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Vaya a la hoja "Usuarios y grupos" en el Portal de administración de Azure AD. | [Portal de administración de Azure AD: Usuarios y grupos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Elija la hoja "Todos los usuarios". |  |
| En la barra superior, elija el botón "Multi-Factor Authentication". | Dirección URL directa para el portal de Azure MFA: https://aka.ms/mfaportal. |
| En la configuración de "Usuario", seleccione los usuarios de prueba de concepto y habilítelas para MFA. | [Estados de usuario en Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) |
| Inicie sesión como usuario de prueba de concepto y recorra el proceso de prueba.  |  |

### <a name="considerations"></a>Consideraciones

1. Los pasos de la prueba de concepto de este bloque de creación se configuran explícitamente MFA para un usuario en todos los inicios de sesión. Hay otras herramientas como el acceso condicional y la protección de identidad que interactúan con MFA en otros escenarios. Se trata de algo que debe tener en cuenta al pasar de la prueba de concepto a producción.
2. Los pasos de la prueba de concepto de este bloque de creación usan explícitamente las llamadas de teléfono como método de MFA para la experiencia. Al pasar de la prueba de concepto a la producción, se recomienda usar aplicaciones como [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) como segundo factor siempre que sea posible.
Más información: [Estándar DRAFT NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Acceso condicional de MFA para aplicaciones SaaS

Tiempo aproximado para completarlo: 10 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Identificar a los usuarios de prueba de concepto que tengan como destino la directiva. Estos usuarios deben estar en un grupo de seguridad para el ámbito de la directiva de acceso condicional. | [Configuración de SSO federado en SaaS](#saas-federated-sso-configuration) |
| Aplicación de SaaS ya está configurado |  |
| Los usuarios de prueba de concepto ya están asignados a la aplicación |  |
| Las credenciales del usuario de prueba de concepto están disponibles |  |
| El usuario de prueba de concepto está registrado para MFA Uso de un teléfono con una buena cobertura | http://aka.ms/ssprsetup |
| Dispositivo en la red interna. Dirección IP configurada en el intervalo de direcciones internas | Localice la dirección IP: https://www.bing.com/search?q=what%27s+my+ip |
| Dispositivo en la red externa (puede ser un teléfono que use la red móvil del operador) |  |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Vaya a la hoja Portal de administración de Azure AD: Acceso condicional. | [Portal de administración de Azure AD: Acceso condicional](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Cree una directiva de acceso condicional:<br/>- Usuarios de prueba de concepto de destino en "Usuarios y grupos".<br/>- Aplicación de prueba de concepto de destino en "Aplicaciones en la nube".<br/>- Coloque todas las ubicaciones, excepto las de confianza, en "Condiciones" -> "Ubicaciones" **Nota:** Las IP de confianza se configuran en el [Portal MFA](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx).<br/>- Requiere Multi-Factor Authentication en "Conceder". | [Introducción al acceso condicional en Azure Active Directory: Pasos de la configuración de la directiva](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Acceda a la aplicación desde la red corporativa. | [Introducción al acceso condicional en Azure Active Directory: Prueba de la directiva](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Acceda a la aplicación desde la red pública. | [Introducción al acceso condicional en Azure Active Directory: Prueba de la directiva](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Consideraciones

Si está utilizando la federación, puede usar el proveedor de identidades (IdP) local para comunicar el estado de la red corporativa interna/externa con notificaciones. Puede usar esta técnica sin tener que administrar la lista de direcciones IP, lo que puede ser complejo, para evaluar y administrar grandes organizaciones. En esa configuración, necesita la cuenta para el escenario "itinerancia de red" (un inicio de sesión del usuario desde la red interna y, mientras está conectado, cambia la ubicación, como una cafetería) y asegúrese de comprender las implicaciones. Obtener más información: [Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS: Direcciones IP de confianza para usuarios federados](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Tiempo aproximado para completarlo: 15 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Identificar al administrador global que va a formar parte de la prueba de concepto para PIM. | [Comenzar a usar Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Identificar al administrador global que se convertirá en el Administrador de seguridad. | [Comenzar a usar Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Rol administrativo diferente en Azure Active Directory PIM](active-directory-privileged-identity-management-roles.md) |
| Opcional: Confirme si los administradores globales tienen acceso al correo electrónico para obtener notificaciones de correo electrónico en PIM. | [¿Qué es Azure AD Privileged Identity Management?: Configuración de las opciones de activación de rol](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Inicie sesión en https://portal.azure.com como un administrador global (GA) y arranque en la hoja PIM. El administrador global que realiza este paso se considera el administrador de seguridad.  Vamos a llamar a este actor GA1. | [Uso del Asistente para seguridad de Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Identifique al administrador global y muévalo de permanente a apto. Para mayor claridad, debe ser un administrador distinto al usado en el paso 1. Vamos a llamar a este actor GA2. | [Privileged Identity Management de Azure AD: Incorporación o eliminación de un rol de usuario](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[¿Qué es Azure AD Privileged Identity Management?: Configuración de las opciones de activación de rol](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Ahora, inicie sesión como GA2 en https://portal.azure.com y pruebe a cambiar "Configuración de usuario". Tenga en cuenta que algunas opciones aparecen atenuadas. | |
| En una nueva pestaña en la misma sesión que el paso 3, vaya a https://portal.azure.com y agregue la hoja PIM al panel. | [Activación y desactivación de roles en Azure AD Privileged Identity Management: Incorporación de la aplicación Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Solicite la activación para el rol de administrador global. | [Activación y desactivación de roles en Azure AD Privileged Identity Management: Activación de un rol](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Tenga en cuenta que si GA2 nunca se registró para MFA, será necesario registrarse para Azure MFA. |  |
| Vuelva a la pestaña original en el paso 3 y haga clic en el botón Actualizar, en el explorador. Tenga en cuenta que ahora tiene acceso para cambiar la "Configuración de usuario". | |
| Si lo desea, si los administradores globales tienen el correo electrónico habilitado, puede consultar la bandeja de entrada de GA1 y de GA2 y ver la notificación del rol que se va a activar. |  |
| 8 Consulte el historial de auditoría y observe el informe para confirmar que aparece la elevación del GA2. | [¿Qué es Azure AD Privileged Identity Management?: Revisión de la actividad de un rol](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Consideraciones

Esta funcionalidad forma parte de Azure AD Premium P2 y EMS E5.

## <a name="discovering-risk-events"></a>Detección de eventos de riesgo

Tiempo aproximado para completarlo: 20 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Dispositivo con el explorador Tor descargado e instalado | [Descarga del explorador Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Acceso al usuario de prueba de concepto para realizar el inicio de sesión | [Azure Active Directory Identity Protection playbook (Guía de Azure Active Directory Identity Protection)](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Abra el explorador Tor | [Descarga del explorador Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Inicie sesión en https://myapps.microsoft.com con la cuenta de usuario de prueba de concepto. | [Guía de Azure Active Directory Identity Protection: Simulación de eventos de riesgo](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Espere 5-7 minutos. |  |
| Inicie sesión como administrador global en https://portal.azure.com y abra la hoja Protección de identidad. | https://aka.ms/aadipgetstarted |
| Abra la hoja Eventos de riesgo. Debería ver una entrada en "Inicios de sesión desde direcciones IP anónimas".  | [Guía de Azure Active Directory Identity Protection: Simulación de eventos de riesgo](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Consideraciones

Esta funcionalidad forma parte de Azure AD Premium P2 y EMS E5.

## <a name="deploying-sign-in-risk-policies"></a>Implementación de directivas de riesgo de inicio de sesión

Tiempo aproximado para completarlo: 10 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Dispositivo con el explorador Tor descargado e instalado | [Descarga del explorador Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Acceso como un usuario de prueba de concepto para probar el registro |  |
| Usuario de prueba de concepto registrado en MFA. Asegúrese de usar un teléfono con una buena cobertura. | Bloque de creación: [Azure Multi-Factor Authentication con llamadas de teléfono](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Inicie sesión como administrador global en https://portal.azure.com y abra la hoja Protección de identidad. | https://aka.ms/aadipgetstarted |
| Habilite una directiva de inicio de sesión de riesgo de la manera siguiente:<br/>- Asignado a: usuario de prueba de concepto<br/>- Condiciones: inicio de sesión de riesgo medio o superior (el inicio de sesión en desde una ubicación anónima se considera un nivel de riesgo medio)<br/>- Controles: requiere MFA | [Guía de Azure Active Directory Identity Protection: Riesgo de inicio de sesión](active-directory-identityprotection-playbook.md#sign-in-risk) |
| Abra el explorador Tor | [Descarga del explorador Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Inicie sesión en https://myapps.microsoft.com con la cuenta de usuario de prueba de concepto. |  |
| Tenga en cuenta el desafío de MFA. | [Experiencias de inicio de sesión con Azure AD Identity Protection: Recuperación de inicios de sesión peligrosos](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Consideraciones

Esta funcionalidad forma parte de Azure AD Premium P2 y EMS E5. Para más información acerca de los eventos de riesgo, visite [Eventos de riesgo de Azure Active Directory](active-directory-reporting-risk-events.md).

## <a name="configuring-certificate-based-authentication"></a>Configuración de autenticación basada en certificados

Tiempo aproximado para completarlo: 20 minutos

### <a name="pre-requisites"></a>Requisitos previos

| Requisito previo | Recursos |
| --- | --- |
| Dispositivos con certificado de usuario aprovisionado (Windows, iOS o Android) de Enterprise PKI | [Implementar certificados de usuario](https://msdn.microsoft.com/library/cc770857.aspx) |
| Dominio de Azure AD federado con ADFS | [Azure AD Connect y la federación](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Información general de Servicios de certificados de Active Directory](https://technet.microsoft.com/library/hh831740.aspx)|
| Para dispositivos iOS, tener instalada la aplicación Microsoft Authenticator | [Introducción a la aplicación Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Pasos

| Paso | Recursos |
| --- | --- |
| Habilite la Autenticación de certificados en ADFS. | [Configurar las directivas de autenticación: Para configurar la autenticación principal globalmente en Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Opcional: Habilite la autenticación de certificados en Azure AD para los clientes de Exchange Active Sync. | [Introducción a la autenticación basada en certificados de Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Vaya al Panel de acceso y autentíquese con el certificado de usuario. | https://myapps.microsoft.com |

### <a name="considerations"></a>Consideraciones

Para más información sobre las advertencias de esta implementación, visite [ADFS: Certificate Authentication with Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/) (ADFS: autenticación de certificados con Azure AD y Office 365).



> [!NOTE]
> Debe mantenerse la posesión del certificado de usuario. Mediante la administración de dispositivos o con PIN en el caso de las tarjetas inteligentes.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
