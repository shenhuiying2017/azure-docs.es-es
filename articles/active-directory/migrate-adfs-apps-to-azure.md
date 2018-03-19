---
title: "Migración de aplicaciones locales de AD FS a Azure. | Microsoft Docs"
description: "Este documento tiene como finalidad ayudar a las organizaciones a comprender cómo migrar aplicaciones locales a Azure AD. Se centra en las aplicaciones SaaS federadas."
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2018
ms.author: billmath
ms.openlocfilehash: f1256e2fefe1ad3d37403f37804888ab9dcf6e99
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>Migración de aplicaciones locales de AD FS a Azure 

Este documento tiene como finalidad ayudar a las organizaciones a comprender cómo migrar aplicaciones locales a Azure AD.  Se centra en las aplicaciones SaaS federadas.  No se incluyen instrucciones paso a paso.  Se proporciona una guía conceptual para ayudarle en la migración y que pueda comprender como convertir las configuraciones locales a Azure AD. También se describen los requisitos de los escenarios más comunes.

## <a name="introduction"></a>Introducción

Si tiene un directorio local que contiene cuentas de usuario, lo más probable es que tenga al menos una o dos aplicaciones.  Y estas aplicaciones están configuradas para que los usuarios accedan mediante inicio de sesión con esas identidades.

Si su organización es como la mayoría de las organizaciones, es probable que en algún momento del camino adopte aplicaciones e identidades en la nube.  Quizás ya trabaja con Office 365 y Azure AD Connect.  Puede que haya configurado aplicaciones SaaS basadas en la nube para algunas cargas de trabajo principales, pero no todas.  

Muchas organizaciones tienen aplicaciones de línea de negocio (LOB) SaaS o personalizadas que están federadas directamente con un servicio de inicio de sesión local como Servicios de federación de Active Directory (AD FS), además de aplicaciones de Office 365 y Azure AD.  En esta guía de migración se describe por qué y cómo migrar aplicaciones locales a Azure AD.

>[!NOTE]
>Se proporciona información detallada sobre la configuración y la migración de aplicaciones SaaS, con información general sobre las aplicaciones de LoB personalizadas.  En un futuro se podrá disponer de instrucciones detalladas sobre las aplicaciones de LoB personalizadas.

Figura 1: Aplicaciones conectadas directamente en el entorno local ![locales](media/migrate-adfs-apps-to-azure/migrate1.png)

Figura 2: Aplicaciones federadas mediante Azure AD ![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>¿Por qué migrar aplicaciones a Azure AD?

En las organizaciones que ya usan AD FS, Ping u otro proveedor de autenticación local, migrar las aplicaciones a Azure AD permite las siguientes ventajas:

**Acceso más seguro**
- Configure controles de acceso por aplicaciones pormenorizados, como Multi-Factor Authentication (MFA), mediante el [acceso condicional de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).  Las directivas pueden aplicarse a aplicaciones SaaS y personalizadas de la misma manera que lo podría estar haciendo actualmente con Office 365
- Para detectar amenazas y proteger el inicio de sesión basado en el aprendizaje automático y la heurística que identifican el tráfico de riesgo, aproveche las funcionalidades integradas y en constante evolución de Azure AD con [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection).

**Colaboración B2B de Azure AD**
- Una vez que el inicio de sesión en aplicaciones SaaS se basa en Azure AD, puede conceder a los asociados acceso a los recursos en la nube con [colaboración B2B de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

**Experiencia de administración más sencilla y funcionalidades adicionales de Azure AD**
- Azure AD como proveedor de identidades para aplicaciones SaaS admite funcionalidades adicionales, como certificados de firma de tokens por aplicación, [fechas de expiración de certificados configurables](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs) y [aprovisionamiento automatizado](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) de cuentas de usuario (en aplicaciones clave de la Galería) basadas en identidades de Azure AD.

**Mantener las ventajas de un proveedor de identidades local**
- Como se obtienen las ventajas de Azure AD, es posible seguir usando la solución local para la autenticación, de forma que se conservan ventajas como las soluciones locales de Multi-Factor Authentication (MFA) y auditoría. 

**Impulsar la retirada del proveedor de identidades local**
- Para las organizaciones que buscan retirar el producto de autenticación local, migrar las aplicaciones a Azure AD permite una transición fácil al tener ya hecho parte del trabajo. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Asignación de tipos de aplicaciones locales a tipos de aplicaciones de Azure AD
La mayoría de las aplicaciones entran en unas cuantas categorías basadas en el tipo de inicio de sesión que usan.  Estas categorías determinan cómo se representa la aplicación en Azure AD.

En resumen, las aplicaciones de SAML 2.0 se pueden integrar con Azure AD a través de la galería de aplicaciones de Azure AD o como aplicaciones que no son de la galería.  Las aplicaciones que usan OAuth 2.0 u OpenID Connect se pueden integrar con Azure AD de forma parecida a los "registros de aplicación".  Siga leyendo para más información.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Aplicaciones SaaS federadas frente a aplicaciones de LoB personalizadas
Las aplicaciones federadas incluyen aplicaciones que se incluyen en las categorías enumeradas.

- Aplicaciones SaaS 
    - Si los usuarios inician sesión en aplicaciones SaaS como Salesforce, ServiceNow o Workday, y se va a realizar la integración con un proveedor de identidades local, como AD FS o Ping, se usará el inicio de sesión federado para aplicaciones SaaS.
    - Por lo general, las aplicaciones usan el protocolo SAML 2.0 para el inicio de sesión federado.
    - Las aplicaciones que entran en esta categoría se pueden integrar con Azure AD como aplicaciones de empresa, bien desde la galería o como aplicaciones que no son de la galería.
- Aplicaciones de LoB personalizadas
    - Son aplicaciones que no son SaaS, que la organización desarrolla internamente o que están disponibles como un producto empaquetado estándar que se instala en el centro de datos.  Se incluyen las aplicaciones de SharePoint y las aplicaciones compiladas en Windows Identity Foundation (WIF).
    - Las aplicaciones pueden usar SAML 2.0, WS-Federation, OAuth u OpenID Connect para el inicio de sesión federado.
    - Las aplicaciones personalizadas que usan Oauth 2.0, OpenID Connect o WS-Federation se pueden integrar con Azure AD como registros de aplicaciones, y las aplicaciones personalizadas que usan SAML 2.0 o WS-Federation se pueden integrar como aplicaciones que no son de la galería dentro de aplicaciones empresariales.

### <a name="non-federated-apps"></a>Aplicaciones no federadas
Además, es posible integrar aplicaciones no federadas con Azure AD mediante el proxy de aplicación de Azure AD y las funcionalidades relacionadas.  Para más información sobre las funcionalidades, siga los vínculos:
- Aplicaciones que usan Windows Integrated Auth (WIA) directamente con Active Directory
    - Estas aplicaciones se pueden integrar con Azure AD mediante el [proxy de aplicación de Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).
- Aplicaciones que se integran con el proveedor de inicio de sesión único mediante un agente y que usan encabezados para la autorización
    - Las aplicaciones locales que usan un agente instalado para el inicio de sesión y la autorización basada en encabezados se pueden configurar para el inicio de sesión basado en Azure AD mediante Azure AD Application Proxy con [PingAccess for AzureAD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/)

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Conversión de aplicaciones federadas locales a Azure AD 
Afortunadamente, AD FS y Azure AD funcionan de forma similar, de modo que los conceptos de configuración de direcciones URL e identificadores de confianza de inicio y cierre de sesión se aplican en ambos casos.  Sin embargo, hay algunas pequeñas diferencias que es necesario conocer para realizar la transición.

En la tabla, hemos relacionado varias de las ideas principales que comparten las aplicaciones de AD FS, Azure AD y SaaS para ayudarle en la conversión. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Representación de la aplicación en Azure AD o AD FS
La migración comienza con la evaluación de cómo está configurada la aplicación en el entorno local y la asignación de esa configuración a Azure AD.  La siguiente es una asignación de los elementos de configuración de usuario de confianza de AD FS a los elementos de Azure AD correspondientes.  
- Término de AD FS: usuario de confianza o relación de confianza para usuario autenticado
- Términos de Azure AD: aplicación empresarial o registro de aplicación (en función del tipo de aplicación)

|Elemento configuración de aplicación|DESCRIPCIÓN|Dentro de la configuración de AD FS|Ubicación correspondiente en la configuración de Azure AD|Elemento de token de SAML|
|-----|-----|-----|-----|-----|
|URL de inicio de sesión de aplicación|URL de la página de inicio de sesión de esta aplicación. Aquí es donde el usuario iría a iniciar sesión en la aplicación en un flujo de SAML iniciado por el SP.|N/D|En Azure AD, la dirección URL de inicio de sesión se configura en Azure Portal, en las propiedades de inicio de sesión único de la aplicación, como dirección URL de inicio de sesión.</br></br>(Puede que tenga que hacer clic en Mostrar configuración avanzada de URL para ver la dirección URL de inicio de sesión).||
|Dirección URL de respuesta de la aplicación|Dirección URL de la aplicación desde la perspectiva del IdP.  Aquí es donde el usuario y el token se envían una vez que el usuario ha iniciado sesión en IDP.</br></br>  Esto se conoce también como el punto de conexión del consumidor de aserción de SAML.|Se encuentra en la relación de confianza para usuario autenticado de AD FS de la aplicación.  Haga clic con el botón derecho en el usuario de confianza, elija "Propiedades" y luego la pestaña "Puntos de conexión".|En Azure AD, la dirección URL de respuesta se configura en Azure Portal, en las propiedades de inicio de sesión único de la aplicación, como dirección URL de respuesta.</br></br>(Puede que tenga que hacer clic en Mostrar configuración avanzada de URL para ver la dirección URL de repuesta).|Se asigna al elemento de destino en el token SAML.</br></br>  Valor de ejemplo: https://contoso.my.salesforce.com|
|Dirección URL de cierre de sesión de la aplicación|La dirección URL a la que se envían solicitudes de "limpieza de cierre de sesión" cuando un usuario cierra la sesión en una aplicación, con el fin de cerrar la sesión de todas las demás aplicaciones en las que el IDP ha iniciado la sesión del usuario.|Se encuentra en Administración de AD FS, en Relaciones de confianza de usuario autenticado.  Haga clic con el botón derecho en el RP, elija "Propiedades" y haga clic en la pestaña "Puntos de conexión".|N/D: Azure AD no admite el "cierre de sesión único", lo que significa que se cierra la sesión de todas las aplicaciones.  Simplemente se cierra la sesión del usuario de Azure AD.|N/D|
|Identificador de la aplicación|El identificador de la aplicación desde la perspectiva del IdP. El valor de la dirección URL de inicio de sesión se usa a menudo (pero no siempre) como identificador.</br></br>  En ocasiones, la aplicación lo llama "identificador de entidad".|En AD FS, este es el identificador del usuario de confianza: haga clic con el botón derecho en la relación de confianza para usuario autenticado y elija "Propiedades" y luego haga clic en la pestaña "Identificadores".|En Azure AD, el identificador se configura en Azure Portal en las propiedades de inicio de sesión único de la aplicación como Identificador en Dominio y direcciones URL (puede que deba hacer clic en la casilla "Mostrar configuración avanzada de URL para ver la dirección URL de inicio de sesión").|Corresponde al elemento Audiencia del token SAML.|
|Metadatos de federación de la aplicación|Ubicación de los metadatos de federación de la aplicación.  La usa el IdP para actualizar automáticamente valores de configuración específicos, como puntos de conexión o certificados de cifrado.|La dirección URL de metadatos de federación de la aplicación se encuentra en la relación de confianza para usuario autenticado de AD FS de la aplicación.  Haga clic con el botón derecho en la relación de confianza, elija Propiedades y luego haga clic en la pestaña Supervisión.|N/D: Azure AD no admite el consumo directo de metadatos de federación de aplicaciones.|N/D|
|Identificador de usuario/NameID|Atributo que se usa para indicar de forma única la identidad de usuario de Azure AD o AD FS para la aplicación.</br></br>  Normalmente, el UPN o la dirección de correo electrónico del usuario.|En AD FS, se encuentra como una regla de notificación en el usuario de confianza.  En la mayoría de los casos, es la regla de notificación que emite una notificación con un tipo que termina con "nameidentifier".|En Azure AD, el identificador de usuario puede encontrarse en Azure Portal en las propiedades de inicio de sesión único de la aplicación en el encabezado Atributos de usuario.</br></br>De forma predeterminada, se usa el UPN.|Se comunica desde el IDP hasta la aplicación como el elemento "NameID" en el token SAML.|
|Otras notificaciones que se envían a la aplicación|Además del identificador de usuario/NameID, se envía normalmente otra información de notificación desde el IDP hasta la aplicación; por ejemplo, nombre, apellido, dirección de correo electrónico y los grupos de los que el usuario es miembro.|En AD FS, se encuentra como otras reglas de notificación en el usuario de confianza.|En Azure AD, se encuentra en Azure Portal en las propiedades de inicio de sesión único de la aplicación en el encabezado Atributos de usuario. Haga clic en Ver y edite todos los demás atributos de usuario.|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>Representación de Azure AD como un proveedor de identidades (IdP) en una aplicación SaaS
Como parte de la migración, la aplicación se debe configurar para que apunte a Azure AD (frente al proveedor de identidades local).  Esta sección se centra principalmente en aplicaciones SaaS que usan el protocolo SAML y no en las aplicaciones de LOB/personalizadas. Sin embargo, los conceptos descritos se extenderían a las aplicaciones de LOB o personalizadas. 

En un nivel alto, hay algunos elementos clave que hay que tener en cuenta al apuntar una aplicación SaaS a Azure AD:
- Emisor del proveedor de identidades: https&#58;//sts.windows.net/{tenant-id}/
- Dirección URL de inicio de sesión del proveedor de identidades: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- Dirección URL de cierre de sesión del proveedor de identidades: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Ubicación de metadatos de federación: https&#58;//login.windows.net/{tenant-id} <id. de inquilino>/federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

donde {tenant-id} se reemplaza por el identificador de inquilino, que se encuentra en Azure Portal en Azure Active Directory -> Propiedades como "Id. de directorio", y {application-id} se reemplaza por el identificador de la aplicación que se encuentra en las propiedades de la aplicación como "Id. de aplicación".

En la tabla se describen con más detalle los elementos de configuración principales del IdP para configurar los valores de inicio de sesión único en la aplicación y sus valores o ubicaciones dentro de AD FS y Azure AD.  El marco de la tabla de referencia es la aplicación SaaS, que necesita saber dónde enviar las solicitudes de autenticación y cómo validar los tokens recibidos.

|Elemento configuración|DESCRIPCIÓN|AD FS|Azure AD|
|---|---|---|---|
|URL de </br>inicio de sesión del </br>URL|URL de inicio de sesión del IdP desde la perspectiva de la aplicación (donde se redirige al usuario para el inicio de sesión).|La dirección URL de inicio de sesión de AD FS es el nombre del servicio de federación de AD FS seguido de "/adfs/ls/", por ejemplo: https&#58;//fs.contoso.com/adfs/ls/|El valor correspondiente para Azure AD sigue el patrón, donde {tenant-id} se reemplaza por el identificador de inquilino que se encuentra en Azure Portal en Azure Active Directory -> Propiedades como "Id. de directorio".</br></br>Para aplicaciones que usan el protocolo SAML-P: https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2 </br></br>Para aplicaciones que usan el protocolo WS-Federation https&#58;//login.microsoftonline.com</br>/{tenant-id}/wsfed|
|URL de </br>cierre de sesión del </br>URL|Dirección URL de cierre de sesión del IdP desde la perspectiva del usuario (donde se redirige al usuario cuando elige "cerrar sesión" en la aplicación).|En AD FS, la dirección URL de cierre de sesión es la misma que la dirección URL de inicio de sesión, o la misma dirección URL que lleva anexado "wa=wsignout1.0", por ejemplo: https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0|El valor correspondiente para Azure AD depende de si la aplicación puede admitir el cierre de sesión con SAML 2.0.</br></br>Si la aplicación admite el cierre de sesión con SAML, el valor sigue el patrón, donde el valor de {tenant-id} se reemplaza por el identificador de inquilino que se encuentra en Azure Portal en Azure Active Directory -> Propiedades como "Id. de directorio". https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2</br></br>Si la aplicación no admite el cierre de sesión con SAML: https&#58;//login.microsoftonline.com</br>/common /wsfederation?wa=wsignout1.0|
|Se necesita el cifrado de tokens </br>de firma </br>Certificate|Certificado cuya clave privada usa el IDP para firmar los tokens emitidos.  Comprueba que el token procede del mismo IDP que se ha configurado en la aplicación para confiar.|El certificado de firma de token de AD FS se encuentra en Administración de AD FS, en Certificados.|En Azure AD, el certificado de firma de token se puede encontrar en Azure Portal, en las propiedades de inicio de sesión único de la aplicación, bajo el encabezado Certificado de firma de SAML, donde puede descargar el certificado para cargarlo en la aplicación.</br></br>  Si la aplicación tiene más de un certificado, todos los certificados se pueden encontrar en el archivo XML de metadatos de federación.|
|Identificador/ </br>"emisor"|Identificador del IdP desde la perspectiva de la aplicación (a veces conocido como "Emisor" o "Id. de emisor")</br></br>En el token SAML, el valor aparece como el elemento "Emisor".|El identificador de AD FS es normalmente el identificador del servicio de federación en Administración de AD, bajo Servicio -> Modificar las propiedades del Servicio de federación.  Por ejemplo: http&#58;//fs.contoso.com/adfs/services/trust|El valor correspondiente para Azure AD sigue el patrón, donde el valor de {tenant-id} se reemplaza por el identificado de inquilino que se encuentra en Azure Portal en Azure Active Directory -> Propiedades como "Id. de directorio".  https&#58;//sts.windows.net/{tenant-id}/|
|URL de </br>Federación </br>Metadatos|Ubicación de los metadatos de federación disponibles públicamente del IDP.  (Algunas aplicaciones usan metadatos de federación como alternativa a la configuración de direcciones URL, identificadores y certificados de firma de token de forma individual por el administrador).|Busque la dirección URL de metadatos de federación de AD FS en Administración de AD FS en Servicio -> Puntos de conexión -> Metadatos -> Tipo: Metadatos de federación, por ejemplo: https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|El valor correspondiente para Azure AD sigue el patrón https&#58;//login.microsoftonline.com</br>/{TenantDomainName}/FederationMetadata/2007-06/</br>FederationMetadata.xml donde el valor de {TenantDomainName} se reemplaza por el nombre del inquilino con el formato "contoso.onmicrosoft.com" </br></br>[Más información](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata) sobre los metadatos de federación en Azure AD.

## <a name="migrating-saas-apps"></a>Migración de aplicaciones SaaS
La migración de aplicaciones SaaS desde AD FS u otro proveedor de identidades a Azure AD es actualmente un proceso manual. Para obtener instrucciones específicas de la aplicación, [consulte la lista de tutoriales sobre la integración de aplicaciones SaaS que se encuentra en la Galería](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

En los tutoriales de integración se supone que va a realizar una integración green field.  Hay unos cuantos conceptos clave específicos de la migración que debe conocer mientras planea, evalúa, configura y migra completamente las aplicaciones.  
- Si bien algunas aplicaciones se pueden migrar fácilmente, las aplicaciones con requisitos más complejos, como las notificaciones personalizadas, pueden requerir configuración adicional en Azure AD o Azure AD Connect
- En los escenarios más comunes, las aplicaciones solo requieren la notificación NameId y otras notificaciones de identificador de usuario comunes; para determinar si se necesitan notificaciones adicionales, examine qué notificaciones se emiten desde AD FS o el proveedor de identidades de terceros.
- Cuando haya determinado las notificaciones adicionales necesarias, debe asegurarse de que estén disponibles en Azure AD.  Deberá comprobar la configuración de sincronización de Azure AD Connect para asegurarse de que los atributos necesarios, por ejemplo samAccountName, se sincronizan con Azure AD.
- Cuando los atributos están disponibles en Azure AD, agregue reglas de emisión de notificaciones en Azure AD para incluir esos atributos como notificaciones en los tokens emitidos.  Se realiza dentro de las propiedades de inicio de sesión único de la aplicación en Azure AD.

### <a name="assessing-what-can-be-migrated"></a>Evaluación de lo que se puede migrar
Las aplicaciones de SAML 2.0 se pueden integrar con Azure AD a través de la galería de aplicaciones de Azure AD o como aplicaciones que no son de la galería.  

Hay algunas configuraciones que requieren pasos adicionales en Azure AD y otras que no se admiten actualmente.  Para determinar lo que se pueden mover, examine la configuración actual de cada una de las aplicaciones, en concreto la siguiente:
- Reglas de notificación configuradas (reglas de transformación de emisión)
- Formato y atributo de SAML NameID
- Versiones de token SAML emitidas
- Otras configuraciones, como las reglas de autorización de emisión o las directivas de control de acceso y las reglas de autenticación multifactor (autenticación adicional).

#### <a name="what-can-be-migrated-today"></a>Qué se puede migrar actualmente
Las aplicaciones que se pueden migrar fácilmente en la actualidad incluyen aplicaciones SAML 2.0 que usan el conjunto estándar de notificaciones y elementos de configuración.  Estas aplicaciones pueden constar de
- Nombre principal de usuario
- Dirección de correo electrónico
- Nombre propio
- Surname
- Atributo alternativo como SAML NameID, lo que incluye el atributo de correo de Azure AD, el prefijo de correo, el identificador de empleado, los atributos de extensión 1-15 o SamAccountName local (consulte [Edición de la notificación NameIdentifier)](./develop/active-directory-saml-claims-customization.md).
- Notificaciones personalizadas (consulte el documento [aquí](active-directory-claims-mapping.md) y [aquí](./develop/active-directory-saml-claims-customization.md) para más información sobre las asignaciones de notificaciones admitidas)

Además de las notificaciones y los elementos nameID personalizados, las configuraciones que requieren pasos adicionales en Azure AD como parte de la migración son:
- Reglas de MFA o de autorización personalizadas en AD FS (configuradas mediante la característica de [acceso condicional de Azure AD](active-directory-conditional-access-azure-portal.md))
- Se pueden configurar aplicaciones con varios puntos de conexión de SAML en Azure AD mediante PowerShell (esta funcionalidad no está disponible en el portal)
- Aplicaciones de WS-Federation como las de SharePoint que requieren que se configuren manualmente tokens de la versión SAML 1.1 mediante PowerShell

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>Aplicaciones/configuraciones no admitidas actualmente en Azure AD
Las aplicaciones que necesitan las siguientes funcionalidades no se pueden migrar actualmente.  Proporcione comentarios en la sección correspondiente si tiene aplicaciones que necesitan estas características.
- Funcionalidades de protocolo
    - Compatibilidad con el cierre de sesión único (SLO) de SAML de todas las aplicaciones que han iniciado sesión
    - Compatibilidad con el patrón ActAs de WS-Trust
    - Resolución de artefactos SAML 
    - Comprobación de la firma de solicitudes SAML firmadas (observe que se aceptan solicitudes firmadas, pero no se comprueba la firma)
 - Funcionalidades de token 
     - Cifrado de token SAML 
     - Token de la versión 1.1 de SAML en respuestas de protocolo SAML 
- Funcionalidades de notificaciones en token
    - Emisión de nombres de grupos locales como notificaciones
    - Notificaciones de almacenes distintos de Azure AD
    - Reglas de transformación de emisiones de notificaciones complejas (consulte este [documento](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) y este [documento](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) para más información sobre las asignaciones de notificaciones admitidas)
    - Emisión de extensiones de directorio como notificaciones
    - Especificación personalizada del formato NameID
    - Emisión de atributos de valor múltiple

>[!NOTE]
>Azure AD evoluciona constantemente para agregar funcionalidades adicionales en esta área. Mantenemos este documento actualizado de forma periódica. 

### <a name="configuring-azure-ad"></a>Configuración de Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Configuración del inicio de sesión único (SSO) para la aplicación SaaS

En Azure AD, la configuración del inicio de sesión de SAML que necesita la aplicación se realiza en las propiedades de inicio de sesión único de la aplicación en los atributos de usuario, como se muestra:

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- Haga clic en "View and edit all other user attribute" (Ver y editar todos los demás atributos de usuarios) para ver los atributos para enviar como notificaciones en el token de seguridad.

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- Haga clic en una fila de atributo específica para editarla o haga clic en "Agregar atributo" para agregar un nuevo atributo. 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Asignación de usuarios a la aplicación
Para que los usuarios de Azure AD puedan iniciar sesión en una aplicación SaaS dada, se les debe conceder acceso desde Azure AD.

Para asignar usuarios en el portal de Azure AD, vaya a la pantalla de la aplicación SaaS en el portal y luego haga clic en "Usuarios y grupos" en la barra lateral. Para agregar un usuario o un grupo, haga clic en "Agregar usuario" en la parte superior de la pantalla. 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png)Para comprobar el acceso, el usuario debe ver dicha aplicación SaaS en su [panel de acceso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) cuando inicia sesión, lo cual se puede encontrar en http://myapps.microsoft.com. En la imagen, al usuario se le ha asignado acceso correctamente a Salesforce y ServiceNow.

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>Configuración de la aplicación SaaS
El proceso de migración completa de federación local a Azure AD depende de si la aplicación SaaS con la que trabaja admite varios proveedores de identidades (IdP).  Las siguientes son algunas preguntas comunes respecto a la compatibilidad con varios IdP:

   **P: ¿Qué significa que una aplicación admita varios IdP?**
    
   R: las aplicaciones SaaS que admiten varios IdP le permiten escribir toda la información sobre el nuevo IdP (en nuestro caso, Azure AD) antes de confirmar el cambio de la experiencia de inicio de sesión.  Una vez que se realiza la configuración, puede cambiar la configuración de autenticación de la aplicación para que apunte a Azure AD.

   P: ¿Por qué importa que la aplicación SaaS admita varios IdP?

   R: si no se admiten varios IdP, el administrador tendrá que reservar un breve espacio de tiempo como interrupción por mantenimiento o servicio durante el que configurar Azure AD como el nuevo IdP de la aplicación. Durante esta interrupción, se debe notificar a los usuarios que no podrán iniciar sesión en sus cuentas.

   Si una aplicación admite varios IdP, la configuración de IdP adicionales puede realizarse con antelación, de modo que el administrador puede cambiar simplemente el IdP en la migración completa de Azure.

   Además, si la aplicación admite varios IdP y elije varios IdP para que administren simultáneamente la autenticación de inicio de sesión, al usuario se le da una opción de IdP para autenticarse en su página de inicio de sesión.

#### <a name="example-multiple-idp-support"></a>Ejemplo: compatibilidad con varios IdP
Por ejemplo, en Salesforce, la configuración del IDP puede encontrarse en Settings (Configuración) -> Company Settings (Configuración de empresa) -> My Domain (Mi dominio) -> Authentication Configuration (Configuración de autenticación).

![](media/migrate-adfs-apps-to-azure/migrate9.png)

Debido a la configuración creada anteriormente en Identity (Identidad) -> Single sign-on settings (Configuración de inicio de sesión), debe poder cambiar el IdP de la configuración de autenticación, por ejemplo, de AD FS a Azure AD. 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Opcional: configuración del aprovisionamiento de usuario en Azure AD
Opcionalmente, si quiere que Azure AD administre directamente el aprovisionamiento de usuarios para una aplicación SaaS dada, consulte este documento sobre la administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales en Azure AD.

## <a name="next-steps"></a>Pasos siguientes

- [Administración de aplicaciones con Azure Active Directory](active-directory-enable-sso-scenario.md)
- [Administración del acceso a aplicaciones](active-directory-managing-access-to-apps.md)
- [Azure AD Connect y la federación](active-directory-aadconnectfed-whatis.md)