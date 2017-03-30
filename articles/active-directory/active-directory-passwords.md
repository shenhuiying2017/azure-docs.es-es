---
title: "Restablecimiento de contraseña de Azure Active Directory | Microsoft Docs"
description: "Descripción de las capacidades de administración de contraseñas en Azure AD, incluido el restablecimiento de contraseña, el cambio, la creación de informes de administración de contraseñas y la escritura diferida en Active Directory local."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: be6164fc-bae1-49df-af76-761329ba70a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 4904a3e21a54a2c60ee8b35a46370a9759a029f3
ms.lasthandoff: 03/28/2017


---
# <a name="azure-active-directory-password-reset-for-it-administrators"></a>Restablecimiento de contraseña de Azure Active Directory para administradores de TI
> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

El autoservicio ha sido durante mucho tiempo un objetivo clave para los departamentos de TI de todo el mundo como una medida de reducción de costos y ahorro de mano de obra.  De hecho, el mercado está atestado de productos que le permiten administrar los grupos locales, las contraseñas o los perfiles de usuario en la nube o a nivel local. Azure Active Directory (Azure AD) se distingue de estas ofertas proporcionando algunas de las funcionalidades de autoservicio más fáciles de usar y más eficaces disponibles hoy día.

**La Administración de contraseñas de Azure AD** es un conjunto de capacidades que permiten a los usuarios administrar cualquier contraseña desde cualquier dispositivo, en cualquier momento, desde cualquier ubicación, manteniendo la conformidad con las directivas de seguridad que defina.

## <a name="admins-learn-about-how-to-get-started-with-azure-ad-password-reset"></a>ADMINISTRADORES: cómo empezar a usar el restablecimiento de contraseña de Azure AD
Si es un administrador que desea habilitar el restablecimiento de contraseña de Azure AD o simplemente quiere obtener más información al respecto, comience con los siguientes vínculos hasta llegar a lo que le interesa.

| Tema. |  |
| --- | --- |
| Escenarios admitidos |[¿Qué se puede hacer con el restablecimiento de contraseña de Azure AD?](#what-is-possible-with-azure-ad-password-reset) |
| ¿Por qué usarla? |[¿Por qué usar el restablecimiento de contraseña de Azure AD?](#why-use-azure-ad-password-reset) |
| Precios y disponibilidad |[Precios y disponibilidad](#pricing-and-availability) |
| Habilitar restablecimiento de contraseña |[Habilitación del restablecimiento de contraseña para los usuarios](#enable-password-reset-for-your-users) |
| Personalizar cómo funciona |[Personalización del comportamiento de restablecimiento de contraseña](#customize-password-reset-behavior) |
| Implementarlo en los usuarios |[Configuración de los usuarios para que usen el restablecimiento de contraseña](#configure-your-users-to-use-password-reset) |
| Ver informes |[Visualización de la actividad de restablecimiento de contraseña con informes integrados](#view-password-reset-activity-with-integrated-reports) |
| Restablecimiento de la contraseña del usuario |[Administración de contraseñas de usuarios](#manage-your-users-passwords) |
| Establecer directivas de contraseñas de mi organización |[Establecimiento de directivas de contraseñas](#set-password-policies) |
| Solución de problemas |[Solución de problemas](#troubleshoot-a-problem) |
| P+F |[Lectura de Preguntas más frecuentes](#read-a-faq) |
| Detalles técnicos |[Descripción de los detalles técnicos](#understand-the-technical-details) |
| Características recientes |[Actualizaciones recientes de servicios](#recent-service-updates) |
| Vínculos a otra documentación |[Vínculos a la documentación de restablecimiento de contraseña](#links-to-password-reset-documentation) |

### <a name="what-is-possible-with-azure-ad-password-reset"></a>¿Qué se puede hacer con el restablecimiento de contraseña de Azure AD?
Estas son algunas de las cosas que puede hacer con las capacidades de administración de contraseñas de Azure AD.

* **cambio de la contraseña de autoservicio** permite a los usuarios finales o administradores cambiar sus contraseñas expiradas o no expiradas sin llamar a un administrador o departamento de soporte técnico para obtener soporte técnico.
* **restablecimiento de la contraseña de autoservicio** permite a los usuarios finales o administradores restablecer sus contraseñas automáticamente sin llamar a un administrador o departamento de soporte técnico para obtener soporte técnico. El restablecimiento de la contraseña de autoservicio requiere Azure AD Premium o Básico. Para obtener más información, consulte Ediciones de Azure Active Directory.
* **restablecimiento de la contraseña iniciado por el administrador** permite a un administrador restablecer la contraseña de un usuario final o de otro administrador desde dentro del [Portal de administración de Azure](https://manage.windowsazure.com).
* **informes de actividad de administración de contraseñas** proporcionan a los administradores perspectivas sobre una actividad de registro y restablecimiento de contraseña en su organización.
* **escritura diferida de la contraseña** permite la administración de contraseñas locales desde la nube, por lo que todos los escenarios anteriores pueden realizarse por los usuarios sincronizados con contraseña o federados, o en nombre de ellos. La escritura diferida de la contraseña requiere Azure AD Premium. Para obtener más información, consulte Introducción a Azure AD Premium.

### <a name="why-use-azure-ad-password-reset"></a>¿Por qué usar el restablecimiento de contraseña de Azure AD?
Estas son algunas de las razones por las que se debe usar las capacidades de administración de contraseñas de Azure AD.

* **Reducir los costos** : el restablecimiento de contraseñas con asistencia de un servicio de soporte técnico normalmente supone el 20 % del gasto en TI de una organización.
* **Mejorar las experiencias de usuario** : a los usuarios les incomoda llamar al soporte técnico y pasar una hora al teléfono cada vez que olvidan sus contraseñas.
* **Reducir los volúmenes de soporte técnico** : la administración de contraseñas es el motivo único que empuja a la mayoría de organizaciones a recurrir más veces al departamento de soporte técnico.
* **Permitir la movilidad** : los usuarios pueden restablecer sus contraseñas desde cualquier lugar en que se encuentren.

### <a name="pricing-and-availability"></a>Precios y disponibilidad
El restablecimiento de contraseña de Azure AD está disponible en tres niveles, en función de la suscripción que tenga:

* **Azure AD gratuito** : los administradores que están solo en la nube pueden restablecer sus propias contraseñas.
* **Azure AD Basic o cualquier suscripción de pago de O365** : los usuarios y administradores que están solo en la nube pueden restablecer sus propias contraseñas.
* **Azure AD Premium** : cualquier usuario o administrador, incluidos los que están solo en la nube, los federados o los usuarios sincronizados con contraseña, pueden restablecer sus contraseñas (requiere que [esté habilitada la escritura diferida de contraseñas](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords))

Para obtener más información sobre los precios de Azure AD Premium o Basic, consulte la página [Detalles de precios de Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="enable-password-reset-for-your-users"></a>Habilitación del restablecimiento de contraseña para los usuarios
| Tema. |  |
| --- | --- |
| ¿Cómo se puede habilitar el restablecimiento de contraseña para usuarios en la nube? |[Habilitación de usuarios para que restablezcan sus contraseñas de Azure Active Directory en la nube](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| ¿Cómo se puede habilitar el restablecimiento y el cambio de contraseña para usuarios locales? |[Habilitación de usuarios para que restablezcan o cambien sus contraseñas de Active Directory local](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| ¿Cómo se puede limitar el ámbito de restablecimiento de contraseña a un conjunto específico de usuarios? |[Restricción del restablecimiento de contraseña a usuarios específicos](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| ¿Cómo se puede probar el restablecimiento de contraseña en la nube? |[Restablecimiento de contraseña de Azure AD como usuario](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| ¿Cómo se puede probar el restablecimiento de contraseña local? |[Restablecimiento de contraseña de AD local como usuario](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| ¿Cómo se puede deshabilitar el restablecimiento de contraseña en un momento posterior? |[Configuración: usuarios habilitados para el restablecimiento de contraseña](active-directory-passwords-customize.md#users-enabled-for-password-reset) |

## <a name="customize-password-reset-behavior"></a>Personalización del comportamiento de restablecimiento de contraseña
| Tema. |  |
| --- | --- |
| ¿Cómo se puede cambiar los métodos de autenticación que se admiten? |[Configuración: métodos de autenticación disponibles para los usuarios](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| ¿Cómo se puede cambiar el número de métodos de autenticación necesarios? |[Configuración: número de métodos de autenticación necesarios](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| ¿Cómo se configuran preguntas de seguridad personalizadas? |[Configuración: preguntas de seguridad personalizadas](active-directory-passwords-customize.md#custom-security-questions) |
| ¿Cómo se configuran preguntas de seguridad localizadas predefinidas? |[Configuración: preguntas de seguridad basadas en el conocimiento](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| ¿Cómo se puede cambiar el número de preguntas de seguridad que son necesarias? |[Configuración: número de preguntas de seguridad de registro o restablecimiento](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| ¿Cómo se puede personalizar el modo en que los usuarios se ponen en contacto con un administrador? |[Configuración: personalización del vínculo "póngase en contacto con su administrador"](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| ¿Cómo se puede permitir que los usuarios desbloqueen cuentas de AD sin restablecer una contraseña? |[Configuración: permitir que los usuarios desbloqueen sus cuentas de AD sin restablecer una contraseña](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| ¿Cómo se pueden habilitar las notificaciones de restablecimiento de contraseña para los usuarios? |[Configuración: notificar a los usuarios cuando sus contraseñas se han restablecido](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| ¿Cómo se pueden habilitar las notificaciones de restablecimiento de contraseña para los administradores? |[Configuración: notificar a otros administradores cuando un administrador restablezca su propia contraseña](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| ¿Cómo se puede personalizar la apariencia del restablecimiento de contraseña? |[Configuración: nombre, marca y logotipo de la empresa ](active-directory-passwords-customize.md#password-management-look-and-feel) |

## <a name="configure-your-users-to-use-password-reset"></a>Configuración de los usuarios para que usen el restablecimiento de contraseña
| Tema. |  |
| --- | --- |
| ¿Cómo se puede saber si una cuenta está configurada para el restablecimiento de contraseña? |[Configuración de una cuenta para el restablecimiento de contraseña](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| ¿Cómo se puede configurar a los usuarios para el restablecimiento de contraseña? |[Formas de rellenar los datos de autenticación de restablecimiento de contraseña para los usuarios](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| ¿Cómo se pueden cargar datos manualmente para los usuarios? |[Carga de datos de restablecimiento de contraseña](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| ¿Cómo se puede usar PowerShell para leer o establecer datos para los usuarios? |[Cómo obtener acceso a los datos de restablecimiento de contraseña de los usuarios](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| ¿Cómo se pueden sincronizar datos de restablecimiento de contraseña desde el entorno local? |[¿Qué datos sirven para restablecer la contraseña?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| ¿Cómo se puede usar una campaña de correo electrónico para que los usuarios se registren y utilicen el restablecimiento de contraseña? |[Implementación basada en correo electrónico de restablecimiento de contraseña](active-directory-passwords-best-practices.md#email-based-rollout) |
| ¿Cómo se puede forzar a que los usuarios se registren al iniciar sesión? |[Implementación de restablecimiento de contraseña basado en registro forzoso](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| ¿Cómo se puede forzar a que los usuarios confirmen de nuevo su registro periódicamente? |[Configuración: número de días antes de que los usuarios deban confirmar nuevamente sus datos de autenticación](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| ¿Cuáles son los procedimientos recomendados de comunicar el restablecimiento de contraseña a los usuarios finales? |[Creación de su propio portal de contraseñas para que la utilicen los usuarios](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |

## <a name="view-password-reset-activity-with-integrated-reports"></a>Visualización de la actividad de restablecimiento de contraseña con informes integrados
| Tema. |  |
| --- | --- |
| ¿Dónde se pueden ver los informes de restablecimiento de contraseña? |[Información general de los informes de administración de contraseñas](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| ¿Dónde se puede ver cómo usan los usuarios el restablecimiento de contraseña en mi organización? |[Visualización de la actividad de restablecimiento de contraseña en su organización](active-directory-passwords-get-insights.md#how-to-view-password-management-reports) |
| ¿Dónde se puede ver cuántos usuarios se registran y para qué lo hacen? |[Visualización de la actividad de registro de restablecimiento de contraseña](active-directory-passwords-get-insights.md#how-to-view-password-management-reports) |
| ¿Cómo se pueden obtener informes de restablecimiento de contraseña desde una API? |[Creación de una aplicación de Azure AD para tener acceso a la API de informes](active-directory-reporting-api-getting-started.md) |
| ¿Qué tipo de información de informes de restablecimiento de contraseña está disponible desde una API? |[Eventos de registro y restablecimiento de contraseña disponibles en la API de informes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |

## <a name="manage-your-users-passwords"></a>Administración de contraseñas de usuarios
| Tema. |  |
| --- | --- |
| ¿Cómo se puede restablecer la contraseña de un usuario desde el portal de administración de OfficeO365? |[Restablecimiento de una contraseña de usuario en Office 365](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| ¿Cómo se puede restablecer una contraseña de usuario mediante PowerShell? |[Restablecimiento de una contraseña de usuario con Set-MsolUserPassword](https://msdn.microsoft.com/library/azure/dn194140.aspx) |

## <a name="set-password-policies"></a>Establecimiento de directivas de contraseñas
| Tema. |  |
| --- | --- |
| ¿Cómo se pueden establecer directivas de expiración de contraseñas de organización desde Office 365? |[Establecimiento de directivas de expiración de contraseñas](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| ¿Cómo se pueden establecer las contraseñas de un usuario específico para que no expiren nunca con PowerShell? |[Establecimiento de una contraseña de usuario individual para que no expire nunca con PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| ¿Cómo se puede saber si una contraseña de usuario está establecida para que no expire nunca con PowerShell? |[Comprobación del estado de expiración de contraseñas de usuario individual con PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |

## <a name="troubleshoot-a-problem"></a>Solución de problemas
| Tema. |  |
| --- | --- |
| ¿Qué información se debe proporcionar al soporte técnico si se necesita ayuda? |[Información para incluir si necesita ayuda](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| Cómo se puede corregir un problema con el restablecimiento de contraseña |[Solución de problemas con el portal de restablecimiento de contraseña](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| Cómo se puede corregir un problema con la escritura diferida de contraseñas |[Solución de problemas con la escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Cómo se puede corregir un problema con la conectividad de la escritura diferida de contraseñas |[Solución de problemas con la conectividad de la escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| Cómo se puede corregir un problema con la configuración de restablecimiento de contraseña |[Solución de problemas con la configuración de restablecimiento de contraseña en el Portal de administración de Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| Cómo se puede corregir un problema con los informes de restablecimiento de contraseña |[Solución de problemas con los informes de administración de contraseñas en el Portal de administración de Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| Cómo se puede corregir un problema con el registro de restablecimiento de contraseña |[Solución de problemas con el portal de registro de restablecimiento de contraseña](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| Códigos de error de registro de eventos de escritura diferida de contraseñas |[Códigos de error de registro de eventos de escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |

## <a name="read-a-faq"></a>Lectura de Preguntas más frecuentes
| Tema. |  |
| --- | --- |
| P+F acerca del registro de restablecimiento de contraseña |[P+F del registro de restablecimiento de contraseña](active-directory-passwords-faq.md#password-reset-registration) |
| P+F acerca del restablecimiento de contraseña |[P+F del restablecimiento de contraseña](active-directory-passwords-faq.md#password-reset) |
| P+F acerca de informes de restablecimiento de contraseña |[P+F de informes de administración de contraseñas](active-directory-passwords-faq.md#password-management-reports) |
| P+F acerca de la escritura diferida de contraseñas |[P+F de la escritura diferida de contraseñas](active-directory-passwords-faq.md#password-writeback) |

## <a name="understand-the-technical-details"></a>Descripción de los detalles técnicos
| Tema. |  |
| --- | --- |
| Información sobre lo que es la escritura diferida de contraseñas |[Información general sobre la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#password-writeback-overview) |
| Información sobre cómo funciona la escritura diferida de contraseñas |[Funcionamiento de la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| Información sobre los escenarios admitidos por la escritura diferida de contraseñas |[Escenarios admitidos para la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| Información sobre cómo se protege la escritura diferida de contraseñas |[Modelo de seguridad de la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| Información sobre cómo funciona el portal de restablecimiento de contraseña |[¿Cómo funciona el portal de restablecimiento de contraseñas?](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| Información sobre qué datos se usan para el restablecimiento de contraseña |[¿Qué datos sirven para restablecer la contraseña?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## <a name="recent-service-updates"></a>Actualizaciones recientes de servicios
#### <a name="enforce-password-reset-registration-at-sign-in-to-office-365-apps---november-2015"></a>Aplicación del registro de restablecimiento de contraseña al iniciar sesión en aplicaciones de Office 365, noviembre de 2015
* Ahora, después de habilitar la característica de [registro exigido](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) , los usuarios deberán registrarse desde cualquier lugar que inicien sesión con una cuenta profesional o educativa.  Esto aumenta considerablemente el ritmo al que muchas organizaciones pueden incorporarse al restablecimiento de contraseña.  Con esta nueva característica, ¡hemos visto la incorporación de grandes organizaciones en tan solo dos semanas!

#### <a name="support-for-unlocking-on-premises-ad-accounts-without-resetting-a-password---november-2015"></a>Compatibilidad con el desbloqueo de cuentas de AD locales sin restablecer una contraseña, noviembre de 2015
* Lograr solo el desbloqueo (sin tener que reiniciar) supone un auténtico reto para los departamentos de soporte técnico hoy en día.  De hecho, muchas organizaciones dedican hasta un 70 % del presupuesto destinado al restablecimiento de contraseñas al desbloqueo de cuentas.  Para satisfacer esta demanda, ahora, con el restablecimiento de contraseña de Azure AD puede habilitar una característica para que los usuarios puedan desbloquear cuentas de AD locales sin tener que restablecer la contraseña.  Compruebe cómo habilitarla aquí: [Configuración: permitir que los usuarios desbloqueen sus cuentas de AD locales sin restablecer una contraseña](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password).

#### <a name="usability-updates-to-registration-page---october-2015"></a>Actualizaciones de facilidad de uso para la página de registro, octubre de 2015
* Ahora, cuando un usuario ya tiene datos registrados, solo tiene que hacer clic en "Tiene buen aspecto" para actualizar los datos sin necesidad de volver a enviar el mensaje correo electrónico o una llamada de teléfono.

#### <a name="improved-reliability-of-password-writeback---september-2015"></a>Mejor confiabilidad de escritura diferida de contraseñas, septiembre de 2015
* A partir de la versión de septiembre de Azure AD Connect, el agente de escritura diferida de contraseñas intentará ahora conexiones de manera más agresiva y funcionalidades adicionales de conmutación por error más sólidas.

#### <a name="api-for-retrieving-password-reset-reporting-data---august-2015"></a>API para recuperar los datos de informes de restablecimiento de contraseña, agosto de 2015
* Ahora, los datos que se encuentran detrás de los informes de restablecimiento de contraseña se pueden recuperar directamente de la [API de eventos e informes de Azure AD](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

#### <a name="support-for-azure-ad-password-reset-during-cloud-domain-join---august-2015"></a>Compatibilidad con el restablecimiento de contraseña de Azure AD durante la unión al dominio de nube, agosto de 2015
* Ahora, cualquier usuario de nube puede restablecer su contraseña directamente desde la pantalla de inicio de sesión de Windows 10 durante la experiencia de incorporación de unión de dominio de nube.  Tenga en cuenta que esto todavía no está expuesto en la pantalla de inicio de sesión de Windows 10.

#### <a name="enforce-password-reset-registration-at-sign-in-to-azure-and-federated-apps---july-2015"></a>Aplicación del registro de restablecimiento de contraseña al iniciar sesión en Azure y aplicaciones federadas, julio de 2015
* Además de aplicar el registro al iniciar sesión en myapps.microsoft.com, ahora admitimos la aplicación del registro durante inicios de sesión para el Portal de administración de Azure y cualquiera de sus aplicaciones de inicio de sesión único federadas.

#### <a name="security-question-localization-support---may-2015"></a>Compatibilidad con la localización de preguntas de seguridad, mayo de 2015
* Ahora tiene la opción de seleccionar preguntas de seguridad predefinidas que están localizadas en el conjunto de idiomas completo de O365 al configurar las preguntas de seguridad para el restablecimiento de contraseña.

#### <a name="account-unlock-support-during-password-reset---june-2015"></a>Compatibilidad con el desbloqueo de cuentas durante el restablecimiento de contraseña, junio de 2015
* Si está utilizando la escritura diferida de contraseñas y restablece la contraseña cuando la cuenta está bloqueada, desbloquearemos automáticamente su cuenta de Active Directory.

#### <a name="branded-self-service-password-reset-sspr-registration---april-2015"></a>Registro (SSPR) de restablecimiento de contraseña de autoservicio con marca, abril de 2015
* La página de registro del restablecimiento de contraseña ahora lleva el logotipo de su empresa.

#### <a name="security-questions---march-2015"></a>Preguntas de seguridad, marzo de 2015
* ¡Hemos puesto las preguntas de seguridad a disposición de todos los usuarios!

#### <a name="account-unlock---march-2015"></a>Desbloqueo de cuenta, marzo de 2015
* Ahora los usuarios pueden desbloquear sus cuentas cuando se produce el restablecimiento de contraseña.

## <a name="coming-soon"></a>Próximamente
A continuación se muestran algunas de las interesantes características en las que estamos trabajando en este momento.

**Compatibilidad para recordar a los usuarios que actualicen sus datos registrados durante el inicio de sesión** , trabajo en curso

* En la actualidad, admitimos recordar a los usuarios que actualicen sus datos registrados al tener acceso a myapps.microsoft.com, pero estamos trabajando en la capacidad para hacerlo para todos los inicios de sesión.

## <a name="next-steps"></a>Pasos siguientes
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis componentes diferentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md): obtenga información acerca de cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md): obtenga información acerca de cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización
* [**Obtener información**](active-directory-passwords-get-insights.md): obtenga información acerca de nuestras funcionalidades integradas de creación de informes.
* [**P+F**](active-directory-passwords-faq.md) : obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.

