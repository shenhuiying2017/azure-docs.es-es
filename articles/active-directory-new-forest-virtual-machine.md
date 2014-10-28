<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha"></tags>

# Instalación de un bosque nuevo de Active Directory en una red virtual de Azure

En este tema se muestra la creación de un nuevo entorno de Windows Server Active Directory en una red virtual de Azure en una máquina virtual (VM) en una [red virtual de Azure][]. En este caso, la red virtual de Azure no está conectada a una red de un entorno local.

Es posible que también le interesen los siguientes temas relacionados:

-   De manera opcional, puede [configurar una VPN de sitio a sitio con el asistente del Portal de administración][] y, a continuación, instalar un bosque nuevo o extender un bosque de entorno local hasta una red virtual de Azure. Para seguir esos pasos, consulte [Instalación de un controlador de dominio réplica de Active Directory en una red virtual de Azure][].
-   Para obtener una orientación en cuanto a conceptos sobre la instalación de los Servicios de dominio de Active Directory (AD DS) en una red virtual de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure][].
-   Para obtener una orientación paso a paso para crear un entorno de laboratorio de pruebas en Azure que incluya AD DS, consulte [Test Lab Guide: Windows Server 2012 R2 Base Configuration in Azure][].

## Tabla de contenido

-   [¿En qué se diferencia del entorno local?][]
-   [Paso 1. Creación de una red virtual de Azure][]
-   [Paso 2: Creación de una VM para ejecutar el controlador de dominio y los roles del servidor DNS][]
-   [Paso 3: Instalación de Windows Server Active Directory][]
-   [Paso 4: Establecimiento del servidor DNS para la red virtual de Azure][]
-   [Paso 5: Creación de VM para miembros del dominio y unión al dominio][]

## <span id="differ"></span></a>¿En qué se diferencia del entorno local?

No existe mucha diferencia entre instalar un controlador de dominio en Azure y una instalación en el entorno local. Las principales diferencias se mencionan en la siguiente tabla.

| Para configurar...                                      | Local                                                                                                                                | Red virtual                                                                                                                      |
|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| **Dirección IP para el controlador de dominio**         | Asigne la dirección IP estática en las propiedades del adaptador de red.                                                             | Obtenga la dirección IP a través de DHCP y, opcionalmente, ejecute el cmdlet Set-AzureStaticVNetIP para convertirla en estática. |
| **Resolución de clientes DNS**                          | Establezca la dirección de servidor DNS preferida y alternativa en las propiedades del adaptador de red de los miembros del dominio. | Establezca la dirección del servidor DNS en las propiedades de la red virtual.                                                   |
| **Almacenamiento de base de datos de Active Directory** | Opcionalmente, cambie la ubicación de almacenamiento predeterminada de C:\\.                                                         | Debe cambiar la ubicación de almacenamiento predeterminada de C:\\.                                                              |

## <span id="createvnet"></span></a>Paso 1: Creación de una red virtual de Azure

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Cree una red virtual. Haga clic en **Redes** \> **Create a virtual network**. Use los valores de la tabla siguiente para completar el asistente.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">En esta página del asistente…</th>
    <th align="left">Especifique estos valores</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Detalles de red virtual</strong></td>
    <td align="left"><p>Nombre: Escriba un nombre para la red virtual.</p>
    <p>Region: Elija la región más cercana.</p>
    <p>Grupo de afinidad: <strong>Crear un nuevo grupo de afinidad</strong></p>
    <p>Nombre del grupo de afinidad Escriba un nombre para el grupo de afinidad.</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>DNS y VPN</strong></td>
    <td align="left"><p>Deje el servidor DNS en blanco.</p>
    <p>No seleccione ninguna de las opciones VPN.</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Espacios de direcciones de la red virtual</strong></td>
    <td align="left"><p>Nombre de subred: Escriba un nombre para la subred.</p>
    <p>Starting IP: <strong>10.0.0.0</strong></p>
    <p>CIDR: <strong>/24 (256)</strong></p></td>
    </tr>
    </tbody>
    </table>

## <span id="createvm"></span></a>Paso 2: Creación de una VM para ejecutar el controlador de dominio y los roles del servidor DNS

1.  Haga clic en **New** \> **Proceso** \> **Máquina virtual** \> **From Gallery**.
2.  Use los valores de la tabla siguiente para completar el asistente.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">En esta página del asistente…</th>
    <th align="left">Especifique estos valores</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Sistema operativos</strong></td>
    <td align="left">Seleccione <strong>Windows Server 2012 R2 Datacenter</strong>.</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Configuración de la máquina virtual</strong></td>
    <td align="left"><p>Fecha de lanzamiento: la fecha de hoy.</p>
    <p>Nombre de máquina: especifique un valor único.</p>
    <p>Nivel: Standard</p>
    <p>Tamaño: seleccione cualquier tamaño.</p>
    <p>Nombre de usuario: Escriba un nombre. La cuenta de este usuario será miembro del grupo de administradores integrado.</p>
    <p>Contraseña: debe tener al menos 8 caracteres e incluir 3 de los siguientes tipos de caracteres:</p>
    <ul>
    <li>una letra en mayúscula</li>
    <li>una letra en minúscula</li>
    <li>un número</li>
    <li>un carácter especial</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Servicio en la nube</strong></td>
    <td align="left"><p>Servicio en la nube: <strong>cree un nuevo servicio en la nube.</strong></p>
    <p>Nombre de servicio en la nube: acepte el valor predeterminado.</p>
    <p>Región/AffinityGroup/VirtualNetwork: seleccione la red virtual que creó.</p>
    <p>Subred de red virtual: Seleccione la subred que creó.</p>
    <p>Cuenta de almacenamiento: <strong>Use una cuenta de almacenamiento generada de forma automática</strong></p>
    <p>Conjunto de disponibilidad: <strong>None</strong></p>
    <p>Endpoints: acepte los valores predeterminados.</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Agente de máquina virtual de Azure</strong></td>
    <td align="left">Seleccione <strong>Instale el Agente de máquina virtual</strong>.</td>
    </tr>
    </tbody>
    </table>

3.  La dirección IP dinámica que se asigna de manera predeterminada a la VM es válida mientras dure el servicio en la nube. Sin embargo, cambiará si se apaga la VM. Puede asignar una dirección IP estática mediante la [ejecución del cmdlet de Azure PowerShell Set-AzureStaticVNetIP (en inglés)][] de modo que la dirección IP se mantenga si alguna vez necesita apagar la VM.
4.  Acople un disco adicional a la VM para almacenar la base de datos de Active Directory, los registros y SYSVOL.
    5.  Haga clic en **VM** \> **Attach** \> **Attach empty disk**.
    6.  Especifique un tamaño (por ejemplo, 10 GB) y acepte los demás valores predeterminados.
7.  Inicie sesión en la VM y formatee el disco adicional.
    8.  Haga clic en **Conectar** para iniciar sesión en la VM, haga clic en **Abrir** para crear una sesión de RDP y vuelva a hacer clic en **Conectar**.
    9.  Cambie las credenciales al nombre de usuario y contraseña nuevos que especificó.
    10. En el Administrador de servidores, haga clic en **Tools** \> **Computer Management**.
    11. Haga clic en **Disk Management** y en **Ok** para inicializar el disco nuevo.
    12. Haga clic con el botón secundario en el nombre del disco y haga clic en **New Simple Volume**. Complete el asistente para formatear la nueva unidad.

## <span id="installad"></span></a>Paso 3: Instalación de Windows Server Active Directory

[Instale AD DS][] usando la misma rutina que usa en el entorno local (es decir, puede usar la interfaz de usuario, un archivo de respuestas o Windows PowerShell). Necesita proporcionar credenciales de Administrador para instalar un bosque nuevo. Para especificar la ubicación de la base de datos de Active Directory, los registros y SYSVOL, cambie la ubicación de almacenamiento predeterminada desde la unidad del sistema operativo hasta el disco de datos adicional que acopló a la VM.

Después de finalizada la instalación del controlador de dominio, vuelva a conectarse a la VM e inicie sesión en el controlador de dominio. Recuerde especificar las credenciales del dominio.

</p>
## <span id="dns"></span></a>Paso 4: Establecimiento del servidor DNS para la red virtual de Azure

1.  Haga clic en **Virtual Networks**, haga doble clic en la red virtual que creó y, a continuación, haga clic en **Configure**.
2.  En **DNS servers**, escriba el nombre y DIP del controlador de dominio y haga clic en **Save**.
3.  Seleccione la VM y haga clic en **Restart** para activar la VM y definir la configuración del solucionador de DNS con la dirección IP del servidor DNS nuevo.

## <span id="domainmembers"></span></a>Paso 5: Creación de VM para miembros del dominio y unión al dominio

Cree VMS adicionales para aprovisionar equipos miembros de dominio. Puede establecer la interfaz de usuario o Azure PowerShell. Si usa la interfaz de usuario, simplemente siga los mismos pasos que utilizó para crear la primera VM. Posteriormente, una las VM al dominio igual que lo haría en el entorno local. Si usa Azure PowerShell, puede aprovisionar las VM y unirlas mediante el dominio en el primer inicio.

Este ejemplo creará una VM unida al dominio denominada DC2 que ejecuta Windows Server 2012 R2 Datacenter. Cuando se haya aprovisionado DC2, inicie sesión en ella como administrador de dominios y promuévala para convertirla en una DC de réplica.

Puede ejecutar Get-AzureVMImage para obtener los nombres de imagen. Por ejemplo, para volver a una lista de imágenes de Windows Server 2012 R2, ejecute Get-AzureVMImage | where-object {$\_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.

    '

    cls

    Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
    Select-AzureSubscription -SubscriptionName "Free Trial"

    #Deploy a new VM and join it to the domain
    #-------------------------------------------
    #Specify my DC's DNS IP (10.0.0.4)
    $myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'

    # OS Image to Use
    $image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
    $service = 'DC2'
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'

    #VM Configuration
    $vmname = 'DC2'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Si vuelve a ejecutar el script, necesita suministrar un valor único para $service. Puede ejecutar Test-AzureName -Service *nombre del servicio*, el cual se devuelve si el nombre ya se utilizó.

## Otras referencias

-   [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Windows Azure][]

-   [Configurar una red virtual solo en la nube en el Portal de administración][]

-   [Configurar una VPN de sitio a sitio en el Portal de administración][]

-   [Instalación de un controlador de dominio réplica de Active Directory en una red virtual de Azure][1]

-   [IaaS para profesionales de TI en Windows Azure: (01) Principios básicos sobre máquinas virtuales][]

-   [IaaS para profesionales de TI en Windows Azure: (05) Creación de redes virtuales y conectividad entre instalaciones][]

-   [Red virtual][red virtual de Azure]

-   [Instalación y configuración de Azure PowerShell][]

-   [Azure PowerShell][]

-   [Azure Management Cmdlets][]

-   [Set Azure VM Static IP Address][]

-   [Asignación de direcciones IP estáticas a una VM de Azure][]

-   [Instalación de un nuevo bosque de Active Directory][Instale AD DS]

-   [Introducción a la virtualización de los Servicios de dominio de Active Directory (AD DS) (nivel 100)][]

-   [Test Lab Guide: Windows Server 2012 R2 Base Configuration in Azure][]

  [red virtual de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx
  [configurar una VPN de sitio a sitio con el asistente del Portal de administración]: http://msdn.microsoft.com/es-es/library/windowsazure/dn133795.aspx
  [Instalación de un controlador de dominio réplica de Active Directory en una red virtual de Azure]: http://www.windowsazure.com/es-es/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156090.aspx
  [Test Lab Guide: Windows Server 2012 R2 Base Configuration in Azure]: http://www.microsoft.com/es-es/download/details.aspx?id=41684
  [¿En qué se diferencia del entorno local?]: #differ
  [Paso 1. Creación de una red virtual de Azure]: #createvnet
  [Paso 2: Creación de una VM para ejecutar el controlador de dominio y los roles del servidor DNS]: #createvm
  [Paso 3: Instalación de Windows Server Active Directory]: #installad
  [Paso 4: Establecimiento del servidor DNS para la red virtual de Azure]: #dns
  [Paso 5: Creación de VM para miembros del dominio y unión al dominio]: #domainmembers
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [ejecución del cmdlet de Azure PowerShell Set-AzureStaticVNetIP (en inglés)]: http://msdn.microsoft.com/es-es/library/windowsazure/dn630228.aspx
  [Instale AD DS]: http://technet.microsoft.com/library/jj574166.aspx
  [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Windows Azure]: http://msdn.microsoft.com/library/azure/jj156090.aspx
  [Configurar una red virtual solo en la nube en el Portal de administración]: http://msdn.microsoft.com/es-es/library/dn631643.aspx
  [Configurar una VPN de sitio a sitio en el Portal de administración]: http://msdn.microsoft.com/es-es/library/dn133795.aspx
  [1]: http://azure.microsoft.com/es-es/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [IaaS para profesionales de TI en Windows Azure: (01) Principios básicos sobre máquinas virtuales]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [IaaS para profesionales de TI en Windows Azure: (05) Creación de redes virtuales y conectividad entre instalaciones]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
  [Instalación y configuración de Azure PowerShell]: http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/
  [Azure PowerShell]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156055.aspx
  [Azure Management Cmdlets]: http://msdn.microsoft.com/es-es/library/windowsazure/jj152841
  [Set Azure VM Static IP Address]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
  [Asignación de direcciones IP estáticas a una VM de Azure]: http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/
  [Introducción a la virtualización de los Servicios de dominio de Active Directory (AD DS) (nivel 100)]: http://technet.microsoft.com/es-es/library/hh831734.aspx
