<properties
   pageTitle="SDK de Resource Manager para .NET | Microsoft Azure"
   description="Información general sobre la autenticación del SDK para .NET de Resource Manager y ejemplos de uso"
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
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>

# SDK de Azure Resource Manager para .NET  
Los SDK de la versión preliminar de Azure Resource Manager (ARM) están disponibles para múltiples lenguajes y plataformas. Cada una de estas implementaciones de lenguajes están disponibles a través de sus administradores de paquetes del ecosistema y GitHub.

El código de cada uno de estos SDK se genera en [Azure RESTful API specifications](https://github.com/azure/azure-rest-api-specs) (Especificaciones de la API de RESTful de Azure). Estas especificaciones son de código abierto y están basadas en la especificación de Swagger v2. El código del SDK se genera mediante un proyecto de código abierto denominado [AutoRest](https://github.com/azure/autorest). AutoRest transforma estas especificaciones de la API de RESTful en bibliotecas de clientes en múltiples lenguajes. Si hay algún aspecto del código generado en los SDK que quiera mejorar, todo el conjunto de herramientas para crear los SDK está abierto, disponible libremente y se basa en el formato de especificación de la API, ampliamente adoptado.

Azure SDK para .NET se proporciona como un conjunto de paquetes de NuGet que le ayuda a llamar a la mayoría de las API expuestas por Azure Resource Manager. Si el SDK no expone la funcionalidad necesaria, puede combinar fácilmente el SDK con llamadas regulares a la API de REST de ARM en segundo plano.

Esta documentación no está diseñada para describir todos los aspectos del SDK de Azure para. NET, las API de Azure ARM o Visual Studio, sino que se proporciona como una forma rápida de empezar.

[Aquí](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) encontrará un proyecto de ejemplo completo descargable de dónde se han tomado todos los fragmentos de código siguientes.

## Autenticación
La autenticación para ARM se controla mediante Azure Active Directory (AD). Para conectarse a cualquier API, primero debe autenticarse con Azure AD para recibir un token de autenticación que pueda pasar a cada solicitud. Para obtener este token, primero debe crear lo que se denomina una aplicación de Azure AD y una entidad de servicio que se usará para iniciar la sesión. Siga las instrucciones paso a paso que encontrará en [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](./resource-group-create-service-principal-portal.md).

Después de crear la entidad de servicio, debe tener:
* Identificador de cliente (GUID)
* Secreto del cliente (cadena)
* Identificador de inquilino (GUID) o nombre de dominio (cadena)

### Recepción de AccessToken desde el código
El token de autenticación puede obtenerse fácilmente con las siguientes líneas de código; para ello, solo es necesario pasar su identificador de inquilino de Azure AD, su identificador de cliente de la aplicación de Azure AD y el secreto de cliente de la aplicación de Azure AD. Guarde el token para varias solicitudes, ya que, de forma predeterminada, es válido durante 1 hora.

```csharp
private static AuthenticationResult GetAccessToken(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}.onmicrosoft.com" /* Tenant ID or AAD domain */);

    var credential = new ClientCredential(clientId, clientSecret);

    AuthenticationResult token = authContext.AcquireToken("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Consulta de las suscripciones de Azure vinculadas a la aplicación autenticada
Una de las primeras cosas que puede desear hacer es consultar qué suscripciones de Azure están asociadas a la aplicación recién autenticada. A partir de ahora será obligatorio pasar el identificador de suscripción para la suscripción de destino a las llamadas a la API.

El siguiente ejemplo de código consulta las API de Azure directamente mediante la API de REST; es decir, que no usa ninguna de las características de Azure SDK para .NET.

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

Tenga en cuenta que obtenemos una respuesta JSON de Azure de la que, a continuación, extraemos los identificadores de suscripción con el fin de devolver una lista de identificadores. Todas las siguientes llamadas a las API de Azure ARM en esta documentación utilizan un único identificador de suscripción de Azure, por lo que si la aplicación está asociada a varias suscripciones, simplemente seleccione el adecuado y páselo como parámetro.

A partir de ahora, cada llamada que hagamos a las API de Azure usará el SDK de Azure para .NET, por lo que el código tendrá un aspecto un poco diferente.

### Ajuste del token como un objeto TokenCredentials
Todas las llamadas a las API siguientes necesitarán el token que recibió de Azure AD con el formato de un objeto "TokenCredentials". Este tipo de objeto se crea fácilmente simplemente pasando el token sin procesar como un parámetro al constructor de la clase.

```csharp
var credentials = new TokenCredentials(token);
```

## Creación de un grupo de recursos
Todo en Azure se centra en los grupos de recursos, así que comenzaremos por crear uno. Los recursos generales y los grupos de recursos se controlan mediante *ResourceManagementClient* y como con cualquiera de los siguientes clientes de administración más especializados que utilizaremos, debe proporcionar sus credenciales, así como un identificador de suscripción para identificar con qué suscripción desea trabajar.

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

## Creación de recursos manualmente o mediante plantillas
Hay varias formas de interactuar con las API de Azure Resource Manager, pero las dos formas principales de hacerlo son:

* Manualmente, al llamar a proveedores de recursos específicos manualmente, o bien
* Mediante el uso de una plantilla de Azure Resource Manager (también llamada plantilla de ARM)

El uso de las plantillas de ARM tiene las siguientes ventajas:

* Especificar mediante declaración el aspecto que desea que tenga el resultado final, en lugar de cómo debe obtenerse.
* No tiene que controlar manualmente la ejecución en paralelo de las implementaciones, ARM lo hará por usted.
* No necesita aprender C# ni ningún otro lenguaje para implementar una plantilla de ARM, aunque puede utilizar cualquier lenguaje para iniciar una implementación basada en una plantilla.
* El lenguaje específico del dominio usado en las plantillas se compila con JSON y es fácil de entender para cualquiera que haya trabajado con JSON.

A pesar de todas las ventajas de las plantillas, empezaremos mostrando cómo llamar a las API manualmente.

### Creación de una máquina virtual paso a paso
Ya tenemos nuestra suscripción y nuestro grupo de recursos. Si deseamos implementar una máquina virtual, tenemos que averiguar qué partes componen realmente una máquina virtual, y son bastantes:

* Una o varias cuentas de almacenamiento, para almacenar discos persistentes
* Una o varias direcciones IP públicas (PIP) para que sea accesible desde Internet (incluye un nombre DNS)
* Una o varias redes virtuales para la comunicación interna entre los recursos
* Una o varias tarjetas de interfaz de red (NIC) para permitir que la máquina virtual se comunique
* Una o varias máquinas virtuales para ejecutar nuestro software

Otra parte interesante es también cómo algunos de estos recursos se pueden crear en paralelo, mientras que otros no. Por ejemplo:

* NIC, dependen de la PIP y la red virtual
* Máquinas virtuales, dependen de las NIC y las cuentas de almacenamiento

Debe asegurarse de no intentar crear una instancia de todos los recursos antes de haber creado las dependencias necesarias. Todo el [ejemplo](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) proporcionado con esta documentación muestra cómo puede crear eficazmente los recursos en paralelo, al tiempo que realiza un seguimiento de lo que ha creado.

#### Creación de la cuenta de almacenamiento
Necesita una cuenta de almacenamiento para almacenar los discos duros virtuales para su máquina virtual. Si tiene una cuenta de almacenamiento existente, puede usarla para varias máquinas virtuales, pero recuerde distribuir la carga entre varias cuentas de almacenamiento para no sobrepasar los límites. Recuerde que el tipo de cuenta de almacenamiento y su ubicación pueden limitar el tamaño de la máquina virtual que puede elegir, ya que no todos los tamaños de máquinas virtuales están disponibles en todas las regiones o para todos los tipos de cuentas de almacenamiento.

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

#### Creación de la dirección IP pública (PIP)
La dirección IP pública es lo que hace que se pueda acceder a los recursos que están en Azure desde Internet. Junto con la dirección IP, también se le asignará un nombre de dominio completo (FQDN), que puede usar para facilitar el acceso.

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
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### Creación de la red virtual
Cada máquina virtual creada con las API de ARM debe formar parte de una red virtual, incluso aunque esté sola en ella. La red virtual debe contener al menos una subred, pero puede tener varias para dividir y proteger los recursos en varias subredes.

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

#### Creación de la tarjeta de interfaz de red (NIC)
La tarjeta de interfaz de red (NIC) es lo que conecta la máquina virtual con la red virtual en la que reside. Una máquina virtual puede tener varias NIC y, por tanto, estar asociada a varias redes virtuales. Este ejemplo supone que solo va a asignar sus máquinas virtuales a una red virtual.

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
Por fin, es hora de crear la máquina virtual real. La máquina virtual depende directa o indirectamente de todos los recursos creados anteriormente, por lo que debe esperar a que todas las opciones anteriores estén listas antes de aprovisionar una máquina virtual. El aprovisionamiento de una máquina virtual es lo que tarda más tiempo de los recursos anteriores, por tanto lo habitual es que la aplicación espere un poco para que esto ocurra.

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

### Uso de una implementación con plantillas
Lea y siga el tutorial [Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla](./arm-template-deployment/#step-4-create-the-credentials-that-are-used-to-authenticate-requests) para obtener instrucciones detalladas sobre cómo implementar una plantilla.

En resumen, la implementación de una plantilla es mucho más sencillo que aprovisionar manualmente los recursos y el código siguiente muestra cómo hacerlo señalando a los URI donde se encuentra la plantilla y un archivo de parámetros.

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


 
   

<!---HONumber=AcomDC_0316_2016-->