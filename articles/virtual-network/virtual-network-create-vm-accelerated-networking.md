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
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 43663ed8becfa69c06699709a18623652df28ed6
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Creación de una máquina virtual con Accelerated Networking

En este tutorial, obtendrá información sobre cómo crear una máquina virtual de Azure con Accelerated Networking. Accelerated Networking habilita la virtualización de E/S de raíz única (SR-IOV) en una máquina virtual (VM), lo que mejora significativamente su rendimiento en la red. Esta ruta de alto rendimiento omite el host de la ruta de acceso de datos, lo que reduce la latencia, la inestabilidad y la utilización de la CPU para su uso con las cargas de trabajo de red más exigentes en tipos de máquina virtual compatibles. En la siguiente imagen, se muestra la comunicación entre dos máquinas virtuales (VM) con y sin Accelerated Networking:

![De comparación](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Sin Accelerated Networking, todo el tráfico de red de entrada y salida de la máquina virtual tiene que atravesar el host y el conmutador virtual. El conmutador virtual se encarga de toda la aplicación de directivas, como grupos de seguridad de red, listas de control de acceso, aislamiento y otros servicios virtualizados de red, al tráfico de red. Para más información sobre los conmutadores virtuales, lea el artículo sobre [virtualización de red y conmutador virtual de Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Con Accelerated Networking, el tráfico de red llega a la interfaz de red (NIC) de la máquina virtual y se reenvía después a la máquina virtual. Todas las directivas de red que el conmutador virtual aplica sin Accelerated Networking se descargan y aplican en el hardware. La aplicación de directivas en hardware permite que la NIC reenvíe el tráfico de red directamente a la máquina virtual, pasando por alto el host y el conmutador virtual, al mismo tiempo que se mantienen todas las directivas aplicadas en el host.

Las ventajas de Accelerated Networking solo se aplican a la máquina virtual donde esté habilitado. Para obtener resultados óptimos, lo ideal es habilitar esta característica en al menos dos máquinas virtuales conectadas a la misma instancia de Azure Virtual Network (VNet). Al comunicarse entre redes virtuales o conectarse de forma local, esta característica tiene un efecto mínimo sobre la latencia total.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Ventajas
* **Menor latencia/Más paquetes por segundo (pps):** al quitarse el conmutador virtual de la ruta de acceso de datos, se elimina el tiempo que los paquetes pasan en el host para el procesamiento de las directivas y se aumenta el número de paquetes que se pueden procesar dentro de la máquina virtual.
* **Inestabilidad reducida:** el procesamiento del conmutador virtual depende de la cantidad de directivas que deben aplicarse y la carga de trabajo de la CPU que se encarga del procesamiento. Al descargarse la aplicación de directivas en el hardware, se elimina esa variabilidad, ya que los paquetes se entregan directamente a la máquina virtual y se elimina el host de la comunicación de la máquina virtual, así como todas las interrupciones de software y los cambios de contexto.
* **Disminución de la utilización de la CPU:** el pasar por alto el conmutador virtual en el host conlleva una disminución de la utilización de la CPU para procesar el tráfico de red.

## <a name="Limitations"></a>Limitaciones
Cuando se utiliza esta funcionalidad, existen las siguientes limitaciones:

* **Creación de interfaz de red:** Accelerated Networking solo se puede habilitar para una nueva interfaz de red. No se puede habilitar para una NIC existente.
* **Creación de máquina virtual:** una NIC con Accelerated Networking habilitado solo se puede asociar a una máquina virtual cuando esta se crea. La NIC no puede asociarse a una máquina virtual existente.
* **Regiones:** la mayoría de las regiones de Azure ofrecen máquinas virtuales de Windows con Accelerated Networking. Las máquinas virtuales de Linux con Accelerated Networking se ofrecen solo en dos regiones: centro-sur de EE. UU. y Oeste de EE. UU. 2. En el futuro el número de regiones en las que esta funcionalidad está disponible aumentará.
* **Sistemas operativos compatibles:** para Windows, Microsoft Windows Server 2012 R2 Datacenter y Windows Server 2016. Linux: Ubuntu Server 16.04 LTS con kernel 4.4.0-77 o superior. Pronto se agregarán distribuciones adicionales.
* **Tamaño de máquina virtual:** instancias de uso general y de proceso optimizado con ocho o más núcleos. Para más información, consulte los artículos sobre los tamaños de máquina virtual de [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y de [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). El conjunto de tamaños de instancia de máquina virtual compatibles se ampliará en el futuro.

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
6. Haga clic en **DS4_V2 Standard** y después en el botón **Seleccionar**.
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
8. En la máquina virtual MyVm, haga clic en el botón Inicio de Windows y luego en **Internet Explorer**.
9. En el cuadro de **Internet Explorer 11** que aparece, haga clic en **Usar la configuración recomendada de compatibilidad, privacidad y seguridad** y haga clic en **Aceptar**.
10. En la barra de direcciones de Internet Explorer, escriba https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84 y pulse la tecla ENTRAR.
11. En el cuadro **Alerta de seguridad** que aparece, haga clic en **Aceptar**.
12. En el cuadro de **Internet Explorer** que aparece, haga clic en **Agregar**, luego en el botón **Agregar** del cuadro **Sitios de confianza** y a continuación, haga clic en el botón **Cerrar**. Siga estos pasos para los cuadros siguientes que aparecen.
13. Para descargar el archivo haga clic en él.
14. Cuando aparezca el cuadro de **Licencia, Términos de uso**, haga clic en **Acepto**.
15. Permita que Internet Explorer guarde el archivo haciendo clic en el botón **Guardar** en el cuadro que aparece en la parte inferior de la pantalla, a continuación, haga clic en el botón **Abrir carpeta**.
16. Para instalar al controlador de Accelerated Networking, haga doble clic en el archivo. En el Asistente de instalación, acepte todos los valores predeterminados y haga clic en el botón **Sí** al final del Asistente para reiniciar la máquina virtual.
17. Una vez que se reinicia la máquina virtual, vuelva a completar los pasos de 9 al 12 para conectarse a la máquina virtual.
18. Haga clic con el botón derecho en el botón Inicio de Windows y haga clic en **Administrador de dispositivos**. Expanda el nodo **Adaptadores de red**. Compruebe que el **Adaptador Ethernet de función virtual ConnectX-3 de Mellanox** aparezca como se muestra en la siguiente imagen:
   
    ![Administrador de dispositivos](./media/virtual-network-create-vm-accelerated-networking/image2.png)

## <a name="create-a-linux-vm"></a>Creación de una máquina virtual Linux
Puede usar Azure Portal o Azure [PowerShell](#linux-powershell) para crear la máquina virtual.

### <a name="linux-portal"></a>Portal
1. Regístrese para Accelerated Networking para la versión preliminar de Linux siguiendo los pasos del 1 al 5 de la sección [Creación de una máquina virtual Linux: PowerShell](#linux-powershell) de este artículo.  No se puede registrar para versión preliminar en el portal.
2. Complete los pasos del 1 al 8 en la sección [Creación de una máquina virtual Windows: Portal](#windows-portal) de este artículo. En el paso 2, haga clic en **Ubuntu Server 16.04 LTS** en lugar de **Windows Server 2016 Datacenter**. Para este tutorial, elija utilizar una contraseña en lugar de una clave SSH, aunque para las implementaciones de producción, puede usar cualquiera de las dos opciones. Si **Accelerated Networking** no aparece cuando se completa el paso 7 de la sección [Creación de una máquina virtual Windows: Portal](#windows-portal) de este artículo, es probable que sea por uno de los siguientes motivos:
    - No se ha registrado para la versión preliminar. Confirme que su estado de registro es **Registrado**, tal como se describe en el paso 4 de la sección [Creación de una máquina virtual Linux: PowerShell](#linux-powershell) de este artículo. **Nota:** Si participó en la versión preliminar de Accelerated Networking para máquinas virtuales de Windows (ya no es necesario registrarse para usar Accelerated Networking para máquinas virtuales de Windows), no se registrará automáticamente para la versión preliminar de Accelerated Networking para máquinas virtuales de Linux. Para poder participar en la versión preliminar de Accelerated Networking para máquinas virtuales de Linux, tiene que registrarse.
    - No ha seleccionado el tamaño de máquina virtual, el sistema operativo o la ubicación que se enumeran en la sección [Limitaciones](#simitations) de este artículo.
3. Para instalar el controlador de Accelerated Networking para Linux, complete los pasos descritos en la sección [Configuración de Linux](#configure-linux) de este artículo.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Si crea máquinas virtuales de Linux con Accelerated Networking en una suscripción y después intenta crear una máquina virtual Windows con Accelerated Networking en la misma suscripción, se puede producir un error en la creación de la máquina virtual Windows. Durante esta versión preliminar, se recomienda probar las máquinas virtuales Windows y Linux con Accelerated Networking en suscripciones independientes.
>

1. Instale la versión más reciente del módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) de Azure PowerShell. Si no está familiarizado con Azure PowerShell, consulte el artículo [Introducción a Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie una sesión de PowerShell haciendo clic en el botón Inicio de Windows, luego escriba **Powershell** y, a continuación, en los resultados de búsqueda, haga clic en **PowerShell**.
3. En la ventana de PowerShell, escriba el comando `login-azurermaccount` para iniciar sesión con su [cuenta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) de Azure. Si aún no dispone de una cuenta, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Regístrese para la versión preliminar de Accelerated Networking para Azure siguiendo los pasos a continuación:
    - Escriba los siguientes comandos:

        ```powershell
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
        ```
    - Envíe un correo electrónico a [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con su identificador de suscripción de Azure y el uso previsto. 
    - Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:
    
        `Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network`

        No continúe con el paso 5 hasta que aparezca **Registrado** en la salida después de escribir el comando anterior. El resultado debe ser similar a la salida siguiente antes de continuar:
    
        ```
        FeatureName                       ProviderName      RegistrationState
        -----------                       ------------      -----------------
        AllowAcceleratedNetworkingFeature Microsoft.Network Registered
        ```
      >[!NOTE]
      >Si participó en la versión preliminar de Accelerated Networking para máquinas virtuales de Windows (ya no es necesario registrarse para usar Accelerated Networking para máquinas virtuales de Windows), no se registrará automáticamente para la versión preliminar de Accelerated Networking para máquinas virtuales de Linux. Para poder participar en la versión preliminar de Accelerated Networking para máquinas virtuales de Linux, tiene que registrarse.
      >
5. En su explorador, copie el siguiente script:
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
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
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
8. En el símbolo del sistema, escriba `uname -r` y confirme que la salida coincide con la siguiente versión: "4.4.0-77-generic".
9.    Cree un bond entre la vNIC de redes estándar y la vNIC de Accelerated Networking mediante la ejecución de los comandos que siguen. El tráfico de red utiliza la vNIC de Accelerated Networking que tiene mayor rendimiento, mientras el bond garantiza que el tráfico de redes no se interrumpe en determinados cambios de configuración. 
    - `wget https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git/plain/tools/hv/bondvf.sh`
    - `chmod +x ./bondvf.sh`
    - `sudo ./bondvf.sh`
    - `sudo mv ~/bondvf.sh /etc/init.d`
    - `sudo update-rc.d bondvf.sh defaults`Nota: Si recibe un error que dice *insserv: warning: script 'bondvf.sh' missing LSB tags and overrides* (insserv: advertencia: al script 'bondvf.sh' le faltan las invalidaciones y etiquetas LSB), puede pasarlo por alto.
    - `sudo nano /etc/network/interfaces.d/50-cloud-init.cfg` abre el editor nano GNU para editar el archivo.
    - En el editor, comente las líneas *auto etho0* y *iface eth0 inet dhcp* agregando *#* al principio de cada línea. Después de agregar *#* a cada línea, las líneas tienen un aspecto similar al ejemplo siguiente:
        - #<a name="auto-eth0"></a>auto eth0
        - #<a name="iface-eth0-inet-dhcp"></a>iface eth0 inet dhcp
10. Mantenga presionadas las teclas **Ctrl+X**, escriba **Y** y, a continuación, presione la tecla **ENTRAR** para guardar el archivo.
11. Reinicie la máquina virtual mediante la especificación del comando `sudo shutdown -r now`.
12. Una vez que se reinicia la máquina virtual, vuelva a conectarse a ello completando de nuevo los pasos del 5 al 7.
13.    Ejecute el comando `ifconfig` y confirme que sale bond0 y se muestra la interfaz como UP. **Nota:** Las aplicaciones que utilicen Accelerated Networking tienen que comunicarse a través de la interfaz *bond0*, no de la interfaz *eth0*.  El nombre de la interfaz puede cambiar antes de que Accelerated Networking alcance la disponibilidad general.

