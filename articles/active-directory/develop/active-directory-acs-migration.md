---
title: "Migración de Azure Access Control Service (ACS)"
description: Opciones para mover aplicaciones y servicios fuera de Azure Access Control Service | Microsoft Azure
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Migración de Azure Access Control Service (ACS)

Microsoft Azure Active Directory Access Control (también conocido como Access Control Service o ACS) se retirará en noviembre de 2018.  Las aplicaciones y servicios que usan actualmente ACS deben migrar completamente a un mecanismo de autenticación diferente antes de esta fecha. Este documento describe las recomendaciones para los clientes actuales que pretenden dejar de usar ACS. Si no usa ACS actualmente, no es necesario realizar ninguna acción.


## <a name="brief-acs-overview"></a>Breve introducción a ACS

ACS es un servicio de autenticación en la nube que ofrece una forma sencilla de autenticar y autorizar a los usuarios a la hora de tener acceso a las aplicaciones y los servicios web, a la vez que se permite que las características de autenticación y autorización queden excluidas del código. Lo usan principalmente los desarrolladores y arquitectos de clientes de. NET, las aplicaciones web ASP.NET y los servicios web WCF.

Los casos de uso de ACS pueden dividirse en tres categorías principales:

- La autenticación en determinados servicios en la nube de Microsoft, incluido Azure Service Bus, Dynamics CRM y otros. Las aplicaciones cliente pueden obtener tokens de ACS que pueden usarse a fin de autenticar estos servicios para realizar diversas acciones.
- Agregar autenticación a las aplicaciones web, tanto de las diversas integradas personalizadas como de las preconfiguradas (como Sharepoint). Mediante la autenticación "pasiva" de ACS, las aplicaciones web podían admitir inicios de sesión con cuentas de Google, Facebook, Yahoo, Microsoft (Live ID), Azure Active Directory y ADFS.
- Protección de servicios web integrados personalizados con tokens emitidos por ACS. Mediante la autenticación "activa", los servicios web podían asegurarse de que solo permiten el acceso de los clientes conocidos que se han autenticado con ACS.

Cada uno de estos casos de uso y sus estrategias recomendadas de migración se describe en las secciones siguientes. En la mayoría de los casos, se requieren cambios de código significativos para migrar los servicios y las aplicaciones existentes a las tecnologías más recientes. Se recomienda que comience el planeamiento y la ejecución de la migración inmediatamente para evitar la posibilidad de que se produzcan interrupciones o tiempo de inactividad.

> [!WARNING]
> En la mayoría de los casos, se requieren cambios de código significativos para migrar los servicios y las aplicaciones existentes a las tecnologías más recientes. Se recomienda que comience el planeamiento y la ejecución de la migración inmediatamente para evitar la posibilidad de que se produzcan interrupciones o tiempo de inactividad.

En cuanto a la arquitectura, ACS consta de los siguientes componentes:

- Un servicio de token seguro (STS), que recibe las solicitudes de autenticación y emite tokens de seguridad de vuelta.
- El Portal de Azure clásico, que se utiliza para crear, eliminar y habilitar o deshabilitar los espacios de nombres de ACS.
- Un portal de administración de ACS independiente, que se usa para personalizar y configurar el comportamiento de un espacio de nombres de ACS.
- Un servicio de administración, que se puede usar para automatizar las funciones de los portales.
- Un motor de reglas de transformación de tokens, que se puede usar para definir una lógica compleja a fin de manipular el formato de salida de los tokens emitidos por ACS.

Para utilizar estos componentes, debe crear uno o varios **espacios de nombres** de ACS. Un espacio de nombres es una instancia dedicada de ACS con la que los servicios y aplicaciones se comunican; se aísla de todos los demás clientes de ACS, que usan sus propios espacios de nombres. Un espacio de nombres de ACS tiene una dirección URL dedicada, como:

```HTTP
https://mynamespace.accesscontrol.windows.net
```

Toda comunicación con las operaciones de administración y STS se realizan en esta dirección URL, con diferentes procedimientos para distintos fines. Para determinar si sus aplicaciones o servicios utilizan ACS, supervise para todo el tráfico a `https://{namespace}.accesscontrol.windows.net`.  Todo el tráfico a esta dirección URL lo controla ACS y tiene que interrumpirse.  La única excepción es todo el tráfico a `https://accounts.accesscontrol.windows.net`: el tráfico a esta dirección URL ya se controla mediante un servicio diferente y no se ve afectado por la degradación de ACS.  También debe asegurarse de iniciar sesión en el Portal de Azure clásico y comprobar los espacios de nombres de ACS en las suscripciones que posee.  Los espacios de nombres de ACS se enumeran en el servicio **Active Directory**, en la pestaña **Espacios de nombres de control de acceso**.

Para más información sobre ACS, consulte [esta documentación archivada en MSDN](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Calendario de retiradas

A partir de noviembre de 2017, todos los componentes de ACS son totalmente compatibles y están operativos. La única restricción es que los [nuevos espacios de nombres de ACS no se pueden crear a través del Portal de Azure clásico](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

La escala de tiempo para dejar de usar estos componentes sigue esta programación:

- **Noviembre de 2017**: la experiencia de administración de Azure AD en el Portal de Azure clásico [se retira](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). En este momento, la administración del espacio de nombres de ACS estará disponible en esta nueva dirección URL dedicada: `http://manage.windowsazure.com?restoreClassic=true`. Esto le permite ver los espacios de nombres existentes, habilitarlos o deshabilitarlos, y eliminarlos completamente si lo desea.
- **Abril de 2018**: la administración de los espacios de nombres de ACS ya no está disponible en esta dirección URL dedicada. En este momento, no es posible deshabilitar o habilitar, eliminar o enumerar los espacios de nombres de ACS. El portal de administración de ACS, sin embargo, es totalmente funcional y se encuentra en `https://{namespace}.accesscontrol.windows.net`. Todos los demás componentes de ACS también seguirán funcionando con normalidad.
- **Noviembre de 2018**: todos los componentes de ACS se cierran definitivamente. Esto incluye el portal de administración de ACS, el servicio de administración, STS y el motor de reglas de transformación de tokens. En este momento, las solicitudes enviadas a ACS (ubicado en `{namespace}.accesscontrol.windows.net`) darán un error. Debería haber migrado todos los servicios y aplicaciones existentes a otras tecnologías antes de este período de tiempo.


## <a name="migration-strategies"></a>Estrategias de mitigación

En las secciones siguientes se describen recomendaciones detalladas para la migración de ACS a otras tecnologías de Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Clientes de los Servicios en la nube de Microsoft

Cada uno de los Servicios en la nube de Microsoft que aceptan tokens emitidos por ACS ahora admite al menos una forma alternativa de autenticación. El mecanismo correcto de autenticación varía para cada servicio, por lo que recomendamos que se remita a la documentación específica para obtener una orientación oficial. Para mayor comodidad, cada conjunto de documentación se proporciona aquí:

| Servicio | Guía |
| ------- | -------- |
| Azure Service Bus | [Migración a firmas de acceso compartido](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Relay | [Migración a firmas de acceso compartido](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Caché de Azure | [Migración a Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure Data Market | [Migración a Cognitive Services APIs](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Migración a Azure Logic Apps](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migración a la autenticación de Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Actualización del agente de Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>Aplicaciones web que usan autenticación pasiva

Para las aplicaciones web que usan ACS para la autenticación de usuarios, ACS proporcionaba las siguientes características y funcionalidades a los desarrolladores y arquitectos de aplicaciones web:

- Integración profunda con Windows Identity Foundation (WIF).
- Federación con Google, Facebook, Yahoo, cuenta Microsoft (Live ID), Azure Active Directory y AD FS.
- Compatibilidad con los protocolos de autenticación siguientes: OAuth 2.0 borrador 13, Ws-Trust y Ws-Federation.
- Compatibilidad con los siguientes formatos de token: token web JSON (JWT), SAML 1.1, SAML 2.0 y Token web simple (SWT).
- Una experiencia de detección de dominio de inicio, integrada en WIF, que permite a los usuarios seleccionar el tipo de cuenta que usan para iniciar sesión. Esta experiencia era totalmente personalizable y la proporcionaba la aplicación web.
- La transformación de token que permitía la variada personalización de las notificaciones recibidas por la aplicación web de ACS, incluido:
    - el paso de las notificaciones desde los proveedores de identidad
    - la incorporación de notificaciones personalizadas adicionales
    - la lógica de if-then simple para emitir notificaciones bajo ciertas condiciones

Por desgracia, no hay un servicio que proporcione todas estas funcionalidades equivalentes.  Debe evaluar qué funcionalidades de ACS necesita y, a continuación, elegir entre usar [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD), [ **Azure AD B2C** ](https://azure.microsoft.com/services/active-directory-b2c/) u otro servicio de autenticación en la nube.

#### <a name="migrating-to-azure-active-directory"></a>Migración a Azure Active Directory

Una forma de migración sería integrar las aplicaciones y servicios directamente con Azure Active Directory. Azure AD es el proveedor de identidad basado en la nube de las cuentas educativas y profesionales de Microsoft: es el proveedor de identidades para Office 365, Azure y otros. Proporciona funcionalidades de autenticación federada similares a ACS, pero no admite todas sus características. El ejemplo principal es la federación con proveedores de identidades sociales, como Facebook, Google y Yahoo. Si sus usuarios inician sesión con estos tipos de credenciales, Azure AD no es la mejor opción. Tampoco admite necesariamente los mismos protocolos exactos de autenticación que ACS; mientras que tanto ACS como Azure AD admiten OAuth, por ejemplo, existen diferencias sutiles entre cada implementación que exigen modificar el código como parte de la migración.

Sin embargo, Azure AD proporciona varias ventajas potenciales a los clientes de ACS. De forma nativa, es compatible con las cuentas educativas y profesionales de Microsoft hospedadas en la nube, que normalmente son usadas por clientes de ACS.  Un inquilino de Azure AD también se puede federar en una o varias instancias de Active Directory local a través de ADFS, lo que permite a la aplicación autenticar tanto a los usuarios en la nube como a los que se hospedan de forma local.  También admite el protocolo Ws-Federation, con el que la integración con una aplicación web mediante Windows Identity Foundation (WIF) resulta bastante sencilla.

En la tabla siguiente se comparan las características de ACS pertinentes para las aplicaciones web con las que están disponibles en Azure AD. En líneas generales, **Azure Active Directory probablemente no sea la opción adecuada para la migración, si solo permite a los usuarios iniciar sesión con sus cuentas educativas y profesionales de Microsoft**.

| Capacidad | Compatibilidad de ACS | Soporte técnico de Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipos de cuentas** | | |
| Cuentas educativas y profesionales de Microsoft | Compatible | Compatible |
| Cuentas de Windows Server Active Directory y ADFS | Compatible a través de la federación con un inquilino de Azure AD <br /> Compatible a través de la federación directa con ADFS | Solo es compatible a través de la federación con un inquilino de Azure AD | 
| Cuentas desde otros sistemas de administración de identidades empresariales | Es posible a través de la federación con un inquilino de Azure AD <br />Compatible a través de la federación directa | Es posible a través de la federación con un inquilino de Azure AD |
| Cuentas de Microsoft para uso personal (Windows Live ID) | Compatible | Compatible a través del protocolo v2.0 OAuth de Azure AD, pero no a través de ningún otro protocolo. | 
| Cuentas de Facebook, Google, Yahoo | Compatible | No se admite ningún tipo. |
| **Compatibilidad con el SDK y protocolos** | | |
| Windows Identity Foundation (WIF) | Compatible | Compatible, pero con instrucciones limitadas disponibles. |
| WS-Federation | Compatible | Compatible |
| OAuth 2.0 | Soporte para Draft 13 | Compatibilidad con RFC 6749, la especificación más moderna. |
| WS-Trust | Compatible | No compatible |
| **Formatos de tokens** | | |
| Json Web Tokens (JWT) | Compatible en versión beta | Compatible |
| Tokens SAML 1.1 | Compatible | Compatible |
| Tokens SAML 2.0 | Compatible | Compatible |
| Simple Web Tokens (SWT) | Compatible | No compatible |
| **Personalizaciones** | | |
| UI de selección de cuenta o detección de dominio de inicio personalizable | Código descargable que se puede incorporar en aplicaciones | No compatible |
| Carga de certificados de firma de tokens personalizados | Compatible | Compatible |
| Personalización de notificaciones en tokens | Paso a través de notificaciones de entrada desde proveedores de identidad<br />Obtención de token de acceso del proveedor de identidades como una notificación<br />Emisión de notificaciones de salida basadas en valores de las notificaciones de entrada<br />Emisión de notificaciones de salida con valores constantes | No se puede pasar a través notificaciones desde proveedores de identidades federados<br />No se puede obtener el token de acceso del proveedor de identidades como una notificación<br />No se puede emitir notificaciones de salida basadas en valores de notificaciones de entrada<br />Se puede emitir notificaciones de salida con valores constantes<br />Se puede emitir notificaciones de salida en función de las propiedades de los usuarios que se sincronizan con Azure AD |
| 
            **Automation** | | |
| Automatización de las tareas de configuración y administración | Compatible a través del servicio de administración de ACS | Compatible a través de Microsoft Graph y API Graph de Azure AD. |

Si decide que Azure AD es la forma adecuada para sus aplicaciones y servicios, debe tener en cuenta dos maneras de integrar con ella su aplicación.

Para utilizar Ws-Federation/WIF a fin de integrarla con Azure AD, se recomienda seguir [el enfoque descrito en este artículo](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). El artículo se refiere a la configuración de Azure AD para el inicio de sesión único basado en SAML, pero funciona también para configurar Ws-Federation. Seguir este enfoque requiere una licencia de Azure AD Premium, pero tiene dos ventajas:

- Obtiene toda la flexibilidad de la personalización de tokens de Azure AD. Esto le permite personalizar las notificaciones emitidas por Azure AD para que coincidan con las emitidas por ACS, en especial, la notificación del identificador del nombre o del identificador de usuario. Debe asegurarse de que los identificadores de usuario emitidos por Azure AD coinciden con los emitidos por ACS, por lo que sigue recibiendo identificadores de usuario coherentes para los usuarios incluso después de cambiar las tecnologías.
- Puede configurar un token de firma de certificado específico para su aplicación, cuya duración controle.

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Continuar con este enfoque requiere una licencia Premium de Azure AD. Si es cliente de ACS y requiere una licencia premium para la configuración del inicio de sesión único para una aplicación, contacte con nosotros y le proporcionaremos licencias de desarrollador.

Una solución alternativa es seguir [este ejemplo de código](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), que ofrece instrucciones ligeramente diferentes acerca de cómo configurar Ws-Federation. Este ejemplo de código no usa WIF, sino el middleware OWIN de ASP.NET 4.5. Sin embargo, las instrucciones para el registro de la aplicación son válidas para las aplicaciones que usan WIF y no requieren una licencia Premium de Azure AD.  Si elige esta solución, debe entender la [sustitución de claves de firma de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Esta solución usa la clave de firma global de Azure AD para emitir tokens. De forma predeterminada, WIF no actualiza automáticamente las claves de firma. Cuando Azure AD rota sus claves de firma globales, la implementación de WIF debe estar preparada para aceptar los cambios.

Si puede realizar la integración con Azure AD a través de los protocolos de OpenID Connect o OAuth, se recomienda hacerlo.  Tenemos una extensa documentación y guías sobre cómo integrar Azure AD en una aplicación web disponible en nuestra [Guía del desarrollador de Azure AD](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Migración a B2C de Azure AD

La otra forma de migración que hay que tener en cuenta es B2C de Azure AD.  B2C es un servicio de autenticación en la nube que, de modo similar a ACS, permite a los desarrolladores externalizar la lógica de administración de identidades y la autenticación a un servicio en la nube.  Es un servicio de pago (con niveles premium y gratuito) diseñado para las aplicaciones orientadas al cliente que pueden tener hasta millones de usuarios activos.

Al igual que ACS, una de las características más atractivas de B2C es que admite muchos tipos diferentes de cuentas. Con B2C, puede permitir que los usuarios inicien sesión con sus cuentas de Facebook, Google, Microsoft, LinkedIn, GitHub, Yahoo y mucho más. Además, B2C admite "cuentas locales" o nombres de usuario y contraseñas que los usuarios crean específicamente para la aplicación. B2C de Azure AD también proporciona una amplia extensibilidad que puede usar para personalizar los flujos de inicio de sesión. Sin embargo, no admite la gama de protocolos de autenticación y formatos de token que los clientes de ACS pueden requerir. Tampoco se puede usar para obtener los tokens y consultar información adicional sobre el usuario del proveedor de identidad, Microsoft o de otro modo.

En la tabla siguiente se comparan las características de ACS pertinentes para las aplicaciones web con las que están disponibles en B2C de Azure AD. En general, **B2C de Azure AD es, probablemente, la elección correcta para la migración si su aplicación está orientada hacia el consumidor, o si admite muchos tipos diferentes de cuentas.**

| Capacidad | Compatibilidad de ACS | Compatibilidad de B2C de Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipos de cuentas** | | |
| Cuentas educativas y profesionales de Microsoft | Compatible | Compatible a través de directivas personalizadas  |
| Cuentas de Windows Server Active Directory y ADFS | Compatible a través de la federación directa con ADFS | Compatible a través de federación de SAML mediante directivas personalizadas |
| Cuentas desde otros sistemas de administración de identidades empresariales | Admite a través de la federación directa con Ws-Federation | Compatible a través de federación de SAML mediante directivas personalizadas |
| Cuentas de Microsoft para uso personal (Windows Live ID) | Compatible | Compatible | 
| Cuentas de Facebook, Google, Yahoo | Compatible | Facebook y Google se admiten de forma nativa, Yahoo se admite a través de la federación de OpenID Connect mediante directivas personalizadas |
| **Compatibilidad con el SDK y protocolos** | | |
| Windows Identity Foundation (WIF) | Compatible | No compatible. |
| WS-Federation | Compatible | No compatible. |
| OAuth 2.0 | Soporte para Draft 13 | Compatibilidad con RFC 6749, la especificación más moderna. |
| WS-Trust | Compatible | No compatible. |
| **Formatos de tokens** | | |
| Json Web Tokens (JWT) | Compatible en versión beta | Compatible |
| Tokens SAML 1.1 | Compatible | No compatible |
| Tokens SAML 2.0 | Compatible | No compatible |
| Simple Web Tokens (SWT) | Compatible | No compatible |
| **Personalizaciones** | | |
| UI de selección de cuenta o detección de dominio de inicio personalizable | Código descargable que se puede incorporar en las aplicaciones | Interfaz de usuario totalmente personalizable a través de CSS personalizadas. |
| Carga de certificados de firma de tokens personalizados | Compatible | Las claves de firma personalizadas, no los certificados, se admiten a través de directivas personalizadas. |
| Personalización de notificaciones en tokens | Paso a través de notificaciones de entrada desde proveedores de identidad<br />Obtención de token de acceso del proveedor de identidades como una notificación<br />Emisión de notificaciones de salida basadas en valores de las notificaciones de entrada<br />Emisión de notificaciones de salida con valores constantes | Puede pasar las notificaciones de proveedores de identidad. Se requieren directivas personalizadas para algunas notificaciones.<br />No se puede obtener el token de acceso del proveedor de identidades como una notificación<br />No se pueden emitir notificaciones de salida según los valores de las notificaciones de entrada a través de directivas personalizadas<br />Se pueden emitir notificaciones de salida con valores constantes a través de directivas personalizadas |
| 
            **Automation** | | |
| Automatización de las tareas de configuración y administración | Compatible a través del servicio de administración de ACS | Creación de usuarios con permiso a través de API Graph de Azure AD. No se pueden crear mediante programación las directivas, las aplicaciones ni los inquilinos de B2C. |

Si decide que B2C de Azure AD es la forma adecuada para sus aplicaciones y servicios, debe tener comenzar con los recursos siguientes:

- [Documentación de B2C de Azure AD](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Directivas personalizadas de B2C de Azure AD](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Pecios de B2C de Azure AD](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>Otras opciones de migración

Si ni Azure AD ni B2C de Azure AD satisfacen las necesidades de sus aplicaciones web, póngase en contacto con nosotros; podemos ayudarle a identificar la mejor forma de migrar.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>Servicios web con la autenticación activa

Para los servicios web protegidos con tokens y emitidos por ACS, ACS proporciona las siguientes características y funcionalidades:

- Puede registrar una o varias **identidades de servicio** en el espacio de nombres de ACS que puede usar para solicitar tokens.
- Compatibilidad con los protocolos OAuth WRAP y OAuth 2.0 Borrador 13 para solicitar tokens, mediante los siguientes tipos de credenciales:
    - Una contraseña simple creada para la identidad del servicio
    - Un SWT firmado mediante una clave simétrica o un certificado X509
    - Un token SAML emitido por un proveedor de identidad de confianza (normalmente una instancia de AD FS)
- Compatibilidad con los siguientes formatos de token: token web JSON (JWT), SAML 1.1, SAML 2.0 y Token web simple (SWT).
- Reglas simples de transformación de token

Las identidades de servicio de ACS se suelen usar para implementar la autenticación al estilo de servidor a servidor (S2S).  

#### <a name="migrating-to-azure-active-directory"></a>Migración a Azure Active Directory

Nuestra recomendación para este tipo de flujo de autenticación es migrar a [ **Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD). Azure AD es el proveedor de identidad basado en la nube de las cuentas educativas y profesionales de Microsoft: es el proveedor de identidades para Office 365, Azure y otros. Pero también se puede utilizar para la autenticación de un servidor a otro mediante la implementación de Azure AD de la concesión de credenciales de cliente OAuth.  En la tabla siguiente se comparan las funcionalidades de ACS en la autenticación de servidores con las disponibles en Azure AD.

| Capacidad | Compatibilidad de ACS | Soporte técnico de Azure AD |
| ---------- | ----------- | ---------------- |
| Procedimiento para registrar un servicio web | Cree un usuario de confianza en el portal de administración de ACS. | Cree una aplicación web de Azure AD en Azure Portal. |
| Procedimiento de registro de un cliente | Cree una identidad de servicio en el portal de administración de ACS. | Cree otra aplicación web de Azure AD en Azure Portal. |
| Protocolo usado | Protocolo WRAP de OAuth<br />Concesión de credenciales del cliente de OAuth 2.0 Borrador 13 | Concesión de credenciales del cliente de OAuth 2.0 |
| Métodos de autenticación del cliente | Contraseña simple<br />JWT firmado<br />Token SAML de IDP federado | Contraseña simple<br />JWT firmado |
| Formatos de tokens | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | Solo JWT |
| Transformación de token | Incorporación de notificaciones personalizadas<br />Lógica de emisión de notificaciones if-then simple | Incorporación de notificaciones personalizadas | 
| Automatización de las tareas de configuración y administración | Compatible a través del servicio de administración de ACS | Compatible a través de Microsoft Graph y API Graph de Azure AD. |

Para obtener información sobre escenarios de implementación entre servidores, consulte los siguientes recursos:

- [Service-to-Service section of the Azure AD developer guide](https://aka.ms/aaddev) (Sección Servicio a servicio de la Guía del desarrollador de Azure AD).
- [Daemon code sample using simple password client credentials](https://github.com/Azure-Samples/active-directory-dotnet-daemon) (Ejemplo de código de demonio que usa credenciales de cliente con contraseñas simples)
- [Daemon code sample using certificate client credentials](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) (Ejemplo de código de demonio que usa credenciales de cliente con certificados)

#### <a name="other-migration-options"></a>Otras opciones de migración

Si Azure AD no satisface las necesidades de su servicio web, deje un comentario y le ayudaremos a identificar el mejor plan para su caso concreto.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>Preguntas, problemas y comentarios

Somos conscientes de que muchos clientes de ACS no encontrarán una forma de migración clara después de leer este artículo, y puede que necesiten asistencia o instrucciones para determinar el plan apropiado. Si desea analizar los escenarios de migración y otras cuestiones, deje un comentario en esta página.
