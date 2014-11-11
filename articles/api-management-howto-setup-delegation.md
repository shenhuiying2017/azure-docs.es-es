<properties pageTitle="How to delegate user registration and product subscription" metaKeywords="" description="Learn how to delegate user registration and product subscription to a third party in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to delegate user registration and product subscription in Azure API Management" authors="antonba" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="antonba" />

# Delegación de registros de usuario y suscripciones a producto

La delegación le permite usar su sitio web actual para controlar el inicio de sesión y la suscripción de los desarrolladores, así como sus suscripciones a productos, en contraposición al uso de la funcionalidad integrada en el portal para desarrolladores. Esto habilita su sitio web como propietario de los datos de usuario para poder realizar la validación de estos pasos de forma personalizada.

## En este tema

-   [Delegación de inicios de sesión y suscripciones de desarrolladores][Delegación de inicios de sesión y suscripciones de desarrolladores]
-   [Delegación de suscripciones a productos][Delegación de suscripciones a productos]

## <a name="delegate-signin-up"> </a>Delegación de inicios de sesión y suscripciones de desarrolladores

Para delegar el inicio de sesión y la suscripción de los desarrolladores a su sitio web actual, deberá crear un extremo especial de delegación en el sitio que funcione como punto de entrada de cualquier solicitud de este tipo que se inicie en el portal para desarrolladores de Administración de API.

El flujo de trabajo final será el siguiente:

1.  El desarrollador hace clic en el enlace de inicio de sesión o de suscripción que se encuentra en el portal para desarrolladores de Administración de API.
2.  El explorador se redirige al extremo de delegación.
3.  Como respuesta, el extremo de delegación se redirige a la interfaz de usuario o la presenta para pedir al usuario que inicie sesión o se suscriba.
4.  Una vez conseguido, se redirige de nuevo al usuario a la página del portal para desarrolladores de Administración de API de la que partió.

Para empezar, configuremos primero Administración de API para que enrute las solicitudes a través del extremo de delegación. En el portal de publicadores de Administración de API, en el título **Portal para desarrolladores** del menú de la izquierda, haga clic en **Delegación** y luego en **Delegar inicio y cierre de sesión**.

![Delegation page][Delegation page]

-   Determine cuál será la URL del extremo especial de delegación e introdúzcala en el campo **URL de extremo de delegación**.

-   En el campo **Clave de autenticación de delegación**, introduzca el secreto que se usará para procesar la que suministrará para su comprobación con objeto de garantizar que la solicitud procede efectivamente de Administración de API de Azure.

Ahora debe crear el **extremo de delegación**. Este tiene que realizar varias acciones:

1.  Recibir una solicitud de la forma siguiente:

    > *<http://www.yourwebsite.com/apimdelegation?operation=SignIn&redirectUrl>={URL de la página de origen}&salt={cadena}&sid={cadena}*

    Parámetros de consulta en el caso de inicio de sesión o suscripción:

    -   **operation**: identifica el tipo de solicitud de delegación de que se trata: solo puede ser "SignIn" en este caso.
    -   **redirectUrl**: dirección URL de la página en donde el usuario hizo clic en el vínculo de suscripción o de inicio de sesión.
    -   **salt**: cadena salt especial que se usa para procesar un hash de seguridad.
    -   **sig**: hash de seguridad procesado que se comparará con su propio hash procesado.

2.  Compruebe que la solicitud procede de Administración de API de Azure (opcional, pero especialmente recomendado por motivos de seguridad).

    -   Procese un hash HMAC-SHA512 de una cadena en función de los parámetros de consulta **redirectUrl** y **salt**:

        > HMAC(**redirectUrl** + '\\n' + **salt**)

    -   Compare el hash procesado anteriormente con el valor del parámetro de consulta **sig**. Si los dos hashes coinciden, vaya a paso siguiente; de lo contrario, deniegue la solicitud.

3.  Compruebe que se recibe una solicitud de inicio de sesión o suscripción: el parámetro de consulta **operation** se establecerá en "**SignIn**".

4.  Presente al usuario la interfaz de usuario para que inicie sesión o se suscriba.

5.  Si el usuario se suscribe, hay que crear la cuenta correspondiente en Administración de API. [Cree un usuario][Cree un usuario] con la API REST de Administración de API. Al hacerlo, asegúrese de que el identificador de usuario se establece en el mismo que existe en su almacén de usuario o en un identificador al que pueda realizar el seguimiento.

6.  Cuando el usuario se autentique correctamente:

    -   [solicite un token de inicio de sesión único (SSO)][solicite un token de inicio de sesión único (SSO)] a través de la API de REST de Administración de API

    -   anexe un parámetro de consulta returnUrl a la URL de SSO que se recibió de la llamada de API anterior:

        > por ejemplo, <https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url>

    -   redirija al usuario a la URL producida anteriormente

## <a name="delegate-product-subscription"> </a>Delegación de suscripciones a productos

La delegación de una suscripción a productos funciona de forma similar a la delegación de inicio de sesión y suscripción de usuario. El flujo de trabajo final sería el siguiente:

1.  El desarrollador selecciona un producto en el portal para desarrolladores de Administración de API y hace clic en el botón Suscribir.
2.  El explorador se redirige al extremo de delegación.
3.  El extremo de delegación realiza los pasos necesarios para la suscripción al producto: esto depende de usted, y puede que implique la redirección a otra página para solicitar información de facturación, la formulación de otras preguntas o simplemente el almacenamiento de la información sin que se requiera ninguna acción del usuario.
4.  Una vez conseguido, se redirige de nuevo al usuario a la página del portal para desarrolladores de Administración de API de la que partió.

Para habilitar la funcionalidad, en la página **Delegación**, haga clic en **Delegar suscripción de productos**.

A continuación, asegúrese de que el extremo de delegación realiza las siguientes acciones:

1.  Recibir una solicitud de la forma siguiente:

    > *<http://www.yourwebsite.com/apimdelegation?operation>={operación}&productId={producto al que se suscribe}&userId={usuario que realiza la solicitud}&salt={cadena}&sid={cadena}*

    Parámetros de consulta en el caso de suscripción a producto:

    -   **operation**: identifica el tipo de solicitud de delegación de que se trata. En las solicitudes de suscripción a producto las opciones válidas son:

        -   "Subscribe": solicitud de suscripción del usuario a un determinado producto con el identificador especificado (consulte a continuación).
        -   "Unsubscribe": solicitud de cancelación de la suscripción de un usuario a un producto.
        -   "Renew": solicitud de renovación de una suscripción (por ejemplo, que pueda expirar).
    -   **productId**: identificador del producto al que el usuario solicitó suscribirse.
    -   **userId**: identificador del usuario para el que se realiza la solicitud.
    -   **salt**: cadena salt especial que se usa para procesar un hash de seguridad.
    -   **sig**: hash de seguridad procesado que se comparará con su propio hash procesado.

2.  Compruebe que la solicitud procede de Administración de API de Azure (opcional, pero especialmente recomendado por motivos de seguridad).

    -   Procesar un hash HMAC-SHA512 de una cadena en función de los parámetros de consulta **productId**, **userId** y **salt**:

        > HMAC(**productId** + '\\n' + **userId** + '\\n' + **salt**)

    -   Compare el hash procesado anteriormente con el valor del parámetro de consulta **sig**. Si los dos hashes coinciden, vaya a paso siguiente; de lo contrario, deniegue la solicitud.

3.  Realice cualquier procesamiento de la suscripción a producto en función del tipo de operación solicitada en **operation**; por ejemplo, facturación, preguntas adicionales, etc.

4.  Tras la correcta suscripción del usuario al producto por su parte, suscriba el usuario al producto de Administración de API [llamando a la API de REST para la suscripción del producto][llamando a la API de REST para la suscripción del producto].

5.  Redirija de nuevo al usuario a la **redirectUrl** especificada al recibir la solicitud.

  [Delegación de inicios de sesión y suscripciones de desarrolladores]: #delegate-signin-up
  [Delegación de suscripciones a productos]: #delegate-product-subscription
  [Delegation page]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
  [Cree un usuario]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
  [llamando a la API de REST para la suscripción del producto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
