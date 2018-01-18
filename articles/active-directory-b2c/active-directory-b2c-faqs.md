---
title: "Preguntas más frecuentes - Azure Active Directory B2C"
description: "Preguntas más frecuentes (P+F) acerca de Azure Active Directory B2C"
services: active-directory-b2c
author: PatAltimore
manager: mtillman
editor: saeeda
ms.custom: seo
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 38f33f2a5d0bba4686cfeef121f2087326e1897d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: preguntas más frecuentes (P+F) 
Esta página responde a las preguntas más frecuentes sobre Azure Active Directory (Azure AD) B2C. Siga comprobando si hay actualizaciones.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>¿Puedo usar las características de Azure AD B2C en mi inquilino de Azure de AD existente, basado en empleados?
Azure AD y Azure AD B2C son ofertas de producto independientes y no pueden coexistir en el mismo inquilino.  Un inquilino de Azure AD representa una organización.  Un inquilino de Azure AD B2C representa una colección de identidades para su uso con aplicaciones de usuario de confianza.  Con las directivas personalizadas (en versión preliminar), Azure AD B2C puede federarse con Azure AD, lo que permite la autenticación de los empleados de una organización.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>¿Puedo usar Azure AD B2C para proporcionar un inicio de sesión social (Facebook y Google+) en Office 365?
Azure AD B2C no se puede usar para autenticar los usuarios de Microsoft Office 365.  Azure AD es la solución de Microsoft para administrar el acceso de los empleados a las aplicaciones SaaS y tiene características diseñadas para este propósito, como el acceso condicional y las licencias.  Azure AD B2C proporciona una plataforma de administración de identidades y acceso para la creación de aplicaciones web y móviles.  Cuando Azure AD B2C está configurado para federarse con un inquilino de Azure AD, el inquilino de Azure AD administra el acceso de los empleados a las aplicaciones que se basan en Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>¿Qué son las cuentas locales en Azure AD B2C? ¿En qué se distinguen de las cuentas de trabajo o educativas en Azure AD?
En un inquilino de Azure AD, los usuarios que pertenecen al inquilino inician sesión con una dirección de correo electrónico con el formato `<xyz>@<tenant domain>`.  `<tenant domain>` es uno de los dominios comprobados del inquilino o el dominio `<...>.onmicrosoft.com` inicial. Este tipo de cuenta es una cuenta profesional o educativa.

En un inquilino de Azure AD B2C, la mayoría de las aplicaciones desean que el usuario inicie sesión con cualquier dirección de correo electrónico arbitraria (por ejemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com o jim@live.com). Este tipo de cuenta es una cuenta local.  También se admiten nombres de usuario arbitrarios tales como cuentas locales (por ejemplo, joe, bob, sarah o jim). Para elegir uno de estos dos tipos de cuentas locales, puede configurar Azure AD B2C en Azure Portal.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>¿Qué proveedores de identidades sociales se admiten ahora? ¿Cuáles se prevén que se van a admitir en el futuro?
Actualmente admitimos Facebook, Google+, LinkedIn, Amazon, Twitter (versión preliminar), WeChat (versión preliminar), Weibo (versión preliminar) y QQ (versión preliminar). Agregaremos compatibilidad con otros proveedores de identidades sociales conocidos en función de la demanda del cliente.

Azure AD B2C también ha agregado compatibilidad para [directivas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom).  Estas [directivas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom) permiten a un desarrollador crear su propia directiva con cualquier proveedor de identidades que admita [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) o SAML. 

Empiece a trabajar con directivas personalizadas consultando nuestro [paquete de inicio de directivas personalizadas](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>¿Puedo configurar ámbitos para recopilar más información acerca de los consumidores de distintos proveedores de identidades sociales?
No, pero esta característica está en nuestro mapa de ruta. Los ámbitos predeterminados usados para el conjunto de proveedores de identidades sociales admitidos son:

* Facebook: correo electrónico
* Google+: correo electrónico
* Cuenta Microsoft: perfil de correo electrónico de OpenID
* Amazon: perfil
* LinkedIn: r_emailaddress r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>¿Tiene mi aplicación que ejecutarse en Azure para que funcione con Azure AD B2C?
No, puede hospedar la aplicación en cualquier lugar (en la nube o de forma local). Todo lo que necesita para interactuar con Azure AD B2C es la capacidad de enviar y recibir solicitudes HTTP en puntos de conexión de acceso público.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tengo varios inquilinos de Azure AD B2C. ¿Cómo puedo administrarlos en Azure Portal?
Antes de abrir "Azure AD B2C" en el menú de la izquierda de Azure Portal, debe cambiarse al directorio que desea administrar.  Cambie los directorios haciendo clic en su identidad en la esquina superior derecha de Azure Portal y después elija un directorio en la lista desplegable que aparece.  Para una descripción paso a paso con imágenes, consulte la sección [Ir a la configuración de B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>¿Cómo puedo personalizar los mensajes de correo electrónico de comprobación (el contenido y el campo "De:") enviados por Azure AD B2C?
Puede utilizar la [característica de personalización de marca de compañía](../active-directory/customize-branding.md) para personalizar el contenido de los mensajes de correo electrónico de comprobación. En concreto, se pueden personalizar estos dos elementos del correo electrónico:

* **Logotipo del banner**: se muestra en la parte inferior derecha.
* **Color de fondo**: se muestra en la parte superior.

    ![Captura de pantalla de un correo electrónico de comprobación personalizado](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La firma de correo electrónico contiene el nombre del inquilino de Azure AD B2C que proporcionó cuando lo creó por primera vez. Puede cambiar el nombre siguiendo estas instrucciones:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global.
1. Abra la hoja **Azure Active Directory**.
1. Haga clic en la pestaña **Propiedades**.
1. Cambie el campo **Nombre**.
1. Haga clic en **Guardar** en la parte superior de la página.

En estos momentos no se puede cambiar el valor del campo De del correo electrónico. Vote en [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) que está interesado en personalizar el cuerpo del correo electrónico de comprobación.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>¿Cómo puedo migrar mis nombres de usuario, contraseñas y perfiles existentes desde la base de datos a Azure AD B2C?
Puede usar la API Graph de Azure AD para escribir la herramienta de migración. Consulte la [guía de migración para el usuario](active-directory-b2c-user-migration.md) para detalles.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>¿Qué directiva de contraseñas se utiliza para las cuentas locales en Azure AD B2C?
La directiva de contraseñas de Azure AD B2C para cuentas locales se basa en la directiva para Azure AD. Las directivas de restablecimiento de la contraseña, inicio de sesión, registro e inicio de sesión de Azure AD B2C utilizan la seguridad de la contraseña "segura" y las contraseñas no caducan. Lea [Directiva de contraseñas en Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obtener más información.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>¿Puedo usar Azure AD Connect para migrar identidades de consumidores almacenadas en mi entorno Active Directory local a Azure AD B2C?
No, Azure AD Connect no está diseñado para funcionar con Azure AD B2C. Considere la posibilidad de usar la [API Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para la migración de usuarios.  Consulte la [guía de migración para el usuario](active-directory-b2c-user-migration.md) para detalles.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>¿Mi aplicación puede abrir páginas de Azure AD B2C dentro de un iFrame?
No, por motivos de seguridad, las páginas de Azure AD B2C no se pueden abrir dentro de un iFrame.  Nuestro servicio se comunica con el explorador para prohibir iFrames.  La comunidad de seguridad en general y la especificación OAUTH2 recomiendan no usar iFrames para experiencias de identidad debido al riesgo de secuestro de clics, también conocido como "clickjacking".

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>¿Funciona Azure AD B2C con sistemas CRM, como Microsoft Dynamics?
La integración básica con el portal de Microsoft Dynamics 365 está disponible.  Consulte [Configuración del proveedor Azure AD B2C para portales](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>¿Funciona Azure AD B2C con SharePoint local 2016 o una versión anterior?
Azure AD B2C no se ha diseñado para escenarios de uso compartido con asociados externos de SharePoint; en su lugar, consulte [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>¿Debo utilizar Azure AD B2C o B2B para administrar identidades externas?
Lea este artículo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para obtener más información sobre cómo aplicar las características apropiadas a los escenarios de identidades externas.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>¿Qué características de auditoría e informes proporciona Azure AD B2C? ¿Son las mismas que en Azure AD Premium?
No, Azure AD B2C no admite el mismo conjunto de informes que Azure AD Premium. Sin embargo, hay muchos elementos en común:

* **Informes de inicio de sesión:** solo están disponibles en Azure Portal (Azure Active Directory > Actividad > Inicios de sesión) y no están disponibles a través de la API Graph. Estos informes proporcionan un registro de cada inicio de sesión con menos detalles.
* **Informes de auditoría:** solo están disponibles en Azure Portal (Azure Active Directory > Actividad > Registros de auditoría) y no están disponibles a través de la API Graph. Incluyen tanto la actividad administrativa como la actividad de la aplicación. 
* **Informes de uso:** solo están disponibles mediante [Usage Reporting API](active-directory-b2c-reference-usage-reporting-api.md) y no están disponibles en Azure Portal. Incluyen el número de usuarios, el número de inicios de sesión y el volumen de MFA. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>¿Puedo localizar la interfaz de usuario de páginas servidas por Azure AD B2C? ¿Qué idiomas se admiten?
Sí.  Obtenga información sobre la [personalización de lenguaje](active-directory-b2c-reference-language-customization.md), que se encuentra en versión preliminar pública.  Se ofrecen traducciones a 36 idiomas, y puede invalidar cualquier cadena para satisfacer sus necesidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>¿Puedo usar mis propias direcciones URL en las páginas de registro y de inicio que proporciona Azure AD B2C? Por ejemplo, ¿puedo cambiar las direcciones URL de login.microsoftonline.com a login.contoso.com?
Actualmente, no. Esta característica está en nuestro mapa de ruta. Comprobar el dominio en la pestaña **Dominios** de Azure Portal no logra este objetivo.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>¿Cómo puedo eliminar al inquilino de Azure AD B2C?
Siga estos pasos para eliminar al inquilino de Azure AD B2C:

1. Siga estos pasos para [ir a la configuración de Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) en Azure Portal.
1. Vaya a **Aplicaciones**, **Proveedores de identidades** y **Todas las directivas**, y elimine todas las entradas de cada una.
1. Ahora inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador de la suscripción. (Use la misma cuenta profesional o educativa o la misma cuenta Microsoft que usó para suscribirse a Azure).
1. Cambie al inquilino de Azure AD B2C que desea eliminar.
2. Vaya al menú de Active Directory de la izquierda.
3. Seleccione **Usuarios y grupos**.
4. Seleccione cada usuario de uno en uno (excluya al administrador de suscripciones con el que inició sesión). Haga clic en la opción **Eliminar** de la parte inferior de la página y, después, haga clic en **SÍ** cuando se pida confirmación.
5. Haga clic en **Registros de aplicaciones**.
6. Seleccione la aplicación llamada **b2c-extensions-app**. Haga clic en **Eliminar** y, a continuación, en **SÍ** cuando se le solicite.
7. Seleccione **Información general**.
8. Haga clic en **Eliminar directorio**. Siga las instrucciones en pantalla para completar el proceso.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>¿Puedo obtener Azure AD B2C como parte de Enterprise Mobility Suite?
No, Azure AD B2C es un servicio de Azure de pago por uso y no forma parte de Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>¿Cómo puedo informar sobre problemas con Azure AD B2C?
Consulte [Versión preliminar de Azure Active Directory B2C: presentación de solicitudes de soporte técnico](active-directory-b2c-support.md).
