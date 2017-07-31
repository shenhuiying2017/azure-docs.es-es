---
title: "Directivas y configuraciones para correo electrónico seguro de Azure AD | Microsoft docs"
description: "Describe los conceptos básicos y las recomendaciones de Microsoft para implementar directivas y configuraciones para correo electrónico seguro."
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 06/14/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: bb869e67340aa7a9f645c0b51f5a44d91ab51ce8
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017


---

# <a name="secure-email-policies-and-configurations"></a>Directivas y configuraciones para correo electrónico seguro
 
## <a name="introduction"></a>Introducción

Las recomendaciones de este documento describen las directrices generales de Microsoft para aplicar directivas y configuraciones para el correo electrónico en toda la empresa. Estas recomendaciones aseguran la productividad y protección de los empleados.  En concreto, este documento se centra en la protección del correo electrónico de la organización y, al mismo tiempo, minimiza el impacto del uso para los usuarios.

### <a name="intended-audience"></a>Destinatarios

Este documento está pensado para arquitectos de infraestructuras de empresa y profesionales de TI familiarizados con [Exchange Online](https://technet.microsoft.com/library/jj200580.aspx) (Office 365) y productos de [Microsoft Enterprise Mobility + Security](http://microsoft.com/ems). Estos productos incluyen, sin limitación, Azure Active Directory (identidad), Microsoft Intune (administración de dispositivos) y Azure Information Protection (protección de datos). 

### <a name="customer-environment"></a>Entorno del cliente

Las directivas recomendadas en este documento se aplican a organizaciones empresariales que no solo operan completamente dentro de la nube de Microsoft, sino también para clientes con la infraestructura implementada en local y en la nube de Microsoft. 

### <a name="assumptions"></a>Supuestos

La mayoría de las recomendaciones de este documento utilizan servicios disponibles únicamente con suscripciones de Enterprise Mobility + Security (EMS) E5. Las recomendaciones presentadas asumen todas las funcionalidades de la suscripción de EMS E5. 

En este documento se describe cómo aplicar la configuración recomendada para correo electrónico protegido en un entorno recién implementado.  Los futuros documentos pueden proporcionan una guía adicional sobre cómo migrar de las directivas y configuraciones existentes a las que se describen a continuación.

### <a name="caveats"></a>Advertencias

Su organización puede estar sujeta a requisitos reguladores o de cumplimiento de cualquier otro tipo, incluidas las recomendaciones específicas que pueden exigir al usuario que aplique directivas que difieren de estas configuraciones recomendadas.  Estas configuraciones recomiendan controles de utilización que históricamente no han estado disponibles.  Recomendamos el uso de estos controles porque creemos que representan un equilibrio entre seguridad y productividad.  

Hemos hecho todo lo posible para justificar una amplia variedad de requisitos de protección de la organización. Sin embargo, no podemos justificar todos los requisitos posibles o todos los aspectos exclusivos de su organización. Utilice este documento como guía para saber cómo Microsoft y el equipo empresarial productivo seguro piensan aplicar correctamente la directiva en su organización. 

>[!NOTE]
>Para información general de los conceptos fundamentales necesarios para comprender las funcionalidades de protección descritas en estas recomendaciones, consulte [EMS and Office 365 Service Descriptions](secure-email-ems-office365-service-descriptions.md) (Descripciones de los servicios de EMS y Office 365).
>

## <a name="core-concepts"></a>Conceptos principales

Todas las medidas de seguridad del mundo no importan cuando los usuarios que experimentan problemas innecesarios al intentar hacer su trabajo evitan las directivas de seguridad de la organización. El inicio de sesión único (SSO) de Azure AD intenta minimizar la carga a los usuarios. De esta forma, los usuarios pueden seguir siendo productivos y, al mismo tiempo, cumplir las directivas de control de acceso de la organización. 

### <a name="single-sign-on-authentication"></a>Autenticación de inicio de sesión único

El siguiente diagrama muestra un flujo de autenticación de SSO típico:

![Experiencia de inicio de sesión único del usuario final](./media/secure-email/typical-authentication-flow.png)

Para empezar la autenticación, el cliente envía credenciales (por ejemplo, un nombre de usuario y una contraseña), así como cualquier artefacto de SSO obtenido en el pasado a Azure AD. Un artefacto de SSO puede ser un token de sesión para explorador o un token de actualización para aplicaciones enriquecidas. 

Azure AD comprueba las credenciales y/o el artefacto de SSO y evalúa todas las directivas aplicables. A continuación, emite un token de acceso para el proveedor de recursos (Exchange Online en el diagrama). Azure AD también emite un artefacto de SSO, como parte de la respuesta para permitir que el cliente consiga el SSO en solicitudes futuras. 

El cliente almacena el artefacto de SSO y envía el token de acceso como una prueba de identidad al proveedor de recursos. Después de que Exchange Online compruebe el token de acceso y realice las comprobaciones necesarias, concederá el acceso de cliente a los mensajes de correo electrónico.

#### <a name="single-sign-on-sso-refresh-tokens"></a>Tokens de actualización de inicio de sesión único (SSO)

El SSO se puede lograr de varias maneras. Por ejemplo, las cookies de un proveedor de identidades se pueden utilizar como artefacto de SSO para almacenar el estado de inicio de sesión para un usuario dentro de un explorador. A continuación, serán posibles los futuros intentos de inicio de sesión en modo silencioso (sin las peticiones de credenciales) en las aplicaciones que usan el mismo proveedor de identidades. 

Cuando un usuario se autentica con Azure AD, se establece una sesión SSO con Azure AD y el explorador del usuario. El token de SSO, en forma de cookie, representa esta sesión. Azure AD usa dos tipos de tokens de sesión SSO: persistente y no persistente. Los tokens de sesión persistentes los almacenan los exploradores como cookies persistentes cuando se activa la casilla "Mantener la sesión iniciada" durante el inicio de sesión. Los tokens de sesión no persistentes se almacenan como cookies de sesión y se destruyen cuando el explorador se cierra. 

Para aplicaciones sólidas capaces de usar protocolos de autenticación modernos, como [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html) y [OAuth 2.0](https://tools.ietf.org/html/rfc6749), el SSO se habilita mediante tokens de actualización como los artefactos de SSO (además de las cookies de SSO descritas anteriormente). Los tokens de actualización se presentan en un servidor de autorización cuando una aplicación solicita un nuevo token de acceso. 

El token de actualización contiene notificaciones y atributos sobre el tipo de métodos de autenticación que se usa al autenticar usuarios. Por ejemplo, si un usuario se autentica correctamente utilizando varios métodos (nombre de usuario y contraseña y autenticación basada en teléfono), una notificación de autenticación multifactor (MFA) está presente en el token de actualización. Además, puede haber notificaciones adicionales que contienen datos como la duración de la validez MFA, etc. 

Los tokens de actualización permiten al cliente obtener un nuevo token de acceso, sin necesidad de realizar una nueva autenticación interactiva. Los tokens de actualización tienen una vigencia mucho mayor que los tokens de acceso y se pueden canjear para obtener un nuevo par de token de acceso y actualización. A continuación, los nuevos tokens de actualización obtenidos se pueden utilizar continuamente para capturar otro conjunto de tokens de acceso y actualización. 

El cliente sigue este proceso de SSO hasta que tenga lugar una de las siguientes circunstancias: el valor del tiempo inactivo máximo del token de actualización expire, la antigüedad máxima del token de actualización expire o los requisitos de la directiva de autenticación y autorización cambien. Este cambio se produce durante el tiempo en el que se emitió el token de actualización original. Los cambios significativos de atributos de usuario, por ejemplo un restablecimiento de contraseña, también requieren que se genere un nuevo token de autenticación. El cliente debe realizar una nueva autenticación interactiva para continuar. Básicamente, esto significa una interrupción en el proceso de SSO que el cliente no ha experimentado hasta ahora. 

#### <a name="conditional-access"></a>Acceso condicional

Azure AD, como servidor de autorización para las aplicaciones, determina si se deben emitir tokens de acceso según una evaluación de cualquier directiva de acceso condicional aplicada al recurso al que está intentando acceder. Si se cumplen los requisitos de la directiva, se emite tanto un token de acceso como un token de actualización actualizado. Si no se cumple la directiva, el usuario recibe instrucciones sobre cómo cumplirla y/o se le pide que complete pasos adicionales que incluyen la autenticación multifactor (MFA).  Una vez que se ha completado la MFA, la notificación de MFA se agrega al token de actualización resultante.  

Las notificaciones del token de actualización se acumulan con el tiempo. Algunas de las notificaciones tienen escalas de tiempo de expiración, tras las cuales ya no se consideran durante las comprobaciones de autorización. En ocasiones, esto puede causar resultados inesperados. Por ejemplo, si una directiva de acceso condicional se configura para que se requiera MFA para los intentos de autenticación procedentes de ubicaciones de extranet. En este caso, es posible que los usuarios a veces no reciban el aviso de MFA esperado al acceder a un recurso de extranet. Una posible razón de esta situación es que los usuarios podrían haber efectuado anteriormente una MFA poco antes de salir de la intranet. Por lo tanto, tienen una notificación de MFA válida en sus token de acceso. Esta notificación de MFA satisface el requisito de la directiva y, por tanto, Azure AD no muestra al usuario una solicitud de MFA adicional.

#### <a name="token-lifetime-policy"></a>Directiva de vigencia del token

Más allá de la expiración de las notificaciones individuales en un token, los propios tokens tienen tiempos de expiración. Tal y como se indicó antes, los tokens expirados son un motivo por el que se puede interrumpir la experiencia de SSO. Puede establecer la vigencia de un token emitido por Azure AD mediante [directivas de vigencia del token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes). Como puede deducirse de lo anterior, definir los contornos de una sesión de SSO es más difícil de capturar. Por ejemplo, cuando las aplicaciones enriquecidas como distintos factores que aparentemente están desconectados pueden afectar a la vigencia de una sesión de SSO.

>[!NOTE]
>Los administradores globales de Azure AD pueden controlar los períodos de validez e inactividad de los tokens de actualización. En el artículo [Vigencia de tokens configurables de Azure Active Directory (versión preliminar pública)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes) puede encontrar información sobre el token de acceso y las reclamaciones utilizando la configuración descrita.
>

#### <a name="primary-refresh-tokens"></a>Tokens de actualización primarios

Hasta ahora, en este artículo se describe el funcionamiento de SSO en el contexto de un solo cliente. Pero esto no es suficiente para experimentar SSO en una sola aplicación. Hoy en día, los usuarios experimentan flujos de trabajo interactivos que abarcan varias aplicaciones dentro del mismo conjunto de aplicaciones, como Microsoft Office. Los usuarios necesitan tener acceso a aplicaciones no relacionadas, incluidas las aplicaciones de LOB desarrolladas internamente. 

Tradicionalmente, los dispositivos Windows unidos a un dominio, mediante la autenticación integrada de Windows (Kerberos), logran un alto grado de experiencia de SSO a través de varias aplicaciones y recursos. Estas aplicaciones incluyen exploradores admitidos como Internet Explorer o Edge. Existe una analogía para el dominio de Azure AD, en forma de un token de actualización primario (PRT). 

Este token PRT con privilegios solo está disponible para un conjunto selecto de entidades de cliente (como pueden ser los componentes del sistema de plataforma). A continuación, las entidades pueden permitir el acceso de autenticación asincrónica a otras aplicaciones cliente, por lo que también pueden ofrecer una experiencia SSO sin problemas. 

Para los usuarios de Office 365 en dispositivos [iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios) y [Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android), se ha realizado un trabajo adicional para reducir el número de autenticaciones necesarias mediante la aplicación de la funcionalidad de agente de autenticación. Esta funcionalidad está integrada en las aplicaciones Microsoft Authenticator y Portal de empresa de Intune.

>[!NOTE]
>Las recomendaciones descritas en este documento asumen que una de las aplicaciones se ha implementado en dispositivos Android o iOS de los usuarios. Por ejemplo, una aplicación como un agente de autenticación (Microsoft Authenticator o Portal de empresa de Intune).
>

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

La autenticación multifactor (MFA) proporciona un alto nivel de confianza sobre el sujeto de la autenticación, dado que el sujeto proporcionó varias pruebas o evidencias parciales sobre su identidad. Las pruebas pueden ser secretos previamente establecidos de que solo el sujeto y la entidad de certificación conocen o una entidad física que se espera que solo el sujeto posea. La MFA normalmente se realiza en fases. Primero establece la identidad mediante contraseñas y luego requiere un método de autenticación diferente (menos propenso a ataques malintencionados) como el segundo factor, o viceversa.

Las diferentes entidades pueden tener una interpretación ligeramente distinta de MFA, o una autenticación segura. Por ejemplo, algunas entidades de certificación (o más específicamente los administradores que configuran directivas en dichas entidades de certificación) pueden optar por interpretar la autenticación basada en tarjeta inteligente física como MFA. Puede ocurrir, aunque en sentido estricto, que la autenticación basada en tarjeta inteligente sea una autenticación de una sola fase. 

La combinación de exigir una tarjeta inteligente física y el requisito de especificar un código PIN (secreto) para usar la tarjeta inteligente se puede interpretar como MFA. Sin embargo, otros usuarios pueden optar por ser más flexibles en cuanto a la frecuencia con la que se requieren métodos de autenticación más molestos. Por tanto, se considera que las autenticaciones normales, que tienen lugar entre las autenticaciones más seguras, son válidas para recursos que normalmente requieren autenticación segura. Por ejemplo, en algunas organizaciones puede ser aceptable exigir a un usuario que realice la MFA cada pocas horas o días. El tiempo depende de la importancia de los recursos que se están protegiendo y siempre que la ubicación física del usuario que intenta acceder a un recurso no cambie.

Azure AD y AD FS utilizan la notificación de MFA para indicar si la autenticación se realiza con MFA. De forma predeterminada, Azure AD emite tokens con notificación de MFA cuando la autenticación se realiza con [Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud) o [Windows Hello para empresas](https://docs.microsoft.com/en-us/windows/access-protection/hello-for-business/hello-identity-verification). En el escenario de federación, Azure AD acepta la notificación de MFA de proveedores de identidades federados como AD FS y transfiere la notificación de MFA en los tokens. 

### <a name="security-guidelines"></a>Directrices de seguridad

Esta sección contiene directrices de seguridad que se deben seguir al implementar cualquiera de las recomendaciones de correo electrónico seguro proporcionadas en secciones posteriores.

#### <a name="security-and-productivity-trade-offs"></a>Equilibrio entre seguridad y productividad

Es necesario encontrar el equilibrio entre seguridad y productividad. Para ayudar a entender esta relación de equilibrio, se usa ampliamente la tríada de seguridad Seguridad-Funcionalidad-Facilidad de uso (SFU):

![Equilibrio entre seguridad y productividad](./media/secure-email/security-triad.png)

Según el modelo de tríada SFU, las recomendaciones de este documento se basan en los siguientes principios: 

* Conocer la audiencia: ser flexible por función de trabajo/barra de seguridad
* Aplicar la directiva de seguridad justo a tiempo y asegurarse de que tiene sentido

#### <a name="administrators-versus-users"></a>Administradores frente a usuarios

Se recomienda crear grupos de seguridad que contengan todos los usuarios que tienen cuentas administrativas o tienen derecho a recibir una cuenta o privilegios administrativos de forma temporal. Estos grupos de seguridad se utilizan para definir directivas de acceso condicional específicas para Azure AD y los administradores de Office 365.  

Las recomendaciones de directiva tienen en cuenta los privilegios asociados a una cuenta. Los roles de [administrador de Office 365](https://support.office.com/en-us/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) tienen muchos más privilegios en relación con Exchange Online y Office 365. Por lo tanto, nuestras recomendaciones de directiva para estas cuentas son más estrictas que para las cuentas de usuario convencionales. Todas las directivas que hacen referencia a los administradores indican la directiva recomendada para las cuentas administrativas de Office 365.

#### <a name="reduce-the-number-of-accounts-with-persistent-admin-access"></a>Reducción del número de cuentas con acceso de administrador persistente

Use Azure AD Privileged Identity Management para reducir el número de cuentas administrativas persistentes. Además, se recomienda que los administradores de Office 365 tengan una cuenta de usuario independiente para su uso no administrativo convencional. Solo aplican las cuentas administrativas cuando es necesario para completar una tarea asociada a su función de trabajo.

## <a name="next-steps"></a>Pasos siguientes
[Directivas recomendadas](secure-email-recommended-policies.md)

