<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Integrating Multi-Tenant Cloud Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# Integración de aplicaciones multiempresa en la nube con Azure Active Directory

## <a name="introduction"></a>Introducción

Azure Active Directory (Azure AD) es un servicio moderno basado en REST que ofrece capacidades de administración de identidades y control de acceso para las aplicaciones en la nube. Azure AD se integra fácilmente con los servicio en la nube, así como con Azure, Microsoft Office 365, Dynamics CRM Online y Windows Intune. Las implementaciones locales de Active Directory también pueden aprovechar al máximo Azure AD. Para obtener más información, consulte la [página de identidades][página de identidades] en [windowsazure.com][windowsazure.com].

Este tutorial está destinado a los desarrolladores de .NET que deseen integrar una aplicación multiempresa con Azure AD. Aprenderá a:

-   Permitir que los clientes inicien sesión en la aplicación usando Azure AD
-   Habilitar el inicio de sesión único (SSO) con Azure AD
-   Consultar los datos del directorio de un cliente usando la API Graph de Azure AD

La aplicación complementaria de ejemplo de este tutorial se puede [descargar aquí][descargar aquí]. El ejemplo se puede ejecutar sin cambios, aunque puede que tenga que modificar la [asignación de puertos en Visual Studio][asignación de puertos en Visual Studio] para que use https. Siga las instrucciones del vínculo y configure el protocolo de enlace como "https" en la sección enlaces del archivo ApplicationHost.config. Todos los fragmentos de código de los pasos que aparecen a continuación se han extraído del ejemplo.

> [WACOM.NOTE]
> La aplicación de directorio multiempresa de ejemplo tiene fines ilustrativos únicamente. Este ejemplo (incluyendo sus clases de biblioteca auxiliar) no debe usarse en producción.

### Requisitos previos

A este tutorial se aplican los siguientes requisitos previos de desarrollador:

-   [Visual Studio 2012][Visual Studio 2012]
-   [Servicios de datos WCF para OData][Servicios de datos WCF para OData]

### Tabla de contenido

-   [Introducción][Introducción]
-   [Parte 1: Obtener un Id. de cliente para el acceso a Azure AD][Parte 1: Obtener un Id. de cliente para el acceso a Azure AD]
-   [Parte 2: Permitir que los clientes inicien sesión usando Azure AD][Parte 2: Permitir que los clientes inicien sesión usando Azure AD]
-   [Parte 3: Habilitar el inicio de sesión único][Parte 3: Habilitar el inicio de sesión único]
-   [Parte 4: Obtener acceso a Azure AD Graph][Parte 4: Obtener acceso a Azure AD Graph]
-   [Parte 5: Publicar la aplicación][Parte 5: Publicar la aplicación]
-   [Resumen][Resumen]

## <a name="getclientid"></a>Parte 1: Obtener un Id. de cliente para el acceso a Azure AD

En esta sección de indica cómo obtener un Id. y un secreto de cliente después de haber creado una cuenta del Panel de vendedores de Microsoft. El Id. de cliente es el único identificador de la aplicación y el secreto de cliente es la clave asociada necesaria para realizar solicitudes usando el Id. de cliente. Ambos son necesarios para integrar la aplicación con Azure AD.

### Paso 1: Crear una cuenta con el Panel de vendedores de Microsoft

Para desarrollar y publicar aplicaciones que se integren con Azure AD, debe registrarse en una cuenta del [Panel de vendedores de Microsoft][Panel de vendedores de Microsoft]. Después se le pedirá que [cree un perfil de cuenta][cree un perfil de cuenta] como empresa o como particular. Este perfil se usa para publicar aplicaciones en Azure Marketplace, o en otros, y es necesario para generar un Id. y un secreto de cliente.

Las nuevas cuentas se ponen en estado "Account Pending Approval". Este estado no impide el inicio del desarrollo, puesto que puede seguir creando un Id. de cliente, así como una lista preliminar de aplicaciones. Sin embargo, la lista de aplicaciones solo se puede enviar para su aprobación cuando la cuenta en sí se haya aprobado. Solo los clientes podrán ver la lista de aplicaciones enviadas en Azure Marketplace una vez que se haya aprobado.

### Paso 2: Obtener un Id. de cliente para la aplicación

Necesitará un Id. y un secreto de cliente para integrar la aplicación con Azure AD. El Id. de cliente es el identificador único de la aplicación y se usa principalmente para identificar una aplicación para inicio de sesión único o para autenticar las llamadas a Azure AD Graph. Para obtener más información acerca de cómo obtener un id. y un secreto de cliente, consulte [Creación de secretos e identificadores de cliente en el Panel de vendedores de Microsoft][Creación de secretos e identificadores de cliente en el Panel de vendedores de Microsoft].

> [WACOM.NOTE]
> Necesitará el Id. y el secreto de cliente más adelante en este tutorial; asegúrese de registrarlos.

Para generar un Id. y un secreto de cliente, tendrá que especificar las siguientes propiedades en el Panel de vendedores de Microsoft:

**App Domain**: El nombre de host de la aplicación, por ejemplo "contoso.com". Esta propiedad no puede contener números de puerto. Durante el desarrollo, esta propiedad debe configurarse como "localhost".

**App Redirect URL**: Dirección URL de redireccionamiento a la que Azure AD enviará una respuesta después de que el usuario inicie sesión y cuando una organización haya autorizado la aplicación, por ejemplo: "<https://contoso.com/>." Durante el desarrollo, esta propiedad debe establecerse en "<https://localhost>:\<número de puerto\>"

### Paso 3: Configurar la aplicación para que use el Id. y el secreto de cliente

Este paso requiere el Id. y el secreto de cliente generados durante la suscripción del Panel de vendedores. El Id. de cliente se usa para el inicio de sesión único y tanto el Id. como el secreto de cliente se usarán más adelante para obtener un token de acceso que llame a la API Graph de Azure AD.

La aplicación de ejemplo se ha preconfigurado para usar Azure AD y carga el Id. y el secreto de cliente desde config. Realice los cambios siguientes en el archivo **Web.config** de la aplicación de ejemplo:

1.  En el nodo **appSettings**, reemplace los valores de "clientId" y "SymmetricKey" por el Id. de cliente, el secreto de cliente y el dominio de la aplicación:

        <appSettings>
            <add key="clientId" value="(Your Client ID value)"/>
            <add key="SymmetricKey" value="(Your Client Secret value)"/>
            <add key="AppHostname" value="(Your App Domain)"/>
        </appSettings>

2.  En el nodo **audienceUris** de **system.identityModel**, inserte el Id. de cliente después de "spn:":

        <system.identityModel>
            <audienceUris>
                <add value="spn:(Your Client ID value)" />
            </audienceUris>

## <a name="enablesignup"></a>Parte 2: Permitir que los clientes inicien sesión usando Azure AD

En esta sección se indica cómo permitir que los clientes inicien sesión en la aplicación usando Azure AD. Antes de que los clientes puedan usar una aplicación integrada con Azure AD, el administrador de inquilinos debe autorizarla. Este proceso de autorización se inicia con una solicitud de consentimiento de la aplicación a Azure, lo que genera una respuesta que la aplicación debe controlar. Los pasos siguientes describen cómo generar una solicitud de consentimiento y controlar la respuesta.

Los pasos de esta sección usan clases auxiliares de la aplicación de ejemplo. Estas clases se encuentran en la biblioteca *Microsoft.IdentityModel.WAAD.Preview* del ejemplo y facilitan que pueda centrarse en el código de la aplicación en lugar de en los detalles de identidad y protocolo.

### Paso 1: Solicitar consentimiento para la aplicación

La interacción del ejemplo siguiente muestra el proceso de solicitud de consentimiento para la aplicación:

1.  El cliente hace clic en el vínculo "sign up using Azure AD" de una página web de la aplicación.
2.  Redirigirá al cliente hacia la página de autorización de Azure AD con la información de la aplicación anexada a la solicitud.
3.  El cliente da su consentimiento para la aplicación o lo deniega.
4.  Azure AD lleva al cliente a la dirección URL de redireccionamiento de la aplicación especificada. Ha especificado esta dirección URL al generar un Id. y un secreto de cliente en el Panel de vendedores de Microsoft. Si se ha concedido el consentimiento, la solicitud de redireccionamiento indica el resultado de la solicitud de consentimiento, incluyendo la información sobre su inquilino.

A fin de generar la solicitud de redireccionamiento en el paso 2 anterior, debe anexar los parámetros de cadena de consulta a la siguiente dirección URL para la página de autorización de Azure AD: *<http://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx>*

Los parámetros de cadena de consulta se describen a continuación:

**ApplicationID**: (obligatorio) Valor **ClientID** que ha recibido en el Panel de vendedores.

**RequestedPermissions**: (Opcional) Permisos que el inquilino debe conceder a la aplicación.
Durante el desarrollo, estos permisos se usan para probar las aplicaciones no publicadas. Este parámetro se ignora en el caso de las aplicaciones publicadas. Y en vez de lo anterior, se usarán los permisos solicitados en la lista de aplicaciones. Vea la parte 5 para obtener más información acerca de esta lista.
Hay tres valores posibles para este parámetro:

**DirectoryReader**: Concede el permiso para leer datos de directorio, como cuentas de usuario, grupos e información acerca de la organización. Habilita el inicio de sesión único.

**UserAccountAdministrator**: Concede permiso para leer y escribir datos, como cuentas de usuario, grupos e información acerca de la organización. Habilita el inicio de sesión único.

**None**: Habilita el inicio de sesión único, pero deshabilita el acceso a los datos del directorio.

El valor predeterminado es "None" si el parámetro no se especifica o se especifica de forma incorrecta.

A continuación, encontrará un ejemplo de dirección URL de solicitud de consentimiento válida:
*<https://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx?ApplicationId=33E48BD5-1C3E-4862-BA79-1C0D2B51FB26&RequestedPermissions=DirectoryReader>*

En la aplicación de ejemplo, el vínculo "Register" contiene una dirección URL similar para la solicitud del consentimiento, como se indica a continuación:

![inicio de sesión][inicio de sesión]

> [WACOM.NOTE]
> Al probar la aplicación sin publicar, vivirá una experiencia de consentimiento similar a la de los clientes. Sin embargo, la página de autorización para una aplicación sin publicar tiene un aspecto diferente al de la página de autorización para una aplicación publicada. Una aplicación publicada muestra el nombre, el logotipo y los detalles del publicador de la aplicación, mientras que una aplicación sin publicar no lo hace.

### Paso 2: Controlar la respuesta de consentimiento

Después de que un cliente conceda su consentimiento para la aplicación o lo deniegue, Azure AD envía una respuesta a la dirección URL de redireccionamiento de la aplicación. Esta respuesta contiene los parámetros de cadena de consulta siguientes:

**TenantId**: El GUID único del inquilino de Azure AD que ha autorizado la aplicación. Este parámetro solo se especificará si el cliente ha autorizado la aplicación.

**Consent**: El valor se configurará en "Granted" si la aplicación se ha autorizado, o en "Denied" si la solicitud se ha rechazado.

A continuación, encontrará un ejemplo de respuesta válida a la solicitud de consentimiento que indica que se ha autorizado la aplicación:
*<https://app.litware.com/redirect.aspx&TenantId=7F3CE253-66DB-4AEF-980A-D8312D76FDC2&Consent=Granted>*

La aplicación tendrá que mantener el contexto de forma que la solicitud enviada a la página de autorización de Azure AD se relacione con la respuesta (y rechace las respuestas que no tengan una solicitud asociada).

<div class="dev-callout"><strong>Nota:</strong><p>Una vez concedido el consentimiento, Azure AD puede tardar unos instantes antes de que se aprovisionen el acceso a Graph y el SSO. El primer usuario de cada organizaci&oacute;n que inicie sesi&oacute;n en la aplicaci&oacute;n podr&iacute;a ver errores de inicio de sesi&oacute;n hasta que termine el aprovisionamiento.</p></div>

Una vez que el cliente haya concedido su consentimiento a la aplicación, es importante asociar y almacenar el inquilino recién creado en la aplicación con el TenantId devuelto por la respuesta de consentimiento. La aplicación de ejemplo contiene un *HttpModule* en el espacio de nombres *Microsoft.IdentityModel.WAAD.Preview.Consent* que registra automáticamente el TenantId en un almacén de datos customer/TenantId de todas las respuestas de consentimiento correctas. El código se incluye más abajo y el método *TrustedIssuers.Add* realiza el registro de TenantId en un almacén de datos customer/TenantId:

    private void Application_BeginRequest(Object source,
             EventArgs e)
    {
        HttpApplication application = (HttpApplication)source;
        HttpRequest req = application.Context.Request;             

        if((!string.IsNullOrEmpty(req.QueryString["TenantId"]) && (!string.IsNullOrEmpty(req.QueryString["Consent"]))))
        { 
            if(req.QueryString["Consent"].Equals("Granted",StringComparison.InvariantCultureIgnoreCase))
            {
                // For this sample we store the consenting tenants in
                // an XML file. We strongly recommend that you change
                // this to use your DataStore
                TrustedIssuers.Add(req.QueryString["TenantId"]; 
            }
        }            
    }

Antes de poder probar el código de respuesta/solicitud de consentimiento para la aplicación, debe obtener un inquilino de directorio de Azure AD.

### Paso 3: Obtener un inquilino de Azure AD para probar la aplicación

Necesitará un inquilino de Azure AD para probar la capacidad de integración con Azure AD de la aplicación. Si ya usa un inquilino para probar otra aplicación, puede volver a utilizarlo. Le recomendamos que se haga con dos inquilinos como mínimo para garantizar que la aplicación la puedan probar y usar varios inquilinos. No recomendamos usar un inquilino de producción para este fin. [Obtención de un inquilino de Azure AD][Obtención de un inquilino de Azure AD].

Una vez que se haya hecho con un inquilino de Azure AD, podrá crear y ejecutar la aplicación presionando **F5**. Además, puede intentar iniciar sesión en la aplicación usando el nuevo inquilino.

<div class="dev-callout"><strong>Nota:</strong><p>Si los clientes inician sesi&oacute;n en un nuevo inquilino de Azure AD, el aprovisionamiento del inquilino podr&iacute;a tardar unos instantes. Puede que los usuarios vean errores en la p&aacute;gina de consentimiento hasta que termine el aprovisionamiento.</p></div>

## <a name="enablesso"></a>Parte 3: Habilitar el inicio de sesión único

En esta sección se indica cómo habilitar el inicio de sesión único (SSO). El proceso comienza con la construcción de una solicitud de inicio de sesión en Azure AD que autentica a un usuario en la aplicación y, después, comprueba que el cliente pertenezca a un inquilino que ha autorizado la aplicación en la respuesta de inicio de sesión. La solicitud de inicio de sesión requiere el Id. de cliente del Panel de vendedores y el Id. de inquilino de la organización del cliente.

La solicitud de inicio de sesión es específica para un inquilino de directorio y debe incluir un TenantID. Un TenantID se puede determinar a partir del nombre de dominio del inquilino de un directorio de Azure AD. Son dos las formas habituales de conseguir este nombre de dominio desde el usuario final mientras inicia sesión:

-   Si la dirección URL de la aplicación es *<https://contoso.myapp.com>* o *<https://myapp.com/contoso.com>*, *contoso* y *contoso.com* representan los nombres de dominio de Azure AD y *myapp.com* representa la dirección URL de la aplicación.
-   La aplicación podría pedir al usuario la dirección de correo electrónico o el nombre de dominio de Azure AD. Este enfoque se usa en la aplicación de ejemplo, donde el usuario debe especificar el nombre de dominio de Azure AD, como se indica a continuación:

![inicio de sesión][inicio de sesión]

### Paso 1: Buscar el Id. de inquilino

Mediante el nombre de dominio de Azure AD que ha proporcionado el cliente, puede buscar el Id. de inquilino analizando los metadatos de federación de Azure AD. En la aplicación de ejemplo, el método *Domain2TenantId* de la clase *Microsoft.IdentityModel.WAAD.Preview.TenantUtils.Globals* controla este proceso.

Para ilustrar este proceso, los pasos siguientes usan el nombre de dominio contoso.com.

1.  Obtenga el archivo **FederationMetadata.xml** para el inquilino de Azure AD. Por ejemplo,
    *<https://accounts.accesscontrol.windows.net/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml>*
2.  En el archivo **FederationMetadata.xml**, busque la entrada **Entity Descriptor**. El Id. de inquilino se incluye como parte de la propiedad **entityID** que sigue a "<https://sts.windows.net>" como se indica a continuación:

         <EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sts.windows.net/a7456b11-6fe2-4e5b-bc83-67508c201e4b/" ID="_cba45203-f8f4-4fc3-a3bb-0b136a2bafa5"> 

    En este caso, el valor TenantID es **a7456b11-6fe2-4e5b-bc83-67508c201e4b**.

3.  En el almacén de datos customer/TenantId de la aplicación, debe almacenar el dominio y el TenantID asociado. Estos dos valores se pueden usar juntos para futuras solicitudes de inicio de sesión y eliminar la necesidad de obtener el archivo **FederationMetadata.xml** constantemente. La aplicación de ejemplo no incluye esta optimización.

### Paso 2: Generar la solicitud de inicio de sesión

Cuando un cliente se conecta a la aplicación, por ejemplo, haciendo clic en el botón de inicio de sesión, la solicitud de inicio de sesión debe generarse usando el Id. de inquilino del cliente y el Id. de cliente de la aplicación. En la aplicación de ejemplo, el método *GenerateSignInMessage* de la clase *Microsoft.IdentityModel.WAAD.Preview.WebSSO.URLUtils* genera esta solicitud. Este método comprueba que el TenantID del cliente represente a una organización que ha autorizado su aplicación y genera la dirección URL de destino para el botón de inicio de sesión, como se indica a continuación:

![inicio de sesión][inicio de sesión]

Al hacer clic en el botón, irá a una página de inicio de sesión del explorador del usuario para Azure AD. Una vez que haya iniciado sesión, Azure AD devolverá una respuesta de inicio de sesión a la aplicación.

### Paso 3: Validar el emisor del token de entrada en la respuesta de inicio de sesión

Cuando un cliente inicia sesión en la aplicación, tendrá que validar la autorización de la aplicación que ha realizado el inquilino. Su respuesta de inicio de sesión contiene un token, y el token contiene propiedades y notificaciones que la aplicación puede inspeccionar.

Para realizar esta validación, debe obtener el TenantID de la propiedad de emisor en el token y asegurarse de que se encuentra en el almacén de datos customer/TenantId. En la aplicación de ejemplo, esta validación se consigue creando una clase de controlador de token personalizado que extienda *Saml2SecurityTokenHandler* de Windows Identity Foundation. El controlador de token personalizado comprueba el token de seguridad entrante y hace que las reclamaciones y las propiedades estén disponibles para la aplicación a fin de que el TenantID se pueda validar. A continuación se muestra el fragmento de código para esta clase.

En la aplicación de ejemplo, podrá encontrar el código original debajo del espacio de nombres *Microsoft.IdentityModel.WAAD.Preview.WebSSO*. El controlador de token también usa el método Contains de la clase *Microsoft.IdentityModel.WAAD.Preview.WebSSO.TrustedIssuers*, que comprueba que el TenantID esté guardado en el almacén de datos customer/TenantId.

    /// <summary>
    /// Extends the out of the box SAML2 token handler by ensuring
    /// that incoming tokens have been issued by registered tenants 
    /// </summary>
    public class ConfigurationBasedSaml2SecurityTokenHandler : Saml2SecurityTokenHandler
    {
        public override ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> ValidateToken(SecurityToken token)
        {
            ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> aa = base.ValidateToken(token);
            Saml2SecurityToken ss = token as Saml2SecurityToken;
            string tenant = ss.Assertion.Issuer.Value.Split('/')[3];
            if (!TrustedIssuers.Contains(tenant))
            {
                throw new SecurityTokenValidationException(string.Format("The tenant {0} is not registered with the application", tenant));
            }
            return aa;
        }
    }

Una vez que se ha comprobado el token, el usuario inicia sesión en la aplicación. Ejecute la aplicación e intente iniciar sesión usando una cuenta de Azure AD en el inquilino con consentimiento creado anteriormente.

## <a name="accessgraph"></a>Parte 4: Obtener acceso a Azure AD Graph

En esta sección se indica cómo obtener un token de acceso y llamar a la API Graph de Azure AD para el acceso a los datos del directorio del inquilino. Por ejemplo, aunque el token obtenido durante el inicio de sesión contiene información de usuario, como el nombre y la dirección de correo electrónico, puede que la aplicación necesite información como la pertenencia a grupos o el nombre del administrador de usuario. Esta información se puede extraer del directorio del inquilino usando la API Graph. Para obtener más información acerca de la API Graph, consulte [este tema][este tema].

Para que la aplicación pueda llamar a Azure AD Graph, debe autenticarse y obtener un token de acceso. Los tokens de acceso se obtienen autenticando la aplicación con el Id. y el secreto de cliente. Los pasos siguientes le indicarán cómo:

1.  Usar una clase de proxy generada para llamar a Azure AD Graph
2.  Adquirir un token de acceso usando la Biblioteca de autenticación de Azure (AAL)
3.  Llamar a Azure AD Graph para obtener una lista de usuarios inquilino

<div class="dev-callout"><strong>Nota:</strong><p>La biblioteca auxiliar de la aplicaci&oacute;n de ejemplo Microsoft.IdentityModel.WAAD.Preview ya contiene una clase de proxy generada autom&aacute;ticamente (creada agregando una referencia de servicio a https://graph.windows.net/your-domain-name llamada GraphService). La aplicaci&oacute;n usar&aacute; esta clase de proxy para llamar al servicio Azure AD Graph.</p></div>

### Paso 1: Usar la clase de proxy para llamar a Azure AD Graph

En este paso se usará la aplicación de ejemplo para indicarle cómo:

1.  Crear un extremo de Azure AD Graph específico de un inquilino
2.  Usar el extremo para crear instancias del proxy para llamar a Graph
3.  Agregar el encabezado de autorización a la solicitud y adquirir el token

En la aplicación de ejemplo, el método GraphInterface de la clase *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* controla estas llamadas a la API, como se indica en el código siguiente.

    public GraphInterface()
    {
        // 1a: When the customer was signed in, we get a security token 
        // that contains a tenant id. Extract that here
        TenantDomainName = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/ws/2012/10/identity/claims/tenantid").Value;

        // 1b: We generate a URL (https://graph.windows.net/<CustomerDomainName>)
        // to access the Azure AD Graph API endpoint for the tenant 
        connectionUri = new Uri(string.Format(@"https://{0}/{1}", TenantUtils.Globals.endpoint, TenantDomainName));

        // 2: create an instance of the AzureAD Service proxy with the connection URL
        dataService = new DirectoryDataService(connectionUri);

        // This flags ignores the resource not found exception
        // If AzureAD Service throws this exception, it returns null
        dataService.IgnoreResourceNotFoundException = true;
        dataService.MergeOption = MergeOption.OverwriteChanges;
        dataService.AddAndUpdateResponsePreference = DataServiceResponsePreference.IncludeContent;

        // 3: This adds the default required headers to each request
        AddHeaders(GetAuthorizationHeader());
    }

### Paso 2: Adquirir un token de acceso usando la Biblioteca de autenticación de Azure (AAL)

La aplicación de ejemplo usa la Biblioteca de autenticación de Azure (AAL) para adquirir tokens a fin de obtener acceso a la API Graph. El proceso de adquisición del token, que se muestra a continuación, lo administra el método *GetAuthorizationHeader* de la clase *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface*.

<div class="dev-callout"><strong>Nota:</strong><p>AAL est&aacute; disponible como paquete de NuGet y se puede instalar dentro de Visual Studio.</p></div>

    /// <summary>
    /// Method to get the Oauth2 Authorization header from ACS
    /// </summary>
    /// <returns>AOauth2 Authorization header</returns>
    private string GetAuthorizationHeader()
    {
        // AAL values
        string fullTenantName = TenantUtils.Globals.StsUrl + TenantDomainName;
        string serviceRealm = string.Format("{0}/{1}@{2}", TenantUtils.Globals.GraphServicePrincipalId, TenantUtils.Globals.GraphServiceHost, TenantDomainName);
        string issuingResource = string.Format("{0}@{1}", Globals.ClientId, TenantDomainName);
        string clientResource = string.Format("{0}/{1}@{2}", Globals.ClientId, Globals.AppHostname, TenantDomainName);

        string authzHeader = null;
        AuthenticationContext _authContext = new AuthenticationContext(fullTenantName);

        try
        {
            ClientCredential credential = new ClientCredential(issuingResource, clientResource, Globals.ServicePrincipalKey);
            AssertionCredential _assertionCredential = _authContext.AcquireToken(serviceRealm, credential);
            authzHeader = _assertionCredential.CreateAuthorizationHeader();
        }
        catch (Exception ex)
        {
            AALException aex = ex as AALException;
            string a = aex.InnerException.Message;
        }

        return authzHeader;
    }

La información siguiente se usa para adquirir el token de acceso, como se indica en el código anterior:

1.  La información de la aplicación (ClientID, ServicePrincipalKey y AppHostname)
2.  La información de destino, que es Graph, denominado ServiceRealm anteriormente
3.  El TenantDomainName que ha adquirido anteriormente

### Paso 3: Llamar a Azure AD Graph para obtener una lista de usuarios

El método siguiente de la clase *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* obtiene una lista todos los usuarios de su inquilino, usando el proxy *DataService* generado anteriormente.

    public List<User> GetUsers()
    {
        // Add the page size using top
        var users = dataService.Users.AddQueryOption("$top", 20);

        // Execute the Query
        var userQuery = users.Execute();

        // Get the return users list
        return userQuery.ToList();
    }

Este método se llama desde el archivo **HomeController.cs** para mostrar la lista de usuarios en la pestaña Usuarios de la aplicación web.

## <a name="publish"></a>Parte 5: Publicar la aplicación

Una vez que haya probado exhaustivamente la aplicación, puede crear una lista de aplicaciones y publicarla en Azure Marketplace. Estos pasos se realizan en el Panel de vendedores de Microsoft.

<div class="dev-callout"><strong>Nota:</strong><p>La aplicaci&oacute;n es responsable de administrar las relaciones de facturaci&oacute;n con los clientes. Azure Marketplace solo proporciona v&iacute;nculos al sitio web de la aplicaci&oacute;n e informaci&oacute;n acerca de ella.</p></div>

### Paso 1: Crear un manifiesto de aplicación y una lista de aplicaciones

Antes de crear una lista de aplicaciones, debe generar un Id. y secreto de cliente nuevos para la versión de producción de la aplicación. En la Parte 1 de este tutorial ha generado un Id. y un secreto de cliente para una versión de prueba de la aplicación. Repita los pasos y configure la aplicación para que use los nuevos valores, asegurándose de configurar un dominio de aplicación de producción y una dirección URL de redireccionamiento de aplicación.

A continuación, debe crear un manifiesto de aplicación que incluya los permisos que la aplicación solicitará para el consentimiento del cliente. Este manifiesto se escribe en formato XML y lo rige un archivo XSD. El manifiesto debe cargarse como parte de la lista de aplicaciones que está creando.

Hay tres niveles de permisos diferentes, como se indica en la Parte 1 del tutorial:

**DirectoryReader**: Concede el permiso para leer datos de directorio, como cuentas de usuario, grupos e información acerca de la organización. Habilita el inicio de sesión único.

**UserAccountAdministrator**: Concede permiso para leer y escribir datos, como cuentas de usuario, grupos e información acerca de la organización. Habilita el inicio de sesión único.

**None**: Habilita el inicio de sesión único, pero deshabilita el acceso a los datos del directorio.

A continuación se incluyen dos manifiestos de aplicación de ejemplo. El primero demuestra los permisos para una aplicación de solo SSO, y el segundo demuestra los permisos para una aplicación de solo lectura:

    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="None" Scope="http://directory" />
      </AppPermissionRequests>
    </AppRequiredPermissions>


    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="Directory Reader" Scope="http://directory">
          <Reason culture="es-es" value="Needs to read the app"/>
        </AppPermissionRequest>
      </AppPermissionRequests>
    </AppRequiredPermissions>

El atributo *Policy* de los ejemplos anteriores describe el tipo de permiso de aplicación que se solicita. Actualmente, solo se admite el atributo de permiso "AppOnly". Este tipo de permiso indica que la aplicación solo tiene acceso al directorio como tal.

El elemento opcional *Reason* permite especificar (en varias culturas) su justificación para el nivel de permiso necesario. Este texto aparece en la página de consentimiento para ayudar al cliente cuando esté aprobando o rechazando la aplicación.

Al usar el nuevo Id. de cliente y el manifiesto de la aplicación, puede crear una lista de aplicaciones siguiendo las instrucciones que se indican en [Agregar aplicaciones en el Panel de vendedores de Microsoft][Agregar aplicaciones en el Panel de vendedores de Microsoft]. Al crear una lista de aplicaciones, asegúrese de seleccionar el tipo de aplicación de Azure AD. Una vez que haya terminado de crear la lista de aplicaciones, haga clic en "submit" para publicar la aplicación en Azure Marketplace. Tendrá que esperar a que la aplicación se apruebe antes de finalizar la publicación.

<div class="dev-callout"><strong>Nota:</strong><p>Si se le pide que agregue la informaci&oacute;n de pagos e impuestos, puede saltarse este paso puesto que vende su aplicaci&oacute;n directamente al cliente y no a trav&eacute;s de Microsoft.</p></div>

### Paso 2: Finalizar la prueba y publicar la aplicación

Una vez que la lista de aplicaciones se aprueba, debería probar todos los aspectos de la aplicación de nuevo. Por ejemplo, asegúrese de que la aplicación se haya actualizado con el Id. y el secreto de cliente de producción. Repase la lista de comprobación para la prueba por última vez, asegurándose de que la página de consentimiento muestre la información que ha incluido en la lista de aplicaciones.

## <a name="summary"></a>Resumen

En este tutorial ha aprendido a integrar su aplicación multiempresa con Azure AD. El proceso incluye tres pasos:

-   Permitir que los clientes inicien sesión en la aplicación usando Azure AD
-   Habilitar el inicio de sesión único (SSO) con Azure AD
-   Consultar los datos del directorio de un cliente usando la API Graph de Azure AD

La integración con Azure AD permite a sus clientes registrarse e iniciar sesión en la aplicación usando un sistema de administración de identidades que ya mantienen, lo que reduce o elimina la necesidad de realizar tareas de administración de identidades por separado asociadas a su aplicación. Esta funcionalidad aporta a sus clientes una experiencia más directa al usar la aplicación, y elimina el tiempo dedicado a realizar tareas de administración.

  [página de identidades]: http://www.windowsazure.com/es-es/home/features/identity/
  [descargar aquí]: http://go.microsoft.com/fwlink/?LinkId=271213
  [asignación de puertos en Visual Studio]: http://msdn.microsoft.com/es-es/library/ms178109(v=vs.100).aspx
  [Visual Studio 2012]: http://www.microsoft.com/visualstudio/eng/downloads
  [Servicios de datos WCF para OData]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Introducción]: #introduction
  [Parte 1: Obtener un Id. de cliente para el acceso a Azure AD]: #getclientid
  [Parte 2: Permitir que los clientes inicien sesión usando Azure AD]: #enablesignup
  [Parte 3: Habilitar el inicio de sesión único]: #enablesso
  [Parte 4: Obtener acceso a Azure AD Graph]: #accessgraph
  [Parte 5: Publicar la aplicación]: #publish
  [Resumen]: #summary
  [Panel de vendedores de Microsoft]: https://sellerdashboard.microsoft.com/
  [cree un perfil de cuenta]: http://msdn.microsoft.com/es-es/library/jj552460.aspx
  [Creación de secretos e identificadores de cliente en el Panel de vendedores de Microsoft]: http://msdn.microsoft.com/es-es/library/jj552461.aspx
  [inicio de sesión]: ./media/active-directory-dotnet-integrate-multitent-cloud-applications/login.png
  [Obtención de un inquilino de Azure AD]: http://g.microsoftonline.com/0AX00en/5
  [este tema]: http://msdn.microsoft.com/es-es/library/windowsazure/hh974476.aspx
  [Agregar aplicaciones en el Panel de vendedores de Microsoft]: http://msdn.microsoft.com/es-es/library/jj552465.aspx
