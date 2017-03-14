---
title: "Consideraciones de seguridad al obtener acceso a aplicaciones de forma remota con el Proxy de aplicación de Azure AD | Microsoft Docs"
description: "Se tratan las consideraciones de seguridad al utilizar el Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5d741836f5defd5d9287b90e53e71aeea439a1df
ms.openlocfilehash: 81a7a57e6b025710660f7d55145ee286b71acf24
ms.lasthandoff: 03/01/2017


---

# <a name="security-considerations-for-accessing-apps-remotely-by-using-azure-ad-application-proxy"></a>Consideraciones de seguridad al obtener acceso a aplicaciones de forma remota con el Proxy de aplicación de Azure AD

>[!NOTE]
>La característica Proxy de aplicación solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

En este artículo se explica cómo el Proxy de aplicación de Azure Active Directory (Azure AD) proporciona un servicio seguro para la publicación y el acceso a las aplicaciones de forma remota.

El Proxy de aplicación de Azure AD ofrece las siguientes ventajas en materia de seguridad:

**Acceso autenticado:** las conexiones autenticadas son las únicas que pueden tener acceso a la red.

* El Proxy de aplicación de Azure AD se basa en el servicio de token de seguridad (STS) de Azure AD para toda la autenticación. En el caso de las aplicaciones publicadas con autenticación previa, no se permite el paso de ningún tráfico a través del servicio de Proxy de aplicación a su entorno sin un token STS válido.
* La autenticación previa, por su propia naturaleza, bloquea un número significativo de ataques anónimos, ya que las identidades autenticadas son las únicas que pueden tener acceso a la aplicación de back-end.

**Acceso condicional:** aplique controles de directiva más completos antes de que se establezcan las conexiones a la red.

* Con el acceso condicional, es posible definir aún más las restricciones sobre el tráfico que se permite que tenga acceso a las aplicaciones de back-end. Puede definir restricciones basadas en la ubicación, el nivel de autenticación y el perfil de riesgo de usuario.
* Esta característica permite establecer barreras adicionales para los atacantes. Para más información sobre el acceso condicional, consulte [Introducción al acceso condicional de Azure Active Directory](https://azure.microsoft.com/en-us/documentation/articles/active-directory-conditional-access-azuread-connected-apps).

**Terminación de tráfico:** todo el tráfico se termina en la nube.

* Como el Proxy de aplicación de Azure AD es un servidor proxy inverso, todo el tráfico que va a las aplicaciones de back-end se termina en el servicio. Se puede restablecer la sesión solo con el servidor back-end, lo que significa que los servidores back-end no se exponen al tráfico HTTP directo. Por ejemplo, puede mitigar más fácilmente los ataques dirigidos.

**Todo el acceso es de salida:** no es necesario abrir conexiones de entrada a la red corporativa.

* Los conectores de Azure AD mantienen las conexiones salientes al servicio de Proxy de aplicación de Azure AD, lo que significa que no es necesario abrir los puertos de firewall para las conexiones entrantes.
* Los enfoques tradicionales requerían una red perimetral (antiguamente conocida como *DMZ*, *zona desmilitarizada* y *subred filtrada*) y la apertura del acceso a las conexiones no autenticadas en el perímetro de la red. Como consecuencia de esto, se necesitaba una gran inversión adicional en productos de firewall de aplicaciones web (WAF) para analizar el tráfico y ofrecer una mayor protección al entorno. Con el Proxy de aplicación, puede evitar esta situación. Puede incluso considerar prescindir de la red perimetral, ya que todas las conexiones son de salida y van a través de un canal seguro.

**Análisis de seguridad e inteligencia automática basada en lenguaje:** consiga protección de seguridad avanzada.

* Azure AD Identity Protection con inteligencia controlada por aprendizaje automático con fuentes de datos basados en nuestra Unidad de crímenes digitales y el Centro de respuesta de seguridad de Microsoft. Juntos identificamos de manera proactiva las cuentas en peligro y ofrecemos protección en tiempo real frente a inicios de sesión de alto riesgo. Tomamos en consideración diversos factores, como el acceso desde dispositivos infectados y a través de redes anónimas, y desde ubicaciones atípicas y poco probables.
* Muchos de estos informes y eventos ya están disponibles a través de una API para la integración con los sistemas de información de seguridad y administración de eventos (SIEM).
* Para más información, consulte [Azure AD Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection).

**Acceso remoto como servicio:** no tiene que preocuparse sobre cómo mantener y aplicar revisiones a servidores locales.

* El Proxy de aplicación de Azure AD es un servicio de escala de Internet que es propiedad de Microsoft, por lo que puede estar seguro que siempre obtendrá las actualizaciones y revisiones de seguridad más recientes.
* El software al que no se aplican revisiones sigue siendo objeto de un gran número de ataques. Con nuestro modelo de servicio, ya no tiene que ocuparse de la ingrata tarea de la administración de los servidores perimetrales.

Los servicios de acceso remoto que se proporcionan con Azure AD funcionan según las instrucciones y normas que se describen en el [Centro de confianza de Azure](https://azure.microsoft.com/en-us/support/trust-center).

El diagrama siguiente muestra cómo Azure AD permite el acceso remoto seguro a sus aplicaciones locales.

 ![Diagrama de acceso remoto seguro a través del Proxy de aplicación de Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

>[!NOTE]
>Para mejorar la seguridad de las aplicaciones publicadas por el Proxy de aplicación de Azure AD, se impide que los robots de agentes de búsqueda indexen y archiven sus aplicaciones. Cada vez que un robot de agente de búsqueda intenta recuperar la configuración de robots de una aplicación publicada, el proxy de aplicación responde con un archivo llamado robots.txt que incluye el siguiente texto:
>
>_User-agent: *_  
>_Disallow: /_

## <a name="components-of-the-azure-ad-application-proxy-solution"></a>Componentes de la solución de Proxy de aplicación de Azure AD

El Proxy de aplicación de Azure AD consta de dos partes:

* El servicio basado en la nube: este servicio es donde se realizan las conexiones de usuario o cliente externo.
* El conector del Proxy de aplicación de Azure AD: un componente local, el conector escucha las solicitudes del servicio Proxy de aplicación de Azure AD y controla las conexiones con las aplicaciones internas. El servicio incluye la supervisión de elementos como la delegación limitada de Kerberos (KCD) para SSO.

Se establece un flujo entre el conector y el servicio de Proxy de aplicación cuando:

* El conector se configura en primer lugar.
* El conector extrae información de configuración del servicio de Proxy de aplicación, incluido el grupo de conectores al que pertenece cada conector.
* Un usuario tiene acceso a una aplicación publicada.

>[!NOTE]
>Todas las comunicaciones se producen a través de SSL, y siempre se originan en el conector del servicio de Proxy de aplicación. Este servicio solo es de salida.

El conector usa un certificado de cliente para autenticarse en el servicio de Proxy de aplicación para casi todas las llamadas. La única excepción para este proceso es el momento de la instalación inicial, donde se establece el certificado de cliente.

### <a name="installing-the-connector"></a>Instalación del conector

Los siguientes eventos de flujo ocurren cuando el conector se configura en primer lugar:

1. El registro del conector en el servicio se produce en el marco de la instalación del conector. Actualmente, se solicita a los usuarios que escriban sus credenciales de administrador de Azure AD. A continuación, se presenta el token obtenido al servicio de Proxy de aplicación de Azure AD.
2. El Proxy de aplicación evalúa el token para asegurarse de que el usuario es miembro del rol de administrador de empresa en el inquilino para el que se emitió el token. Si el usuario no es un miembro del rol de administrador, se finaliza el proceso.
3. El conector genera una solicitud de certificado de cliente y la pasa con el token al servicio de Proxy de aplicación que, a su vez, comprueba el token y firma la solicitud de certificado de cliente.
4. El conector usa el certificado de cliente para la comunicación posterior con el servicio de Proxy de aplicación.
5. El conector realiza una extracción inicial de los datos de configuración del sistema desde el servicio usando su certificado de cliente y ya está listo para aceptar solicitudes.

### <a name="updating-the-configuration-settings"></a>Actualización de los valores de configuración

Cada vez que el servicio de Proxy de aplicación actualiza los valores de configuración, tienen lugar los siguientes eventos de flujo:

1. El conector se conecta al punto de conexión de configuración en el servicio de Proxy de aplicación mediante su certificado de cliente.
2. Una vez que se valida el certificado de cliente, el servicio de Proxy de aplicación devuelve datos de configuración al conector; por ejemplo, el grupo de conectores al que debe pertenecer el conector.
3. El conector genera una nueva solicitud de certificado si el certificado actual tiene más de 30 días de antigüedad, lo cual permite actualizar de manera efectiva el certificado de cliente cada 30 días.

### <a name="accessing-published-applications"></a>Acceso a aplicaciones publicadas

Se producen los siguientes eventos de flujo cuando los usuarios tienen acceso a una aplicación publicada:

1. El servicio de Proxy de aplicación comprueba los valores de configuración de la aplicación. Si la aplicación está configurada para usar la autenticación previa con Azure AD, se redirige a los usuarios a STS de Azure AD para su autenticación. Este paso se omite si publica la aplicación mediante el acceso directo.

 a. Durante la autenticación con Azure AD, Proxy de aplicación comprueba los requisitos de directiva de acceso condicional para la aplicación específica. Este paso consiste en asegurarse de que el usuario se ha asignado a la aplicación. Si se requiere Multi-Factor Authentication (MFA), la secuencia de autenticación solicitará al usuario una autenticación de segundo factor.

 b. Una vez que hayan pasado todas las comprobaciones, el STS de Azure AD emite un token firmado para la aplicación y redirige al usuario al servicio de Proxy de aplicación.

 c. El Proxy de aplicación, a continuación, valida el token para asegurarse de que se ha emitido para la aplicación a la que el usuario solicitó acceso. También realiza otras comprobaciones como verificar que el token se firmó con Azure AD y que sigue dentro de la ventana válida.

 d. El Proxy de aplicación establece una cookie de autenticación cifrada (como una cookie no persistente) para indicar que se ha producido la autenticación para la aplicación. Esta cookie incluye una marca de tiempo de expiración basada en el token de Azure AD y otros datos, como el nombre de usuario en que se basa la autenticación. Esta cookie se cifra con una clave privada conocida solo por el servicio de Proxy de aplicación.

 e. El Proxy de aplicación redirige al usuario a la dirección URL solicitada originalmente.

 >[!NOTE]
 >Si se produce un error en cualquier parte de los pasos de la autenticación previa, se deniega la solicitud del usuario y este ve un mensaje que indica el origen del problema.
 >

2. El Proxy de aplicación, al recibir la solicitud del cliente, valida que se ha cumplido la condición de autenticación previa y que la cookie sigue siendo válida (según sea necesario). A continuación, realiza una solicitud en la cola adecuada para que la administre un conector local. 

 >[!NOTE]
 >Todas las solicitudes realizadas por el conector salen hacia el servicio de Proxy de aplicación. Los conectores mantienen abierta una conexión de salida hacia el Proxy de aplicación. Cuando llega una solicitud, el Proxy de aplicación la pone en cola en una de las conexiones abiertas para que el conector la recoja.

 * La solicitud incluye elementos de la aplicación, como los encabezados de la solicitud y los datos de la cookie cifrada, el usuario que realiza la solicitud y el identificador de solicitud. Sin embargo, la cookie de autenticación cifrada no se envía al conector.

3. El conector recibe la solicitud de la cola, en función de una conexión de salida de larga duración. En función de la solicitud, el Proxy de aplicación realizará una de las siguientes acciones:

 * El conector confirma si puede identificar la aplicación. Si no puede identificar la aplicación, el conector establece una conexión con el servicio de Proxy de aplicación para recopilar detalles acerca de la aplicación y la almacena en caché localmente.

 * Si la solicitud es una operación sencilla, como cuando no hay ningún dato dentro del cuerpo (por ejemplo, una solicitud *GET* de RESTful), el conector establece una conexión con el recurso interno de destino y espera una respuesta.

 * Si la solicitud tiene datos asociados en el cuerpo, como en una operación de *POST* de RESTful, el conector establece una conexión saliente mediante el certificado de cliente con la instancia del Proxy de aplicación. Esta conexión se establece para solicitar los datos y abrir una conexión a los recursos internos. Tras la recepción de la solicitud del conector, el servicio de Proxy de aplicación comienza a aceptar el contenido del usuario y reenvía los datos al conector. El conector, a su vez, reenvía los datos a los recursos internos.

4. Una vez completada la operación de solicitud y transmisión de todo el contenido al back-end, el conector espera una respuesta.

5. Después de recibir una respuesta, el conector establece una conexión saliente con el servicio de Proxy de aplicación para devolver los detalles del encabezado y empezar a transmitir los datos de devolución.

6. El Proxy de aplicación "transmite" los datos al usuario. Parte del procesamiento de los encabezados se puede producir en este momento, según se necesite y defina por la aplicación.

Si necesita ayuda con la comunicación desde una aplicación web de Azure por medio de un explorador cliente hacia un punto de conexión del Protocolo simple de acceso a objetos (SOAP) autenticado en Windows local, consulte este [blog de notas de campo de Azure](http://www.azurefieldnotes.com/2016/12/02/claims-to-windows-identity-translation-solutions-and-its-flaws-when-using-azure-ad-application-proxy).

## <a name="next-steps"></a>Pasos siguientes

[Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md)

