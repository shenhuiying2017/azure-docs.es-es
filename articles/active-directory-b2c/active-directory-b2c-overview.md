---
title: "Información general de Azure Active Directory B2C | Microsoft Docs"
description: Desarrollo de aplicaciones orientadas al consumidor con Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/24/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 217ffc63e07d66de522accc42c246125d01713c8


---
# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory B2C: Registro e inicio de sesión de los consumidores en sus aplicaciones
Azure Active Directory B2C es una solución completa de administración de identidades en la nube, destinada a aplicaciones móviles y web orientadas al consumidor. Se trata de un servicio global de alta disponibilidad que se puede escalar a cientos de millones de identidades de consumidor. Basado en una plataforma segura de nivel empresarial, Azure Active Directory B2C protege las aplicaciones, la empresa y los consumidores.

En el pasado, los desarrolladores de aplicaciones que deseaban registrar e iniciar la sesión de los consumidores en sus aplicaciones tenían que escribir su propio código. Y usaban bases de datos o sistemas locales para almacenar nombres de usuario y contraseñas. Azure Active Directory B2C ofrece a los desarrolladores una manera mejor de integrar la administración de identidades de consumidor en sus aplicaciones gracias a la ayuda de una plataforma segura basada en estándares y un amplio conjunto de directivas extensibles. El uso de Azure Active Directory B2C permite a los consumidores registrarse en las aplicaciones con sus cuentas de redes sociales existentes (Facebook, Google, Amazon, LinkedIn) o creando nuevas credenciales (dirección de correo electrónico y contraseña o nombre de usuario y contraseña); a esto último lo llamamos "cuentas locales".

## <a name="get-started"></a>Primeros pasos
Para crear una aplicación que acepte registros e inicios de sesión de consumidores, primero deberá registrarla en un inquilino de Azure Active Directory B2C. Para obtener su propio inquilino, siga los pasos descritos en [Creación de un inquilino de Azure AD B2C](active-directory-b2c-get-started.md).

Puede escribir la aplicación para el servicio Azure Active Directory B2C de dos maneras: puede elegir enviar mensajes de protocolo directamente con [OAuth 2.0 u Open ID Connect](active-directory-b2c-reference-protocols.md), o bien usar nuestras bibliotecas para que el trabajo se realice automáticamente. Elija su plataforma favorita en la tabla siguiente y empiece a trabajar.

[!INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Novedades
Vuelva a esta sección a menudo para conocer los cambios futuros en Azure Active Directory B2C. También enviaremos tweets acerca de las actualizaciones mediante @AzureAD.

* Conozca nuestro [marco de directivas extensible](active-directory-b2c-reference-policies.md) y los tipos de directivas que puede crear y usar en sus aplicaciones.
* Agregue un marcador a nuestro [blog de servicio](https://blogs.msdn.microsoft.com/azureadb2c/) para ver notificaciones sobre problemas de servicio menores, el estado y las mitigaciones. Continúe también con la supervisión del [panel de estado de Azure](https://azure.microsoft.com/status/) .
* [Limitaciones y restricciones actuales del servicio](active-directory-b2c-limitations.md).
* Por último, un [código de ejemplo](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) con Azure AD B2C y ASP.NET Core.

## <a name="howto-articles"></a>Artículos de procedimientos
Aprenda a usar determinadas características de Azure Active Directory B2C:

* Configure las cuentas de [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Cuenta Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) y [LinkedIn](active-directory-b2c-setup-li-app.md) para usarlas en las aplicaciones orientadas al consumidor.
* [Uso de atributos personalizados para recopilar información sobre los consumidores](active-directory-b2c-reference-custom-attr.md).
* [Habilitación de Azure Multi-Factor Authentication en las aplicaciones orientadas al consumidor](active-directory-b2c-reference-mfa.md).
* [Configuración del restablecimiento de contraseña de autoservicio para los consumidores](active-directory-b2c-reference-sspr.md).
* [Personalización de la apariencia de las páginas de registro, de inicio de sesión y de otras páginas orientadas al consumidor](active-directory-b2c-reference-ui-customization.md) a las que presta servicio Azure Active Directory B2C.
* [Uso de la API Graph de Azure Active Directory para crear, leer, actualizar y eliminar consumidores mediante programación](active-directory-b2c-devquickstarts-graph-dotnet.md) en el directorio de Azure Active Directory B2C.

## <a name="next-steps"></a>Pasos siguientes
Estos vínculos serán útiles para explorar el servicio en profundidad:

* Consulte la [información de precios de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Obtenga ayuda sobre Stack Overflow con las etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
* Envíenos sus ideas mediante [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/); ¡queremos conocerlas! Use la frase "AzureADB2C:" en el título de la entrada para que podamos encontrarla.
* Revise la [referencia del protocolo Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Revise la [referencia de token de Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Lea las [P+F sobre Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Presentación de solicitudes de soporte técnico para Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obtención de actualizaciones de seguridad para nuestros productos
Le animamos a que obtenga notificaciones de los incidentes de seguridad que se produzcan; para ello, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscríbase a las alertas de avisos de seguridad.




<!--HONumber=Nov16_HO2-->


