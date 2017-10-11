---
title: "Habilitación del inicio de sesión único entre aplicaciones en iOS mediante ADAL | Microsoft Docs"
description: "Cómo utilizar las características del SDK de ADAL para habilitar el inicio de sesión único entre las aplicaciones. "
services: active-directory
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 73b8ed7e6a153a0790f7eae9bd51bb2e554ae72e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Habilitación del inicio de sesión único entre aplicaciones en iOS mediante ADAL
Actualmente, los clientes esperan disfrutar de un inicio de sesión único que les permita escribir sus credenciales una sola vez y que esas credenciales se propaguen automáticamente entre las diferentes aplicaciones. La dificultad de escribir el nombre de usuario y contraseña en una pantalla pequeña, a menudo combinado con un factor adicional (2FA) como una llamada de teléfono o un código de mensaje de texto, provoca una rápida insatisfacción si el usuario tiene que hacer esto más de una vez para su producto.

Además, si aplica una plataforma de identidad que puedan usar otras aplicaciones, como cuentas Microsoft o una cuenta de trabajo de Office 365, los clientes esperan que las credenciales estén disponibles para todas sus aplicaciones, independientemente del proveedor.

La plataforma de Microsoft Identity, junto con los SDK correspondientes, se ocupa de este trabajo ingrato y le ofrece la posibilidad de satisfacer a sus clientes con el inicio de sesión único, ya sea dentro de su propio conjunto de aplicaciones o, como sucede con nuestra funcionalidad de agente y las aplicaciones de Authenticator, en todo el dispositivo.

Este tutorial le indicará cómo configurar nuestro SDK dentro de la aplicación para proporcionar esta ventaja a sus clientes.

Este tutorial se aplica a:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Acceso condicional de Azure Active Directory

En el documento anterior se considera que tiene conocimientos acerca de cómo [aprovisionar aplicaciones en el portal heredado para Azure Active Directory](active-directory-how-to-integrate.md) y que ha integrado su aplicación con el [SDK de iOS de Microsoft Identity](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Conceptos de inicio de sesión único en la plataforma de Microsoft Identity
### <a name="microsoft-identity-brokers"></a>Agentes de Microsoft Identity
Microsoft proporciona aplicaciones para todas las plataformas móviles que permiten el traspaso de credenciales entre aplicaciones de distintos proveedores, y habilita características mejoradas especiales que requieren un único lugar seguro desde el que validar las credenciales. A estas aplicaciones las llamamos **agentes**. En iOS y Android, estos agentes se consiguen a través de aplicaciones descargables que los clientes instalan de forma independiente o bien se insertan en el dispositivo gracias a una empresa que administra el dispositivo de manera total o parcial para sus empleados. Gracias a estos agentes, es posible administrar la seguridad de solo unas aplicaciones o bien del dispositivo en su totalidad, en función de lo que decidan los administradores de TI. En Windows, esta funcionalidad se proporciona mediante un selector de cuentas integrado en el sistema operativo, que se conoce técnicamente como agente de autenticación web.

Para más información sobre el uso que hacemos de estos agentes y la percepción que tendrán de ellos sus clientes durante el proceso de inicio de sesión para la plataforma Microsoft Identity, siga leyendo.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Patrones para iniciar sesión en dispositivos móviles
El acceso a credenciales en los dispositivos sigue dos patrones básicos para la plataforma Microsoft Identity:

* Inicios de sesión no asistidos por agente
* Inicios de sesión asistidos por agente

#### <a name="non-broker-assisted-logins"></a>Inicios de sesión no asistidos por agente
Los inicios de sesión no asistidos por agente son experiencias de inicio de sesión que ocurren en línea con la aplicación y usan el almacenamiento local en el dispositivo para esa aplicación. Este almacenamiento puede compartirse entre las aplicaciones, pero las credenciales están estrechamente vinculadas a la aplicación o al conjunto de aplicaciones que usan esa credencial. Probablemente haya experimentado esto en muchas aplicaciones móviles en las que escribe un nombre de usuario y una contraseña en la propia aplicación.

Estos inicios de sesión tienen las siguientes ventajas:

* La experiencia de usuario se desarrolla por completo dentro de la aplicación.
* Las credenciales se pueden compartir entre aplicaciones que estén firmadas por el mismo certificado, proporcionando una experiencia de inicio de sesión único para el conjunto de aplicaciones.
* Se proporciona el control de la experiencia de inicio de sesión a la aplicación antes y después del inicio de sesión.

Estos inicios de sesión tienen las siguientes desventajas:

* El usuario no puede experimentar el inicio de sesión único entre todas las aplicaciones que usan una identidad de Microsoft Identity, solo en aquellas identidades de Microsoft Identity que su aplicación ha configurado.
* La aplicación no se puede usar con las características empresariales más avanzadas, como el acceso condicional, ni utilizar el conjunto de aplicaciones de InTune.
* La aplicación no admite la autenticación basada en certificados para usuarios empresariales.

A continuación se representa el funcionamiento de los SDK de Microsoft Identity con el almacenamiento compartido de sus aplicaciones para habilitar el inicio de sesión único:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAK SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Inicios de sesión asistidos por agente
Los inicios de sesión asistidos por agente son experiencias de inicio de sesión que se producen dentro de la aplicación del agente y usan el almacenamiento y la seguridad del agente para compartir las credenciales entre todas las aplicaciones del dispositivo que se aplican a la plataforma Microsoft Identity. Esto significa que las aplicaciones dependen del agente para que los usuarios inicien sesión. En iOS y Android, estos agentes se consiguen a través de aplicaciones descargables que los clientes instalan de forma independiente o bien se insertan en el dispositivo gracias a una empresa que administra el dispositivo para sus usuarios. Un ejemplo de este tipo de aplicación es la aplicación Microsoft Authenticator en iOS. En Windows, esta funcionalidad se proporciona mediante un selector de cuentas integrado en el sistema operativo, que se conoce técnicamente como agente de autenticación web.
La experiencia varía según la plataforma y a veces puede ser problemática para los usuarios si no se administra correctamente. Quizá este modelo le resulte más familiar si tiene instalada la aplicación Facebook y usa Facebook Connect desde otra aplicación. La plataforma Microsoft Identity utiliza este mismo modelo.

En el caso de iOS, esto da lugar a una animación de "transición" en la que la aplicación se envía a un segundo plano y las aplicaciones de Microsoft Authenticator vienen al primer plano para que el usuario seleccione la cuenta en la que quiere iniciar sesión.  

En el caso de Android y Windows, el selector de cuentas se muestra sobre la aplicación, lo cual interrumpe menos la experiencia del usuario.

#### <a name="how-the-broker-gets-invoked"></a>¿Cómo se invoca el agente?
Si se instala un agente compatible en el dispositivo, como la aplicación Microsoft Authenticator, los SDK de Microsoft Identity se ocuparán automáticamente de la tarea de invocar al agente cuando un usuario indique que quiere iniciar sesión con alguna de las cuentas de la plataforma Microsoft Identity. Esta cuenta podría tratarse de una cuenta personal de Microsoft, una cuenta profesional o educativa o una cuenta que especifique y hospede en Azure mediante nuestros productos B2C y B2B. 

 #### <a name="how-we-ensure-the-application-is-valid"></a>Cómo se garantiza que la aplicación es válida
 
 La necesidad de asegurar la identidad de una llamada de la aplicación al agente es fundamental para la seguridad que proporcionamos en los inicios de sesión asistidos por agente. Ni IOS ni Android exigen identificadores únicos que solo son válidos para una aplicación determinada, por lo que aplicaciones malintencionadas pueden "suplantar" un identificador de la aplicación legítima y recibir los tokens destinados a la aplicación legítima. Para asegurarse de que siempre se comunica con la aplicación correcta en tiempo de ejecución, le pedimos al desarrollador que proporcione un redirectURI personalizado al registrar su aplicación con Microsoft. **A continuación se describe cómo los desarrolladores deben diseñar este URI de redirección.** Este redirectURI personalizado contiene el identificador de paquete de la aplicación y la App Store de Apple garantiza que es único para la aplicación. Cuando una aplicación llama al agente, este solicita al sistema operativo iOS que le proporcione el identificador de paquete que llamó al agente. El agente proporciona este identificador a Microsoft en la llamada a nuestro sistema de identidad. Si el identificador de paquete de la aplicación no coincide con el identificador de paquete que el desarrollador nos ha proporcionado durante el registro, denegaremos el acceso a los tokens del recurso que está solicitando la aplicación. Esta comprobación asegura que solo la aplicación registrada por el desarrollador recibe los tokens.

**El desarrollador tiene la opción de elegir si el SDK de Microsoft Identity llama al agente o usa el flujo sin asistencia del agente.** Sin embargo, si el desarrollador decide no usar el flujo asistido por agente, no podrá beneficiarse del uso de las credenciales de SSO que el usuario puede ya haber agregado en el dispositivo; además, impide que la aplicación se use con las características empresariales que Microsoft proporciona a sus clientes, como el acceso condicional, las funcionalidades de administración de Intune y la autenticación basada en certificados.

Estos inicios de sesión tienen las siguientes ventajas:

* El usuario experimenta el inicio de sesión único en todas sus aplicaciones, con independencia del proveedor.
* La aplicación puede usar las características empresariales más avanzadas, como el acceso condicional, o usar el conjunto de productos de InTune.
* La aplicación puede admitir la autenticación basada en certificados para los usuarios empresariales.
* La experiencia de inicio de sesión es mucho más segura, ya que tanto la identidad de la aplicación como el usuario se verifican por parte de la aplicación de agente con algoritmos de seguridad y sistemas de cifrado adicionales.

Estos inicios de sesión tienen las siguientes desventajas:

* En iOS, el usuario pasa por un proceso externo a la experiencia de la aplicación mientras se eligen las credenciales.
* Se pierde la capacidad de administrar la experiencia de inicio de sesión para los clientes dentro de la aplicación.

A continuación se representa el funcionamiento de los SDK de Microsoft Identity con las aplicaciones de agente para habilitar el inicio de sesión único:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Una vez que disponga de toda esta información, probablemente pueda entender e implementar mejor el inicio de sesión único en la aplicación mediante los SDK y la plataforma Microsoft Identity.

## <a name="enabling-cross-app-sso-using-adal"></a>Habilitación del SSO entre aplicaciones mediante ADAL
Ahora vamos a usar el SDK de iOS de ADAL para:

* Activar el SSO no asistido por agente para su conjunto de aplicaciones
* Activar la compatibilidad para el SSO asistido por agente

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Activación del SSO para el SSO no asistido por agente
En el caso del SSO entre aplicaciones no asistido por agente, los SDK de Microsoft Identity administran automáticamente gran parte de la complejidad que entraña este proceso. Esto incluye la búsqueda del usuario adecuado en la memoria caché y el mantenimiento de una lista de usuarios que han iniciado sesión en la que se pueden realizar consultas.

Para habilitar el SSO entre aplicaciones de las que es el propietario debe hacer lo siguiente:

1. Garantizar que todas las aplicaciones utilizan el mismo identificador de cliente o aplicación
2. Garantizar que todas las aplicaciones comparten el mismo certificado de firma de Apple para que sea posible compartir los llaveros.
3. Solicitar los mismos permisos de llaveros para cada una de las aplicaciones.
4. Indicar a los SDK de Microsoft Identity cuál es el llavero compartido que quiere que utilicemos.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Uso del mismo identificador de cliente o aplicación para todas las aplicaciones de su conjunto
A fin de que la plataforma Microsoft Identity sepa que puede compartir tokens entre las aplicaciones, es necesario que todas ellas tengan el mismo identificador de cliente o aplicación. Se trata del identificador único que se le suministró al registrar su primera aplicación en el portal.

Quizás se pregunte cómo se puede identificar a las diferentes aplicaciones en el servicio Microsoft Identity si todas utilizan el mismo identificador. Pues bien, esto es posible gracias a los **URI de redirección**. Cada aplicación puede tener varios URI de redirección registrados en el portal de incorporación. Cada una de las aplicaciones de su conjunto tendrá diferentes URI de redirección. A continuación se muestra un ejemplo típico de su aspecto:

URI de redirección de la aplicación 1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

URI de redirección de la aplicación 2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

URI de redirección de la aplicación 3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Estos están anidados en el mismo identificador de cliente o aplicación y se consultan en función del URI de redirección que nos devuelve en su configuración del SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Tenga en cuenta que el formato de estos URI de redirección se explica a continuación. Puede usar cualquier URI de redirección, a menos que desee utilizar el agente, en cuyo caso debe tener un aspecto como el anterior*

#### <a name="create-keychain-sharing-between-applications"></a>Permitir el uso compartido de llaveros entre aplicaciones
La habilitación del uso compartido de llaveros está fuera del ámbito de este documento; sin embargo, Apple lo trata en su documento [Adding Capabilities](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)(Incorporación de funcionalidades). Lo importante es que decida cómo desea llamar a su llavero y agregar esa funcionalidad en todas sus aplicaciones.

Cuando haya configurado los derechos correctamente, verá un archivo en el directorio del proyecto llamado `entitlements.plist` , que contiene algo parecido a lo siguiente:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Una vez que haya habilitado los derechos del llavero en cada una de las aplicaciones y esté listo para usar el SSO, informe al SDK de Microsoft Identity acerca del llavero usando el siguiente valor en `ADAuthenticationSettings` :

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Cuando se comparte un llavero entre aplicaciones, cualquiera de ellas puede eliminar usuarios o, peor aún, eliminar todos los tokens de la aplicación. Esto puede resultar especialmente problemático si tiene aplicaciones que dependen de los tokens para hacer el trabajo en segundo plano. Compartir un llavero implica ser muy cuidadoso en todas y cada una de las operaciones de eliminación realizadas a través de los SDK de Microsoft Identity
> 
> 

Eso es todo. El SDK de Microsoft Identity ahora compartirá las credenciales entre todas sus aplicaciones. La lista de usuarios también se compartirá entre las instancias de la aplicación.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Activación del SSO para el SSO asistido por agente
La capacidad de una aplicación de usar cualquier agente que esté instalado en el dispositivo está **desactivada de forma predeterminada**. Para poder utilizar la aplicación con el agente debe realizar algunos pasos de configuración adicionales y agregar código a la aplicación.

Los pasos que debe seguir son los siguientes:

1. Habilitar el modo de agente en la llamada al código de la aplicación para el SDK de Microsoft.
2. Establecer un nuevo URI de redirección e indicarlo tanto en la aplicación como en el registro de la aplicación.
3. Registrar un esquema de dirección URL.
4. Compatibilidad con iOS 9: agregar un permiso al archivo info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Paso 1: Habilitar el modo de agente en la aplicación
La capacidad de la aplicación de utilizar el agente se activa al crear el "contexto" o la configuración inicial del objeto de autenticación. Para ello, configure el tipo de credenciales en el código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
El valor `AD_CREDENTIALS_AUTO` permitirá al SDK de Microsoft Identity llamar al agente, `AD_CREDENTIALS_EMBEDDED` impedirá que el SDK de Microsoft Identity llame al agente.

#### <a name="step-2-registering-a-url-scheme"></a>Paso 2: Registrar un esquema de dirección URL
La plataforma Microsoft Identity utiliza direcciones URL para invocar al agente y, a continuación, devolver el control a la aplicación. Para terminar este viaje de ida y vuelta se requiere un esquema de dirección URL registrado para la aplicación del que tenga conocimiento la plataforma Microsoft Identity. Esto puede sumarse a otros esquemas de aplicación que haya registrado previamente en la aplicación.

> [!WARNING]
> Se recomienda dotar al esquema de dirección URL de unas características bastante únicas para minimizar las posibilidades de que otra aplicación use el mismo esquema de dirección URL. Apple no exige que los esquemas de dirección URL registrados en el almacén de aplicaciones sean únicos.
> 
> 

A continuación, se muestra un ejemplo del aspecto que podría tener esto en la configuración de un proyecto. También es posible hacerlo en XCode del modo siguiente:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Paso 3: Establecer un nuevo URI de redirección con el esquema de dirección URL
Para garantizar que siempre se devuelvan los tokens de credencial a la aplicación correcta, es necesario asegurarse de que se llama a la aplicación de tal manera que el sistema operativo iOS pueda verificarla. El sistema operativo iOS informa a las aplicaciones de agente de Microsoft sobre el identificador de agrupación que lo llama. Este sistema no se puede suplantar por ninguna aplicación malintencionada. Por lo tanto, se aprovecha este elemento junto con el URI de la aplicación de agente para garantizar que los tokens se devuelven a la aplicación correcta. Es necesario establecer este URI de redirección único en la aplicación y configurarlo como URI de redirección en nuestro portal para desarrolladores.

Para ser correcto, el URI de redirección debe presentar el formato siguiente:

`<app-scheme>://<your.bundle.id>`

Por ejemplo, *x-msauth-mytestiosapp://com.myapp.mytestapp*

Este URI de redirección debe especificarse en el registro de la aplicación mediante [Azure Portal](https://portal.azure.com/). Para más información sobre el registro de aplicaciones de Azure AD, consulte [Integración con Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Paso 3a: Agregar un URI de redirección a la aplicación y al portal de desarrolladores para admitir la autenticación basada en certificados
Para admitir la autenticación basada en certificados, se requiere el registro de un segundo elemento "msauth"en la aplicación y en [Azure Portal](https://portal.azure.com/) para controlar la autenticación de certificados si desea agregar esa compatibilidad a su aplicación.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

Por ejemplo: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Paso 4: iOS 9: Agregar un parámetro de configuración a la aplicación
ADAL usa – canOpenURL: para comprobar si el agente está instalado en el dispositivo. En iOS 9, Apple ha bloqueado los esquemas que puede consultar una aplicación. Debe agregar "msauth" a la sección LSApplicationQueriesSchemes de `info.plist file`.

<key>LSApplicationQueriesSchemes</key>

<array><string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>Ya ha configurado el SSO.
Ahora, el SDK de Microsoft Identity compartirá automáticamente las credenciales entre las aplicaciones e invocará al agente si está presente en su dispositivo.

