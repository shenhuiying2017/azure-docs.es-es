---
title: "Administración de máquinas virtuales con Azure Resource Manager y C# | Microsoft Docs"
description: "Administración de máquinas virtuales con Azure Resource Manager y C#."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 93898bad-b861-4359-9a4b-348e1d491822
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: d32dd39b7835e327e5bc1a40237da43617f6e61c
ms.lasthandoff: 03/02/2017


---
# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Administración de máquinas virtuales de Azure con Azure Resource Manager y C# #

Las tareas de este artículo, como iniciar, detener y actualizar, muestran cómo administrar máquinas virtuales.

## <a name="set-up-visual-studio"></a>Configuración de Visual Studio

### <a name="create-a-project"></a>Creación de un proyecto

Asegúrese de que está instalado Visual Studio y cree una aplicación de consola que se use para administrar las máquinas virtuales.

1. Si aún no lo ha hecho, instale [Visual Studio](https://www.visualstudio.com/).
2. En Visual Studio, haga clic en **Archivo** > **Nuevo** > **proyecto**.
3. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

### <a name="install-libraries"></a>Instalación de bibliotecas

Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para realizar las tareas en este artículo. Para obtener las bibliotecas que necesita en Visual Studio, siga estos pasos:

1. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet** y, luego, en **Examinar**.
2. Escriba *Microsoft.IdentityModel.Clients.ActiveDirectory* en el cuadro de búsqueda, haga clic en **Instalar**y, luego, siga las instrucciones para instalar el paquete.
3. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.Compute* en el cuadro de búsqueda, haga clic en **Instalar** y siga las instrucciones para instalar el paquete.

Ahora está preparado para comenzar a usar las bibliotecas para administrar sus máquinas virtuales.

### <a name="create-credentials-and-add-variables"></a>Creación de las credenciales y adición de variables

Para interactuar con el Azure Resource Manager, asegúrese de que tiene acceso a una [entidad de servicio de Active Directory](../azure-resource-manager/resource-group-authenticate-service-principal.md). Desde la entidad de servicio, adquiera un token para autenticar solicitudes a Azure Resource Manager.

1. Abra el archivo Program.cs del proyecto que ha creado y agregue las siguientes instrucciones using en la parte superior del archivo:

    ```   
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. Agregue variables al método Main de la clase Program para especificar el nombre del grupo de recursos y el nombre de la máquina virtual, así como el identificador de la suscripción:

    ```   
    var groupName = "myResourceGroup";
    var vmName = "myVM";  
    var subscriptionId = "subsciptionId";
    ```

    Puede encontrar el identificador de suscripción en la hoja Suscripciones de Azure Portal.    

3. Agregue este método a la clase Program para obtener el token necesario para crear las credenciales:
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Reemplace estos valores:
    
    - *{client-id}* con el identificador de la aplicación de Azure Active Directory. Puede encontrar este identificador en la hoja Propiedades de la aplicación de AD. Para encontrar la aplicación de AD en Azure Portal, haga clic en **Azure Active Directory** en el menú de recursos y, luego, haga clic en **Registros de aplicación**.
    - *{client-secret}* con la clave de acceso de la aplicación de AD. Puede encontrar este identificador en la hoja Propiedades de la aplicación de AD.
    - *{Id. de inquilino}* con el identificador del inquilino de su suscripción. Puede encontrar el identificador del inquilino en la hoja Propiedades de Azure Active Directory en Azure Portal. Se etiqueta como *identificación de directorio*.

4. Para llamar al método que agregó anteriormente, agregue este código al método Main en el archivo Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

5. Guarde el archivo Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Visualización de información acerca de una máquina virtual

1. Agregue este método a la clase Program en el proyecto que creó anteriormente:

    ```   
    public static async void GetVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Getting information about the virtual machine...");
   
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
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
    ```

2. Para llamar al método que acaba de agregar, agregue este código al método Main:

    ```   
    GetVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

3. Guarde el archivo Program.cs.

4. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.
   
    Al ejecutar este método debería ver algo parecido a esto:
   
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_D1_v2
   
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

## <a name="stop-a-virtual-machine"></a>Detención de una máquina virtual

Puede detener una máquina virtual de dos maneras. Puede detener una máquina virtual y mantener toda su configuración, pero se le seguirá cobrando, o puede detener una máquina virtual y desasignarla. Cuando se desasigna una máquina virtual, todos los recursos asociados a ella también se desasignan y se le deja de cobrar por ellos.

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

    ```   
    public static async void StopVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Stopping the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    Si desea desasignar la máquina virtual, cambie la llamada de PowerOff por este código:
    
    ```
    computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

3. Para llamar al método que acaba de agregar, agregue este código al método Main:
    
    ```
    StopVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.
   
    Debería ver el estado de la máquina virtual cambiado a Detenido. Si ejecutó la llamada de método Desasignar, el estado es Detenido (desasignado).

## <a name="start-a-virtual-machine"></a>Inicio de una máquina virtual

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

    ```   
    public static async void StartVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Starting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

    ```   
    StartVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.
   
    Debería ver el estado de la máquina virtual cambiado a En ejecución.

## <a name="restart-a-running-virtual-machine"></a>Reinicio de una máquina virtual en ejecución

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

    ```   
    public static async void RestartVirtualMachineAsync(
      TokenCredentials credential,
      string groupName,
      string vmName,
      string subscriptionId)
    {
      Console.WriteLine("Restarting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
    }
    ```

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

    ```   
    RestartVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

## <a name="resize-a-virtual-machine"></a>Cambio de tamaño de una máquina virtual

En este ejemplo se muestra cómo cambiar el tamaño de una máquina virtual en ejecución.

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

    ```   
    public static async void UpdateVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Updating the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_D2_v2";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

3. Para llamar al método que acaba de agregar, agregue este código al método Main:

    ```   
    UpdateVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.
   
    Debería ver que el tamaño de la máquina virtual ha cambiado a Standard_D2_v2.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Adición de disco de datos a una máquina virtual

En este ejemplo se muestra cómo agregar un disco de datos a una máquina virtual en funcionamiento.

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:

    ```   
    public static async void AddDataDiskAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
          {
            Lun = 0,
            Name = "mydatadisk1",
            Vhd = new VirtualHardDisk
              {
                Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
              },
            CreateOption = DiskCreateOptionTypes.Empty,
            DiskSizeGB = 2,
            Caching = CachingTypes.ReadWrite
          });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

    Reemplace mystorage1 con el nombre de la cuenta de almacenamiento donde se almacenan los discos de la máquina virtual.

3. Para llamar al método que acaba de agregar, agregue este código al método Main:
    
    ```
    AddDataDiskAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

## <a name="delete-a-virtual-machine"></a>Eliminación de una máquina virtual

1. Comente cualquier código que haya agregado anteriormente al método Main, excepto el código para obtener las credenciales.

2. Agregue este método a la clase Program:
   
    ```
    public static async void DeleteVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Deleting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
    }
    ```

3. Para llamar al método que acaba de agregar, agregue este código al método Main:
    
    ```
    DeleteVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Guarde el archivo Program.cs.

5. Haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

## <a name="next-steps"></a>Pasos siguientes

- Si se produjeron problemas con una implementación, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).
- Obtenga información sobre cómo implementar una máquina virtual y los recursos de apoyo leyendo [Implementación de una máquina virtual de Azure con C#](virtual-machines-windows-csharp.md).
- Aproveche las ventajas de usar una plantilla para crear una máquina virtual con la información que se muestra en [Implementación de una máquina virtual de Azure con C# y una plantilla de Resource Manager](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



