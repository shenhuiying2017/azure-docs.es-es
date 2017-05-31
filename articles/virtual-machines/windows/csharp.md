---
title: "Implementación de una máquina virtual de Azure con C# | Microsoft Docs"
description: "Use C# y Azure Resource Manager para implementar una máquina virtual y todos sus recursos de apoyo."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 7f708087dda4cfb7e998b42ce36632d5764c6c0e
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c"></a>Implementación de una máquina virtual de Azure con C# #

En este artículo se muestra cómo usar C# para crear una máquina virtual de Azure y sus recursos de apoyo.

Tardará unos 20 minutos en realizar estos pasos.

## <a name="step-1-create-a-visual-studio-project"></a>Paso 1: Creación de un proyecto de Visual Studio

En este paso, asegúrese de que está instalado Visual Studio y cree una aplicación de consola que se utilice para crear los recursos.

1. Si aún no lo ha hecho, instale [Visual Studio](https://www.visualstudio.com/).
2. En Visual Studio, haga clic en **Archivo** > **Nuevo** > **proyecto**.
3. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola (.NET Framework)**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

## <a name="step-2-install-libraries"></a>Paso 2: Instalación de las bibliotecas

Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar estos pasos. Para obtener las bibliotecas que necesita en Visual Studio, siga estos pasos:


1. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes NuGet para la solución** y, luego, en **Examinar**.
2. Escriba *Microsoft.IdentityModel.Clients.ActiveDirectory* en el cuadro de búsqueda, seleccione su proyecto y haga clic en **Instalar**. A continuación, siga las instrucciones para instalar el paquete.
3. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.Compute* en el cuadro de búsqueda, haga clic en **Instalar** y siga las instrucciones para instalar el paquete.
4. Escriba *Microsoft.Azure.Management.Network* en el cuadro de búsqueda, haga clic en **Instalar** y siga las instrucciones para instalar el paquete.
5. Escriba *Microsoft.Azure.Management.Storage* en el cuadro de búsqueda, haga clic en **Instalar** y siga las instrucciones para instalar el paquete.
6. Escriba *Microsoft.Azure.Management.ResourceManager* en el cuadro de búsqueda, haga clic en **Instalar** y siga las instrucciones para instalar el paquete.

Ahora está preparado para comenzar a usar las bibliotecas para crear su aplicación.

## <a name="step-3-create-the-credentials-used-to-authenticate-requests"></a>Paso 3: Creación de las credenciales que se utilizan para autenticar las solicitudes

Antes de empezar este paso, asegúrese de que tiene acceso a una [entidad de servicio de Active Directory](../../resource-group-authenticate-service-principal.md). Desde la entidad de servicio, adquiera un token para autenticar solicitudes a Azure Resource Manager.

1. Abra el archivo Program.cs del proyecto que ha creado y agregue las siguientes instrucciones using a las instrucciones existentes en la parte superior del archivo:
   
    ```
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
    ```

2. Agregue este método a la clase Program para obtener el token necesario para crear las credenciales:
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("client-id", "client-secret");
      var context = new AuthenticationContext("https://login.windows.net/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Reemplace estos valores:
    
    - *client-id* con el identificador de la aplicación de Azure Active Directory. Puede encontrar este identificador en la hoja Propiedades de la aplicación de AD. Para encontrar la aplicación de AD en Azure Portal, haga clic en **Azure Active Directory** en el menú de recursos y, luego, haga clic en **Registros de aplicación**.
    - *client-secret* con la clave de acceso de la aplicación de AD. Puede encontrar este identificador en la hoja Propiedades de la aplicación de AD.
    - *tenant-id* con el identificador de inquilino de la suscripción. Puede encontrar el identificador del inquilino en la hoja Propiedades de Azure Active Directory en Azure Portal. Se etiqueta como *identificación de directorio*.

3. Para llamar al método que agregó anteriormente, agregue este código al método Main en el archivo Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Guarde el archivo Program.cs.

## <a name="step-4-create-the-resources"></a>Paso 4: Creación de los recursos

### <a name="register-the-providers-and-create-a-resource-group"></a>Registre los proveedores y cree un grupo de recursos

Todos los recursos deben encontrarse en un grupo de recursos. Para poder agregar recursos a un grupo, la suscripción se debe registrar con los proveedores de recursos.

1. Agregue variables al método Main de la clase Program para especificar los nombres que quiere usar para los recursos:

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var storageName = "myStorageAccount";
    var ipName = "myPublicIP";
    var subnetName = "mySubnet";
    var vnetName = "myVnet";
    var nicName = "myNIC";
    var avSetName = "myAVSet";
    var vmName = "myVM";  
    var location = "location";
    var adminName = "adminName";
    var adminPassword = "adminPassword";
    ```

    Reemplace estos valores:

    - Todos los nombres de los recursos que empiezan por *my* con nombres que tengan sentido para su entorno.
    - *subscriptionId* con su identificador de suscripción. Puede encontrar el identificador de suscripción en la hoja Suscripciones de Azure Portal.
    - *location* con la [región de Azure](https://azure.microsoft.com/regions/) donde desea crear los recursos.
    - *adminName* con el nombre de la cuenta de administrador en la máquina virtual.
    - *adminPassword* con la contraseña de la cuenta de administrador.

2. Para crear el grupo de recursos y registrar los proveedores, agregue este método a la clase Program:
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      Console.WriteLine("Registering the providers...");
      var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
      Console.WriteLine(rpResult.RegistrationState);
   
      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Para llamar al método que ha agregado anteriormente, agregue este código al método Main:
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Al usar un disco no administrado, se necesita una [cuenta de almacenamiento](../../storage/storage-create-storage-account.md) para almacenar el archivo de disco duro virtual que se crea para la máquina virtual.

1. Para crear la cuenta de almacenamiento, agregue este método a la clase Program:

    ```   
    public static async Task<StorageAccount> CreateStorageAccountAsync(
      TokenCredentials credential,       
      string groupName,
      string subscriptionId,
      string location,
      string storageName)
    {
      var storageManagementClient = new StorageManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the storage account...");

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
    ```

2. Para llamar al método que ha agregado anteriormente, agregue este código al método Main:
   
    ```
    var stResult = CreateStorageAccountAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      storageName);
    Console.WriteLine(stResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

Se necesita una dirección IP pública para la comunicación con la máquina virtual.

1. Para crear la dirección IP pública de la máquina virtual, agregue este método a la clase Program:
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
      TokenCredentials credential,  
      string groupName,
      string subscriptionId,
      string location,
      string ipName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the public ip...");

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
    ```

2. Para llamar al método que ha agregado anteriormente, agregue este código al método Main:
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      ipName);
    Console.WriteLine(ipResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-virtual-network"></a>Crear una red virtual

Una máquina virtual creada con el modelo de implementación del Administrador de recursos debe estar en una red virtual.

1. Para crear una subred y una red virtual, agregue este método a la clase Program:

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string vnetName,
      string subnetName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      var subnet = new Subnet
        {
          Name = subnetName,
          AddressPrefix = "10.0.0.0/24"
        };
   
      var address = new AddressSpace 
        {
          AddressPrefixes = new List<string> { "10.0.0.0/16" }
        };
         
      Console.WriteLine("Creating the virtual network...");
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
    ```

2. Para llamar al método que ha agregado anteriormente, agregue este código al método Main:

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      vnetName,
      subnetName);
    Console.WriteLine(vnResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-network-interface"></a>Crear una interfaz de red

Una máquina virtual requiere una interfaz de red para comunicarse en la red virtual que acaba de crear.

1. Para crear una interfaz de red, agregue este método a la clase Program:

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        vnetName,
        subnetName
      );
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        ipName
      );
   
      Console.WriteLine("Creating the network interface...");
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
                    PublicIPAddress = publicIP,
                    Subnet = subnet
                  }
              }
          }
      );
    }
    ```

2. Para llamar al método que ha agregado anteriormente, agregue este código al método Main:
   
    ```
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
    ```

### <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad

Los conjuntos de disponibilidad facilitan la administración del mantenimiento de las máquinas virtuales que utiliza su aplicación.

1. Para crear un conjunto de disponibilidad, agregue este método a la clase Program:

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string avsetName)
    {
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
        
      Console.WriteLine("Creating the availability set...");
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        avsetName,
        new AvailabilitySet()
          { Location = location }
      );
    }
    ```

2. Para llamar al método que ha agregado anteriormente, agregue este código al método Main:
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      credential,  
      groupName,
      subscriptionId,
      location,
      avSetName);
    Console.ReadLine();
    ```

### <a name="create-a-virtual-machine"></a>de una máquina virtual

Ahora que ha creado todos los recursos auxiliares, puede crear una máquina virtual.

1. Para crear una máquina virtual, agregue este método a la clase Program:

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };  
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        nicName);
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        avsetName);
   
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
    }
    ```

    > [!NOTE]
    > En este tutorial se crea una máquina virtual donde se ejecuta una versión del sistema operativo Windows Server. Para más información sobre cómo seleccionar otras imágenes, consulte [Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    >

2. Para llamar al método que ha agregado anteriormente, agregue este código al método Main:
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      nicName,
      avSetName,
      storageName,
      adminName,
      adminPassword,
      vmName);
    Console.WriteLine(vmResult.Result.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-4-delete-the-resources"></a>Paso 4: Eliminación de los recursos

Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. Si quiere eliminar las máquinas virtuales y todos los recursos auxiliares, lo único que tiene que hacer es eliminar el grupo de recursos.

1. Para eliminar el grupo de recursos, agregue este método a la clase Program:
   
    ```
    public static async void DeleteResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId)
    {
      Console.WriteLine("Deleting resource group...");
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. Para llamar al método que ha agregado anteriormente, agregue este código al método Main:
   
    ```   
    DeleteResourceGroupAsync(
      credential,
      groupName,
      subscriptionId);
    Console.ReadLine();
    ```

## <a name="step-5-run-the-console-application"></a>Paso 5: Ejecutar la aplicación de la consola

1. Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

2. Presione **Entrar** cuando aparezca el estado *Correcto*. 
   
3. Antes de presionar **Entrar** para comenzar la eliminación de recursos y después de que se cree la máquina virtual, puede dedicar unos minutos a comprobar la creación de los recursos en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes
* Aproveche las ventajas de usar una plantilla para crear una máquina virtual con la información que se muestra en [Implementación de una máquina virtual de Azure con C# y una plantilla de Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aprenda a administrar la máquina virtual que ha creado. Para ello, consulte [Administración de máquinas virtuales con Azure Resource Manager y C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


