---
title: "Ejemplo de red perimetral de Azure: creación de una red perimetral simple con grupos de seguridad de red | Microsoft Docs"
description: "Creación de una red perimetral con grupos de seguridad de red"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Ejemplo 1: crear una red perimetral simple mediante grupos de seguridad de red con PowerShell clásico
[Volver a la página de procedimientos recomendados de límites de seguridad][HOME]

> [!div class="op_single_selector"]
> * [Plantilla de Resource Manager](virtual-networks-dmz-nsg.md)
> * [Clásico: PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

En este ejemplo se crea una red perimetral primitiva con cuatro servidores Windows y grupos de seguridad de red. También se describe cada uno de los comandos de PowerShell pertinentes para que comprenda mejor cada paso. Hay también una sección llamada "Escenario de tráfico" para proporcionar información detallada paso a paso de cómo pasa el tráfico a través de los niveles de defensa de la red perimetral. Por último, en la sección de referencias está el código completo e instrucciones para crear este entorno para probar y experimentar con diferentes escenarios. 

![Red perimetral de entrada con grupo de seguridad de red][1]

## <a name="environment-description"></a>Descripción del entorno
En este ejemplo, una suscripción contiene los siguientes recursos:

* Dos servicios en la nube: "FrontEnd001" y "BackEnd001"
* Una red virtual, “CorpNetwork”, con dos subredes, “FrontEnd” y “BackEnd”
* Un grupo de seguridad de red que se aplica a ambas subredes
* Un servidor Windows que representa un servidor de aplicaciones web ("IIS01")
* Dos servidores Windows que representan servidores back-end de aplicaciones ("AppVM01", "AppVM02")
* Un servidor Windows que representa un servidor DNS ("DNS01")

En la sección de referencias, hay un script de PowerShell que compila la mayor parte del entorno descrito en este ejemplo. Aunque la creación de las máquinas virtuales y las redes virtuales la realiza el script de ejemplo, no se describe en detalle en este documento. 

Para crear el entorno:

1. Guarde el archivo xml de configuración de red incluido en la sección de referencias (actualizado con nombres, ubicación y direcciones IP para que coincidan con el escenario dado).
2. Actualice las variables de usuario en el script para que coincida con el entorno en el que se va a ejecutar el script (suscripciones, nombres de servicio, etc.).
3. Ejecución del script en PowerShell

>[!Note]
>La región indicada en el script de PowerShell debe coincidir con la región indicada en el archivo xml de configuración de red.
>
>

Una vez ejecutado el script correctamente, se pueden realizar pasos adicionales opcionales. En la sección de referencias hay dos scripts para configurar el servidor web y el servidor de aplicaciones con una aplicación web sencilla para permitir las pruebas con esta configuración de red perimetral.

Las secciones siguientes le acompañan por las líneas clave del script de PowerShell para ofrecerle una descripción detallada de los grupos de seguridad de red y cómo funcionan en este ejemplo.

## <a name="network-security-groups-nsg"></a>Grupos de seguridad de red (NSG)
En este ejemplo, se crea un grupo de seguridad de red y se cargan después seis reglas. 

> [!TIP]
> Por lo general, debe crear primero las reglas específicas de "Permitir" y, por último, las reglas de "Denegar" más genéricas. La prioridad asignada indica qué reglas se evalúan primero. Una vez que se encuentra el tráfico para aplicar a una regla específica, no se evalúa ninguna otra regla. Se pueden aplicar reglas de grupo de seguridad de red en cualquier dirección, entrante o saliente (desde la perspectiva de la subred).
> 
> 

De forma declarativa, se compilan las reglas siguientes para el tráfico entrante:

1. Se permite el tráfico DNS interno (puerto 53)
2. Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina virtual
3. Se permite el tráfico HTTP (puerto 80) desde Internet al servidor web (IIS01)
4. Se permite cualquier tráfico (todos los puertos) desde IIS01 a AppVM1.
5. Se deniega todo el tráfico (todos los puertos) desde Internet a toda la red virtual (ambas subredes).
6. Se deniega todo el tráfico (todos los puertos) desde la subred front-end a la subred back-end.

Con estas reglas enlazadas a cada subred, si una solicitud HTTP era entrante desde Internet al servidor web, se aplicarían ambas reglas, la 3 (permitir) y la 5 (denegar), pero como la regla 3 tiene mayor prioridad, solo se aplicaría esa y la regla 5 no entraría en juego. Por lo tanto, se permitiría la solicitud HTTP al servidor web. Si ese mismo tráfico estaba intentando ponerse en contacto con el servidor DNS01, se aplicaría primero la regla 5 (denegar) y no se permitiría que el tráfico pase al servidor. La regla 6 (denegar) impide que la subred front-end se comunique con la subred back-end (excepto el tráfico permitido en las reglas 1 y 4); este conjunto de reglas protege la red back-end en el caso de que un atacante ponga en peligro la aplicación web en el front-end, y el atacante tendría acceso limitado a la red back-end "protegida" (solo a los recursos expuestos en el servidor AppVM01).

Hay una regla de salida predeterminada que permite el tráfico saliente a Internet. En este ejemplo, permitimos el tráfico saliente y no modificamos las reglas de salida. Para bloquear el tráfico en ambas direcciones, es necesario el enrutamiento definido por el usuario; esto se verá en el "Ejemplo 3" de la [página de procedimientos recomendados de límites de seguridad][HOME].

Cada regla se describe con más detalle a continuación. (**Nota**: Cualquier elemento de la lista siguiente que comience por un signo de dólar (p. ej.: $NSGName) es una variable definida por el usuario del script en la sección de referencias de este documento):

1. En primer lugar, debe compilarse un grupo de seguridad de red para contener las reglas:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. En este ejemplo, la primera regla permite el tráfico DNS entre todas las redes internas al servidor DNS en la subred back-end. La regla tiene algunos parámetros importantes:
   
   * "Type" indica en qué dirección del flujo de tráfico esta entrada surte efecto. La dirección se entiende desde la perspectiva de la subred o máquina virtual (depende de donde esté enlazado este NSG). Por lo tanto, si Type es "Inbound" y el tráfico está entrando en la subred, la regla se aplicará y tráfico que sale de la subred no se vería afectado por esta regla.
   * "Priority" establece el orden de evaluación de un flujo de tráfico. Cuanto menor sea el número de prioridad, mayor será la prioridad de la regla. En cuanto se aplica una regla a un flujo de tráfico específico, no se procesa ninguna otra regla. Por tanto, si una regla con prioridad 1 permite el tráfico y una regla de prioridad 2 deniega el tráfico y ambas reglas se aplican al tráfico, se permitirá que el tráfico fluya (como la regla 1 tenía una prioridad más alta, entró en vigor y no se aplicó ninguna otra regla).
   * "Action" indica si se bloquea o se permite el tráfico al que afecta esta regla.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. Esta regla permite que el tráfico RDP fluya desde Internet al puerto RDP en cualquier servidor de la subred enlazada. Esta regla usa dos tipos especiales de prefijos de dirección: "VIRTUAL_NETWORK" e "INTERNET". Estas etiquetas representan una manera sencilla de incluir una categoría mayor de prefijos de dirección.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Esta regla permite que el tráfico entrante de Internet llegue al servidor web. Esta regla no cambia el comportamiento de enrutamiento. La regla solo permite el paso del tráfico destinado a IIS01. Por lo tanto, si el tráfico de Internet tenía el servidor web como destino, esta regla lo permitiría y detendría el procesamiento de más reglas. (En la regla con prioridad 140, se bloquea todo el tráfico entrante de Internet restante). Si solo está procesando tráfico HTTP, esta regla se podría restringir aún más para permitir solo el puerto 80 de destino.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Esta regla permite que el tráfico pase desde el servidor IIS01 al servidor AppVM01, una regla posterior bloqueará todo el tráfico de front-end a back-end restante. Para mejorar esta regla, se debe agregar el puerto si se conoce. Por ejemplo, si el servidor IIS solo accede a SQL Server en AppVM01, el intervalo de puertos de destino debe cambiarse de "*" (cualquiera) a 1433 (puerto SQL), lo que deja una superficie de ataque entrante menor en AppVM01 en caso de que la aplicación web se vea comprometida.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Esta regla deniega el tráfico desde Internet a cualquiera de los servidores de la red. Con las reglas con prioridad 110 y 120, se permite solo el tráfico entrante de Internet al firewall y los puertos RDP en servidores, y se bloquea todo lo demás. Esta regla está configurada "a prueba de errores" para bloquear todos los flujos inesperados.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. La regla final deniega el tráfico desde la subred front-end a la subred back-end. Puesto que se trata de una regla solo entrante, se permite el tráfico inverso (desde el back-end al front-end).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Escenarios de tráfico
#### <a name="allowed-internet-to-web-server"></a>(*Permitido*) Internet a servidor web
1. Un usuario de Internet solicita una página HTTP desde FrontEnd001.CloudApp.Net (servicio en la nube accesible desde Internet).
2. El servicio en la nube pasa el tráfico por un extremo abierto en el puerto 80 hacia IIS01 (el servidor web).
3. La subred front-end comienza el procesamiento de las reglas de entrada:
   1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   2. No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   3. Se aplica la regla 3 (Internet a IIS01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4. El tráfico llega a la dirección IP interna del servidor web IIS01 (10.0.1.5).
5. IIS01 escucha el tráfico web, recibe esta solicitud y comienza a procesarla.
6. IIS01 pide información al servidor SQL Server en AppVM01
7. Puesto que no hay reglas de salida en la subred front-end, se permite el tráfico.
8. La subred back-end comienza el procesamiento de las reglas de entrada:
   1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   2. No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   3. No se aplica la regla 3 (Internet a firewall) de grupo de seguridad de red, pasar a la regla siguiente.
   4. No se aplica la regla 4 (IIS01 a AppVM01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
9. AppVM01 recibe la consulta SQL y responde.
10. Como no hay ninguna regla de salida en la subred back-end, se permite la respuesta.
11. La subred front-end comienza el procesamiento de las reglas de entrada:
    1. No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
    2. La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
12. El servidor IIS recibe la respuesta SQL y completa la respuesta HTTP y la envía al solicitante.
13. Puesto que no hay reglas de salida en la subred front-end, se permite la respuesta y el usuario de Internet recibe la página web solicitada.

#### <a name="allowed-rdp-to-backend"></a>(*Permitido*) RDP a back-end
1. El administrador del servidor en Internet solicita la sesión RDP para AppVM01 en BackEnd001.CloudApp.Net:xxxxx donde xxxxx es el número de puerto asignado de forma aleatoria para RDP a AppVM01 (el puerto asignado puede encontrarse en Azure Portal o a través de PowerShell).
2. La subred back-end comienza el procesamiento de las reglas de entrada:
   1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   2. Se aplica la regla 2 (RDP) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
3. No hay reglas de salida, se aplican las reglas predeterminadas y se permite el tráfico de retorno.
4. La sesión RDP está habilitada.
5. AppVM01 solicita el nombre de usuario y la contraseña

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Permitido*) Búsqueda de DNS del servidor web en el servidor DNS
1. El servidor web, IIS01, necesita una fuente de datos en www.data.gov, pero debe resolver la dirección.
2. La configuración de red de la red virtual incluye DNS01 (10.0.2.4 en la subred back-end) como servidor DNS principal, IIS01 envía la solicitud DNS a DNS01.
3. No hay reglas de salida en la subred front-end, se permite el tráfico.
4. La subred back-end comienza el procesamiento de las reglas de entrada:
   * Se aplica la regla 1 (DNS) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
5. El servidor DNS recibe la solicitud.
6. El servidor DNS no tiene la dirección en caché y solicita un servidor DNS raíz en Internet.
7. No hay reglas de salida en la subred back-end, se permite el tráfico.
8. El servidor DNS de Internet responde, puesto que esta sesión se inició internamente, se permite la respuesta.
9. El servidor DNS almacena en caché la respuesta y responde a la solicitud inicial a IIS01.
10. No hay reglas de salida en la subred back-end, se permite el tráfico.
11. La subred front-end comienza el procesamiento de las reglas de entrada:
    1. No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
    2. La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
12. IIS01 recibe la respuesta de DNS01.

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitido*) Archivo de acceso del servidor web en AppVM01
1. IIS01 solicita un archivo en AppVM01
2. No hay reglas de salida en la subred front-end, se permite el tráfico.
3. La subred back-end comienza el procesamiento de las reglas de entrada:
   1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   2. No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   3. No se aplica la regla 3 (Internet a IIS01) de grupo de seguridad de red, pasar a la regla siguiente.
   4. No se aplica la regla 4 (IIS01 a AppVM01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4. AppVM01 recibe la solicitud y responde con el archivo (suponiendo que se autoriza el acceso).
5. Como no hay ninguna regla de salida en la subred back-end, se permite la respuesta.
6. La subred front-end comienza el procesamiento de las reglas de entrada:
   1. No hay ninguna regla de grupo de seguridad de red que se aplique al tráfico de entrada desde la subred back-end a la subred front-end, por lo que no se aplica ninguna de las reglas de grupo de seguridad de red.
   2. La regla del sistema predeterminada que permite el tráfico entre subredes permitiría este tráfico, por lo que se permite el tráfico.
7. El servidor IIS recibe el archivo.

#### <a name="denied-web-to-backend-server"></a>(*Denegado*) Web a servidor back-end
1. Un usuario de Internet intenta acceder a un archivo en AppVM01 a través del servicio BackEnd001.CloudApp.Net.
2. Puesto que no hay ningún punto de conexión abierto para el uso compartido de archivos, el tráfico no pasaría por el servicio en la nube y no llegaría al servidor.
3. Si hubiera extremos abiertos por alguna razón, la regla 5 (Internet a la red virtual) de grupo de seguridad de red bloquearía este tráfico.

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Denegado*) Búsqueda de DNS web en el servidor DNS
1. Un usuario de Internet intenta buscar un registro DNS interno en DNS01 a través del servicio BackEnd001.CloudApp.Net.
2. Puesto que no hay ningún punto de conexión abierto para DNS, este tráfico no pasaría por el servicio en la nube y no llegaría al servidor.
3. Si hubiera puntos de conexión abiertos por alguna razón, la regla 5 (Internet a red virtual) de grupo de seguridad de red bloquearía este tráfico. (Nota: Esa regla 1 (DNS) no se aplicaría por dos motivos; en primer lugar, la dirección de origen es Internet, esta regla solo se aplica a la red virtual local como origen. También se trata una regla de permiso, por lo que nunca denegaría tráfico).

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Denegado*) Web a acceso SQL a través de firewall
1. Un usuario de Internet solicita los datos SQL desde FrontEnd001.CloudApp.Net (servicio en la nube accesible desde Internet).
2. Puesto que no hay ningún extremo abierto para SQL, el tráfico no pasaría por el servicio en la nube y no llegaría al firewall.
3. Si hubiera extremos abiertos por alguna razón, la subred front-end comenzaría el procesamiento de las reglas de entrada:
   1. No se aplica la regla 1 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   2. No se aplica la regla 2 (DNS) de grupo de seguridad de red, pasar a la regla siguiente.
   3. Se aplica la regla 3 (Internet a IIS01) de grupo de seguridad de red, se permite el tráfico, detener el procesamiento de las reglas.
4. El tráfico llega a dirección IP interna del IIS01 (10.0.1.5).
5. IIS01 no está escuchando en el puerto 1433, por lo que no hay respuesta a la solicitud.

## <a name="conclusion"></a>Conclusión
Este ejemplo representa una manera relativamente sencilla y directa de aislar la subred back-end del tráfico entrante.

Puede encontrar más ejemplos y una descripción general de los límites de seguridad de red [aquí][HOME].

## <a name="references"></a>Referencias
### <a name="main-script-and-network-config"></a>Script principal y configuración de red
Guarde el script completo en un archivo de script de PowerShell. Guarde la configuración de red en un archivo llamado "NetworkConf1.xml".
Modifique las variables definidas por el usuario según sea necesario y ejecute el script.

#### <a name="full-script"></a>Script completo
En función de las variables definidas por el usuario, este script realizará las siguientes acciones:

1. Conexión a una suscripción de Azure
2. Crear una cuenta de almacenamiento
3. Creación de una red virtual y dos subredes, como se define en el archivo de configuración de red
4. Creación de cuatro máquinas virtuales de Windows Server
5. Configuración del grupo de seguridad de red, incluido lo siguiente:
   * Creación de un grupo de seguridad de red
   * Rellenado con reglas
   * Enlace del grupo de seguridad de red a las subredes adecuadas

Este script de PowerShell debe ejecutarse localmente en un equipo o servidor conectado a Internet.

> [!IMPORTANT]
> Cuando se ejecuta este script, puede haber advertencias u otros mensajes informativos que se muestran en PowerShell. Solo los mensajes de error indicados en rojo son motivo de preocupación.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

  BackEnd Service (BackEnd subnet 10.0.2.0/24)
   DNS01      - 10.0.2.4
   AppVM01    - 10.0.2.5
   AppVM02    - 10.0.2.6

#>

# Fixed Variables
    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
    $VMName = @()
    $ServiceName = @()
    $VMFamily = @()
    $img = @()
    $size = @()
    $SubnetName = @()
    $VMIP = @()

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
# Configuration past this point #
# ----------------------------- #    

  # Get your Azure accounts
    Add-AzureAccount
    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

  # Create Storage Account
    If (Test-AzureName -Storage -Name $StorageAccountName) { 
        Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
        Return}
    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

  # Update Subscription Pointer to New Storage Account
    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

# Validation
$FatalError = $false

If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
     $FatalError = $true}

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
            $FatalError = $true}
        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

If ($FatalError) {
    Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
    Return}
Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

# Create VNET
    Write-Host "Creating VNET" -ForegroundColor Cyan 
    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

# Create Services
    Write-Host "Creating Services" -ForegroundColor Cyan
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Archivo de configuración de red
Guarde este archivo xml con la ubicación actualizada y agregue el vínculo a este archivo a la variable $NetworkConfigFile en el script anterior.

```XML
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
        <DnsServer name="Level3" IPAddress="209.244.0.3" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FrontEnd">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BackEnd">
            <AddressPrefix>10.0.2.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
        <DnsServersRef>
          <DnsServerRef name="DNS01" />
          <DnsServerRef name="Level3" />
        </DnsServersRef>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Scripts de aplicación de ejemplo
Si desea instalar una aplicación de ejemplo para este y otros ejemplos de red perimetral, hay una en el siguiente vínculo: [Script de aplicación de ejemplo][SampleApp].

## <a name="next-steps"></a>Pasos siguientes
* Actualice y guarde el archivo XML.
* Ejecute el script de PowerShell para compilar el entorno.
* Instale la aplicación de ejemplo.
* Pruebe diferentes flujos de tráfico a través de esta red perimetral.

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Red perimetral de entrada con grupo de seguridad de red"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

