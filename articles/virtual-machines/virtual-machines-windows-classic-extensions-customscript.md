---
title: Extensión de script personalizada en una máquina virtual Windows | Microsoft Docs
description: Automatizar tareas de configuración de máquina virtual de Azure mediante la extensión de script personalizada para ejecutar scripts de PowerShell en una máquina virtual remota de Windows
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/06/2015
ms.author: kundanap

---
# Extensión de script personalizada para máquinas virtuales Windows
Este artículo ofrece información general del uso de la extensión de script personalizada en máquinas virtuales Windows mediante los cmdlets de Azure PowerShell con las API de Azure Service Management.

Las extensiones de máquina virtual (VM) se crean por Microsoft y editores de confianza de terceros para extender la funcionalidad de la máquina virtual. Para obtener información general de las extensiones de máquina virtual, vea 
[Características y extensiones de máquina virtual de Azure](virtual-machines-windows-extensions-features.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Obtenga información acerca de cómo [realizar estos pasos con el modelo de Resource Manager](virtual-machines-windows-extensions-customscript.md).

## Introducción a la extensión de script personalizada
La extensión de script personalizada para Windows le permite ejecutar scripts de PowerShell en una VM remota, sin iniciar sesión en ella. Puede ejecutar los scripts después de aprovisionar la VM o en cualquier momento durante el ciclo de vida de la VM sin necesidad de abrir los puertos adicionales. Los casos de uso más comunes para la ejecución de la extensión de script personalizada incluyen la ejecución, la instalación y la configuración de software adicional en la VM después de su aprovisionamiento.

### Requisitos previos para ejecutar la extensión de script personalizada
1. Instale <a href="http://azure.microsoft.com/downloads" target="_blank">cmdlets de Azure PowerShell</a>, versión 0.8.0 o superior.
2. Si desea que los scripts se ejecuten en una VM existente, asegúrese de que el Agente de VM está habilitado en la VM. Si no está instalado, siga estos [pasos](virtual-machines-windows-classic-agents-and-extensions.md). Si la VM se crea en el portal de Azure, el agente de VM se instala de forma predeterminada.
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden proceder de un único contenedor de almacenamiento o de varios.
4. El script debe crearse de forma tal que el script de entrada que inicia la extensión inicie, a su vez, otros scripts.

## Escenarios de la extensión de script personalizada
### Cargar archivos en el contenedor predeterminado
El siguiente ejemplo le muestra cómo puede ejecutar los scripts en la VM si están en el contenedor de almacenamiento de la cuenta predeterminada de su suscripción. Cargue los scripts en ContainerName. Puede comprobar la cuenta de almacenamiento predeterminada mediante el comando **Get-AzureSubscription –Default**.

En el ejemplo siguiente se crea una nueva VM, pero puede ejecutar el mismo escenario en una VM existente.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Cargar archivos a un contenedor de almacenamiento no predeterminado
Este escenario muestra cómo usar un contenedor de almacenamiento no predeterminado en la misma suscripción o en una suscripción diferente para cargar archivos y scripts. Este ejemplo muestra una VM existente, pero se pueden realizar las mismas operaciones al crear una VM.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Carga de secuencias de comandos en varios contenedores en diferentes cuentas de almacenamiento
  Si los archivos de scripts se almacenan en varios contenedores, debe ofrecer la dirección URL de firmas de acceso compartido (SAS) completas para que los archivos ejecuten los scripts.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Agregar la extensión de script personalizada desde el Portal de Azure
Vaya a la VM en el <a href="https://portal.azure.com/ " target="_blank">Portal de Azure </a> y especifique el archivo de scripts que se debe ejecutar para agregar la extensión.

  ![Especificar l archivo de script][5]

### Desinstalar la extensión de script personalizada
Puede desinstalar la extensión del script personalizado de la VM con el siguiente comando.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Usar la extensión de scripts personalizada con plantillas
Para obtener información sobre cómo utilizar la extensión del script personalizado con plantillas de Azure Resource Manager, consulte [Uso de la extensión de script personalizada para máquinas virtuales de Windows con plantillas de Azure Resource Manager](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png

<!---HONumber=AcomDC_0824_2016-->