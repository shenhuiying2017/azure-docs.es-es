<properties
	pageTitle="Administración de máquinas virtuales con Azure Resource Manager y C# | Microsoft Azure"
	description="Administración de máquinas virtuales con Azure Resource Manager y C#."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/17/2016"
	ms.author="davidmu"/>

# Administración de máquinas virtuales de Azure con Azure Resource Manager y C#  

Para completar las tareas de este artículo, necesitará lo siguiente:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx).
- [Un token de autenticación](../resource-group-authenticate-service-principal.md)

## Creación de un proyecto de Visual Studio e instalación de paquetes

Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar las tareas en este artículo. Debe instalar la biblioteca de autenticación de Azure Active Directory y la biblioteca del proveedor de recursos de equipo. Para obtener estas bibliotecas en Visual Studio, haga lo siguiente:

1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

4. Escriba *Active Directory* en el cuadro de búsqueda, haga clic en **Instalar** para el paquete de la biblioteca de autenticación de Active Directory y, a continuación, siga las instrucciones para instalar el paquete.

5. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.Compute* en el cuadro de búsqueda, haga clic en **Instalar** para las bibliotecas .NET de proceso y, luego, siga las instrucciones para instalar el paquete.

Ahora está preparado para comenzar a usar las bibliotecas para administrar sus máquinas virtuales.

## Configuración del proyecto

Ahora que la aplicación Azure Active Directory se crea y se instala la biblioteca de autenticación, de a la información de la aplicación formato de credenciales que se utilizan para autenticar las solicitudes al Administrador de recursos de Azure.

1. Abra el archivo Program.cs del proyecto que ha creado y agregue las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Agregue variables al método Main de la clase Program para especificar los nombres de los recursos que desea administrar, la ubicación de los recursos (por ejemplo, "Centro de EE. UU.") y el identificador de la suscripción:

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var location = "location name";
        var subscriptionId = "subsciption id";

    Reemplace todos los valores de variables por los nombres y el identificador que desea usar. Para encontrar el identificador de la suscripción, ejecute Get-AzureRmSubscription.
    
3. Agregue este método a la clase Program para obtener el token necesario para crear las credenciales:

	    private static string GetAuthorizationHeader()
	    {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);

          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }
	
    Reemplace {application-id} por el identificador de aplicación que anotó anteriormente, {password} por la contraseña que eligió para la aplicación AD y {tenant-id} por el identificador del inquilino de su suscripción.
    
4. Agregue este código al método Main en Program.cs para crear las credenciales:

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

5. Guarde el archivo Program.cs.

## Visualización de información acerca de una máquina virtual

1. Agregue este método a la clase Program en el proyecto que creó anteriormente:

        public static void GetVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var vmResult = computeManagementClient.VirtualMachines.Get(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);

          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);

          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }

          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }

          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }

          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
        }

2. Agregue este código al método Main para llamar al método que acaba de agregar:

        GetVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Guarde el archivo Program.cs.

4. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

	Al ejecutar este método debería ver algo parecido a esto:
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite

          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True

          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1

          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM

          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM

          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus

          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## Inicio de una máquina virtual

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

        public static void StartVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          computeManagementClient.VirtualMachines.Start(groupName, vmName);
        }

3. Agregue este código al método Main para llamar al método que acaba de agregar:

        StartVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

	Debería ver el estado de la máquina virtual cambiado a En ejecución.

## Detención de una máquina virtual

Puede detener una máquina virtual de dos maneras. Puede detener una máquina virtual y mantener todos sus valores, pero seguirá pagando por ella, o puede detener una máquina virtual y desasignarla, por lo que también se desasignarán todos los recursos asociados a ella y finalizará la facturación de la máquina virtual.

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

        public static void StopVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          computeManagementClient.VirtualMachines.PowerOff(groupName, vmName);
        }

	Si desea desasignar la máquina virtual, cambie la llamada PowerOff a esto:

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. Agregue este código al método Main para llamar al método que acaba de agregar:

        StopVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

    Debería ver el estado de la máquina virtual cambiado a Detenido. Si ejecutó la llamada de método Desasignar, el estado es Detenido (desasignado).

## Reinicio de una máquina virtual en ejecución

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

        public static void RestartVirtualMachine(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          computeManagementClient.VirtualMachines.Restart(groupName, vmName);
        }

3. Agregue este código al método Main para llamar al método que acaba de agregar:

        RestartVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

## Eliminación de una máquina virtual

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

        public static void DeleteVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          computeManagementClient.VirtualMachines.Delete(groupName, vmName);
        }

3. Agregue este código al método Main para llamar al método que acaba de agregar:

        DeleteVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

## Actualización de una máquina virtual

En este ejemplo se muestra cómo cambiar el tamaño de una máquina virtual en ejecución.

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

        public static void UpdateVirtualMachine(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var vmResult = computeManagementClient.VirtualMachines.Get(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          computeManagementClient.VirtualMachines.CreateOrUpdate(groupName, vmName, vmResult);
        }

3. Agregue este código al método Main para llamar al método que acaba de agregar:

        UpdateVirtualMachine(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

    Debería ver el tamaño de la máquina virtual cambiado a Standard\_A1.
    
## Pasos siguientes

Si se produjeron problemas con la implementación, debería echar un vistazo a [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0420_2016-->