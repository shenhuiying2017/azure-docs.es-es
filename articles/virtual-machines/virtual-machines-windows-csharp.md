<properties
	pageTitle="Implementación de recursos de Azure con C# | Microsoft Azure"
	description="Aprenda a usar C# y Azure Resource Manager para crear recursos de Microsoft Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="davidmu"/>

# Implementación de recursos de Azure mediante C# 

En este artículo se muestra cómo crear recursos de Azure con C#.

En primer lugar, debe asegurarse de haber hecho lo siguiente:

- Instalar [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Comprobar la instalación de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obtener un [token de autenticación](../resource-group-authenticate-service-principal.md)

Tardará unos 30 minutos en realizar estos pasos.

## Paso 1: Crear un proyecto de Visual Studio e instalar las bibliotecas

Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar este tutorial. Debe instalar la biblioteca de administración de recursos de Azure, la biblioteca de autenticación de Azure Active Directory y la biblioteca de proveedor de recursos de equipo. Para obtener estas bibliotecas en Visual Studio, haga lo siguiente:

1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

4. Escriba *Active Directory* en el cuadro de búsqueda, haga clic en **Instalar** para el paquete de la biblioteca de autenticación de Active Directory y, a continuación, siga las instrucciones para instalar el paquete.

5. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.Compute* en el cuadro de búsqueda, haga clic en **Instalar** en las bibliotecas .NET de proceso y siga las instrucciones para instalar el paquete.

6. Escriba *Microsoft.Azure.Management.Network* en el cuadro de búsqueda, haga clic en **Instalar** en las bibliotecas .NET de red y siga las instrucciones para instalar el paquete.

7. Escriba *Microsoft.Azure.Management.Storage* en el cuadro de búsqueda, haga clic en **Instalar** en las bibliotecas .NET de almacenamiento y siga las instrucciones para instalar el paquete.

8. Escriba *Microsoft.Azure.Management.ResourceManager* en el cuadro de búsqueda y haga clic en **Instalar** en las bibliotecas de administración de recursos.

Ahora está preparado para comenzar a usar las bibliotecas para crear su aplicación.

## Paso 2: Crear las credenciales que se usan para autenticar solicitudes

Ahora que la aplicación Azure Active Directory está creada y que la biblioteca de autenticación está instalada, la información de la aplicación se convierte en las credenciales que se usan para autenticar las solicitudes en Azure Resource Manager. Haga esto...

1. Abra el archivo Program.cs del proyecto que ha creado y agregue las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Agregue este método a la clase Program para obtener el token necesario para crear las credenciales:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

	Reemplace {client-id} con el identificador de la aplicación Azure Active Directory, {client-secret} con la clave de acceso de la aplicación de AD y {tenant-id} con el identificador del inquilino de su suscripción. Para encontrar el identificador del inquilino, ejecute Get-AzureSubscription. Puede encontrar la clave de acceso mediante el Portal de Azure.

3. Agregue este código al método Main en el archivo Program.cs para crear las credenciales:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Guarde el archivo Program.cs.

## Paso 3: Agregar el código para registrar los proveedores y crear los recursos

### Registre los proveedores y cree un grupo de recursos

Todos los recursos deben encontrarse en un grupo de recursos. Para poder agregar recursos a un grupo, la suscripción se debe registrar con los proveedores de recursos.

1. Agregue variables al método Main de la clase Program para especificar los nombres que desea usar para los recursos, la ubicación de los recursos (por ejemplo, "centro de EE. UU."), la información de la cuenta de administrador y el identificador de la suscripción:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Reemplace todos los valores de variables por los nombres y el identificador que desea usar. Para encontrar el identificador de la suscripción, ejecute Get-AzureRmSubscription.

2. Agregue este método a la clase Program para crear el grupo de recursos y registrar los proveedores:

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Agregue este código al método Main para llamar al método que acaba de agregar:

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### Crear una cuenta de almacenamiento

Se necesita una [cuenta de almacenamiento](../storage/storage-create-storage-account.md) para almacenar el archivo de disco duro virtual que se crea para la máquina virtual.

1. Agregue este método a la clase Program para crear la cuenta de almacenamiento:

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential);
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### Crear la dirección IP pública

Se necesita una dirección IP pública para la comunicación con la máquina virtual.

1. Agregue este método a la clase Program para crear la dirección IP pública de la máquina virtual:

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### Crear la red virtual

Una máquina virtual creada con el modelo de implementación del Administrador de recursos debe estar en una red virtual.

1. Agregue este método a la clase Program para crear una subred y una red virtual:

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### Creación de la interfaz de red

Una máquina virtual requiere una interfaz de red para comunicarse en la red virtual que acaba de crear.

1. Agregue este método a la clase Program para crear una interfaz de red:

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### Crear un conjunto de disponibilidad

Los conjuntos de disponibilidad facilitan la administración del mantenimiento de las máquinas virtuales que utiliza su aplicación.

1. Agregue este método a la clase Program para crear el conjunto de disponibilidad:

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### de una máquina virtual

Ahora que ha creado todos los recursos auxiliares, puede crear una máquina virtual.

1. Agregue este método a la clase Program para crear la máquina virtual:

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential);
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
          Console.WriteLine(vm.ProvisioningState);
        }

	>[AZURE.NOTE] En este tutorial se crea una máquina virtual donde se ejecuta una versión del sistema operativo Windows Server. Para obtener más información sobre cómo seleccionar otras imágenes, consulte [Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Agregue este código al método Main para llamar al método que acaba de agregar:

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avsetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##Paso 4: Incorporar el código para eliminar los recursos

Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. Si desea eliminar las máquinas virtuales y todos los recursos auxiliares, lo único que tiene que hacer es eliminar el grupo de recursos.

1.	Agregue este método a la clase Program para eliminar el grupo de recursos:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Agregue este código al método Main para llamar al método que acaba de agregar:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## Paso 5: Ejecutar la aplicación de la consola

1. Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

2. Presione **Entrar** después de que se devuelva cada código de estado para crear cada recurso. Después de crear la máquina virtual, realice el siguiente paso antes de presionar Entrar para eliminar todos los recursos.

	Esta aplicación de consola tardará unos cinco minutos en ejecutarse completamente de principio a fin. Antes de presionar Entrar para comenzar la eliminación de recursos, puede dedicar unos minutos a comprobar la creación de los recursos en el Portal de Azure.

3. Vaya a los registros de auditoría en el Portal de Azure para ver el estado de los recursos:

	![Exploración de registros de auditoría en el Portal de Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## Pasos siguientes

- Aproveche las ventajas de usar una plantilla para crear una máquina virtual con la información que se muestra en [Implementación de una máquina virtual de Azure con C# y una plantilla de Resource Manager](virtual-machines-windows-csharp-template.md).
- Obtenga más información sobre cómo administrar la máquina virtual que acaba de crear. Para ello, consulte [Manage virtual machines using Azure Resource Manager and PowerShell](virtual-machines-windows-csharp-manage.md) (Administración de máquinas virtuales con Azure Resource Manager y PowerShell).

<!---HONumber=AcomDC_0629_2016-->