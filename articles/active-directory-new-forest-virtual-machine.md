<properties urlDisplayName="Active Directory forest" pageTitle="Instalación de un bosque nuevo de Active Directory en una red virtual de Azure" metaKeywords="" description="En este tutorial se explica cómo crear un nuevo bosque de Active Directory en una máquina virtual (VM) en una Red virtual de Azure." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="Justinha" />




#Instalación de un bosque nuevo de Active Directory en una red virtual de Azure

En este tema se muestra la creación de un nuevo entorno de Windows Server Active Directory en una red virtual de Azure en una máquina virtual (VM) de una [red virtual de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx). En este caso, la red virtual de Azure no está conectada a una red de un entorno local. 

Es posible que también le interesen los siguientes temas relacionados:

- De manera opcional, puede [configurar una VPN de sitio a sitio con el asistente del Portal de administración](http://msdn.microsoft.com/es-es/library/windowsazure/dn133795.aspx) y, a continuación, instalar un bosque nuevo o extender un bosque de entorno local hasta una red virtual de Azure. Para seguir esos pasos, consulte [Instalación de un controlador de dominio réplica de Active Directory en una red virtual de Azure](http://www.windowsazure.com/es-es/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-  Para obtener una orientación en cuanto a conceptos sobre la instalación de los Servicios de dominio de Active Directory (AD DS) en una red virtual de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/jj156090.aspx).
-  Para obtener una orientación paso a paso para crear un entorno de laboratorio de pruebas en Azure que incluya AD DS, consulte [Test Lab Guide: Configuración básica de Windows Server 2012 R2 en Azure](http://www.microsoft.com/es-es/download/details.aspx?id=41684).



##Tabla de contenido##

* [¿En qué se diferencia del entorno local?](#differ)
* [Paso 1: Creación de una red virtual de Azure](#createvnet)
* [Paso 2: Creación de una VM para ejecutar el controlador de dominio y los roles del servidor DNS](#createvm)
* [Paso 3: Instalación de Windows Server Active Directory](#installad)
* [Paso 4: Establecimiento del servidor DNS para la red virtual de Azure](#dns)
* [Paso 5: Creación de VM para miembros del dominio y unión al dominio](#domainmembers)


<h2><a id="differ"></a>¿En qué se diferencia del entorno local?</h2>
No existe mucha diferencia entre instalar un controlador de dominio en Azure y una instalación en el entorno local. Las principales diferencias se mencionan en la siguiente tabla. 

Para configurar...  | Local  | Red virtual de Azure	
------------- | -------------  | ------------
**Dirección IP del controlador de dominio**  | Asigne la dirección IP estática en las propiedades de adaptador de red  | Ejecute el cmdlet Set-AzureStaticVNetIP para asignar una dirección IP estática
**Solucionador de cliente DNS**  | Establezca una dirección de servidor DNS preferida y otra alternativa en las propiedades del adaptador de red de los miembros del dominio  | Configure la dirección del servidor DNS en las propiedades de la red virtual
**Almacenamiento de base de datos de Active Directory** | Si lo desea, cambie la ubicación de almacenamiento predeterminada de C:\  | Debe cambiar la ubicación de almacenamiento predeterminada de C:\



<h2><a id="createvnet"></a>Paso 1: Creación de una red virtual de Azure</h2>
1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Cree una red virtual. Haga clic en <b>Redes</b> > <b>Crear una red virtual</b>. Use los valores de la tabla siguiente para completar el asistente. 

	En esta página del asistente...  | Especifique estos valores
	------------- | -------------
	**Detalles de red virtual** | <p>Nombre: Escriba un nombre para la red virtual.</p><p>Region: Elija la región más cercana.</p>
	**DNS y VPN**  | <p>Deje el servidor DNS en blanco.</p><p>No seleccione ninguna de las opciones VPN.</p>
	**Espacios de direcciones de la red virtual**  | <p>Nombre de subred: Escriba un nombre para la subred.</p><p>Starting IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



<h2><a id="createvm"></a>Paso 2: Creación de una VM para ejecutar el controlador de dominio y los roles del servidor DNS</h2>
 
1. Haga clic en <b>Nuevo</b> > <b>Proceso</b> > <b>Máquina virtual</b> > <b>De la galería</b>. 
2. Use los valores de la tabla siguiente para completar el asistente.

	En esta página del asistente...  | Especifique estos valores
	------------- | -------------
	**Sistema operativo** | Seleccione **Windows Server 2012 R2 Datacenter**
	**Configuración de la máquina virtual** | <p>Fecha de lanzamiento: la fecha de hoy.</p><p>Nombre de máquina: especifique un valor único.</p><p>Nivel: Standard</p><p>Tamaño seleccione cualquier tamaño.</p><p>Nombre de usuario: Escriba un nombre. La cuenta de este usuario será miembro del grupo de administradores integrado. </p><p>Contraseña: debe tener al menos 8 caracteres e incluir 3 de los siguientes tipos de caracteres:</p><ul><li>una letra en mayúscula</li><li>una letra en minúscula</li><li>un número</li><li>un carácter especial</li></ul>
	**Servicio en la nube**  | <p>Servicio en la nube: <b>cree un nuevo servicio en la nube.</b></p><p>Nombre de servicio en la nube: acepte el valor predeterminado.</p><p>Región/AffinityGroup/VirtualNetwork: seleccione la red virtual que creó.</p><p>Subred de red virtual: Seleccione la subred que creó. </p><p>Cuenta de almacenamiento: <b>Use una cuenta de almacenamiento generada de forma automática</b></p><p>Conjunto de disponibilidad: <b>None</b></p><p>Endpoints: acepte los valores predeterminados.</p>
	**Agente de VM**  | Seleccione **Instalar el Agente de máquina virtual**

1. La dirección IP dinámica que se asigna de manera predeterminada a la VM es válida mientras dure el servicio en la nube. Sin embargo, cambiará si se apaga la VM. Asigne una dirección IP estática mediante la [ejecución del cmdlet de Azure PowerShell Set-AzureStaticVNetIP](http://msdn.microsoft.com/es-es/library/windowsazure/dn630228.aspx) de modo que la dirección IP se mantenga si alguna vez necesita apagar la VM. 
2. Acople un disco adicional a la VM para almacenar la base de datos de Active Directory, los registros y SYSVOL. 
  3. Haga clic en <b>VM</b> > <b>Adjuntar</b> > <b>Acoplar disco vacío</b>. 
  4. Especifique un tamaño (por ejemplo, 10 GB) y acepte los demás valores predeterminados.
3. Inicie sesión en la VM y formatee el disco adicional. 
  4. Haga clic en <b>Conectar</b> para iniciar sesión en la máquina virtual, haga clic en <b>Abrir</b> para crear una sesión de RDP y haga clic en <b>Conectar</b> de nuevo.
  4. Cambie las credenciales al nombre de usuario y contraseña nuevos que especificó.
  5. En el Administrador del servidor, haga clic en <b>Herramientas</b> > <b>Administración de equipos</b>. 
  6. Haga clic en <b>Administración de discos</b> y en <b>Aceptar</b> para inicializar el nuevo disco. 
  6. Haga clic con el botón derecho en el nombre del disco y haga clic en <b>Nuevo volumen simple</b>. Complete el asistente para formatear la nueva unidad. 

<h2><a id="installad"></a>Paso 3: Instalación de Windows Server Active Directory</h2>
[Instale AD DS](http://technet.microsoft.com/library/jj574166.aspx) usando la misma rutina que usa en el entorno local (es decir, puede usar la interfaz de usuario, un archivo de respuestas o Windows PowerShell). Necesita proporcionar credenciales de Administrador para instalar un bosque nuevo. Para especificar la ubicación de la base de datos de Active Directory, los registros y SYSVOL, cambie la ubicación de almacenamiento predeterminada desde la unidad del sistema operativo hasta el disco de datos adicional que acopló a la VM. 
<p>Después de finalizada la instalación del controlador de dominio, vuelva a conectarse a la VM e inicie sesión en el controlador de dominio. Recuerde especificar las credenciales del dominio.</p>

<h2><a id="dns"></a>Paso 4: Establecimiento del servidor DNS para la red virtual de Azure</h2>
1. Haga clic en <b>Redes virtuales</b>, haga doble clic en la red virtual que creó y haga clic en <b>Configurar</b>. 
2. En <b>Servidores DNS</b>, escriba el nombre y la DIP del controlador de dominio y haga clic en <b>Save</B>. 
3. Seleccione la máquina virtual y haga clic en <b>Reinicio</b> para activar la VM y definir la configuración del solucionador de DNS con la dirección IP del servidor DNS nuevo. 


<h2><a id="domainmembers"></a>Paso 5: Creación de VM para miembros del dominio y unión al dominio</h2>
<p>Cree VMS adicionales para aprovisionar equipos miembros de dominio. Puede establecer la interfaz de usuario o Azure PowerShell. Si usa la interfaz de usuario, simplemente siga los mismos pasos que utilizó para crear la primera VM. Posteriormente, una las VM al dominio igual que lo haría en el entorno local. Si usa Azure PowerShell, puede aprovisionar las VM y unirlas mediante el dominio en el primer inicio. </p><p>Este ejemplo creará una VM unida al dominio denominada DC2 que ejecuta Windows Server 2012 R2 Datacenter. Cuando se haya aprovisionado DC2, inicie sesión en ella como administrador de dominios y promuévala para convertirla en una DC de réplica. </p><p>Puede ejecutar Get-AzureVMImage para obtener los nombres de imagen. Por ejemplo, para volver a una lista de imágenes de Windows Server 2012 R2, ejecute Get-AzureVMImage | where-object {$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.</p>
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
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'DC2'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Si vuelve a ejecutar el script, necesita suministrar un valor único para $service. Puede ejecutar Test-AzureName -Service <i>nombre de servicio</i>, que se devuelve si el nombre ya existe. 	

## Otras referencias

-  [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurar una red virtual solo en la nube en el Portal de administración](http://msdn.microsoft.com/es-es/library/dn631643.aspx)

-  [Configurar una VPN de sitio a sitio en el Portal de administración](http://msdn.microsoft.com/es-es/library/dn133795.aspx)

-  [Instalación de un controlador de dominio réplica de Active Directory en una red virtual de Azure](http://azure.microsoft.com/es-es/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-  [IaaS para profesionales de TI en Microsoft Azure: (01) Principios básicos sobre máquinas virtuales](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [IaaS para profesionales de TI en Microsoft Azure: (05) Creación de redes virtuales y conectividad entre instalaciones](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Red virtual](http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx)

-  [Instalación y configuración de Azure PowerShell](http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/)

-  [Azure PowerShell](http://msdn.microsoft.com/es-es/library/windowsazure/jj156055.aspx)

-  [Azure Management Cmdlets](http://msdn.microsoft.com/es-es/library/windowsazure/jj152841)

-  [Set Azure VM Static IP Address](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Asignación de direcciones IP estáticas a una VM de Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Instalación de un nuevo bosque de Active Directory](http://technet.microsoft.com/library/jj574166.aspx)

-  [Introducción a la virtualización de los Servicios de dominio de Active Directory (AD DS) (nivel 100)](http://technet.microsoft.com/es-es/library/hh831734.aspx)

-  [Test Lab Guide: Windows Server 2012 R2 Base Configuration in Azure](http://www.microsoft.com/es-es/download/details.aspx?id=41684)


<!--HONumber=35.2-->
