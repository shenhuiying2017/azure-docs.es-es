---
title: "Habilitación de la virtualización anidada en Azure Virtual Machines | Microsoft Docs"
description: "Habilitación de la virtualización anidada en Azure Virtual Machines"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: philmea
manager: timlt
ms.author: philmea
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 180b87e18d98bb1e7ddefdcce09fc45d2fc26d0f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Habilitación de la virtualización anidada en una máquina virtual de Azure

La virtualización anidada es compatible con las series Dv3 y Ev3 de Azure Virtual Machines. Esta funcionalidad proporciona gran flexibilidad para admitir escenarios como entornos de desarrollo, pruebas, aprendizaje y demostración. 

En este artículo se analiza la habilitación de la virtualización anidada en una máquina virtual de Azure y la configuración de la conectividad de Internet a esa máquina virtual invitada.

## <a name="create-a-dv3-or-ev3-series-azure-vm"></a>Creación de una máquina virtual de Azure de la serie Dv3 o Ev3

Cree una nueva máquina virtual Windows Server 2016 y elija un tamaño de la serie Dv3 o Ev3. Asegúrese de elegir un tamaño suficientemente grande para admitir las exigencias de una máquina virtual invitada. En este ejemplo, se usa una máquina virtual con tamaño D3_v3. 

Puede ver la disponibilidad regional de las máquinas virtuales de las series Dv3 o Ev3 [aquí](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Para instrucciones detalladas sobre cómo crear una máquina virtual nueva, consulte [Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Conexión a una máquina virtual de Azure

Cree una conexión a Escritorio remoto en la máquina virtual.

1. Haga clic en el botón **Conectar** en las propiedades de la máquina virtual. Se crea y se descarga un archivo de Protocolo de Escritorio remoto (archivo .rdp).

2. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le solicite, haga clic en **Conectar**. En un equipo Mac, necesita un cliente RDP como este [Cliente de Escritorio remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) de Mac App Store.

3. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y, a continuación, haga clic en **Aceptar**.

4. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en **Sí** o **Conectar** para continuar con la conexión.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Habilitación de la característica Hyper-V en la máquina virtual de Azure
Puede configurar estos ajustes de manera manual o usar el script de PowerShell que se proporciona para automatizar la configuración.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Opción 1: Usar un script de PowerShell para configurar la virtualización anidada
Hay un script de PowerShell para habilitar la virtualización anidada en un host de Windows Server 2016 disponible en [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). El script comprueba los requisitos previos y, luego, configura la virtualización anidada en la máquina virtual de Azure. Es necesario realizar un reinicio de la máquina virtual de Azure para completar la configuración. Este script puede funcionar en otros entornos, pero no está garantizado. Consulte la entrada de blog de Azure que incluye una demostración de vídeo en directo sobre la virtualización anidada en Azure. https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Opción 2: Configurar manualmente la virtualización anidada

1. En la máquina virtual de Azure, abra PowerShell como administrador. 

2. Habilite la característica Hyper-V y las herramientas de administración.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Con este comando se reinicia la máquina virtual de Azure. Perderá la conexión RDP durante el proceso de reinicio.
    
3. Una vez que la máquina virtual de Azure se reinicie, reconéctese a la VM con RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Configuración de la conectividad de Internet de la máquina virtual invitada
Cree un adaptador de red virtual nuevo para la máquina virtual invitada y configure una puerta de enlace NAT para habilitar la conectividad de Internet.

### <a name="create-a-nat-virtual-network-switch"></a>Creación de un conmutador de red virtual NAT

1. En la máquina virtual de Azure, abra PowerShell como administrador.
   
2. Cree un conmutador interno.

    ```powershell
    New-VMSwitch -Name "InternalNATSwitch" -SwitchType Internal
    ```

3. Consulte las propiedades del conmutador y anote el valor de ifIndex del adaptador nuevo.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Anote el valor de "ifIndex" del conmutador virtual que acaba de crear.
    
4. Cree una dirección IP para la puerta de enlace NAT.
    
Para configurar la puerta de enlace, necesita información sobre la red:    
  * IPAddress: la dirección IP de la puerta de enlace NAT especifica la dirección IPv4 o IPv6 que se usará como la dirección de puerta de enlace predeterminada de la subred de la red virtual. El formato genérico es a.b.c.1 (por ejemplo, "192.168.0.1"). Si bien no es necesario que la posición final sea .1, habitualmente sí lo es (según la longitud de prefijo). Típicamente, debe usar un espacio de direcciones de red privadas RFC 1918. 
  * PrefixLength: la longitud de prefijo de la subred define el tamaño de la subred local (máscara de subred). La longitud de prefijo de la subred será un valor entero entre 0 y 32. 0 asignaría todo Internet, 32 solo permitiría una dirección IP asignada. Los valores comunes van de 24 a 12 en función de cuántas direcciones IP se deben adjuntar a NAT. Un valor de PrefixLength común es 24, que es una máscara de subred de 255.255.255.0.
  * InterfaceIndex - **ifIndex** es el índice de interfaz del conmutador virtual que se creó en el paso anterior. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Creación de la red NAT

Para configurar la puerta de enlace, deberá proporcionar información sobre la red y la puerta de enlace NAT:
  * Name: el nombre de la red NAT. 
  * InternalIPInterfaceAddressPrefix: el prefijo de la subred NAT describe tanto el prefijo de la dirección IP de la puerta de enlace NAT como la longitud de prefijo de la subred NAT anteriores. El formato genérico será a.b.c.0/longitud de prefijo de la subred NAT. 

En PowerShell, cree una red NAT nueva.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Creación de la máquina virtual invitada

1. Abra el Administrador de Hyper-V y cree una máquina virtual nueva. Configure la máquina virtual para usar la red interna nueva que creó.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Instale un sistema operativo en la máquina virtual invitada.
    
    >[!NOTE] 
    >
    >Necesita los medios de instalación de un sistema operativo para instalarlo en la máquina virtual. En este caso se usa Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Asignación de una dirección IP a la máquina virtual invitada

Puede asignar una dirección IP a la máquina virtual invitada ya sea estableciendo manualmente una dirección IP estática en la máquina virtual invitada o configurando DHCP en la máquina virtual de Azure para asignar de manera dinámica la dirección IP.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Opción 1: Configurar DHCP para asignar dinámicamente una dirección IP a la máquina virtual invitada
Siga estos pasos para configurar DHCP en la máquina virtual host para la asignación dinámica de direcciones.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Instalación del servidor DCHP en la máquina virtual de Azure

1. Abra el Administrador del servidor. En el panel, haga clic en **Agregar roles y característica**. Aparecerá el Asistente para agregar roles y características.
  
2. En el asistente, haga clic en **Siguiente** hasta llegar a la página Roles del servidor.
  
3. Haga clic para activar la casilla **Servidor DHCP**, haga clic en **Agregar características** y en **Siguiente** hasta completar el asistente.
  
4. Haga clic en **Instalar**.

#### <a name="configure-a-new-dhcp-scope"></a>Configuración de un nuevo ámbito de DHCP

1. Abra el Administrador DHCP.
  
2. En el panel de navegación, expanda el nombre del servidor, haga clic con el botón derecho **IPv4** y, luego, haga clic en **Nuevo ámbito**. Aparece el asistente para agregar un nuevo ámbito. Haga clic en **Siguiente**.
  
3. Escriba un nombre y una descripción para el ámbito y haga clic en **Siguiente**.
  
4. Defina un intervalo IP para el servidor DHCP (por ejemplo, de 192.168.0.100 a 192.168.0.200).
  
5. Haga clic en **Siguiente** hasta la página Puerta de enlace predeterminada. Escriba la dirección IP que creó anteriormente (por ejemplo, 192.168.0.1) como la puerta de enlace predeterminada.
  
6. Haga clic en **Siguiente** hasta completar el asistente, deje todos los valores predeterminados y haga clic en **Finalizar**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Opción 2: Establecer manualmente una dirección IP estática en la máquina virtual invitada
Si no configuró DHCP para asignar de manera dinámica una dirección IP a la máquina virtual invitada, siga estos pasos para establecer una dirección IP estática.

1. En la máquina virtual de Azure, abra PowerShell como administrador.

2. Haga clic con el botón derecho en la máquina virtual invitada y haga clic en Conectar.

3. Inicie sesión en la máquina virtual invitada.

4. En la máquina virtual invitada, abra el Centro de redes y recursos compartidos.

5. Configure el adaptador de red para una dirección dentro del intervalo de la red NAT que creó en la sección anterior.

En este ejemplo usará una dirección dentro del intervalo 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Prueba de la conectividad en la máquina virtual invitada

En la máquina virtual invitada, abra el explorador y navegue a una página web.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)
