<properties
   pageTitle="SDK de Resource Manager para .NET | Microsoft Azure"
   description="Información general sobre la autenticación del SDK de Resource Manager para .NET y ejemplos de uso"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# SDK de Azure Resource Manager para .NET  
Los SDK de Azure Resource Manager (ARM) están disponibles para múltiples lenguajes y plataformas. Cada una de estas implementaciones de lenguajes está disponibles a través de su administrador de paquetes del ecosistema y GitHub.

El código de cada uno de estos SDK se genera en [Azure RESTful API specifications](https://github.com/azure/azure-rest-api-specs) (Especificaciones de la API de RESTful de Azure). Estas especificaciones son de código abierto y se basan en la especificación Swagger 2.0. El código del SDK se genera mediante un proyecto de código abierto denominado [AutoRest](https://github.com/azure/autorest). AutoRest transforma estas especificaciones de la API de RESTful en bibliotecas de clientes en múltiples lenguajes. Si desea mejorar cualquiera de los aspectos del código generado en los SDK, todo el conjunto de herramientas para crear los SDK es abierto, está disponible de forma gratuita y se basa en un formato de especificaciones de API, ampliamente adoptado.

[Azure SDK para .NET](https://azure.microsoft.com/downloads/) es un conjunto de paquetes NuGet que le ayudan a llamar a la mayoría de las API de Azure Resource Manager. Si el SDK no expone la funcionalidad necesaria, se puede combinar fácilmente con llamadas regulares a la API de REST de Resource Manager en segundo plano.

Este artículo no pretende describir todos los aspectos de Azure SDK para .NET, las API de Azure Resource Manager o Visual Studio. Se proporciona como una forma rápida de empezar a trabajar.

Los siguientes fragmentos de código proceden de un [proyecto de ejemplo que se puede descargar](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Instalación de paquetes NuGet

Los ejemplos de este artículo requieren dos paquetes NuGet (además de Azure SDK para .NET). Para instalarlos:

1. En Visual Studio, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**.
2. Busque **Microsoft.IdentityModel.Clients.ActiveDirectory** e instale la última versión estable del paquete.
3. Busque **Microsoft.Azure.Management.ResourceManager** y seleccione **Incluir versión preliminar**. Instale la última versión preliminar (por ejemplo, 1.1.2-preview).

## Autenticar
Azure Active Directory (Azure AD) controla la autenticación de Resource Manager. Para conectarse a cualquier API, primero debe autenticarse en Azure AD para recibir un token de acceso que pueda pasar a cada solicitud. Para obtener dicho token, es preciso crear una aplicación de Azure AD y una entidad de servicio, que se usará para iniciar sesión. Para obtener instrucciones detalladas, consulte cualquiera de estos artículos:

- [Uso de Azure PowerShell para crear una aplicación de Active Directory con el fin de acceder a recursos](resource-group-authenticate-service-principal.md)
- [Uso de la CLI de Azure para crear una aplicación de Active Directory con el fin de acceder a recursos](resource-group-authenticate-service-principal-cli.md)
- [Uso del Portal de Azure para crear una aplicación de Active Directory con acceso a los recursos](resource-group-create-service-principal-portal.md)

Después de crear la entidad de servicio es preciso tener:

- Identificador de cliente o de aplicación (GUID)
- Secreto del cliente o contraseña (cadena)
- Identificador de inquilino (GUID) o nombre de dominio (cadena)

### Recepción del token de acceso del código
Para adquirir el token de acceso, utilice las siguientes líneas de código; solo es preciso que pase su identificador de inquilino de Azure AD, su identificador de cliente de la aplicación de Azure AD y el secreto de cliente de la aplicación de Azure AD. Guarde el token, ya que, de forma predeterminada, tiene una validez de una hora.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

En lugar de utilizar el identificador de inquilino para iniciar sesión, puede utilizar el dominio de Azure AD, tal como se muestra en el siguiente código. Este enfoque requiere que cambie la firma del método para que incluya el nombre de dominio, en lugar del identificador de inquilino.

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* Azure AD URI */
    + $"{domain}.onmicrosoft.com");
```

Mediante el siguiente código puede obtener el token de acceso de una aplicación de Azure AD que use un certificado para la autenticación:

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
        + $"{tenantId}" /* Tenant ID or Azure AD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Consulta de las suscripciones de Azure adjuntas a la aplicación autenticada
Pueden realizarse consultas en las suscripciones de Azure asociadas a la aplicación recién autenticada. A partir de ahora será obligatorio pasar el identificador de suscripción de la suscripción de destino a todas las llamadas a la API que se realicen.

El código de ejemplo siguiente consulta las API de Azure directamente mediante la API de REST. Es decir, no utiliza características de Azure SDK para .NET.

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

Observe que recibe una respuesta JSON de Azure. A continuación, extraiga los identificadores de suscripción de esta respuesta, con el fin de devolver una lista de identificadores. Todas las llamadas subsiguientes a las API de Azure Resource Manager que se realicen en este artículo utilizarán un solo identificador de suscripción de Azure. Por consiguiente, si su aplicación está asociada con varias suscripciones, elija la correcta y pásela como parámetro a partir de ahora.

A partir de aquí, todas las llamadas que realice a las API de Azure usarán Azure SDK para .NET. Por lo tanto, el código será un poco diferente.

### Encapsulación del token como objeto TokenCredentials
Todas las llamadas a las API siguientes necesitarán que el token que recibió de Azure AD tenga el formato de un objeto TokenCredentials. Para crear dicho objeto, pase el token sin procesar como un parámetro al constructor de la clase.

```csharp
var credentials = new TokenCredentials(token);
```

Si tiene una versión anterior del paquete NuGet de Resource Manager (denominado Microsoft.Azure.Management.Resources), es preciso que use el siguiente código:

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## Creación de un grupo de recursos
En Azure, todo está enfocado hacia los grupos de recursos, así que vamos a crear uno. *ResourceManagementClient* controla los recursos generales y los grupos de recursos. Con cualquiera de los siguientes clientes de administración más especializados que se utilizaran, es preciso proporcionar las credenciales, así como un identificador de suscripción para identificar la suscripción con la que se desea trabajar.

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## Creación de recursos manual o mediante plantillas
Hay varias formas de interactuar con las API de Azure Resource Manager, pero las dos principales son:

* Manualmente, llamando a proveedores de recursos concretos.
* Mediante el uso de una plantilla de Azure Resource Manager.

El uso de una plantilla de Resource Manager tiene las siguientes ventajas:

* Es preciso especificar mediante declaración el aspecto que se desea que tenga el resultado, en lugar de cómo debe obtenerse.
* No es preciso que controle manualmente la ejecución en paralelo de las implementaciones, Resource Manager lo hará automáticamente.
* No es preciso aprender C#, ni ningún otro lenguaje, para implementar una plantilla de Resource Manager, aunque se puede utilizar cualquier lenguaje para iniciar una implementación realizada mediante plantillas.
* El lenguaje específico de dominio (DSL) que se usa en las plantillas se genera a través de JSON. A cualquiera que haya trabajado con JSON le debe resultar bastante fácil de conocer.

A pesar de todas las ventajas de las plantillas, empezaremos por mostrarle cómo llamar a las API manualmente.

### Creación de una máquina virtual paso a paso
Ya tiene la suscripción y el grupo de recursos. Si desea implementar una máquina virtual (VM), es preciso que sepa de lo que está hecha:

* Una o varias cuentas de almacenamiento para almacenar discos persistentes.
* Una o varias direcciones IP públicas para que se pueda acceder a los recursos de Azure desde Internet (incluye un nombre DNS).
* Una o varias redes virtuales para la comunicación interna entre los recursos.
* Una o varias tarjetas de interfaz de red (NIC) que permitan la comunicación entre máquinas virtuales.
* Una o varias máquinas virtuales para ejecutar el software.

Algunos de estos recursos se pueden crear en paralelo, pero otros no. Por ejemplo:

* Las NIC dependen tanto de las direcciones IP públicas como de las redes virtuales.
* Las máquinas virtuales dependen de las NIC y de las cuentas de almacenamiento.

No intente crear una instancia de todos los recursos antes de haber creado las dependencias necesarias. El [ejemplo](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) completo muestra cómo se pueden crear eficazmente los recursos en paralelo, al tiempo que realiza un seguimiento de lo que ha creado.

#### Creación de la cuenta de almacenamiento
Para almacenar los discos duros virtuales de su máquina virtual necesita una cuenta de almacenamiento. Si ya tiene una cuenta de almacenamiento, puede utilizarla para varias máquinas virtuales. Pero recuerde distribuir la carga entre varias cuentas de almacenamiento, con el fin de no superar sus límites. Recuerde también que el tipo de cuenta de almacenamiento y su ubicación pueden limitar el tamaño de la máquina virtual que puede elegir, ya que no todos los tamaños de máquina virtual están disponibles en todas las regiones o para todos los tipos de cuentas de almacenamiento.

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### Crear la dirección IP pública
La dirección IP pública es lo que hace que se pueda acceder a los recursos de Azure desde Internet. Junto con la dirección IP, se le asignará un nombre de dominio completo (FQDN), que puede usar para facilitar el acceso.

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support static IP addresses but can be extended to do so
        });

    return createPipTask;
}
```

#### Crear la red virtual
Es preciso que cada una de las máquinas virtuales creadas con las API de Resource Manager formen parte de una red virtual, aunque esté sola en ella. La red virtual debe contener al menos una subred, pero se pueden usar varias para dividir y proteger los recursos.

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### Creación de la tarjeta de interfaz de red
La NIC es lo que conecta una máquina virtual con la red virtual en la que reside. Una máquina virtual puede tener muchas NIC y, por tanto, estar asociada a varias redes virtuales. En este ejemplo se supone que las máquinas virtuales se van a adjuntar a una sola red virtual.

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### Creación de la máquina virtual
Por último, ha llegado la hora de crear la máquina virtual. La máquina virtual depende, directa o indirectamente, de todos los recursos creados con anterioridad, por lo que es preciso esperar a que todos los recursos estén listos antes de intentar aprovisionar una máquina virtual. El aprovisionamiento de una máquina virtual tarda más tiempo en realizarse que la creación de los recursos anteriores, por tanto lo habitual es que la aplicación espere un poco para que esto ocurra.

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### Implementación de una plantilla
Para obtener instrucciones detalladas acerca de cómo implementar una plantilla, consulte el artículo [Implementación de una máquina virtual de Azure con C# y una plantilla de Resource Manager](./virtual-machines/virtual-machines-windows-csharp-template.md).

En resumen, la implementación de una plantilla es tarea mucho más fácil que el aprovisionamiento manual de los recursos. El siguiente código muestra cómo hacerlo señalando a los identificadores URI en los que se encuentra la plantilla y un archivo de parámetros.

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```

<!---HONumber=AcomDC_0629_2016-->