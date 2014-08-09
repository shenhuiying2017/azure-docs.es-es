<properties  linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

# Instalación de un bosque nuevo de Active Directory en una red virtual de Azure

En este tema se muestra la creación de un nuevo entorno de Windows Server Active Directory en una red virtual de Azure en una máquina virtual (VM) en una [red virtual de Azure][1]. En este caso, la red virtual de Azure no está conectada a una red de un entorno local.

Es posible que también le interesen los siguientes temas relacionados:

* De manera opcional, puede [configurar una VPN de sitio a sitio con el
  asistente del Portal de administración][2] y, a continuación, instalar
  un bosque nuevo o extender un bosque de entorno local hasta una red
  virtual de Azure. Para seguir esos pasos, consulte [Instalación de un
  controlador de dominio réplica de Active Directory en una red virtual
  de Azure][3].
* Para obtener una orientación en cuanto a conceptos sobre la
  instalación de los Servicios de dominio de Active Directory (AD DS) en
  una red virtual de Azure, consulte [Directrices para implementar
  Windows Server Active Directory en máquinas virtuales de Azure][4].
* Para obtener una orientación paso a paso para crear un entorno de
  laboratorio de pruebas en Azure que incluya AD DS, consulte [Test Lab
  Guide: Windows Server 2012 R2 Base Configuration in Azure][5].

## Tabla de contenido

* [&iquest;En qué se diferencia del entorno local?](#differ)
* [Paso 1. Creación de una red virtual de Azure](#createvnet)
* [Paso 2: Creación de una VM para ejecutar el controlador de dominio y
  los roles del servidor DNS](#createvm)
* [Paso 3: Instalación de Windows Server Active Directory](#installad)
* [Paso 4: Establecimiento del servidor DNS para la red virtual de
  Azure](#dns)
* [Paso 5: Creación de VM para miembros del dominio y unión al
  dominio](#domainmembers)

<h2><a  id="differ" ></a>&iquest;En qué se diferencia del entorno local?</h2>


No existe mucha diferencia entre instalar un controlador de dominio en Azure y una instalación en el entorno local. Las principales diferencias se mencionan en la siguiente tabla.

Para configurar... | Entorno local | Red virtual de Azure
 -------------| ------------- | ------------ 
**IP address for the domain controller**| Asignar dirección IP estática en las propiedades del adaptador de red| Obtener una dirección IP mediante DHCP y ejecutar el cmdlet Set-AzureStaticVNetIP para dejarla estática 
**DNS client resolver** | Establecer la dirección de servidor DNS preferido y alternativo en las propiedades del adaptador de red de los miembros de dominio | Establecer dirección del servidor DNS en las propiedades de la red virtual 
**Active Directory database storage** | De manera opcional, cambiar la ubicación predeterminada del almacenamiento en C:\ | Necesita cambiar la ubicación predeterminada del almacenamiento en C:\

<h2><a  id="createvnet" ></a>Paso 1: Creación de una red virtual de Azure</h2>


1.  Inicie sesión en el [Portal de administración de Azure][6].
2.  Cree una red virtual. Haga clic en **Redes** > **Create a virtual network**. Use los valores de la tabla siguiente para completar el asistente.
    
    En esta página del asistente | Especifique estos valores
    ------------- | ------------- 
	**Virtual Network Details** | <p>Nombre: Escriba un nombre para la red virtual</p>	<p>Región: Seleccione la región más cercana</p><p>Grupo de afinidad: <b>Crear un grupo de afinidad nuevo</b></p><p>Nombre del grupo de afinidad: Escriba un nombre para el grupo de afinidad</p>
	**DNS and VPN** | <p>Deje el servidor DNS en blanco</p><p>No seleccione ninguna opción de VPN</p>
	**Virtual network address spaces** | <p>Nombre de subred: Escriba un nombre para la subred</p>	<p>Dirección IP inicial: **10.0.0.0**</p><p>CIDR: **/24 (256)**</p>

<h2><a  id="createvm" ></a>Paso 2: Creación de una VM para ejecutar el controlador de dominio y los roles del servidor DNS</h2>


1.  Haga clic en **New** > **Proceso** > **Máquina virtual** > **From Gallery**. 2.  Use los valores de la tabla siguiente para completar el asistente.
    
    En esta página del asistente | Especifique estos valores
    ------------- | ------------- 
	**Operating system** | Seleccione **Windows Server 2012 R2 Datacenter**
	**Virtual machine configuration** | <p>Fecha de lanzamiento: Fecha de hoy</p><p>Nombre de la máquina: Especifique un valor único</p><p>Nivel: Estándar</p><p>Tamaño: Seleccione cualquier tamaño</p><p>Nombre de usuario: Escriba un nombre. La cuenta de este usuario será miembro del grupo de administradores integrado.</p><p>Contraseña: Debe tener al menos 8 caracteres e incluir 3 de los siguientes tipos de caracteres:</p><p><ul><li>una letra mayúscula</li><li>una letra minúscula</li><li>un número</li><li>un carácter especial</li></ul></p>
	**Servicio en la nube** | <p>Servicio en la nube: **Create a new cloud service**</p><p>Nombre del servicio en la nube: Acepte el valor predeterminado</p><p>Región/Grupo de afinidad/Red virtual: Seleccione la red virtual que creó</p><p>Subred de la red virtual: Seleccione la subred que creó.</p><p>Cuenta de almacenamiento: **Use an automatically generated storage account**</p><p>Conjunto de disponibilidad: **None**</p><p>Endpoints: Acepte los valores predeterminados</p>
	 **VM Agent** | Seleccione **Install the VM Agent**

3.  La dirección IP dinámica que se asigna de manera predeterminada a la
    VM es válida mientras dure el servicio en la nube. Sin embargo,
    cambiará si se apaga la VM. Puede asignar una dirección IP estática
    mediante la [ejecución del cmdlet de Azure PowerShell
    Set-AzureStaticVNetIP (en inglés)][7] de modo que la dirección IP se
    mantenga si alguna vez necesita apagar la VM.
4.  Acople un disco adicional a la VM para almacenar la base de datos de
    Active Directory, los registros y SYSVOL.
	5.  Haga clic en **VM** > **Attach** > **Attach empty disk**.
	6.  Especifique un tamaño (por ejemplo, 10 GB) y acepte los demás
    valores predeterminados.
7.  Inicie sesión en la VM y formatee el disco adicional.
	8.  Haga clic en **Conectar** para iniciar sesión
	en la VM, haga clic en **Abrir** para crear
    una sesión de RDP y vuelva a hacer clic en **Conectar**.
	9.  Cambie las credenciales al nombre de usuario y contraseña nuevos que
    especificó.
	10. En el Administrador de servidores, haga clic en **Tools** > **Computer Management**.
	11. Haga clic en **Disk Management** y en
	    **Ok** para inicializar el disco nuevo.
	12. Haga clic con el botón secundario en el nombre del disco y haga clic
    en **New Simple Volume**. Complete el
    asistente para formatear la nueva unidad.

<h2><a  id="installad" ></a>Paso 3: Instalación de Windows Server Active Directory</h2>


[Instale AD DS][8] usando la misma rutina que usa en el entorno local
(es decir, puede usar la interfaz de usuario, un archivo de respuestas o
Windows PowerShell). Necesita proporcionar credenciales de Administrador para instalar un bosque nuevo. Para especificar la ubicación de la base de datos de Active Directory, los registros y SYSVOL, cambie la ubicación de almacenamiento predeterminada desde la unidad del sistema operativo hasta el disco de datos adicional que acopló a la VM.

Después de finalizada la instalación del controlador de dominio, vuelva a conectarse a la VM e inicie sesión en el controlador de dominio. Recuerde especificar las credenciales del dominio.


<h2><a  id="dns" ></a>Paso 4: Establecimiento del servidor DNS para la red virtual de Azure</h2>


1.  Haga clic en **Virtual Networks**, haga doble
    clic en la red virtual que creó y, a continuación, haga clic en
    **Configure**.
2.  En **DNS servers**, escriba el nombre y DIP
    del controlador de dominio y haga clic en **Save**.
3.  Seleccione la VM y haga clic en **Restart**
    para activar la VM y definir la configuración del solucionador de
    DNS con la dirección IP del servidor DNS nuevo.

<h2><a  id="domainmembers" ></a>Paso 5: Creación de VM para miembros del dominio y unión al dominio</h2>


Cree VMS adicionales para aprovisionar equipos miembros de dominio. Puede establecer la interfaz de usuario o Azure PowerShell. Si usa la interfaz de usuario, simplemente siga los mismos pasos que utilizó para crear la primera VM. Posteriormente, una las VM al dominio igual que lo haría en el entorno local. Si usa Azure PowerShell, puede aprovisionar las VM y unirlas mediante el dominio en el primer inicio, como en el siguiente ejemplo. '

	cls

	Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
	Select-AzureSubscription -SubscriptionName "Free Trial"

	#Deploy a new VM and join it to the domain
	#-------------------------------------------
	#Specify my DC's DNS IP (10.0.0.4)
	$myDNS = New-AzureDNS -Name 'DC-1' -IPAddress '10.0.0.4'
	
	# OS Image to Use
	$image = 'a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201310.01-en.us-127GB.vhd'
	$service = 'ConApp1'
	$AG = 'YourAffinityGroup'
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'ConApp1'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Si vuelve a ejecutar el script, necesita suministrar un valor único para
$service. Puede ejecutar Test-AzureName -Service *nombre del servicio*, el cual se devuelve si el nombre ya se utilizó.

## Otras referencias

* [Directrices para implementar Windows Server Active Directory en
  máquinas virtuales de Windows Azure][4]

* [Creación de una red virtual en Azure][9]

* [Creación de una red virtual para una conectividad sitio a sitio entre
  locales][10]

* [Instalación de un controlador de dominio réplica de Active Directory
  en una red virtual de Azure][3]

* [Red virtual][1]

* [Instalación y configuración de Azure PowerShell][11]

* [Azure PowerShell][12]

* [Azure Management Cmdlets][13]

* [Set Azure VM Static IP Address][14]

* [Instale un nuevo bosque de Active Directory Server 2012 de Windows
  (nivel 200)][8]

* [Introducción a la virtualización de los Servicios de dominio de
  Active Directory (AD DS) (nivel 100)][15]

* [Test Lab Guide: Windows Server 2012 R2 Base Configuration in
  Azure][5]



[1]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx
[2]: http://msdn.microsoft.com/es-es/library/windowsazure/dn133795.aspx
[3]: http://www.windowsazure.com/es-es/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
[4]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156090.aspx
[5]: http://www.microsoft.com/en-us/download/details.aspx?id=41684
[6]: https://manage.windowsazure.com
[7]: http://msdn.microsoft.com/es-es/library/windowsazure/dn630228.aspx
[8]: http://technet.microsoft.com/library/jj574166.aspx
[9]: http://www.windowsazure.com/es-es/manage/services/networking/create-a-virtual-network/
[10]: http://www.windowsazure.com/es-es/documentation/articles/virtual-networks-create-site-to-site-cross-premises-connectivity/
[11]: http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/
[12]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156055.aspx
[13]: http://msdn.microsoft.com/es-es/library/windowsazure/jj152841
[14]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
[15]: http://technet.microsoft.com/en-us/library/hh831734.aspx