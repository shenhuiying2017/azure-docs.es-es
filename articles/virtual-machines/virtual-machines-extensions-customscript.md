<properties
   pageTitle="Extensión de la secuencia de comandos personalizada en Windows"
   description="Automatización de las tareas de configuración de máquina virtual de Azure mediante la extensión de la secuencia de comandos personalizada en Windows"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="madhana"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Extensión de la secuencia de comandos personalizada para Windows

Este artículo proporciona información general del uso de la extensión de la secuencia de comandos personalizada en Windows mediante los cmdlets de Powershell de Azure.


Extensiones de máquina virtual (VM) creadas por Microsoft y editores de confianza de terceros para extender la funcionalidad de la máquina virtual. Para obtener información general detallada de las extensiones de máquina virtual, consulte la <a href="https://msdn.microsoft.com/library/azure/dn606311.aspx" target="_blank">Documentación de MSDN</a>.

## Introducción a la extensión de la secuencia de comandos personalizada

La extensión de la secuencia de comandos personalizada para Windows le permite ejecutar scripts de Powershell en una máquina virtual remota, sin iniciar sesión en ella. Las secuencias de comandos se pueden ejecutar después de aprovisionar la máquina virtual o en cualquier momento durante el ciclo de vida de la máquina virtual sin necesidad de abrir los puertos adicionales en la máquina virtual. El caso de uso más común de la secuencia de comandos personalizada incluye la ejecución de la instalación y la configuración de software adicional en la máquina virtual después del aprovisionamiento.

### Requisitos previos para ejecutar la extensión de la secuencia de comandos personalizada

1. Instale Azure PowerShell Cmdlets V0.8.0 o superior desde <a href="http://azure.microsoft.com/downloads" target="_blank">aquí</a>.
2. Si las secuencias de comandos se van a ejecutar en una máquina virtual existente, asegúrese de que el agente de máquina virtual está habilitado en la máquina virtual; si no lo está, siga las indicaciones de este <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">artículo</a> para instalar uno.
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden proceder de un único contenedor de almacenamiento o de varios.
4. La secuencia de comandos debe crearse de forma tal que la secuencia de comandos de entrada que inicia la extensión inicie, a su vez, otras secuencias de comandos.

## Escenarios de la extensión de la secuencia de comandos personalizada:

 ### Carga de archivos en el contenedor predeterminado: Si los secuencias de comandos se encuentran en el contenedor de almacenamiento de la cuenta predeterminada de su suscripción, el fragmento de cmdlet siguiente le muestra cómo puede ejecutarlos en la máquina virtual. El valor de ContainerName del ejemplo siguiente es donde se cargan las secuencias de comandos. Puede comprobar la cuenta de almacenamiento predeterminada mediante el cmdlet ‘Get-AzureSubscription –Default’

Nota: este caso de uso crea una máquina virtual nueva, pero también se pueden realizar las mismas operaciones en una máquina virtual existente.

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Cargar archivos a un contenedores de almacenamiento no predeterminados.

Este caso de uso muestra cómo usar un almacenamiento no predeterminado en la misma suscripción o en una suscripción diferente para cargar archivos y secuencias de comandos. Aquí vamos a usar una máquina virtual existente, pero se pueden realizar las mismas operaciones al crear una máquina virtual nueva.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM
  ### Carga de secuencias de comandos en varios contenedores en diferentes cuentas de almacenamiento. Si los archivos de la secuencia de comandos se almacenan en varios contenedores, para ejecutar dichas secuencias, debe proporcionar la dirección URL de SAS completa de estos archivos.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Agregar la extensión de la secuencia de comandos personalizada desde el Portal.
Vaya a la máquina virtual en el <a href="https://portal.azure.com/ " target="_blank">Portal de vista previa de Azure</a> y especifique el archivo de scripts que se debe ejecutar para agregar la extensión. ![][5]

  ### Desinstalación de la extensión de scripts personalizada.

La extensión de la secuencia de comandos personalizada se puede desinstalar de la máquina virtual mediante el cmdlet siguiente

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Uso de la extensión de scripts personalizada con plantillas

Para obtener información sobre cómo usar la extensión de scripts personalizada con plantillas, haga clic en la documentación [aquí](virtual-machines-extensions-customscript -with template.md).

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=August15_HO8-->