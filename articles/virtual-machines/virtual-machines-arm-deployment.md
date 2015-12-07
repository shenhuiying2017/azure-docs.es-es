<properties
	pageTitle="Implementación de recursos mediante bibliotecas de .NET | Microsoft Azure"
	description="Obtenga información sobre cómo utilizar las bibliotecas .NET de proceso, almacenamiento y red para crear y eliminar recursos de Microsoft Azure."
	services="virtual-machines,virtual-network,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


Este tutorial muestra cómo utilizar algunos de los clientes disponibles en las bibliotecas .NET de proceso, red y almacenamiento para crear y eliminar recursos de Microsoft Azure. También muestra cómo autenticar las solicitudes al Administrador de recursos de Azure con Azure Active Directory.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Para completar este tutorial, también necesita:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Cuenta de Almacenamiento de Azure](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/es-ES/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/es-ES/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Tardará unos 30 minutos en realizar estos pasos.

## Paso 1: Adición de una aplicación a Azure AD y establecer permisos

Para usar Azure AD para autenticar las solicitudes con el Administrador de recursos de Azure, es necesario agregar una aplicación en el directorio predeterminado. Siga este procedimiento para agregar una aplicación:

1. Abra un símbolo del sistema de Azure PowerShell y, a continuación, ejecute este comando y escriba las credenciales de la suscripción cuando se le solicite:

	    Login-AzureRmAccount

2. Reemplace {password} en el siguiente comando por la contraseña que desea utilizar y, a continuación, ejecútelo para crear la aplicación:

	    New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]Anote el identificador de la aplicación que se devuelve después de crear la aplicación, ya que la necesitará para el siguiente paso. También puede encontrar el identificador de la aplicación en el campo de id. de cliente de la aplicación en la sección de Active Directory del portal de vista previa de Azure.

3. Reemplace {application-id} por el identificador que acaba de anotar y, a continuación, cree la entidad de servicio correspondiente a la aplicación:

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Establezca el permiso para usar la aplicación:

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Paso 2: Creación de un proyecto de Visual Studio e instalar las bibliotecas

Los Paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar este tutorial. Debe instalar la biblioteca de administración de recursos de Azure, la biblioteca de autenticación de Azure Active Directory y la biblioteca de proveedor de recursos de equipo. Para obtener estas bibliotecas en Visual Studio, haga lo siguiente:

1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

4. Escriba *Active Directory* en el cuadro de búsqueda, haga clic en **Instalar** para el paquete de la biblioteca de autenticación de Active Directory y, a continuación, siga las instrucciones para instalar el paquete.

5. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Proceso de Azure* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas .NET de proceso y, a continuación, siga las instrucciones para instalar el paquete.

6. Escriba *Red de Azure* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas .NET de red y, a continuación, siga las instrucciones para instalar el paquete.

7. Escriba *Almacenamiento de Azure* en el cuadro de búsqueda, haga clic en **instalar** para las bibliotecas .NET de red y, a continuación, siga las instrucciones para instalar el paquete.

8. Escriba *Administración de recursos de Azure* en el cuadro de búsqueda, haga clic en **Instalar** para las bibliotecas de administración de recursos.

Ahora está preparado para comenzar a usar las bibliotecas para crear la aplicación.

## Paso 3: Creación de las credenciales que se utilizan para autenticar las solicitudes

Ahora que la aplicación Azure Active Directory se crea y se instala la biblioteca de autenticación, de a la información de la aplicación formato de credenciales que se utilizan para autenticar las solicitudes al Administrador de recursos de Azure. Haga lo siguiente:

1.	Abra el archivo Program.cs para el proyecto que ha creado y, a continuación, agregue las siguientes instrucciones using a la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure.Management.Resources;
		using Microsoft.Azure.Management.Resources.Models;
		using Microsoft.Azure.Management.Storage;
		using Microsoft.Azure.Management.Storage.Models;
		using Microsoft.Azure.Management.Network;
		using Microsoft.Azure.Management.Network.Models;
		using Microsoft.Azure.Management.Compute;
		using Microsoft.Azure.Management.Compute.Models;


2. Agregue el método siguiente a la clase Program para obtener el token necesario para crear las credenciales:

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);

          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Reemplace {application-id} por el identificador de aplicación que anotó anteriormente, {password} por la contraseña que eligió para la aplicación AD y {tenant-id} por el identificador del inquilino de su suscripción. Puede encontrar el Id. del inquilino ejecutando Get-AzureSubscription.

3.	Agregue el código siguiente al método Main en el archivo Program.cs para crear las credenciales:

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	Reemplace {subscription-id} por el identificador de su suscripción.

4.	Guarde el archivo Program.cs.

## Paso 4: Adición del código para registrar los proveedores y crear los recursos

### Registre los proveedores y cree un grupo de recursos

Los recursos siempre se implementan en un grupo de recursos. Utilice las clases [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) y [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) para crear el grupo de recursos en el que se implementan los recursos.

1.	Agregue el método siguiente a la clase Program para crear el grupo de recursos:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");

          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

		CreateResourceGroup(credential);
		Console.ReadLine();

###Crear una cuenta de almacenamiento

Se necesita una cuenta de almacenamiento para almacenar el archivo de disco duro virtual que se crea para la máquina virtual.

1.	Agregue el método siguiente a la clase Program para crear la cuenta de almacenamiento:

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");

          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

		CreateStorageAccount(credential);
		Console.ReadLine();

###Creación de configuración de red

Una máquina virtual es más productiva, cuando se agrega a una red virtual.

1.	Agregue el método siguiente a la clase Program para crear una subred, una dirección IP pública y una red virtual:

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

		CreateNetwork(credential);
		Console.ReadLine();

###de una máquina virtual

Ahora que ha creado todos los recursos auxiliares, puede crear una máquina virtual.

1.	Agregue el método siguiente a la clase Program para crear la máquina virtual:

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);

            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]Los nombres de imagen vhd cambiarán regularmente en la galería de imágenes, por lo que necesita obtener un nombre de imagen actual para implementar la máquina virtual. Para ello, consulte [Administrar imágenes de Windows con Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx), y, a continuación, reemplace {source-image-name} por el nombre del archivo vhd que desea utilizar. Por ejemplo, "a699494373c04fc0bc8f2bb1389d6106\_\_Windows-Server-2012-R2-201411.01-en.us-127GB.vhd".

	Reemplace {subscription-id} por el identificador de su suscripción.

2.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

		CreateVirtualMachine(credential);
    Console.ReadLine();

##Paso 5: Adición del código para eliminar los recursos

Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. Si desea eliminar las máquinas virtuales y todos los recursos auxiliares, lo único que tiene que hacer es eliminar el grupo de recursos.

1.	Agregue el método siguiente a la clase Program para crear el grupo de recursos:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

		DeleteResourceGroup(credential);
        Console.ReadLine();

##Paso 6: Ejecución de la aplicación de la consola.

1.	Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

2.	Presione **Entrar** después de que se devuelva cada código de estado para crear cada recurso. Después de crear la máquina virtual, realice el siguiente paso antes de presionar Entrar para eliminar todos los recursos.

	Esta aplicación de consola tardará unos cinco minutos en ejecutarse completamente de principio a fin. Antes de presionar Entrar para comenzar la eliminación de recursos, puede dedicar unos minutos a comprobar la creación de los recursos en el Portal de vista previa de Azure antes de eliminarlos.

3. Vaya a los registros de auditoría en el Portal de vista previa de Azure para ver el estado de los recursos:

	![Crear una aplicación de AD](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_1125_2015-->