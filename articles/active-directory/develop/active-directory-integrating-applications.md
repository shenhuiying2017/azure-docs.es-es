---
title: "Integración de aplicaciones con Azure Active Directory"
description: "Cómo agregar, actualizar o eliminar una aplicación en Azure Active Directory (Azure AD)."
services: active-directory
documentationcenter: 
author: PatAltimore
manager: mtillman
editor: mbaldwin
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: e398536ff6f660c75e4e063040eab33a831d65c6
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integración de aplicaciones con Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Los desarrolladores de la empresa y los proveedores de software como servicio (SaaS) pueden desarrollar aplicaciones de línea de negocio o servicios comerciales en la nube que se pueden integrar con Azure Active Directory (Azure AD) para ofrecer inicio de sesión seguro y autorización para los servicios. Para integrar una aplicación o un servicio con Azure AD, el desarrollador debe registrar primero la aplicación en Azure AD.

En este artículo se muestra cómo agregar, actualizar o eliminar el registro de una aplicación en Azure AD. Aprenderá sobre los diferentes tipos de aplicaciones que se pueden integrar con Azure AD, cómo configurar las aplicaciones para que tengan acceso a otros recursos como las API web y mucho más.

Para más información sobre los dos objetos de Azure AD que representan una aplicación registrada y la relación entre ellos, consulte [Application Objects and Service Principal Objects](active-directory-application-objects.md) (Objetos de aplicación y de entidad de servicio). Para más información sobre las directrices de personalización de marca que debe usar al desarrollar aplicaciones con Azure Active Directory, consulte [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Agregar una aplicación
Cualquier aplicación que quiera usar las funciones de Azure AD debe registrarse primero en un inquilino de Azure AD. Este proceso de registro implica proporcionar los detalles de Azure AD sobre la aplicación, como la dirección URL donde se encuentra, la dirección URL para enviar respuestas cuando un usuario está autenticado, el URI que identifica la aplicación y así sucesivamente.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Para registrar una aplicación nueva mediante Azure Portal
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.
3. En el panel de navegación izquierdo, haga clic en el servicio **Azure Active Directory**, haga clic en **Registros de aplicaciones** y haga clic en **Nuevo registro de aplicación**.

   ![Registro de una nueva aplicación](./media/active-directory-integrating-applications/add-app-registration.png)

4. Cuando aparece la página **Crear**, escriba la información de registro de la aplicación: 

  - **Nombre:** Escriba un nombre de aplicación significativo
  - **Tipo de aplicación:** 
    - Seleccione "Nativo" para [aplicaciones cliente](active-directory-dev-glossary.md#client-application) que están instaladas localmente en un dispositivo. Esta configuración se utiliza para [clientes nativos](active-directory-dev-glossary.md#native-client) públicos de OAuth.
    - Seleccione "Aplicación Web / API" para [aplicaciones cliente](active-directory-dev-glossary.md#client-application) y [aplicaciones de recursos/API](active-directory-dev-glossary.md#resource-server) para las que están instaladas en un servidor seguro. Esta configuración se utiliza para [clientes web](active-directory-dev-glossary.md#web-client) confidenciales de OAuth y [clientes basados en agente de usuario](active-directory-dev-glossary.md#user-agent-based-client) públicos. La misma aplicación también puede exponer tanto un cliente como un recurso o API.
  - **URL de inicio de sesión:** Para aplicaciones "Aplicación Web / API", proporcione la dirección URL base de la aplicación. Por ejemplo, `http://localhost:31544` podría ser la dirección URL de una aplicación web que se ejecuta en la máquina local. Los usuarios utilizan esta dirección URL para iniciar sesión en una aplicación cliente web. 
  - **URI de redireccionamiento:** para aplicaciones "Nativas", proporcione el identificador URI usado por Azure AD para devolver las respuestas de los tokens. Escriba un valor específico para la aplicación, por ejemplo, `http://MyFirstAADApp`

   ![Registro de una nueva aplicación: crear](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Si desea ejemplos específicos de aplicaciones web o aplicaciones nativa, visite nuestras [guías de inicio rápido](active-directory-developers-guide.md#get-started).

5. Cuando haya terminado, haga clic en **Crear**. Azure AD le asigna un identificador de aplicación único a la aplicación y le lleva a la página de registro principal de la aplicación. En función de que se trate de una aplicación web o nativa, se proporcionan opciones diferentes para agregar funcionalidades adicionales a la aplicación. Consulte la sección siguiente para obtener información general sobre el consentimiento y detalles acerca de cómo habilitar características de configuración adicionales en el registro de la aplicación (credenciales, permisos, habilitación del inicio de sesión para usuarios de otros inquilinos.)

  > [!NOTE]
  > De forma predeterminada, la aplicación recién registrada está configurada para permitir **solo** el inicio de sesión en la aplicación a los usuarios del mismo inquilino.
  > 
  > 

## <a name="updating-an-application"></a>Actualización de una aplicación
Una vez registrada la aplicación con Azure AD, es posible que tenga que actualizarse para proporcionar acceso a las API web, ponerla a disposición de otras organizaciones y mucho más. En esta sección se describen distintas formas adicionales en las que puede configurar la aplicación. Primero, comenzamos con información general sobre el marco de consentimiento, el cual es importante entender al crear aplicaciones que necesitan ser utilizadas por otros usuarios o aplicaciones.

### <a name="overview-of-the-consent-framework"></a>Información general sobre el marco de consentimiento

El marco de consentimiento de Azure AD facilita el desarrollo de aplicaciones web de varios inquilinos y aplicaciones de cliente nativo, incluidas las aplicaciones de varios niveles. Estas aplicaciones permiten el inicio de sesión por cuentas de usuario de un inquilino de Azure AD diferente al que ha registrado la aplicación. También pueden necesitar acceso a diferentes API web como la API Graph de Microsoft (para tener acceso a Azure Active Directory, Intune y los servicios de Office 365) y otras API de servicios de Microsoft, además de sus propias API web. El marco se basa en que un usuario o un administrador da consentimiento a una aplicación que solicita el registro en su directorio, lo cual puede implicar el acceso a los datos de directorio.

Por ejemplo, si una aplicación cliente web debe leer información del calendario sobre el usuario desde Office 365, se pide primero a ese usuario que de su consentimiento a la aplicación cliente. Después de dar su consentimiento, la aplicación cliente podrá llamar a la API Graph de Microsoft en nombre del usuario y usar la información de calendario según sea necesario. La [API Graph de Microsoft](https://graph.microsoft.io) proporciona acceso a datos de Office 365 (como calendarios y mensajes de Exchange, sitios y listas de SharePoint, documentos de OneDrive, blocs de notas de OneNote, tareas de Planner, libros de trabajo de Excel, etc.), así como a usuarios y grupos de Azure AD y otros objetos de datos de más servicios en la nube de Microsoft. 

El marco de consentimiento se basa en OAuth 2.0 y sus distintos flujos, como la concesión de credenciales de cliente y la concesión de código de autorización, mediante clientes públicos o confidenciales. Mediante el uso de OAuth 2.0, Azure AD permite crear muchos tipos diferentes de aplicaciones cliente, como en un teléfono, tableta, servidor o una aplicación web, y obtener acceso a los recursos necesarios.

Para más información acerca del uso del marco de consentimiento con concesiones de autorización de OAuth2.0, consulte [Autorización del acceso a aplicaciones web mediante OAuth 2.0 y Azure AD](active-directory-protocols-oauth-code.md) y [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md). Para obtener información sobre cómo obtener acceso autorizado a Office 365 a través de Microsoft Graph, consulte [Autenticación de la aplicación con Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Ejemplo de la experiencia de consentimiento

Los siguientes pasos muestran cómo funciona la experiencia de consentimiento para el desarrollador de la aplicación y para el usuario.

1. Suponga que tiene una aplicación de cliente web que necesita solicitar permisos concretos para tener acceso a un recurso o API. Obtendrá información sobre cómo realizar esta configuración en la sección siguiente, pero básicamente Azure Portal se utiliza para declarar solicitudes de permisos en el momento de la configuración. Al igual que otras opciones de configuración, pasan a formar parte del registro de Azure AD de la aplicación:
   
  ![Permisos para otras aplicaciones](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Plantéese la posibilidad de que los permisos de la aplicación estén actualizados, la aplicación se ejecute y un usuario esté a punto de usarla por primera vez. En primer lugar, la aplicación debe obtener un código de autorización del punto de conexión `/authorize` de Azure AD. El código de autorización, a continuación, se puede utilizar para adquirir un nuevo acceso y un token de actualización.

3. Si el usuario no está aún autenticado, el punto de conexión `/authorize` de Azure AD solicita el inicio de sesión.
   
  ![Inicio de sesión de usuario o administrador en Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. Cuando el usuario inicie sesión, Azure AD determinará si se debe mostrar una página de consentimiento al usuario. Esta determinación se basa en que el usuario (o el administrador de la organización) ya concediese el consentimiento de la aplicación. Si todavía no se concedió el consentimiento, Azure AD se lo pide al usuario y muestra los permisos necesarios para que funcione. El conjunto de permisos que aparecen en el cuadro de diálogo de consentimiento coinciden con los que se seleccionaron en Permisos delegados en Azure Portal.
   
  ![Experiencia de consentimiento de usuario](./media/active-directory-integrating-applications/consent.png)

5. Después de que el usuario concede el consentimiento, se devuelve un código de autorización a la aplicación, que se canjea para adquirir un token de acceso y un token de actualización. Para más información sobre este flujo, consulte la [sección Aplicación web a API web en Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. Como administrador, también puede dar su consentimiento para permisos delegados de una aplicación en nombre de todos los usuarios del inquilino. El consentimiento del administrador evita que el cuadro de diálogo de consentimiento aparezca para cada usuario del inquilino y se lleva a cabo en la página de la aplicación en [Azure Portal](https://portal.azure.com). En la página **Configuración** de la aplicación, haga clic en **Permisos necesarios** y haga clic en el botón **Conceder permisos**. 

  ![Concesión de permisos para el consentimiento explícito del administrador](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > La concesión explícita de consentimiento con el botón **Conceder permisos** se requiere actualmente para aplicaciones de página única (SPA) que usan ADAL.js. En caso contrario, se produce un error en la aplicación cuando se solicita el token de acceso.   

### <a name="configure-a-client-application-to-access-web-apis"></a>Configuración de una aplicación cliente para tener acceso a las API web
Para que una aplicación cliente web o confidencial pueda participar en un flujo de concesión de autorización que requiera autenticación (y obtener un token de acceso), debe establecer credenciales seguras. El método de autenticación predeterminado compatible con Azure Portal es un identificador de cliente y una clave secreta. En esta sección se describen los pasos de configuración necesarios para proporcionar la clave secreta para las credenciales de cliente.

Además, para que un cliente pueda tener acceso a una API web expuesta por una aplicación de recursos (como la API Graph de Microsoft), el marco de consentimiento garantiza que el cliente obtenga la concesión de los permisos necesarios en función de los solicitados. De forma predeterminada, todas las aplicaciones pueden elegir permisos de "Windows Azure Active Directory" (API Graph) y "Windows Azure Service Management API." El [permiso de "inicio de sesión y lectura del perfil de usuario" de la API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) también se selecciona de forma predeterminada. Si el cliente está registrado en un inquilino que tiene cuentas con suscripciones a Office 365, las API web y los permisos para SharePoint y Exchange Online están disponibles para seleccionarse. Puede seleccionar entre [dos tipos de permisos](active-directory-dev-glossary.md#permissions) para cada API web que desee:

- Permisos de la aplicación: la aplicación cliente necesita acceso directo a la API web como sí misma (sin contexto de usuario). Este tipo de permiso requiere el consentimiento del administrador y no está disponible para aplicaciones cliente nativas.

- Permisos delegados: la aplicación cliente necesita acceso a la API web como el usuario que inició sesión, pero con acceso limitado por el permiso seleccionado. Este tipo de permiso lo puede conceder un usuario a menos que el permiso requiera el consentimiento del administrador. 

  > [!NOTE]
  > La adición de un permiso delegado a una aplicación no concede consentimiento automáticamente a los usuarios del inquilino. Los usuarios todavía deben consentir manualmente los permisos delegados agregados en tiempo de ejecución, a menos que el administrador haga clic en el botón **Conceder permisos** en la sección **Permisos necesarios** de la página de la aplicación en Azure Portal. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Para agregar credenciales o permisos para tener acceso a las API web
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.
3. En el panel de navegación izquierdo, haga clic en el servicio **Azure Active Directory**, haga clic en **Registros de aplicaciones** y encuentre y haga clic en la aplicación que desea configurar.

   ![Actualización del registro de una aplicación](./media/active-directory-integrating-applications/update-app-registration.png)

4. Pasará a la página de registro principal de la aplicación, lo que abre la página **Configuración** de la aplicación. Para agregar una clave secreta a las credenciales de la aplicación web:
  - Haga clic en la sección **Claves** en la página **Configuración**.  
  - Agregue una descripción para la clave.
  - Seleccione uno o dos años de duración.
  - Haga clic en **Save**(Guardar). La columna situada más a la derecha contendrá el valor de clave después de que guarde los cambios de configuración. **Asegúrese de copiar la clave** para su uso en el código de la aplicación cliente, ya que no es accesible una vez se sale de esta página.

  ![Actualización del registro de una aplicación: claves](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Para agregar permisos para tener acceso a las API de recursos desde el cliente
  - Haga clic en la sección **Permisos necesarios** en la página **Configuración**. 
  - Haga clic en el botón **Agregar**.
  - Haga clic en **Seleccionar una API** para seleccionar el tipo de recursos entre los que desea elegir.
  - Examine la lista de las API disponibles o use el cuadro de búsqueda para seleccionar entre las aplicaciones de recursos disponibles del directorio que exponen una API web. Haga clic en el recurso que le interese y luego en **Seleccionar**.
  - Se le remitirá a la página **Habilitar acceso**. Seleccione los Permisos de la aplicación o Permisos delegados que necesita la aplicación al tener acceso a la API.
   
  ![Actualización del registro de una aplicación: api de permisos](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Actualización del registro de una aplicación: permisos](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Cuando termine, haga clic en el botón **Seleccionar** de la página **Habilitar acceso** y, a continuación, en el botón **Listo** de la página **Agregar acceso de API**. Se le redirigirá a la página **Permisos necesarios**, donde se agrega el nuevo recurso a la lista de las API.

  > [!NOTE]
  > Al hacer clic en el botón **Listo** también se establecen automáticamente los permisos para la aplicación en el directorio en función de los permisos para otras aplicaciones que configuró.  Puede ver estos permisos de la aplicación examinando la página **Configuración** de la aplicación.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configuración de una aplicación de recursos para exponer las API web

Puede desarrollar una API web y ponerla a disposición de las aplicaciones cliente si expone los [ámbitos](active-directory-dev-glossary.md#scopes) y los [roles](active-directory-dev-glossary.md#roles) de acceso. Una API web configurada correctamente se pone a disposición de otras aplicaciones del mismo modo que otras API web de Microsoft, incluidas la API Graph y las API de Office 365. Los ámbitos y los roles de acceso se exponen a través del [manifiesto de la aplicación](active-directory-dev-glossary.md#application-manifest), que es un archivo JSON que representa la configuración de identidad de la aplicación. 

En la siguiente sección se muestra cómo exponer ámbitos de acceso modificando el manifiesto de la aplicación de recursos.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Agregar ámbitos de acceso a la aplicación de recursos

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.

3. En el panel de navegación izquierdo, haga clic en el servicio **Azure Active Directory**, haga clic en **Registros de aplicaciones** y encuentre y haga clic en la aplicación que desea configurar.

   ![Actualización del registro de una aplicación](./media/active-directory-integrating-applications/update-app-registration.png)

4. Pasará a la página de registro principal de la aplicación, lo que abre la página **Configuración** de la aplicación. Cambie a la página **Editar manifiesto**, haciendo clic en **Manifiesto** desde la página de registro de la aplicación. Se abre un editor de manifiestos basado en web que le permite **Editar** el manifiesto desde el portal. Si lo desea, puede hacer clic en **Descargar**, editar de forma local y, a continuación, usar **Cargar** para volver a aplicarlo a la aplicación.

5. En este ejemplo, se expondrá un ámbito nuevo llamado `Employees.Read.All` en el recurso o API, agregando el siguiente elemento JSON a la colección `oauth2Permissions`. El ámbito `user_impersonation` existente se proporciona de forma predeterminada durante el registro. `user_impersonation` permite a una aplicación cliente solicitar permiso para tener acceso al recurso, bajo la identidad del usuario que inició sesión. No olvide agregar la coma después del elemento de ámbito `user_impersonation` existente y cambiar los valores de las propiedades para satisfacer las necesidades de su recurso. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > El valor del "identificador" debe generarse con una herramienta de generación de GUID como [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) o mediante programación. Representa un identificador único para el ámbito que se expone mediante la API web. Una vez que un cliente se configura apropiadamente con permisos para tener acceso a la API web, Azure AD emite un token de acceso de OAuth2.0. Cuando el cliente llama a la API web, presenta el token de acceso que tiene la notificación de ámbito (scp) establecida para los permisos solicitados en el registro de la aplicación.
  >
  > Puede exponer ámbitos adicionales posteriormente si es necesario. Tenga en cuenta que la API web podría exponer varios ámbitos asociados a diversas funciones diferentes. El recurso puede controlar el acceso a la API web en tiempo de ejecución, mediante la evaluación de las notificaciones de ámbito (`scp`) del token de acceso de OAuth 2.0 recibido.
  > 

6. Cuando termine, haga clic en **Guardar**. Ahora la API web está configurada para que la usen otras aplicaciones del directorio.  

  ![Actualización del registro de una aplicación](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Comprobación de que la web API se expone a otras aplicaciones del inquilino
1. Vuelva al inquilino de Azure AD, haga clic en **Registros de aplicación** de nuevo y, a continuación, busque y haga clic en la aplicación cliente que va a configurar.

   ![Actualización del registro de una aplicación](./media/active-directory-integrating-applications/update-app-registration.png)

2. Repita el paso 5 como hizo en [Configuración de una aplicación cliente para tener acceso a las API web](#configure-a-client-application-to-access-web-apis). Cuando llegue al paso **Seleccionar una API**, busque el recurso escribiendo el nombre de la aplicación en el campo de búsqueda y haga clic en **Seleccionar**. 

3. En la página **Habilitar acceso** debería ver el nuevo ámbito, disponible para las solicitudes de permiso de clientes.

  ![Se muestran los nuevos permisos](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Más sobre el manifiesto de aplicación

El manifiesto de aplicación realmente actúa como un mecanismo para actualizar la entidad de aplicación, que define todos los atributos de configuración de identidad de una aplicación de Azure AD, incluidos los ámbitos de acceso de API que analizamos. Para más información sobre la entidad Application y su esquema, consulte la [documentación sobre la entidad Application de la API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). El artículo contiene una completa información de referencia sobre los miembros de la entidad Application utilizados para especificar los permisos para la API, incluidos:  

- El miembro appRoles, que es una colección de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) usadas para definir los [permisos de aplicación](active-directory-dev-glossary.md#permissions) para una API web. 
- El miembro oauth2Permissions, que es una colección de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) usadas para definir los [permisos delegados](active-directory-dev-glossary.md#permissions) para una API web.

Para más información sobre los conceptos del manifiesto de aplicación en general, consulte [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Acceso a Azure AD Graph y Office 365 mediante las API Graph de Microsoft  

Como se mencionó anteriormente, además de exponer y tener acceso a las API en sus propias aplicaciones, también puede registrar la aplicación cliente para tener acceso a las API expuestas por los recursos de Microsoft. La API Graph de Microsoft, llamada "Microsoft Graph" en lista de recursos y API del portal, está disponible para todas las aplicaciones registradas en Azure AD. Si va a registrar la aplicación cliente en un inquilino que contiene cuentas registradas en una suscripción de Office 365, también puede tener acceso a los ámbitos expuestos por los distintos recursos de Office 365.

Para obtener una descripción completa sobre los ámbitos expuestos por la API Graph de Microsoft, consulte el artículo [Referencia de permisos de Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

> [!NOTE]
> Debido a una limitación actual, las aplicaciones cliente nativas solo pueden llamar a la API Graph de Azure AD si usan el permiso "Acceso al directorio de la organización". Esta restricción no se aplica a las aplicaciones web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configuración de aplicaciones multiempresa

Al registrar una aplicación en Azure AD, tal vez quiera que solo tengan acceso a la aplicación los usuarios de la organización. Como alternativa, tal vez quiera que los usuarios de organizaciones externas tengan acceso a la aplicación. Estos dos tipos de aplicaciones se denominan aplicaciones de un solo inquilino y aplicaciones multiinquilino. Esta sección describe cómo modificar la configuración de una aplicación de un solo inquilino para convertirla en una aplicación multiinquilino.

Es importante tener en cuenta las diferencias entre una aplicación de un solo inquilino y otra multiinquilino:  

- La finalidad de una aplicación de un solo inquilino es su uso en una sola organización. Suele tratarse de aplicaciones de línea de negocio (LOB) escritas por un desarrollador de la empresa. Una aplicación de un solo inquilino solo es accesible por usuarios con cuentas en el mismo inquilino que el de registro de aplicación. Como resultado, solo debe haberse aprovisionado en un directorio.
- La finalidad de una aplicación multiinquilino es su uso en muchas organizaciones. Se trata de aplicaciones web de software como servicio (SaaS) que suelen ser creadas por un proveedor de software independiente (ISV). Las aplicaciones multiinquilino deben haberse aprovisionado en cada inquilino en el que los usuarios necesiten tener acceso. Para los inquilinos distintos de aquél donde se registró la aplicación, es necesario para registrarlos el consentimiento de un usuario o administrador. Tenga en cuenta que las aplicaciones cliente nativas son multiinquilino de forma predeterminada porque están instaladas en el dispositivo del propietario del recurso. Consulte la sección [Información general sobre el marco de consentimiento](#overview-of-the-consent-framework) presentada anteriormente para más detalles sobre el marco de consentimiento.

Hacer que una aplicación sea multiinquilino requiere cambios tanto en el registro de la aplicación como en la propia aplicación web. Las secciones siguientes tratan ambos.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Cambio del registro de la aplicación para que sea multiinquilino

Si está escribiendo una aplicación que quiere poner a disposición de clientes o asociados externos a la organización, debe actualizar la definición de la aplicación en Azure Portal.

> [!IMPORTANT]
> Azure AD necesita que el URI del identificador de las aplicaciones multiinquilino sea único globalmente. El URI de id. de aplicación es una de las maneras en que una aplicación se identifica en los mensajes de protocolo. Cuando la aplicación es de un solo inquilino, es suficiente con que el URI de id. de aplicación sea único en dicho inquilino. En el caso de una aplicación multiempresa, debe ser único a nivel global de forma que Azure AD pueda encontrar la aplicación entre todos los inquilinos. El carácter globalmente único viene impuesto por la necesidad de que el URI de id. de aplicación tenga un nombre de host que coincida con un dominio comprobado del inquilino de Azure AD. Por ejemplo, si el nombre del inquilino es contoso.onmicrosoft.com, un URI del identificador de aplicación válido sería https://contoso.onmicrosoft.com/myapp. Si el inquilino tiene el dominio comprobado contoso.com, un URI del identificador de aplicación válido también sería https://contoso.com/myapp. Si el URI de id. de aplicación no sigue este patrón, la configuración de una aplicación como multiinquilino dará error.
> 

Para dar a los usuarios externos acceso a la aplicación: 

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.
3. En el panel de navegación izquierdo, haga clic en el servicio **Azure Active Directory**, haga clic en **Registros de aplicaciones** y encuentre y haga clic en la aplicación que desea configurar. Pasará a la página de registro principal de la aplicación, lo que abre la página **Configuración** de la aplicación.
4. En la página **Configuración**, haga clic en **Propiedades** y coloque el conmutador **Multiinquilino** en la posición **Sí**.

Después de realizar los cambios, los usuarios y administradores de otras organizaciones pueden conceder a los usuarios la posibilidad de iniciar sesión en la aplicación, permitiendo que la aplicación tenga acceso a recursos protegidos por su inquilino.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Cambio de la aplicación para que sea multiinquilino

La compatibilidad para aplicaciones multiinquilino se basa principalmente en el marco de consentimiento de Azure AD. El consentimiento es el mecanismo que permite que un usuario de otro inquilino conceda a la aplicación acceso a recursos protegidos por el inquilino del usuario. Esta experiencia se conoce como "consentimiento del usuario."

La aplicación web también puede ofrecer:

- La capacidad para que los administradores puedan "suscribir mi compañía." Esta experiencia, que se conoce como "consentimiento del administrador", ofrece al administrador la funcionalidad de conceder consentimiento en nombre de *todos los usuarios* de su organización. Solo un usuario que se autentique con una cuenta que pertenezca al rol Administrador global puede proporcionar el consentimiento del administrador; otros usuarios reciben un error.

- Una experiencia de inicio de sesión para los usuarios. Se espera que se proporcione al usuario un botón "Inicio de sesión" que redirigirá el explorador al punto de conexión `/authorize` de OAuth2.0 de Azure AD o al punto de conexión `/userinfo` de OpenID Connect. Estos extremos permiten que la aplicación obtenga información sobre el nuevo usuario inspeccionando el id_token. Después de la fase de inicio de sesión, se presentará al usuario una petición de consentimiento similar a la que se mostró en la sección [Información general sobre el marco de consentimiento](#overview-of-the-consent-framework).

Para más información sobre los cambios de aplicación necesarios para admitir el acceso multiinquilino y experiencias de inicio de sesión y suscripción, consulte:

- [Inicio de sesión de cualquier usuario de Azure Active Directory (AD) mediante el patrón de aplicación multiempresa](active-directory-devhowto-multi-tenant-overview.md)
- La lista de [ejemplos de código multiinquilino](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Guía de inicio rápido: Incorporación de personalización de marca de empresa a la página de inicio de sesión de Azure AD](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Habilitación de la concesión implícita de OAuth 2.0 para aplicaciones de una sola página

Las aplicaciones de una sola página (SPA) normalmente tienen una estructura con un front-end de JavaScript que se ejecuta en el explorador, que llama al back-end de la API web de la aplicación para llevar a cabo la lógica empresarial. Para las SPA hospedadas en Azure AD, se usa la concesión implícita de OAuth 2.0 para autenticar al usuario en Azure AD y obtener un token que puede usar para proteger las llamadas desde el cliente JavaScript de la aplicación hasta la API web de back-end. 

Después de que el usuario haya dado su consentimiento, este mismo protocolo de autenticación se puede usar para obtener tokens para proteger las llamadas entre el cliente y otros recursos de API web configurados para la aplicación. Para conocer más sobre la concesión de autorización implícita y ayudarle a decidir si es adecuada para el escenario de su aplicación, consulte [Descripción del flujo de concesión implícita de OAuth2 de Azure Active Directory (AD)](active-directory-dev-understanding-oauth2-implicit-grant.md).

De forma predeterminada, la concesión implícita de OAuth 2.0 está deshabilitada para las aplicaciones. Puede habilitar la concesión implícita de OAuth 2.0 para la aplicación estableciendo el valor `oauth2AllowImplicitFlow` en el [manifiesto de aplicación](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Para habilitar la concesión implícita de OAuth 2.0

> [!NOTE]
> Para más información sobre cómo editar el manifiesto de aplicación, asegúrese primero de revisar la sección anterior, [Configuración de una aplicación de recursos para exponer las API web](#configuring-a-resource-application-to-expose-web-apis).
>

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.
3. En el panel de navegación izquierdo, haga clic en el servicio **Azure Active Directory**, haga clic en **Registros de aplicaciones** y encuentre y haga clic en la aplicación que desea configurar. Pasará a la página de registro principal de la aplicación, lo que abre la página **Configuración** de la aplicación.
4. Cambie a la página **Editar manifiesto**, haciendo clic en **Manifiesto** desde la página de registro de la aplicación. Se abre un editor de manifiestos basado en web que le permite **Editar** el manifiesto desde el portal. Busque y establezca el valor de "oauth2AllowImplicitFlow" en "true." De forma predeterminada, se establece en "false."
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Guarde el manifiesto actualizado. Una vez guardado, la API web estará ahora configurada para usar la concesión implícita de OAuth 2.0 para autenticar a los usuarios.

## <a name="removing-an-application"></a>Eliminación de una aplicación
En esta sección se describe cómo eliminar el registro de una aplicación del inquilino de Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Eliminación de una aplicación creada por su organización
Las aplicaciones que su organización ha registrado aparecen en el filtro "Mis aplicaciones" en la página principal "Registros de aplicación" del inquilino. Se trata de las aplicaciones que ha registrado manualmente mediante Azure Portal o mediante programación a través de PowerShell o la API Graph. Más específicamente, se representan mediante un objeto Application y ServicePrincipal en el inquilino. Para obtener más información, vea [Objetos de aplicación y objetos de entidad de servicio](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para eliminar una aplicación de un solo inquilino del directorio
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.
3. En el panel de navegación izquierdo, haga clic en el servicio **Azure Active Directory**, haga clic en **Registros de aplicaciones** y encuentre y haga clic en la aplicación que desea configurar. Pasará a la página de registro principal de la aplicación, lo que abre la página **Configuración** de la aplicación.
4. En la página de registro principal de la aplicación, haga clic en **Eliminar**.
5. Haga clic en **Sí** en el mensaje de confirmación.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Para eliminar una aplicación multiinquilino de su directorio particular
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.
3. En el panel de navegación izquierdo, haga clic en el servicio **Azure Active Directory**, haga clic en **Registros de aplicaciones** y encuentre y haga clic en la aplicación que desea configurar. Pasará a la página de registro principal de la aplicación, lo que abre la página **Configuración** de la aplicación.
4. Desde la página **Configuración**, elija **Propiedades** y coloque el conmutador **Multiinquilino** en la posición **No**, para cambiar primero la aplicación a un solo inquilino y, a continuación, haga clic en **Guardar**. Los objetos de la entidad de servicio de la aplicación permanecen en los inquilinos de todas las organizaciones que ya han dado su consentimiento.
5. Haga clic en el botón **Eliminar** desde la página de registro principal de la aplicación.
6. Haga clic en **Sí** en el mensaje de confirmación.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Eliminación de una aplicación multiinquilino autorizada por otra organización
Un subconjunto de las aplicaciones que se muestran en el filtro "Todas las aplicaciones" (sin incluir los registros de "Mis aplicaciones") en la página principal "Registros de aplicación" de su inquilino, son aplicaciones multiinquilino. En términos técnicos, estas aplicaciones multiinquilino proceden de otro inquilino y se registraron en su inquilino durante el proceso de consentimiento. Más específicamente, se representan solo mediante un objeto de entidad de servicio en su inquilino, sin un objeto aplicación correspondiente. Para más información sobre las diferencias entre objetos de aplicación y objetos de entidad de servicio, consulte [Objetos de aplicación y de entidad de servicio en Azure AD](active-directory-application-objects.md).

Para eliminar el acceso de una aplicación multiinquilino a su directorio (después de concederle consentimiento), el administrador de la compañía debe eliminar su entidad de servicio. El administrador debe tener acceso de administrador global y puede usar a través de Azure Portal los [Cmdlets de PowerShell de Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151) para eliminar el acceso.

## <a name="next-steps"></a>pasos siguientes
- Para más información sobre el funcionamiento de la autenticación en Azure AD, consulte [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).
- Consulte [Directrices de personalización de marca para aplicaciones integradas](active-directory-branding-guidelines.md) para ver sugerencias de orientación visual para la aplicación.
- Para más información sobre la relación entre los objetos de aplicación y de entidad de servicio para una aplicación, consulte [Objetos de aplicación y de entidad de servicio](active-directory-application-objects.md).
- Para más información sobre el rol que desempeña el manifiesto de la aplicación, consulte [Descripción del manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md).
- Consulte el [glosario del desarrollador de Azure Active Directory](active-directory-dev-glossary.md) para ver definiciones de algunos de los conceptos fundamentales para el desarrollador de Azure Active Directory (AD).
- Consulte la [Guía del desarrollador de Active Directory](active-directory-developers-guide.md) para información general sobre todo el contenido de interés para los desarrolladores.

