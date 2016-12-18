---
title: "Integración de aplicaciones con Azure Active Directory | Microsoft Docs"
description: "Información detallada sobre cómo agregar, actualizar o quitar una aplicación en Azure Active Directory (Azure AD)."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/29/2016
ms.author: mbaldwin;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ff5c4cd342814a3a5baf19f711daae586deed5f6


---
# <a name="integrating-applications-with-azure-active-directory"></a>Integración de aplicaciones con Azure Active Directory
[!INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Los desarrolladores de la empresa y los proveedores de software como servicio (SaaS) pueden desarrollar aplicaciones de línea de negocio o servicios comerciales en la nube que se pueden integrar con Azure Active Directory (Azure AD) para ofrecer inicio de sesión seguro y autorización en sus servicios. Para integrar una aplicación o un servicio con Azure AD, un desarrollador debe registrar primero los detalles de la aplicación con Azure AD mediante el Portal de Azure clásico.

En este artículo se muestra cómo agregar, actualizar o quitar una aplicación en Azure AD. Aprenderá sobre los diferentes tipos de aplicaciones que se pueden integrar con Azure AD, cómo configurar las aplicaciones para que tengan acceso a otros recursos como las API web y mucho más.

Para más información sobre los dos objetos de Azure AD que representan una aplicación registrada y la relación entre ellos, consulte [Application Objects and Service Principal Objects](active-directory-application-objects.md) (Objetos de aplicación y de entidad de servicio). Para más información sobre las directrices de personalización de marca que debe usar al desarrollar aplicaciones con Azure Active Directory, consulte [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Agregar una aplicación
Cualquier aplicación que quiera usar las funciones de Azure AD debe registrarse primero en un inquilino de Azure AD. Este proceso de registro implica proporcionar los detalles de Azure AD sobre la aplicación, como la dirección URL donde se encuentra, la dirección URL para enviar respuestas cuando un usuario está autenticado, el URI que identifica la aplicación y así sucesivamente.

Si está creando una aplicación web que solo necesita admitir el inicio de sesión para usuarios en Azure AD, basta con que siga las instrucciones a continuación. Si la aplicación necesita credenciales o permisos para acceder a una API web o debe permitir el acceso a usuarios de otros inquilinos de Azure AD, consulte la sección [Actualización de una aplicación](#updating-an-application) para seguir configurando la aplicación.

### <a name="to-register-a-new-application-in-the-azure-classic-portal"></a>Para registrar una aplicación nueva en el Portal de Azure clásico
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.
3. En el menú superior, haga clic en **Aplicaciones**. Si no se han agregado aplicaciones al directorio, esta página solo muestra el vínculo Agregar una aplicación. Haga clic en el vínculo; como alternativa, puede hacer clic en el botón **Agregar** de la barra de comandos.
4. En la página ¿Qué desea hacer?, haga clic en el vínculo para **Agregar una aplicación que mi organización está desarrollando**.
5. En la página Proporcione información sobre su aplicación, debe especificar un nombre para la aplicación e indicar el tipo de aplicación que va a registrar con Azure AD.  Puede elegir entre una [aplicación cliente/web](active-directory-dev-glossary.md#client-application) / [aplicación de API/recurso web](active-directory-dev-glossary.md#resource-server) (también puede funcionar como ambas) o una aplicación [cliente nativa](active-directory-dev-glossary.md#native-client). Una vez finalizado, haga clic en el icono de flecha situado en la esquina inferior derecha de la página.
6. En la página Propiedades de la aplicación, indique la URL de inicio de sesión y el URI de id. de aplicación si está registrando una aplicación web o simplemente el URI de redirección de una aplicación cliente nativa. Después, haga clic en la casilla situada en la esquina inferior derecha de la página.
7. La aplicación se agrega y le llevará a la página Inicio rápido de la aplicación. En función de que se trate de una aplicación web o nativa, verá opciones diferentes para agregar otras opciones a la aplicación. Una vez agregada la aplicación, puede empezar a actualizarla para permitir que los usuarios inicien sesión, tengan acceso a las API web de otras aplicaciones o configuren una aplicación multiempresa (que permite que otras organizaciones tengan acceso a la aplicación).

> [!NOTE]
> De forma predeterminada, el registro de aplicaciones recién creadas está configurado para permitir que los usuarios del directorio inicien sesión en la aplicación.
> 
> 

## <a name="updating-an-application"></a>Actualización de una aplicación
Una vez registrada la aplicación con Azure AD, es posible que tenga que actualizarse para proporcionar acceso a las API web, ponerla a disposición de otras organizaciones y mucho más. En esta sección se describen distintas formas en que puede que tenga que realizar configuraciones adicionales en la aplicación. Primero comenzaremos con información general sobre el marco de consentimiento, que es importante comprender si va a crear aplicaciones de recursos o de API que van a consumir las aplicaciones cliente creadas por desarrolladores de su organización o de otra organización.

Para obtener más información sobre la forma en que la autenticación funciona en Azure AD, vea [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

### <a name="overview-of-the-consent-framework"></a>Información general sobre el marco de consentimiento
El marco de consentimiento de Azure AD facilita el desarrollo de aplicaciones cliente web y nativas multiinquilino que requieren acceso a API web protegidas por un inquilino de Azure AD diferente de aquel donde está registrada la aplicación cliente. Estas API web incluyen la API Graph, Office 365 y otros servicios de Microsoft, además de sus propias API web. El marco se basa en que un usuario o un administrador da consentimiento a una aplicación que solicita el registro en su directorio, lo cual puede implicar el acceso a los datos de directorio.

Por ejemplo, si una aplicación cliente web necesita llamar a la aplicación de recursos o la API web de Office 365 para leer información de calendario sobre el usuario, ese usuario tendrá que dar su consentimiento a la aplicación cliente. Después de dar su consentimiento, la aplicación cliente podrá llamar a la API web de Office 365 en nombre del usuario y usar la información de calendario según sea necesario.

El marco de consentimiento se basa en OAuth 2.0 y sus distintos flujos, como la concesión de credenciales de cliente y la concesión de código de autorización, mediante clientes públicos o confidenciales. Mediante el uso de OAuth 2.0, Azure AD permite crear muchos tipos diferentes de aplicaciones cliente, como en un teléfono, tableta, servidor o una aplicación web, y obtener acceso a los recursos necesarios.

Para más información sobre el marco de consentimiento, consulte [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md) y el tema de Office 365 con la [descripción de la autenticación con las API de Office 365](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks).

#### <a name="example-of-the-consent-experience"></a>Ejemplo de la experiencia de consentimiento
Los siguientes pasos le mostrarán cómo funciona la experiencia de consentimiento para el desarrollador de la aplicación y el usuario.

1. En la página de configuración de la aplicación cliente web en el Portal de Azure clásico, establezca los permisos que requiere la aplicación mediante los menús desplegables del control Permisos para otras aplicaciones.
   
    ![Permisos para otras aplicaciones](./media/active-directory-integrating-applications/permissions.png)
2. Plantéese la posibilidad de que los permisos de la aplicación estén actualizados, la aplicación se ejecute y un usuario esté a punto de usarla por primera vez. Si la aplicación todavía no adquirió un token de acceso o de actualización, tiene que ir al extremo de autorización de Azure AD para obtener un código de autorización que sirve para adquirir un nuevo token de acceso y de actualización.
3. Si el usuario no está ya autenticado, se le pedirá que inicie sesión en Azure AD.
   
    ![Inicio de sesión de usuario o administrador en Azure AD](./media/active-directory-integrating-applications/useradminsignin.png)
4. Cuando el usuario inicie sesión, Azure AD determinará si se debe mostrar una página de consentimiento al usuario. Esta determinación se basa en que el usuario (o el administrador de la organización) ya concediese el consentimiento de la aplicación. Si todavía no se concedió el consentimiento, Azure AD se lo pedirá al usuario y mostrará los permisos necesarios para que funcione. El conjunto de permisos que aparecen en el cuadro de diálogo de consentimiento son los mismos que se seleccionaron en el control Permisos para otras aplicaciones del Portal de Azure clásico.
   
    ![Experiencia de consentimiento de usuario](./media/active-directory-integrating-applications/userconsent.png)
5. Después de que el usuario concede el consentimiento, se devuelve un código de autorización para la aplicación, que se puede canjear para adquirir un token de acceso y un token de actualización. Para más información sobre este flujo, consulte la [sección Aplicación web a API web](active-directory-authentication-scenarios.md#web-application-to-web-api) en [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

### <a name="configuring-a-client-application-to-access-web-apis"></a>Configuración de una aplicación cliente para acceder a las API web
Para que una aplicación cliente web o confidencial pueda participar en un flujo de concesión de autorización que requiera autenticación (y obtener un token de acceso), debe establecer credenciales seguras. El método de autenticación predeterminado compatible con Azure Portal es un id. de cliente + una clave simétrica. En esta sección, se describen los pasos de configuración necesarios para proporcionar la clave secreta para las credenciales de cliente.

Además, para que un cliente pueda acceder a una API web expuesta por una aplicación de recursos (es decir, la API Graph de Azure AD), el marco de consentimiento garantizará que el cliente obtenga la concesión de los permisos necesarios en función de los solicitados. De forma predeterminada, todas las aplicaciones pueden elegir permisos de Azure Active Directory (API Graph) y de API de administración de servicios de Azure, con el permiso "Habilitar inicio de sesión y leer perfiles de usuario" de Azure AD ya seleccionado de forma predeterminada. Si la aplicación cliente está registrada en un inquilino de Azure AD de Office 365, las API web y los permisos de SharePoint y Exchange Online también estarán disponibles para seleccionarse. Puede seleccionar entre [dos tipos de permisos](active-directory-dev-glossary.md#permissions) en los menús desplegables situados junto a la API web que desee:

* Permisos de la aplicación: la aplicación cliente necesita acceso directo a la API web como sí misma (sin contexto de usuario). Este tipo de permiso requiere el consentimiento del administrador y no está disponible para aplicaciones cliente nativas.
* Permisos delegados: la aplicación cliente necesita acceso a la API web como el usuario que inició sesión, pero con acceso limitado por el permiso seleccionado. Este tipo de permiso lo puede conceder un usuario a menos que el permiso esté configurado como que requiere el consentimiento del administrador.

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>Para agregar credenciales o permisos para acceder a las API web
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com)
2. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.
3. En el menú superior, haga clic en **Aplicaciones**y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración. Haga clic en el vínculo **Configurar** en la parte superior de la página, el cual le llevará a la página de configuración de la aplicación.
4. Para agregar una clave secreta para las credenciales de la aplicación web, baje hasta la sección "Claves".  
   
   * En primer lugar, haga clic en la lista desplegable "Seleccionar duración" y seleccione 1 o 2 años de duración. 
   * Entonces verá una nueva fila, con las fechas de "Válido desde" y "Expira el" rellenadas.
   * La columna en el extremo derecho contendrá el valor de clave después de que guarde los cambios de configuración (abajo). Asegúrese de volver a esta sección y copiarla después de presionar Guardar, para poder usarla en la aplicación cliente durante la autenticación en tiempo de ejecución.
5. Para agregar permisos para acceder a las API de recursos desde el cliente, baje hasta la sección "Permisos para otras aplicaciones". 
   
   * En primer lugar, haga clic en el botón "Agregar aplicación".
   * Use la lista desplegable "Mostrar" para seleccionar el tipo de recursos entre los que desea elegir.
   * En la primera columna, puede seleccionar entre las aplicaciones de recursos disponibles en el directorio que exponen una API web. Haga clic en el recurso que le interese y en la marca de verificación de la esquina inferior derecha.
   * Una vez seleccionado, verá el recurso agregado a la lista "Permisos para otras aplicaciones".
   * Con las listas desplegables "Permisos de la aplicación" y "Permisos delegados", seleccione los permisos deseados para la aplicación cliente.
6. Cuando termine, haga clic en el botón **Guardar** de la barra de comandos. Si especificó una clave para la aplicación, también se generará una vez que haya hecho clic en Guardar.

> [!NOTE]
> Al hacer clic en el botón Guardar también se establecen automáticamente los permisos para la aplicación en el directorio en función de los permisos para otras aplicaciones que configuró.  Puede ver estos permisos de la aplicación examinando la pestaña Propiedades de la aplicación.
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configuración de una aplicación de recursos para exponer las API web
Puede desarrollar una API web y ponerla a disposición de las aplicaciones cliente si expone los [ámbitos](active-directory-dev-glossary.md#scopes) y los [roles](active-directory-dev-glossary.md#roles) de acceso. Una API web configurada correctamente se pone a disposición de otras aplicaciones del mismo modo que otras API web de Microsoft, incluidas la API Graph y las API de Office 365. Los ámbitos y los roles de acceso se exponen a través del [manifiesto de la aplicación](active-directory-dev-glossary.md#application-manifest), que es un archivo JSON que representa la configuración de identidad de la aplicación.  

En la siguiente sección, se mostrará cómo exponer ámbitos de acceso modificando el manifiesto de la aplicación de recursos.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Agregar ámbitos de acceso a la aplicación de recursos
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.
3. En el menú superior, haga clic en **Aplicaciones**y luego en la aplicación de recursos que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.
4. Haga clic en el botón **Administrar manifiesto** de la barra de comandos y seleccione **Descargar manifiesto**.
5. Abra el archivo de manifiesto de aplicación JSON y reemplace el nodo "oauth2Permissions" por el siguiente fragmento de código JSON. Este fragmento de código es un ejemplo de cómo exponer un ámbito conocido como "suplantación de usuario", que permite que un propietario de recursos proporcione a una aplicación cliente un tipo de acceso delegado a un recurso. Asegúrese de cambiar el texto y los valores para su propia aplicación:
   
        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in     user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],
   
    El valor del identificador debe ser un nuevo GUID generado que cree mediante una [herramienta de generación de GUID](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) o por medio de programación. Representa un identificador único para el permiso que se expone mediante la API web. Una vez que el cliente está configurado correctamente para solicitar el acceso a la API web y llama a la API web, presentará un token JWT de OAuth 2.0 que tiene la notificación de ámbito (scp) establecida en el valor anterior que, en este caso, es user_impersonation.
   
   > [!NOTE]
   > Puede exponer ámbitos adicionales posteriormente si es necesario. Tenga en cuenta que la API web podría exponer varios ámbitos asociados a diversas funciones diferentes. Ahora puede controlar el acceso a la API web mediante la notificación de ámbito (scp) del token JWT de OAuth 2.0 recibido.
   > 
   > 
6. Guarde el archivo JSON actualizado y cárguelo. Para ello, haga clic en el botón **Administrar manifiesto** en la barra de comandos, seleccione **Cargar manifiesto**, vaya al archivo de manifiesto actualizado y selecciónelo. Una vez cargado, la API web ya está configurada para que la usen otras aplicaciones del directorio.

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>Para comprobar que la web API se expone a otras aplicaciones en el directorio
1. En el menú superior, haga clic en **Aplicaciones**, seleccione la aplicación cliente cuyo acceso a la API web quiera configurar y haga clic en **Configurar**.
2. Desplácese hacia abajo hasta la sección Permisos para otras aplicaciones. Haga clic en el menú desplegable Seleccionar aplicación y podrá seleccionar la API web para la que acaba de exponer un permiso. En el menú desplegable Permisos delegados, seleccione el nuevo permiso.

![Se muestran los permisos de la lista de tareas](./media/active-directory-integrating-applications/listpermissions.png)

#### <a name="more-on-the-application-manifest"></a>Más sobre el manifiesto de aplicación
El manifiesto de aplicación realmente actúa como un mecanismo para actualizar la entidad de aplicación, que define todos los atributos de configuración de identidad de una aplicación de Azure AD, incluidos los ámbitos de acceso de API que analizamos. Para más información sobre la entidad de aplicación, consulte la [documentación sobre la entidad de aplicación de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Ahí encontrará completa información de referencia sobre los miembros de la entidad de aplicación utilizados para especificar los permisos para la API:  

* el miembro appRoles, que es una colección de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) que pueden usarse para definir los **permisos de aplicación** para una API web;  
* el miembro oauth2Permissions, que es una colección de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) que pueden usarse para definir los **permisos delegados** para una API web.

Para más información sobre los conceptos del manifiesto de aplicación en general, consulte [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-apis"></a>Acceso a las API de Office 365 y Azure AD Graph
Como se mencionó anteriormente, además de exponer y tener acceso a API en sus propias aplicaciones de recursos, también puede actualizar la aplicación cliente para tener acceso a las API expuestas por los recursos de Microsoft.  La API de Azure AD Graph, que se denomina "Azure Active Directory" en la lista de permisos para otras aplicaciones, está disponible de forma predeterminada para todas las aplicaciones que se registran con Azure AD. Si va a registrar la aplicación cliente en un inquilino de Azure AD que se ha aprovisionado por Office 365, puede tener acceso todos los permisos expuestos por las API a varios recursos de Office 365.

Para más información sobre los ámbitos de acceso expuestos por:  

* API Graph de Azure AD, consulte el artículo [Ámbitos de permiso | Conceptos de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).
* API de Office 365, consulte el artículo [Authentication and Authorization using Common Consent Framework (Autenticación y autorización mediante el marco de consentimiento común)](https://msdn.microsoft.com/office/office365/howto/application-manifest) . Consulte [Set up your Office 365 development environment (Configurar el entorno de desarrollo de Office 365)](https://msdn.microsoft.com/office/office365/HowTo/setup-development-environment) para ver una descripción completa sobre cómo crear una aplicación cliente que se integre con las API de Office 365.

> [!NOTE]
> Debido a una limitación actual, las aplicaciones cliente nativas solo pueden llamar a la API Graph de Azure AD si usan el permiso "Acceso al directorio de la organización".  Esta restricción no se aplica a las aplicaciones web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configuración de aplicaciones multiempresa
Al agregar una aplicación a Azure AD, tal vez quiera que solo tengan acceso a la aplicación los usuarios de la organización. Como alternativa, tal vez quiera que los usuarios de organizaciones externas tengan acceso a la aplicación. Estos dos tipos de aplicaciones se denominan aplicaciones multiempresa y aplicaciones de un solo inquilino. Puede modificar la configuración de una aplicación de un solo inquilino para convertirla en una aplicación multiempresa, lo que se trata a continuación en esta sección.

Es importante tener en cuenta las diferencias entre una aplicación de un solo inquilino y otra multiinquilino:  

* La finalidad de una aplicación de un solo inquilino es su uso en una sola organización. Suele tratarse de aplicaciones de línea de negocio (LOB) escritas por un desarrollador de la empresa. A una aplicación de un solo inquilino solo obtienen acceso usuarios de un solo directorio y, por tanto, solo es necesario aprovisionarla en un directorio.
* La finalidad de una aplicación multiempresa es su uso en muchas organizaciones. Se trata de aplicaciones web de software como servicio (SaaS) que suelen ser creadas por un proveedor de software independiente (ISV). Las aplicaciones multiinquilino se deben aprovisionar en cada uno de los directorios en los que se usarán, lo que requiere el consentimiento del usuario o del administrador para registrarlas, que se transmite a través del marco del consentimiento de Azure AD. Tenga en cuenta que todas las aplicaciones cliente nativas son multiinquilino de forma predeterminada porque están instaladas en el dispositivo del propietario del recurso. Consulte la sección Información general sobre el marco de consentimiento presentada anteriormente para obtener más detalles en el marco de consentimiento.

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>Concesión de acceso a los usuarios externos a la aplicación
Si está escribiendo una aplicación que quiere poner a disposición de sus clientes o asociados externos a la organización, tendrá que actualizar la definición de la aplicación en el Portal de Azure clásico.

> [!NOTE]
> Al habilitar el tipo multiinquilino, debe asegurarse de que el URI del identificador de la aplicación pertenece a un dominio comprobado. Además, la Dirección URL de retorno debe comenzar por https://. Para obtener más información, vea [Objetos de aplicación y objetos de entidad de servicio](active-directory-application-objects.md).
> 
> 

Para permitir que los usuarios externos accedan a la aplicación: 

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com)
2. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.
3. En el menú superior, haga clic en **Aplicaciones**y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con opciones de configuración.
4. Expanda la sección **Configurar aplicación multiempresa** de la página Inicio rápido y haga clic en el vínculo **Configurarla ahora** en la sección Habilitar acceso. Aparecerá la página de propiedades de la aplicación.
5. Haga clic en el botón **Sí** situado junto a La aplicación es multiempresa y luego en el botón **Guardar** de la barra de comandos.

Una vez realizado el cambio anterior, los usuarios y administradores de otras organizaciones podrán conceder acceso de la aplicación a sus directorios y otros datos.

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>Desencadenamiento del marco de consentimiento de Azure AD en tiempo de ejecución
Para utilizar el marco de consentimiento, las aplicaciones cliente multiinquilino deben solicitar autorización mediante OAuth 2.0. Hay [ejemplos de código](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) disponibles que muestran cómo una aplicación web, una aplicación nativa o una aplicación de demonio o de servidor solicita códigos de autorización y tokens de acceso para llamar a las API web.

Es posible que la aplicación web ofrezca también una experiencia de suscripción para los usuarios. Si realmente ofrece una experiencia de suscripción, se espera que el usuario haga clic en un botón de suscripción que redirigirá el explorador al punto de conexión de autorización de OAuth2.0 para Azure AD o a un punto de conexión de información del usuario de OpenID Connect. Estos extremos permiten que la aplicación obtenga información sobre el nuevo usuario inspeccionando el id_token. Después de la fase de inicio de sesión, se presentará al usuario una petición de consentimiento similar a la que se mostró anteriormente en la sección Información general sobre el marco de consentimiento.

Como alternativa, también es posible que la aplicación web ofrezca una experiencia que permite a los administradores "suscribir mi compañía". Esta experiencia también redirigiría al usuario al extremo de autorización de OAuth 2.0 para Azure AD. Sin embargo, en este caso se pasa un parámetro prompt=admin_consent al punto de conexión de la autorización para forzar la experiencia de consentimiento del administrador, donde el administrador concederá consentimiento en nombre de su organización. Solo un usuario que se autentique con una cuenta que pertenezca al rol Administrador global puede proporcionar el consentimiento; otros usuarios recibirán un error. Tras el consentimiento correcto, la respuesta contendrá admin_consent=true. Al canjear un token de acceso, también recibirá un id_token que proporcionará información sobre la organización y el administrador que se suscribió en la aplicación.

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Habilitación de la concesión implícita de OAuth 2.0 para aplicaciones de una sola página
Las aplicaciones de una sola página (SPA) normalmente tienen una estructura con un front-end de JavaScript que se ejecuta en el explorador, que llama al back-end de la API web de la aplicación para llevar a cabo su lógica empresarial. Para las SPA hospedadas en Azure AD, se usa la concesión implícita de OAuth 2.0 para autenticar al usuario en Azure AD y obtener un token que puede usar para proteger las llamadas desde el cliente JavaScript de la aplicación hasta su API web de back-end. Después de que el usuario haya dado su consentimiento, este mismo protocolo de autenticación se puede usar para obtener tokens para proteger las llamadas entre el cliente y otros recursos de API web configurados para la aplicación. Para conocer más sobre la concesión de autorización implícita y ayudarle a decidir si es adecuada para el escenario de su aplicación, consulte [Descripción del flujo de concesión implícita de OAuth2 de Azure Active Directory (AD) ](active-directory-dev-understanding-oauth2-implicit-grant.md).

De forma predeterminada, la concesión implícita de OAuth 2.0 está deshabilitada para las aplicaciones. Puede habilitarla para su aplicación si configura el valor `oauth2AllowImplicitFlow`" en su [manifiesto de aplicación](active-directory-application-manifest.md), que es un archivo JSON que representa la configuración de identidad de la aplicación.

#### <a name="to-enable-oauth-20-implicit-grant"></a>Para habilitar la concesión implícita de OAuth 2.0
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com)
2. Haga clic en el icono de **Active Directory** en el menú de la izquierda y, luego, en el directorio que quiera.
3. En el menú superior, haga clic en **Aplicaciones**y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.
4. Haga clic en el botón **Administrar manifiesto** de la barra de comandos y seleccione **Descargar manifiesto**.
   Abra el archivo de manifiesto de la aplicación JSON y establezca el valor "oauth2AllowImplicitFlow" en "true". De forma predeterminada, es "false".
   
    `"oauth2AllowImplicitFlow": true,`
5. Guarde el archivo JSON actualizado y cárguelo. Para ello, haga clic en el botón **Administrar manifiesto** en la barra de comandos, seleccione **Cargar manifiesto**, vaya al archivo de manifiesto actualizado y selecciónelo. Una vez cargado, la API web está ahora configurada para usar la concesión implícita de OAuth 2.0 para autenticar a los usuarios.

### <a name="legacy-experiences-for-granting-access"></a>Experiencias heredadas para conceder acceso
En esta sección se describe la experiencia de consentimiento heredada antes del 12 de marzo de 2014. Esta experiencia se sigue admitiendo y se describe a continuación. Antes de la nueva funcionalidad, solo se podían conceder los siguientes permisos:

* Iniciar sesión de usuarios en su organización
* Iniciar sesión de usuarios y leer los datos del directorio de su organización (solo como la aplicación)
* Iniciar sesión de usuarios y leer y escribir los datos del directorio de su organización (solo como la aplicación)

Puede seguir los pasos indicados en [Desarrollo de aplicaciones web multiempresa con Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) para conceder acceso a las nuevas aplicaciones registradas en Azure AD. Es importante tener en cuenta que el nuevo marco de consentimiento permite que las aplicaciones sean mucho más eficaces y, además, habilita a los usuarios y no solo a los administradores para que den su consentimiento a estas aplicaciones.

#### <a name="building-the-link-that-grants-access-for-external-users-legacy"></a>Creación del vínculo que concede acceso a usuarios externos (heredado)
Para que los usuarios externos se suscriban en su aplicación con sus cuentas profesionales, tendrá actualizar la aplicación para que muestre un botón que se vincula a la página de Azure AD que les habilita para conceder acceso. Las instrucciones de personalización de marca para este botón de suscripción se describen en el tema [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md) . Después de que el usuario conceda o deniegue el acceso, la página Conceder acceso de Azure AD redirigirá el explorador a su aplicación con una respuesta. Para obtener más información sobre las propiedades de la aplicación, vea [Objetos de aplicación y objetos de entidad de servicio](active-directory-application-objects.md).

La página de concesión de acceso la crea Azure AD, y se puede encontrar un vínculo a ella en la página Configuración de la aplicación del Portal de Azure clásico. Para ir a la página Configuración, haga clic en el vínculo **Aplicaciones** del menú superior de su inquilino de Azure AD, haga clic en la aplicación que desee configurar y en **Configurar** en el menú superior de la página Inicio rápido.

El vínculo de la aplicación tendrá este aspecto: `http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`. En la siguiente tabla se describen los elementos del vínculo:

| Parámetro | Descripción |
| --- | --- |
| ClientId |Obligatorio. Identificador del cliente que obtuvo como parte de la adición de la aplicación. |
| RequestedPermissions |Opcional. Nivel de acceso que solicita la aplicación, que se mostrará al usuario que concede el acceso de la aplicación. Si no se especifica, el nivel de acceso solicitado será de forma predeterminada solo inicio de sesión único. Las otras opciones son DirectoryReaders y DirectoryWriters. Para obtener más información sobre estos niveles de acceso, vea Niveles de acceso de la aplicación. |
| ConsentReturnUrl |Opcional. Dirección URL a la que quiere que se devuelva la respuesta de concesión de acceso. Este valor debe estar en codificación URL y encontrarse en el mismo dominio que la dirección URL de respuesta que se configuró en la definición de la aplicación. Si no se proporciona, la respuesta de concesión de acceso se redirigirá a la URL de respuesta configurada |

La especificación de un parámetro ConsentReturnUrl independiente de la URL de respuesta permitirá que la aplicación implemente una lógica independiente que pueda procesar la respuesta en una dirección URL distinta de la dirección URL de respuesta (que suele procesar tokens SAML de inicio de sesión). También puede especificar parámetros adicionales en la dirección URL codificada de ConsentReturnURL; estos se pasarán como parámetros de cadena de consulta a la aplicación tras la redirección.  Este mecanismo sirve para mantener información adicional o para asociar la solicitud de la aplicación de una concesión de acceso a la respuesta de Azure AD.

#### <a name="grant-access-user-experience-and-response-legacy"></a>Concesión de acceso a la experiencia del usuario y respuesta (heredado)
Cuando una aplicación se redirige al vínculo de acceso de concesión, las siguientes imágenes muestran lo que experimentará el usuario.

Si el usuario no inició sesión todavía, se le pedirá que lo haga:

![Inicio de sesión en AAD](./media/active-directory-integrating-applications/signintoaad.png)

Una vez autenticado el usuario, Azure AD redirigirá al usuario a la página Conceder acceso:

![Conceder acceso](./media/active-directory-integrating-applications/grantaccess.png)

> [!NOTE]
> Solo el administrador de la compañía de la organización externa puede conceder acceso a la aplicación. Si el usuario no es un administrador de la compañía, se le ofrecerá la posibilidad de enviar correo a su administrador de la compañía para solicitar que se conceda acceso a esta aplicación.
> 
> 

Después de que el cliente conceda acceso a una aplicación haciendo clic en Conceder acceso, o lo deniegue haciendo clic en Cancelar, Azure AD enviará una respuesta a la ConsentReturnUrl o a la dirección URL de respuesta configurada. Esta respuesta contiene los parámetros siguientes:

| Parámetro | Descripción |
| --- | --- |
| TenantId |Identificador único de la organización en Azure AD que concedió acceso a la aplicación. Este parámetro solo se especificará si el cliente concedió acceso. |
| Consentimiento |Este valor se establecerá en Concedido si se concedió acceso a la aplicación o en Denegado si se rechazó la solicitud. |

Se devolverán parámetros adicionales a la aplicación si se especificaron como parte de la dirección URL codificada de ConsentReturnUrl. El siguiente es un ejemplo de respuesta a una solicitud de concesión de acceso que indica que la aplicación se autorizó e incluye un ContextID que se suministró en la solicitud de concesión de acceso: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`.

> [!NOTE]
> La respuesta de concesión de acceso no contendrá un token de seguridad para el usuario; la aplicación debe iniciar sesión del usuario por separado.
> 
> 

El siguiente es un ejemplo de respuesta a una solicitud de concesión de acceso que se denegó: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### <a name="rolling-app-keys-for-uninterrupted-access-to-the-graph-api-legacy"></a>Implementación de claves de aplicación para el acceso sin interrupciones a la API Graph (heredado)
A lo largo de la duración de la aplicación, puede que necesite cambiar las claves que usa cuando llama a Azure AD para adquirir un token de acceso para llamar a la API Graph.  El cambio de claves suele dividirse en dos categorías: sustitución de emergencia cuando la clave se encuentra en peligro o sustitución cuando la clave actual está a punto de expirar. Debe seguirse el siguiente procedimiento para proporcionar acceso ininterrumpido a la aplicación mientras se actualizan las claves (sobre todo en el segundo caso).

1. En el Portal de Azure clásico, haga clic en el inquilino de su directorio, haga clic en **Aplicaciones** en el menú superior y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.
2. Haga clic en **Configurar** en el menú superior para ver una lista de propiedades de la aplicación y verá una lista de las claves.
3. En Claves, haga clic en el menú desplegable **Seleccionar duración**, seleccione 1 o 2 años y haga clic en **Guardar** en la barra de comandos. De este modo, genera una nueva clave de contraseña para la aplicación. Copie esta nueva clave de contraseña. En este momento la aplicación puede usar tanto la clave nueva como la existente para obtener un token de acceso de Azure AD.
4. Vuelva a la aplicación y actualice la configuración para que empiece a usar la nueva clave de contraseña. Vea [Uso de la API Graph para consultar Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) si desea obtener un ejemplo de dónde debe suceder esta actualización.
5. Ahora debe implementar este cambio transversalmente en el entorno de producción (compruébelo primero en un nodo del servicio antes de implementarlo en el resto).
6. Cuando la actualización finalice en toda la implementación de producción, puede volver al Portal de Azure clásico y eliminar la clave anterior.

#### <a name="changing-app-properties-after-enabling-access-legacy"></a>Cambio de las propiedades de la aplicación después de habilitar el acceso (heredado)
Después de habilitar el acceso de los usuarios externos a la aplicación, puede seguir efectuando cambios en las propiedades de la aplicación en el Portal de Azure clásico. Sin embargo, los clientes a lo que ya se concedió acceso a la aplicación antes de realizar cambios en ella no verán los cambios reflejados al consultar los detalles sobre la aplicación en el Portal de Azure clásico. Cuando la aplicación esté a disposición de los clientes, tendrá que tener mucho cuidado al realizar determinados cambios. Por ejemplo, si actualiza el URI de id. de aplicación, los clientes existentes a los que se concedió acceso antes de este cambio no podrán iniciar sesión en la aplicación con su cuenta educativa o de la compañía.

Si realiza un cambio al parámetro RequestedPermissions para solicitar un mayor nivel de acceso, los clientes existentes que usen funcionalidad de la aplicación que ahora aprovecha las nuevas llamadas a API con este aumento nivel de acceso pueden obtener una respuesta de acceso denegado de la API Graph.  La aplicación debe controlar estos casos y pedir al cliente al cliente que conceda acceso a la aplicación con la solicitud de un nivel de acceso mayor.

## <a name="removing-an-application"></a>Eliminación de una aplicación
En esta sección se describe cómo quitar una aplicación del inquilino de Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Eliminación de una aplicación creada por su organización
Estas son las aplicaciones que se muestran con el filtro "Aplicaciones que tiene mi compañía" en la página principal de "Aplicaciones" para el inquilino de Azure AD. En términos técnicos, se trata de aplicaciones que se han registrado manualmente mediante el Portal de Azure clásico o mediante programación a través de PowerShell o la API Graph. Más específicamente, se representan mediante un objeto Application y ServicePrincipal en el inquilino. Para más información, consulte [Objetos Application y objetos ServicePrincipal](active-directory-application-objects.md) .

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para quitar una aplicación de un solo inquilino del directorio
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com)
2. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.
3. En el menú superior, haga clic en **Aplicaciones**y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.
4. Haga clic en el botón **Eliminar** de la barra de comandos.
5. Haga clic en **Sí** en el mensaje de confirmación.

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>Para quitar una aplicación multiempresa de su directorio
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com)
2. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.
3. En el menú superior, haga clic en **Aplicaciones**y luego en la aplicación que quiera configurar. Aparecerá la página Inicio rápido con el inicio de sesión único y otra información de configuración.
4. En la sección La aplicación es multiinquilino, haga clic en **No**. Esto hace que la aplicación sea de un solo inquilino, pero la aplicación seguirá estando en la organización que ya le dio su consentimiento.
5. Haga clic en el botón **Eliminar** de la barra de comandos.
6. Haga clic en **Sí** en el mensaje de confirmación.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Eliminación de una aplicación multiinquilino autorizada por otra organización
Este es un subconjunto de las aplicaciones que se muestran con el filtro "Aplicaciones que usa mi compañía" en la página principal de "Aplicaciones" para el inquilino de Azure AD, en concreto los que no aparecen en la lista "Aplicaciones que tiene mi compañía". En términos técnicos, son aplicaciones multiinquilino registradas durante el proceso de consentimiento. Más específicamente, se representan solo mediante un objeto ServicePrincipal en su inquilino. Para más información, consulte [Objetos Application y objetos ServicePrincipal](active-directory-application-objects.md) .

A fin de quitar el acceso de una aplicación multiinquilino a su directorio (después de concederle consentimiento), el administrador de la compañía debe tener una suscripción de Azure para quitar el acceso a través del Portal de Azure clásico. Simplemente vaya a la página de configuración de la aplicación y haga clic en el botón "Administrar el acceso" en la parte inferior. El administrador de la empresa también puede usar los [Cmdlets de Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151) para quitar el acceso.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md) para ver sugerencias de orientación visual para la aplicación.
* Para más detalles sobre la relación entre los objetos de aplicación y de entidad de servicio para una aplicación, consulte [Application Objects and Service Principal Objects](active-directory-application-objects.md) (Objetos de aplicación y de entidad de servicio).
* Para más información sobre el rol que desempeña el manifiesto de la aplicación, consulte [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md).
* Consulte el [glosario del desarrollador de Azure Active Directory](active-directory-dev-glossary.md) para ver definiciones de algunos de los conceptos fundamentales para el desarrollador de Azure Active Directory (AD).
* Visite la [guía del desarrollador de Active Directory](active-directory-developers-guide.md) para ver información general sobre todo el contenido de interés para los desarrolladores.




<!--HONumber=Nov16_HO3-->


