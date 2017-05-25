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
ms.date: 02/08/2017
ms.author: mbaldwin;bryanla
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e8cc9b790224891a0770b18fe2edb8e1bbfd5b72
ms.contentlocale: es-es
ms.lasthandoff: 03/31/2017


---
# <a name="integrating-applications-with-azure-active-directory"></a>Integración de aplicaciones con Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Los desarrolladores de la empresa y los proveedores de software como servicio (SaaS) pueden desarrollar aplicaciones de línea de negocio o servicios comerciales en la nube que se pueden integrar con Azure Active Directory (Azure AD) para ofrecer inicio de sesión seguro y autorización en sus servicios. Para integrar una aplicación o un servicio con Azure AD, un desarrollador debe registrar primero los detalles de la aplicación con Azure AD mediante el Portal de Azure clásico.

En este artículo se muestra cómo agregar, actualizar o quitar una aplicación en Azure AD. Aprenderá sobre los diferentes tipos de aplicaciones que se pueden integrar con Azure AD, cómo configurar las aplicaciones para que tengan acceso a otros recursos como las API web y mucho más.

Para más información sobre los dos objetos de Azure AD que representan una aplicación registrada y la relación entre ellos, consulte [Application Objects and Service Principal Objects](active-directory-application-objects.md) (Objetos de aplicación y de entidad de servicio). Para más información sobre las directrices de personalización de marca que debe usar al desarrollar aplicaciones con Azure Active Directory, consulte [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Agregar una aplicación
Cualquier aplicación que quiera usar las funciones de Azure AD debe registrarse primero en un inquilino de Azure AD. Este proceso de registro implica proporcionar los detalles de Azure AD sobre la aplicación, como la dirección URL donde se encuentra, la dirección URL para enviar respuestas cuando un usuario está autenticado, el URI que identifica la aplicación y así sucesivamente.

Si está creando una aplicación web que solo necesita admitir el inicio de sesión para usuarios en Azure AD, basta con que siga las instrucciones a continuación. Si la aplicación necesita credenciales o permisos para acceder a una API web o debe permitir el acceso a usuarios de otros inquilinos de Azure AD, consulte la sección [Actualización de una aplicación](#updating-an-application) para seguir configurando la aplicación.

### <a name="to-register-a-new-application-in-the-azure-portal"></a>Para registrar una aplicación nueva en Azure Portal
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el panel de navegación izquierdo, elija **Más servicios**, haga clic en **Registros de aplicaciones**y, luego, en **Agregar**.
4. Siga las indicaciones y cree una nueva aplicación. Si desea ejemplos específicos de aplicaciones web o aplicaciones nativa, visite nuestras [guías de inicio rápido](active-directory-developers-guide.md).
  * En el caso de las aplicaciones web, especifique la **URL de inicio de sesión**, que es la dirección URL base de la aplicación, donde los usuarios pueden iniciar sesión; por ejemplo, `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * En el caso de las aplicaciones nativas, especifique un **URI de redireccionamiento**, que utilizará Azure AD para devolver las respuestas de token. Escriba un valor específico para la aplicación, por ejemplo, `http://MyFirstAADApp`
5. Una vez que haya completado el registro, Azure AD asigna a la aplicación un identificador de cliente único, el identificador de aplicación. La aplicación se agrega y le llevará a la página Inicio rápido de la aplicación. En función de que se trate de una aplicación web o nativa, verá opciones diferentes para agregar otras opciones a la aplicación. Una vez agregada la aplicación, puede empezar a actualizarla para permitir que los usuarios inicien sesión, tengan acceso a las API web de otras aplicaciones o configuren una aplicación multiempresa (que permite que otras organizaciones tengan acceso a la aplicación).

> [!NOTE]
> De forma predeterminada, el registro de aplicaciones recién creadas está configurado para permitir que los usuarios del directorio inicien sesión en la aplicación.
> 
> 

## <a name="updating-an-application"></a>Actualización de una aplicación
Una vez registrada la aplicación con Azure AD, es posible que tenga que actualizarse para proporcionar acceso a las API web, ponerla a disposición de otras organizaciones y mucho más. En esta sección se describen distintas formas en que puede que tenga que realizar configuraciones adicionales en la aplicación. Primero comenzaremos con información general sobre el marco de consentimiento, que es importante comprender si va a crear aplicaciones de recursos o de API que van a consumir las aplicaciones cliente creadas por desarrolladores de su organización o de otra organización.

Para obtener más información sobre la forma en que la autenticación funciona en Azure AD, vea [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

### <a name="overview-of-the-consent-framework"></a>Información general sobre el marco de consentimiento
El marco de consentimiento de Azure AD facilita el desarrollo de aplicaciones cliente web y nativas multiinquilino que requieren acceso a API web protegidas por un inquilino de Azure AD diferente de aquel donde está registrada la aplicación cliente. Estas API web incluyen la API Graph de Microsoft (para acceder a Azure Active Directory, Intune y los servicios de Office 365) y otras API de servicios de Microsoft, además de sus propias API web. El marco se basa en que un usuario o un administrador da consentimiento a una aplicación que solicita el registro en su directorio, lo cual puede implicar el acceso a los datos de directorio.

Por ejemplo, si una aplicación cliente web debe leer información del calendario sobre el usuario desde Office 365, se pedirá a ese usuario que de su consentimiento a la aplicación cliente. Después de dar su consentimiento, la aplicación cliente podrá llamar a la API Graph de Microsoft en nombre del usuario y usar la información de calendario según sea necesario. La [API Graph de Microsoft](https://graph.microsoft.io) proporciona acceso a datos de Office 365 (como calendarios y mensajes de Exchange, sitios y listas de SharePoint, documentos de OneDrive, blocs de notas de OneNote, tareas de Planner, libros de trabajo de Excel, etc.), así como a usuarios y grupos de Azure AD y otros objetos de datos de más servicios en la nube de Microsoft. 

El marco de consentimiento se basa en OAuth 2.0 y sus distintos flujos, como la concesión de credenciales de cliente y la concesión de código de autorización, mediante clientes públicos o confidenciales. Mediante el uso de OAuth 2.0, Azure AD permite crear muchos tipos diferentes de aplicaciones cliente, como en un teléfono, tableta, servidor o una aplicación web, y obtener acceso a los recursos necesarios.

Para más información sobre el marco de consentimiento, consulte [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) y [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md); y para más información sobre cómo obtener acceso autorizado a Office 365 mediante Microsoft Graph, consulte [App authentication with Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview) (Autenticación de aplicaciones con Microsoft Graph).

#### <a name="example-of-the-consent-experience"></a>Ejemplo de la experiencia de consentimiento
Los siguientes pasos le mostrarán cómo funciona la experiencia de consentimiento para el desarrollador de la aplicación y el usuario.

1. En la página de configuración de la aplicación cliente web en Azure Portal, establezca los permisos que requiere la aplicación mediante los menús de la sección Permisos necesarios.
   
    ![Permisos para otras aplicaciones](./media/active-directory-integrating-applications/requiredpermissions.png)
2. Plantéese la posibilidad de que los permisos de la aplicación estén actualizados, la aplicación se ejecute y un usuario esté a punto de usarla por primera vez. Si la aplicación todavía no adquirió un token de acceso o de actualización, tiene que ir al extremo de autorización de Azure AD para obtener un código de autorización que sirve para adquirir un nuevo token de acceso y de actualización.
3. Si el usuario no está ya autenticado, se le pedirá que inicie sesión en Azure AD.
   
    ![Inicio de sesión de usuario o administrador en Azure AD](./media/active-directory-integrating-applications/usersignin.png)
4. Cuando el usuario inicie sesión, Azure AD determinará si se debe mostrar una página de consentimiento al usuario. Esta determinación se basa en que el usuario (o el administrador de la organización) ya concediese el consentimiento de la aplicación. Si todavía no se concedió el consentimiento, Azure AD se lo pedirá al usuario y mostrará los permisos necesarios para que funcione. El conjunto de permisos que aparecen en el cuadro de diálogo de consentimiento son los mismos que se seleccionaron en Permisos delegados en Azure Portal.
   
    ![Experiencia de consentimiento de usuario](./media/active-directory-integrating-applications/consent.png)
5. Después de que el usuario concede el consentimiento, se devuelve un código de autorización para la aplicación, que se puede canjear para adquirir un token de acceso y un token de actualización. Para más información sobre este flujo, consulte la [sección Aplicación web a API web](active-directory-authentication-scenarios.md#web-application-to-web-api) en [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

6. Como administrador, también puede dar su consentimiento para permisos delegados de una aplicación en nombre de todos los usuarios del inquilino. Esto impedirá que el cuadro de diálogo de consentimiento aparezca para cada usuario del inquilino. Puede hacer esto desde [Azure Portal](https://portal.azure.com) mediante la página de la aplicación. En la hoja **Configuración** de la aplicación, haga clic en **Permisos necesarios** y haga clic en el botón **Conceder permisos**. 

    ![Concesión de permisos para el consentimiento explícito del administrador](./media/active-directory-integrating-applications/grantpermissions.png)
    
> [!NOTE]
> Actualmente, es necesario conceder consentimiento explícito mediante el botón **Conceder permisos** para aplicaciones de una sola página (SPA) que usan ADAL.js, ya que el token de acceso se solicita sin un mensaje de consentimiento, lo que dará lugar a un error si no está ya concedido.   

### <a name="configuring-a-client-application-to-access-web-apis"></a>Configuración de una aplicación cliente para acceder a las API web
Para que una aplicación cliente web o confidencial pueda participar en un flujo de concesión de autorización que requiera autenticación (y obtener un token de acceso), debe establecer credenciales seguras. El método de autenticación predeterminado compatible con Azure Portal es un id. de cliente + una clave simétrica. En esta sección, se describen los pasos de configuración necesarios para proporcionar la clave secreta para las credenciales de cliente.

Además, para que un cliente pueda acceder a una API web expuesta por una aplicación de recursos (es decir, la API Graph de Microsoft), el marco de consentimiento garantizará que el cliente obtenga la concesión de los permisos necesarios en función de los solicitados. De forma predeterminada, todas las aplicaciones pueden elegir permisos de Azure Active Directory (API Graph) y de API de administración de servicios de Azure, con el permiso "Habilitar inicio de sesión y leer perfiles de usuario" de Azure AD ya seleccionado de forma predeterminada. Si la aplicación cliente está registrada en un inquilino de Azure AD de Office 365, las API web y los permisos de SharePoint y Exchange Online también estarán disponibles para seleccionarse. Puede seleccionar entre [dos tipos de permisos](active-directory-dev-glossary.md#permissions) en los menús desplegables situados junto a la API web que desee:

* Permisos de la aplicación: la aplicación cliente necesita acceso directo a la API web como sí misma (sin contexto de usuario). Este tipo de permiso requiere el consentimiento del administrador y no está disponible para aplicaciones cliente nativas.
* Permisos delegados: la aplicación cliente necesita acceso a la API web como el usuario que inició sesión, pero con acceso limitado por el permiso seleccionado. Este tipo de permiso lo puede conceder un usuario a menos que el permiso esté configurado como que requiere el consentimiento del administrador. 

> [!NOTE]
> La incorporación de un permiso delegado a una aplicación no concede consentimiento automáticamente a los usuarios del inquilino, tal y como se hacía en el Portal de Azure clásico. Los usuarios todavía deben consentir manualmente los permisos delegados agregados en tiempo de ejecución, a menos que el administrador haga clic en el botón **Conceder permisos** en la sección **Permisos necesarios** de la página de aplicación en Azure Portal. 

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>Para agregar credenciales o permisos para acceder a las API web
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el menú superior, elija **Azure Active Directory**, haga clic en **Registros de aplicaciones** y luego haga clic en la aplicación que desee configurar. Esto le llevará a la página de inicio rápido de la aplicación y se abrirá la hoja Configuración de dicha aplicación.
4. Para agregar una clave secreta para las credenciales de la aplicación web, haga clic en la sección "Claves" en la hoja Configuración.  
   
   * Agregue una descripción para la clave y seleccione 1 o 2 años de duración. 
   * La columna situada más a la derecha contendrá el valor de clave después de que guarde los cambios de configuración. Asegúrese de volver a esta sección y copiarla después de presionar Guardar, para poder usarla en la aplicación cliente durante la autenticación en tiempo de ejecución.
5. Para agregar permisos para acceder a las API de recursos desde el cliente, haga clic en "Permisos necesarios" en la hoja Configuración. 
   
   * Primero, haga clic en el botón "Agregar".
   * Haga clic en "Seleccionar una API" para seleccionar el tipo de recursos entre los que desea elegir.
   * Examine la lista de las API disponibles o use el cuadro de búsqueda para seleccionar entre las aplicaciones de recursos disponibles del directorio que exponen una API web. Haga clic en el recurso que le interese y luego en **Seleccionar**.
   * Una vez seleccionado, puede moverse al menú **Seleccionar permisos**, donde puede seleccionar el "Permisos de la aplicación" y "Permisos delegados" para la aplicación.
   
6. Cuando termine, haga clic en el botón **Listo**.

> [!NOTE]
> Al hacer clic en el botón **Listo** también se establecen automáticamente los permisos para la aplicación en el directorio en función de los permisos para otras aplicaciones que configuró.  Puede ver estos permisos de la aplicación examinando la hoja **Configuración** de la aplicación.
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configuración de una aplicación de recursos para exponer las API web
Puede desarrollar una API web y ponerla a disposición de las aplicaciones cliente si expone los [ámbitos](active-directory-dev-glossary.md#scopes) y los [roles](active-directory-dev-glossary.md#roles) de acceso. Una API web configurada correctamente se pone a disposición de otras aplicaciones del mismo modo que otras API web de Microsoft, incluidas la API Graph y las API de Office 365. Los ámbitos y los roles de acceso se exponen a través del [manifiesto de la aplicación](active-directory-dev-glossary.md#application-manifest), que es un archivo JSON que representa la configuración de identidad de la aplicación.  

En la siguiente sección, se mostrará cómo exponer ámbitos de acceso modificando el manifiesto de la aplicación de recursos.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Agregar ámbitos de acceso a la aplicación de recursos
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el menú superior, elija **Azure Active Directory**, haga clic en **Registros de aplicaciones** y luego haga clic en la aplicación que desee configurar. Esto le llevará a la página de inicio rápido de la aplicación y se abrirá la hoja Configuración de dicha aplicación.
4. Haga clic en **Manifiesto** en la página de la aplicación para abrir el editor de manifiestos en línea. 
5. Reemplace el nodo "oauth2Permissions" por el siguiente fragmento de código JSON. Este fragmento de código es un ejemplo de cómo exponer un ámbito conocido como "suplantación de usuario", que permite que un propietario de recursos proporcione a una aplicación cliente un tipo de acceso delegado a un recurso. Asegúrese de cambiar el texto y los valores para su propia aplicación:
   
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
6. Haga clic en **Guardar** para guardar el manifiesto. La API web ya está configurada para que la usen otras aplicaciones del directorio.

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>Para comprobar que la web API se expone a otras aplicaciones en el directorio
1. En el menú superior, haga clic en **Aplicaciones de registros**, seleccione la aplicación cliente cuyo acceso a la API web quiera configurar y navegue hasta la hoja Configuración.
2. En la sección **Permisos necesarios**, seleccione la API web para la que acaba de exponer un permiso. En el menú desplegable Permisos delegados, seleccione el nuevo permiso.

![Se muestran los permisos de la lista de tareas](./media/active-directory-integrating-applications/todolistpermissions.png)

#### <a name="more-on-the-application-manifest"></a>Más sobre el manifiesto de aplicación
El manifiesto de aplicación realmente actúa como un mecanismo para actualizar la entidad de aplicación, que define todos los atributos de configuración de identidad de una aplicación de Azure AD, incluidos los ámbitos de acceso de API que analizamos. Para más información sobre la entidad de aplicación, consulte la [documentación sobre la entidad de aplicación de API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Ahí encontrará completa información de referencia sobre los miembros de la entidad de aplicación utilizados para especificar los permisos para la API:  

* el miembro appRoles, que es una colección de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) que pueden usarse para definir los **permisos de aplicación** para una API web;  
* el miembro oauth2Permissions, que es una colección de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) que pueden usarse para definir los **permisos delegados** para una API web.

Para más información sobre los conceptos del manifiesto de aplicación en general, consulte [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Acceso a Azure AD Graph y Office 365 mediante las API Graph de Microsoft  
Como se mencionó anteriormente, además de exponer y tener acceso a API en sus propias aplicaciones de recursos, también puede actualizar la aplicación cliente para tener acceso a las API expuestas por los recursos de Microsoft.  La API Graph de Microsoft, que se denomina "Microsoft Graph" en la lista de permisos para otras aplicaciones, está disponible para todas las aplicaciones que están registradas en Azure AD. Si va a registrar la aplicación cliente en un inquilino de Azure AD que se ha aprovisionado por Office 365, puede tener acceso todos los permisos expuestos por las API Graph de Microsoft a varios recursos de Office 365.

Para ver una descripción completa sobre los ámbitos de acceso expuestos por la API Graph de Microsoft, consulte el artículo [Permission scopes | Microsoft Graph API concepts](https://graph.microsoft.io/docs/authorization/permission_scopes) (Ámbitos de permisos | Conceptos sobre la API Graph de Microsoft).

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
Si está escribiendo una aplicación que quiere poner a disposición de sus clientes o asociados externos a la organización, tendrá que actualizar la definición de la aplicación en Azure Portal.

> [!NOTE]
> Al habilitar el tipo multiinquilino, debe asegurarse de que el URI del identificador de la aplicación pertenece a un dominio comprobado. Además, la Dirección URL de retorno debe comenzar por https://. Para obtener más información, vea [Objetos de aplicación y objetos de entidad de servicio](active-directory-application-objects.md).
> 
> 

Para permitir que los usuarios externos accedan a la aplicación: 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el menú superior, elija **Azure Active Directory**, haga clic en **Registros de aplicaciones** y luego haga clic en la aplicación que desee configurar. Esto le llevará a la página de inicio rápido de la aplicación y se abrirá la hoja Configuración de dicha aplicación.
4. En la hoja Configuración, haga clic en **Propiedades** y coloque el conmutador **Multiinquilino** en la posición **Sí**.

Una vez realizado el cambio anterior, los usuarios y administradores de otras organizaciones podrán conceder acceso de la aplicación a sus directorios y otros datos.

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>Desencadenamiento del marco de consentimiento de Azure AD en tiempo de ejecución
Para utilizar el marco de consentimiento, las aplicaciones cliente multiinquilino deben solicitar autorización mediante OAuth 2.0. Hay [ejemplos de código](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) disponibles que muestran cómo una aplicación web, una aplicación nativa o una aplicación de demonio o de servidor solicita códigos de autorización y tokens de acceso para llamar a las API web.

Es posible que la aplicación web ofrezca también una experiencia de suscripción para los usuarios. Si realmente ofrece una experiencia de suscripción, se espera que el usuario haga clic en un botón de suscripción que redirigirá el explorador al punto de conexión de autorización de OAuth2.0 para Azure AD o a un punto de conexión de información del usuario de OpenID Connect. Estos extremos permiten que la aplicación obtenga información sobre el nuevo usuario inspeccionando el id_token. Después de la fase de inicio de sesión, se presentará al usuario una petición de consentimiento similar a la que se mostró anteriormente en la sección Información general sobre el marco de consentimiento.

Como alternativa, también es posible que la aplicación web ofrezca una experiencia que permite a los administradores "suscribir mi compañía". Esta experiencia también redirigiría al usuario al extremo de autorización de OAuth 2.0 para Azure AD. Sin embargo, en este caso se pasa un parámetro prompt=admin_consent al punto de conexión de la autorización para forzar la experiencia de consentimiento del administrador, donde el administrador concederá consentimiento en nombre de su organización. Solo un usuario que se autentique con una cuenta que pertenezca al rol Administrador global puede proporcionar el consentimiento; otros usuarios recibirán un error. Tras el consentimiento correcto, la respuesta contendrá admin_consent=true. Al canjear un token de acceso, también recibirá un id_token que proporcionará información sobre la organización y el administrador que se suscribió en la aplicación.

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Habilitación de la concesión implícita de OAuth 2.0 para aplicaciones de una sola página
Las aplicaciones de una sola página (SPA) normalmente tienen una estructura con un front-end de JavaScript que se ejecuta en el explorador, que llama al back-end de la API web de la aplicación para llevar a cabo su lógica empresarial. Para las SPA hospedadas en Azure AD, se usa la concesión implícita de OAuth 2.0 para autenticar al usuario en Azure AD y obtener un token que puede usar para proteger las llamadas desde el cliente JavaScript de la aplicación hasta su API web de back-end. Después de que el usuario haya dado su consentimiento, este mismo protocolo de autenticación se puede usar para obtener tokens para proteger las llamadas entre el cliente y otros recursos de API web configurados para la aplicación. Para conocer más sobre la concesión de autorización implícita y ayudarle a decidir si es adecuada para el escenario de su aplicación, consulte [Descripción del flujo de concesión implícita de OAuth2 de Azure Active Directory (AD) ](active-directory-dev-understanding-oauth2-implicit-grant.md).

De forma predeterminada, la concesión implícita de OAuth 2.0 está deshabilitada para las aplicaciones. Puede habilitarla para su aplicación si configura el valor `oauth2AllowImplicitFlow` en su [manifiesto de aplicación](active-directory-application-manifest.md), que es un archivo JSON que representa la configuración de identidad de la aplicación.

#### <a name="to-enable-oauth-20-implicit-grant"></a>Para habilitar la concesión implícita de OAuth 2.0
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el menú superior, elija **Azure Active Directory**, haga clic en **Registros de aplicaciones** y luego haga clic en la aplicación que desee configurar. Esto le llevará a la página de inicio rápido de la aplicación y se abrirá la hoja Configuración de dicha aplicación.
4. En la página de la aplicación, haga clic en **Manifiesto** para abrir el editor de manifiestos en línea.
   Busque y establezca el valor de "oauth2AllowImplicitFlow" en “true”. De forma predeterminada, es "false".
   
    `"oauth2AllowImplicitFlow": true,`
5. Guarde el manifiesto actualizado. Una vez guardado, la API web estará ahora configurada para usar la concesión implícita de OAuth 2.0 para autenticar a los usuarios.


## <a name="removing-an-application"></a>Eliminación de una aplicación
En esta sección se describe cómo quitar una aplicación del inquilino de Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Eliminación de una aplicación creada por su organización
Estas son las aplicaciones que se muestran con el filtro "Aplicaciones que tiene mi compañía" en la página principal de "Aplicaciones" para el inquilino de Azure AD. En términos técnicos, se trata de aplicaciones que se han registrado manualmente mediante el Portal de Azure clásico o mediante programación a través de PowerShell o la API Graph. Más específicamente, se representan mediante un objeto Application y ServicePrincipal en el inquilino. Para más información, consulte [Objetos Application y objetos ServicePrincipal](active-directory-application-objects.md) .

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para quitar una aplicación de un solo inquilino del directorio
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el menú superior, elija **Azure Active Directory**, haga clic en **Registros de aplicaciones** y luego haga clic en la aplicación que desee configurar. Esto le llevará a la página de inicio rápido de la aplicación y se abrirá la hoja Configuración de dicha aplicación.
4. En la página de la aplicación, haga clic en **Eliminar**.
5. Haga clic en **Sí** en el mensaje de confirmación.

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>Para quitar una aplicación multiempresa de su directorio
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD, seleccione una cuenta en la esquina superior derecha de la página.
3. En el menú superior, elija **Azure Active Directory**, haga clic en **Registros de aplicaciones** y luego haga clic en la aplicación que desee configurar. Esto le llevará a la página de inicio rápido de la aplicación y se abrirá la hoja Configuración de dicha aplicación.
4. En la hoja Configuración, elija **Propiedades** y coloque el conmutador **Multiinquilino** en la posición **No**. Esto hace que la aplicación sea de un solo inquilino, pero la aplicación seguirá estando en la organización que ya le dio su consentimiento.
5. Haga clic en el botón **Eliminar** desde la página de la aplicación.
6. Haga clic en **Sí** en el mensaje de confirmación.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Eliminación de una aplicación multiinquilino autorizada por otra organización
Este es un subconjunto de las aplicaciones que se muestran con el filtro "Aplicaciones que usa mi compañía" en la página principal de "Aplicaciones" para el inquilino de Azure AD, en concreto los que no aparecen en la lista "Aplicaciones que tiene mi compañía". En términos técnicos, son aplicaciones multiinquilino registradas durante el proceso de consentimiento. Más específicamente, se representan solo mediante un objeto ServicePrincipal en su inquilino. Para más información, consulte [Objetos Application y objetos ServicePrincipal](active-directory-application-objects.md) .

A fin de quitar el acceso de una aplicación multiinquilino a su directorio (después de concederle consentimiento), el administrador de la compañía debe tener una suscripción de Azure para quitar el acceso a través de Azure Portal. El administrador de la empresa también puede usar los [Cmdlets de Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151) para quitar el acceso.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md) para ver sugerencias de orientación visual para la aplicación.
* Para más detalles sobre la relación entre los objetos de aplicación y de entidad de servicio para una aplicación, consulte [Application Objects and Service Principal Objects](active-directory-application-objects.md) (Objetos de aplicación y de entidad de servicio).
* Para más información sobre el rol que desempeña el manifiesto de la aplicación, consulte [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md).
* Consulte el [glosario del desarrollador de Azure Active Directory](active-directory-dev-glossary.md) para ver definiciones de algunos de los conceptos fundamentales para el desarrollador de Azure Active Directory (AD).
* Visite la [guía del desarrollador de Active Directory](active-directory-developers-guide.md) para ver información general sobre todo el contenido de interés para los desarrolladores.


