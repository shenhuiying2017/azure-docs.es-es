---
title: "Creación de una máquina virtual de Azure con Accelerated Networking | Microsoft Docs"
description: "Aprenda a crear una máquina virtual con Accelerated Networking."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 449425189a3b42dcb2c31316c1c8e38fac69d761
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Creación de una máquina virtual con Accelerated Networking

En este tutorial, obtendrá información sobre cómo crear una máquina virtual de Azure con Accelerated Networking. Las redes aceleradas están en disponibilidad general para Windows y en versión preliminar pública para distribuciones de Linux específicas. Accelerated Networking habilita la virtualización de E/S de raíz única (SR-IOV) en una máquina virtual (VM), lo que mejora significativamente su rendimiento en la red. Esta ruta de alto rendimiento omite el host de la ruta de acceso de datos, lo que reduce la latencia, la inestabilidad y la utilización de la CPU para su uso con las cargas de trabajo de red más exigentes en tipos de máquina virtual compatibles. En la siguiente imagen, se muestra la comunicación entre dos máquinas virtuales (VM) con y sin Accelerated Networking:

![De comparación](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Sin Accelerated Networking, todo el tráfico de red de entrada y salida de la máquina virtual tiene que atravesar el host y el conmutador virtual. El conmutador virtual se encarga de toda la aplicación de directivas, como grupos de seguridad de red, listas de control de acceso, aislamiento y otros servicios virtualizados de red, al tráfico de red. Para más información sobre los conmutadores virtuales, lea el artículo sobre [virtualización de red y conmutador virtual de Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Con Accelerated Networking, el tráfico de red llega a la interfaz de red (NIC) de la máquina virtual y se reenvía después a la máquina virtual. Todas las directivas de red que el conmutador virtual aplica sin Accelerated Networking se descargan y aplican en el hardware. La aplicación de directivas en hardware permite que la NIC reenvíe el tráfico de red directamente a la máquina virtual, pasando por alto el host y el conmutador virtual, al mismo tiempo que se mantienen todas las directivas aplicadas en el host.

Las ventajas de Accelerated Networking solo se aplican a la máquina virtual donde esté habilitado. Para obtener resultados óptimos, lo ideal es habilitar esta característica en al menos dos máquinas virtuales conectadas a la misma instancia de Azure Virtual Network (VNet). Al comunicarse entre redes virtuales o conectarse de forma local, esta característica tiene un efecto mínimo sobre la latencia total.

> [!WARNING]
> Esta versión preliminar pública de Linux podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Esta característica no se admite, puede tener funcionalidades limitadas y no estar disponible en todas las ubicaciones de Azure. Para ver las notificaciones más actuales sobre la disponibilidad y el estado de esta característica, consulte la página de actualizaciones de Azure Virtual Network.

## <a name="benefits"></a>Ventajas
* **Menor latencia/Más paquetes por segundo (pps):** al quitarse el conmutador virtual de la ruta de acceso de datos, se elimina el tiempo que los paquetes pasan en el host para el procesamiento de las directivas y se aumenta el número de paquetes que se pueden procesar dentro de la máquina virtual.
* **Inestabilidad reducida:** el procesamiento del conmutador virtual depende de la cantidad de directivas que deben aplicarse y la carga de trabajo de la CPU que se encarga del procesamiento. Al descargarse la aplicación de directivas en el hardware, se elimina esa variabilidad, ya que los paquetes se entregan directamente a la máquina virtual y se elimina el host de la comunicación de la máquina virtual, así como todas las interrupciones de software y los cambios de contexto.
* **Disminución de la utilización de la CPU:** el pasar por alto el conmutador virtual en el host conlleva una disminución de la utilización de la CPU para procesar el tráfico de red.

## <a name="Limitations"></a>Limitaciones
Cuando se utiliza esta funcionalidad, existen las siguientes limitaciones:

* **Creación de interfaz de red:** Accelerated Networking solo se puede habilitar para una nueva interfaz de red. No se puede habilitar para una NIC existente.
* **Creación de máquina virtual:** una NIC con Accelerated Networking habilitado solo se puede asociar a una máquina virtual cuando esta se crea. La NIC no puede asociarse a una máquina virtual existente.
* **Regiones:** la mayoría de las regiones de Azure ofrecen máquinas virtuales de Windows con Accelerated Networking. En numerosas regiones se ofrecen máquinas virtuales de Linux con redes aceleradas. El número de regiones en las que esta funcionalidad está disponible va en aumento. Consulte más abajo el blog de actualizaciones de redes virtuales de Azure para ver la información más reciente.   
* **Sistemas operativos compatibles:** para Windows, Microsoft Windows Server 2012 R2 Datacenter y Windows Server 2016. Para Linux, Ubuntu Server 16.04 LTS con kernel 4.4.0-77 o superior, SLES 12 SP2, RHEL 7.3 y CentOS 7.3 (publicado por Rogue Wave Software).
* **Tamaño de máquina virtual:** instancias de uso general y de proceso optimizado con ocho o más núcleos. Para más información, consulte los artículos sobre los tamaños de máquina virtual de [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y de [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). El conjunto de tamaños de instancia de máquina virtual compatibles se ampliará en el futuro.
* **Implementación mediante Azure Resource Manager (ARM):** la funcionalidad de redes aceleradas no se puede implementar mediante ASM/RDFE.

Los cambios en estas limitaciones se anunciarán a través de la página de [actualizaciones para Azure Virtual Networking](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm"></a>Creación de una máquina virtual Windows
Puede usar Azure Portal o Azure [PowerShell](#windows-powershell) para crear la máquina virtual.

### <a name="windows-portal"></a>Portal

1. Desde un explorador de Internet, abra[Azure Portal](https://portal.azure.com) e inicie sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En el portal, haga clic en **Nuevo** > **Compute** > **Windows Server 2016 Datacenter**. 
3. En la hoja **Windows Server 2016 Datacenter**, deje seleccionado *Resource Manager* en **Seleccionar un modelo de implementación** y haga clic en **Crear**.
4. En la hoja **Datos básicos** que aparece, escriba los valores a continuación, deje las opciones restantes con las opciones predeterminadas o seleccione o escriba sus propios valores y haga clic en el botón **Aceptar**:

    |Configuración|Valor|
    |---|---|
    |Nombre|MyVm|
    |Grupos de recursos|Deje seleccionado **Crear nuevo** y escriba *MyResourceGroup*.|
    |Ubicación|Oeste de EE. UU. 2|

    Si no está familiarizado con Azure, consulte más información sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [suscripciones](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [ubicaciones](https://azure.microsoft.com/regions) (también denominadas regiones).
5. En la hoja **Elegir un tamaño** que aparece, escriba *8* en el cuadro **Minimum cores** (cantidad mínima de núcleos), a continuación, haga clic en **Ver todos**.
6. Haga clic en **DS4_V2 Standard** o en cualquier máquina virtual compatible y, después, en el botón **Seleccionar**.
7. En el hoja **Configuración** que aparece, deje todos los valores como están excepto **Habilitado**, haga clic en esta opción en **Accelerated Networking**, a continuación, haga clic en el botón **Aceptar**. **Nota:** Si en los pasos anteriores, seleccionó valores de tamaño de máquina virtual, sistema operativo o ubicación que no aparecen en la sección [Limitaciones](#Limitations) de este artículo, **Accelerated Networking** no estará visible.
8. En la hoja **Resumen** que aparece, haga clic en el botón **Aceptar**. Azure comienza a crear la máquina virtual. La creación tarda unos minutos.
9. Para instalar el controlador de Accelerated Networking para Windows, complete los pasos descritos en la sección [Configuración de Windows](#configure-windows) de este artículo.

### <a name="windows-powershell"></a>PowerShell
1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de Azure PowerShell. Si no está familiarizado con Azure PowerShell, consulte el artículo [Introducción a Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell haciendo clic en el botón Inicio de Windows, luego escriba **Powershell** y, a continuación, en los resultados de búsqueda, haga clic en **PowerShell**.
3. En la ventana de PowerShell, escriba el comando `login-azurermaccount` para iniciar sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. En su explorador, copie el siguiente script:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. En la ventana de PowerShell, haga clic con el botón derecho para pegar el script y empezar a ejecutarlo. Se le pide un nombre de usuario y una contraseña. Use estas credenciales para iniciar sesión en la máquina virtual cuando se conecte a ella en el paso siguiente. Si ha cambiado valores en el script antes de ejecutarlo y se produce un error en el script, confirme que los valores que utilizó para el tamaño, sistema operativo, y ubicación de la máquina virtual se muestran en la sección [Limitaciones](#Limitations) de este artículo.
6. Para instalar el controlador de Accelerated Networking para Windows, complete los pasos descritos en la sección [Configuración de Windows](#configure-windows) de este artículo.

### <a name="configure-windows"></a>Configuración de Windows
Una vez creada la máquina virtual en Azure, tiene que instalar al controlador de Accelerated Networking para Windows. Antes de completar los pasos a continuación, tiene que haber creado una máquina virtual Windows con Accelerated Networking mediante los pasos indicados para el [Portal](#windows-portal) o para [PowerShell](#windows-powershell) de este artículo. 

1. Desde un explorador de Internet, abra[Azure Portal](https://portal.azure.com) e inicie sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *MyVm*. Haga clic en **MyVm** cuando aparezca en los resultados de búsqueda.
3. En la hoja **MyVm** que aparece, haga clic en el botón **Conectar** en la esquina superior izquierda de la hoja. **Nota:** Si **Creando** está visible en el botón **Conectar**, Azure no ha terminado de crear la máquina virtual. Haga clic en **Conectar** solo después de que ya no se vea **Creando** en el botón **Conectar**.
4. Permita que el explorador descargue el archivo **MyVm.rdp**.  Una vez descargado, haga clic en el archivo para abrirlo. 
5. Haga clic en el botón **Conectar** en el cuadro **Conexión a Escritorio remoto** que aparece notificándole que el publicador de esta conexión remota no se puede identificar.
6. En el cuadro **Seguridad de Windows** que aparece, haga clic en **Más opciones**, a continuación, haga clic en **Usar otra cuenta**. Escriba el nombre de usuario y la contraseña que escribió en el paso 4, a continuación, haga clic en el botón **Aceptar**.
7. Haga clic en el botón **Sí** en el cuadro Conexión a Escritorio remoto que le informa de que no se puede comprobar la identidad del equipo remoto.
8. Haga clic con el botón derecho en el botón Inicio de Windows y haga clic en **Administrador de dispositivos**. Expanda el nodo **Adaptadores de red**. Compruebe que el **Adaptador Ethernet de función virtual ConnectX-3 de Mellanox** aparezca como se muestra en la siguiente imagen:
   
    ![Administrador de dispositivos](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. Las redes aceleradas ya están habilitadas para su máquina virtual.

## <a name="create-a-linux-vm"></a>Creación de una máquina virtual Linux
Puede usar Azure Portal o Azure [PowerShell](#linux-powershell) para crear una máquina virtual Ubuntu o SLES. En el caso de las máquinas virtuales RHEL y CentOS, el flujo de trabajo es diferente.  Vea las instrucciones que se describen a continuación.

### <a name="linux-portal"></a>Portal
1. Regístrese para Accelerated Networking para la versión preliminar de Linux siguiendo los pasos del 1 al 5 de la sección [Creación de una máquina virtual Linux: PowerShell](#linux-powershell) de este artículo.  No se puede registrar para versión preliminar en el portal.
2. Complete los pasos del 1 al 8 en la sección [Creación de una máquina virtual Windows: Portal](#windows-portal) de este artículo. En el paso 2, haga clic en **Ubuntu Server 16.04 LTS** en lugar de **Windows Server 2016 Datacenter**. Para este tutorial, elija utilizar una contraseña en lugar de una clave SSH, aunque para las implementaciones de producción, puede usar cualquiera de las dos opciones. Si **Accelerated Networking** no aparece cuando se completa el paso 7 de la sección [Creación de una máquina virtual Windows: Portal](#windows-portal) de este artículo, es probable que sea por uno de los siguientes motivos:
    - No se ha registrado para la versión preliminar. Confirme que su estado de registro es **Registrado**, tal como se describe en el paso 4 de la sección [Creación de una máquina virtual Linux: PowerShell](#linux-powershell) de este artículo. **Nota:** Si participó en la versión preliminar de Accelerated Networking para máquinas virtuales de Windows (ya no es necesario registrarse para usar Accelerated Networking para máquinas virtuales de Windows), no se registrará automáticamente para la versión preliminar de Accelerated Networking para máquinas virtuales de Linux. Para poder participar en la versión preliminar de Accelerated Networking para máquinas virtuales de Linux, tiene que registrarse.
    - No ha seleccionado el tamaño de máquina virtual, el sistema operativo o la ubicación que se enumeran en la sección [Limitaciones](#limitations) de este artículo.
3. Para instalar el controlador de Accelerated Networking para Linux, complete los pasos descritos en la sección [Configuración de Linux](#configure-linux) de este artículo.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Si crea máquinas virtuales de Linux con Accelerated Networking en una suscripción y después intenta crear una máquina virtual Windows con Accelerated Networking en la misma suscripción, se puede producir un error en la creación de la máquina virtual Windows. Durante esta versión preliminar, se recomienda probar las máquinas virtuales Windows y Linux con Accelerated Networking en suscripciones independientes.
>

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de Azure PowerShell. Si no está familiarizado con Azure PowerShell, consulte el artículo [Introducción a Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell haciendo clic en el botón Inicio de Windows, luego escriba **Powershell** y, a continuación, en los resultados de búsqueda, haga clic en **PowerShell**.
3. En la ventana de PowerShell, escriba el comando `login-azurermaccount` para iniciar sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Regístrese para la versión preliminar de Accelerated Networking para Azure siguiendo los pasos a continuación:
    - Envíe un correo electrónico a [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con su identificador de suscripción de Azure y el uso previsto. Espere a recibir una confirmación por correo electrónico de Microsoft que le indique que la suscripción está habilitada.
    - Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        No continúe con el paso 5 hasta que aparezca **Registrado** en la salida después de escribir el comando anterior. El resultado debe ser similar a la salida siguiente antes de continuar:
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >Si participó en la versión preliminar de Accelerated Networking para máquinas virtuales de Windows (ya no es necesario registrarse para usar Accelerated Networking para máquinas virtuales de Windows), no se registrará automáticamente para la versión preliminar de Accelerated Networking para máquinas virtuales de Linux. Para poder participar en la versión preliminar de Accelerated Networking para máquinas virtuales de Linux, tiene que registrarse.
      >
5. En el explorador, copie el script siguiente y sustituya Ubuntu o SLES según sea necesario.  Redhat y CentOS tienen un flujo de trabajo diferente, que se describe a continuación:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. En la ventana de PowerShell, haga clic con el botón derecho para pegar el script y empezar a ejecutarlo. Se le pide un nombre de usuario y una contraseña. Use estas credenciales para iniciar sesión en la máquina virtual cuando se conecte a ella en el paso siguiente. Si se produce un error en el script, confirme que:
    - Se registró para la versión preliminar, tal como se describe en el paso 4
    - Si ha cambiado los valores de tamaño de máquina virtual, tipo de sistema operativo o ubicación en el script antes de ejecutarlo, confirme que los valores que ha utilizado se muestran en la sección [Limitaciones](#Limitations) de este artículo.
7. Para instalar el controlador de Accelerated Networking para Linux, complete los pasos descritos en la sección [Configuración de Linux](#configure-linux) de este artículo.

### <a name="configure-linux"></a>Configuración de Linux

Una vez creada la máquina virtual en Azure, tiene que instalar al controlador de Accelerated Networking para Linux. Antes de completar los pasos a continuación, tiene que haber creado una máquina virtual Linux con Accelerated Networking mediante los pasos indicados para el [Portal](#linux-portal) o para [PowerShell](#linux-powershell) de este artículo. 

1. Desde un explorador de Internet, abra[Azure Portal](https://portal.azure.com) e inicie sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En la parte superior del portal, a la derecha de la barra *Buscar recursos*, haga clic en el icono **>_** para iniciar una instancia de Cloud Shell de Bash (versión preliminar). El panel de Cloud Shell de Bash aparece en la parte inferior del portal y, después de unos segundos, presenta el símbolo del sistema **username@Azure:~ $**. Aunque puede conectarse mediante SSH a la máquina virtual desde su equipo, en lugar de mediante Cloud Shell, las instrucciones de este tutorial asumen que está usando Cloud Shell.
3. En la parte superior de Azure Portal, en el cuadro que contiene el texto *Buscar recursos*, escriba *MyVm*. Haga clic en **MyVm** cuando aparezca en los resultados de búsqueda.
4. En la hoja **MyVm** que aparece, haga clic en el botón **Conectar** en la esquina superior izquierda de la hoja. **Nota:** Si **Creando** está visible en el botón **Conectar**, Azure no ha terminado de crear la máquina virtual. Haga clic en **Conectar** solo después de que ya no se vea **Creando** en el botón **Conectar**.
5. Azure abre un cuadro que le pide que escriba la `ssh adminuser@<ipaddress>`. Escriba este comando en Cloud Shell (o cópielo en el cuadro que aparece en el paso 4 y péguelo en Cloud Shell) y luego presione ENTRAR.
6. Seleccione **Sí** como respuesta a la pregunta de si desea seguir conectado, luego presione ENTRAR.
7. Escriba la contraseña que especificó al crear la máquina virtual. Una vez iniciada sesión correctamente en la máquina virtual, verá un símbolo del sistema adminuser@MyVm:~$. Ahora tiene iniciada sesión en la máquina virtual a través de la sesión de Cloud Shell. **Nota:** Las sesiones de Cloud Shell agotan el tiempo de espera tras 10 minutos de inactividad.

Llegados a este punto, las instrucciones difieren en función de la distribución que se use. 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. En el símbolo del sistema, escriba `uname -r` y confirme la versión para:

    * Ubuntu: "4.4.0-77-generic," o superior
    * SLES: "4.4.59-92.20-default" o superior

2. Cree un bond entre la vNIC de redes estándar y la vNIC de Accelerated Networking mediante la ejecución de los comandos que siguen. El tráfico de red utiliza la vNIC de Accelerated Networking que tiene mayor rendimiento, mientras el bond garantiza que el tráfico de redes no se interrumpe en determinados cambios de configuración.
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. Después de ejecutar el script, la máquina virtual se reiniciará al cabo de una pausa de 60 segundos.
4. Una vez que se reinicia la máquina virtual, vuelva a conectarse a ello completando de nuevo los pasos del 5 al 7.
5. Ejecute el comando `ifconfig` y confirme que sale bond0 y se muestra la interfaz como UP. 
 
 >[!NOTE]
      >Las aplicaciones que usen redes aceleradas deben comunicarse a través de la interfaz *bond0*, no de la interfaz *eth0*.  El nombre de la interfaz puede cambiar antes de que Accelerated Networking alcance la disponibilidad general.

#### <a name="rhelcentos"></a>RHEL/CentOS

Para crear una máquina virtual de Red Hat Enterprise Linux o CentOS 7.3, debe seguir algunos pasos adicionales para cargar los controladores más recientes necesarios para SR-IOV y el controlador de función virtual (VF) de la tarjeta de red. En la primera fase de las instrucciones se prepara una imagen que puede usarse para crear una o varias máquinas virtuales que tengan los controladores precargados.

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>Fase uno: preparar una imagen base de Red Hat Enterprise Linux o CentOS 7.3. 

1.  Aprovisione una máquina virtual que no sea SRIOV CentOS 7.3 en Azure.

2.  Instale LIS 4.2.2:
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  Descargue los archivos de configuración.
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  Desaprovisione esta máquina virtual.

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  Desde Azure Portal, detenga esta máquina virtual, vaya a "Discos" en la máquina virtual y capture el URI de VHD de OSDisk. Este URI contiene el nombre de VHD de la imagen base y su cuenta de almacenamiento. 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>Fase 2: aprovisionar nuevas máquinas virtuales en Azure

1.  Aprovisione nuevas máquinas virtuales con New-AzureRMVMConfig por medio del VHD de la imagen base capturado en la fase uno, con AcceleratedNetworking habilitado en la vNIC:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  Una vez que las máquinas virtuales hayan arrancado, compruebe el dispositivo de función virtual mediante el comando "lspci" y consulte la entrada Mellanox. Por ejemplo, debería ver este elemento en la salida de lspci:
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  Ejecute el script de unión:

    ```bash
    sudo bondvf.sh
    ```

4.  Reinicie las máquinas virtuales nuevas:

    ```bash
    sudo reboot
    ```

La máquina virtual debe arrancar con bond0 configurado y la ruta de acceso de las redes aceleradas habilitada.  Ejecute `ifconfig` para confirmarlo.
