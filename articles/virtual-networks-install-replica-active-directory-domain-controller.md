<properties 
	pageTitle="Instalar un controlador de dominio de réplica en Azure" 
	description="Tutorial que explica cómo instalar un controlador de dominio de un bosque de Active Directory local en una máquina virtual de Azure." 
	services="virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="Justinha"/>


# Instalación de un controlador de dominio de Active Directory de réplica en una red virtual de Azure

En este tema se muestra cómo instalar controladores de dominio adicionales (también conocidos como controladores de dominio de réplica) para un dominio de Active Directory local en máquinas virtuales (VM) en una red virtual de Azure. 

Es posible que también le interesen los siguientes temas relacionados:

- Opcionalmente, puede instalar un nuevo bosque de Active Directory en una red virtual. Para realizar esos pasos, consulte [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/).
-  Para obtener una orientación en cuanto a conceptos sobre la instalación de los Servicios de dominio de Active Directory (AD DS) en una red virtual de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).
-  Para obtener una orientación paso a paso para crear un entorno de laboratorio de pruebas en Azure que incluya AD DS, consulte [Test Lab Guide: Configuración de base de Azure](http://www.microsoft.com/download/details.aspx?id=41684).

## Diagrama del escenario

En este escenario, los usuarios externos necesitan tener acceso a las aplicaciones que se ejecutan en servidores unidos a un dominio. Las máquinas virtuales que ejecutan los servidores de aplicaciones y los controladores de dominio de réplica se instalan en una red virtual de Azure. La red virtual puede estar conectada a la red local mediante una conexión [VPN de sitio a sitio](http://msdn.microsoft.com/library/azure/dn133795.aspx), tal como se muestra en el diagrama siguiente, o se puede usar [ExpressRoute](http://azure.microsoft.com/services/expressroute/) para una conexión más rápida. 

Los servidores de aplicaciones y los controladores de dominio se implementan en [servicios de nube](http://azure.microsoft.com/documentation/articles/cloud-services-what-is/) independientes para distribuir el procesamiento informático y en [conjuntos de disponibilidad](http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/) para una mejor tolerancia a errores. 
Los controladores de dominio se replican entre sí y con controladores de dominio locales mediante la replicación de Active Directory. No se necesitan herramientas de sincronización.

![][1]

## Paso 1: Crear un sitio de Active Directory para la red virtual de Azure

Es buena idea crear un sitio de Active Directory que represente la región de red correspondiente a la red virtual. Eso ayuda a optimizar la autenticación, la replicación y otras operaciones de ubicación del controlador de dominio. Los pasos siguientes explican cómo crear un sitio y para obtener más información, consulte [Agregar un sitio nuevo](http://technet.microsoft.com/library/cc781496.aspx).

1. Abra Servicios y sitios de Active Directory: **Administrador del servidor** > **Herramientas** > **Servicios y sitios de Active Directory**.
2. Cree un sitio para representar la región donde creó una red virtual de Azure: haga clic en **Sitios** > **Acción** > **Nuevo sitio** > escriba el nombre del nuevo sitio, por ejemplo, Azure, oeste de EE. UU. > seleccione un vínculo de sitio > **Aceptar**.
3. Cree una subred y asóciela al nuevo sitio: haga doble clic en **Sitios** > haga clic con el botón secundario en **Subredes** > **Nueva subred** > escriba el intervalo de direcciones IP de la red virtual (por ejemplo, 10.1.0.0/16 en el diagrama del escenario) > seleccione el nuevo sitio de Azure > **Aceptar**.

## Paso 2: Creación de una red virtual de Azure

<ol><li><p>En el Portal de administración de Azure, haga clic en <b>Nuevo</b> > <b>Servicios de red</b> > <b>Red virtual</b> > <b>Creación personalizada</b> y use los siguientes valores para completar el asistente.</p>

<table style="width:100%">
<tr>
<td>En esta página del asistente</td>
<td>Especifique estos valores</td>
</tr>
<tr>
<td><b>Detalles de red virtual</b></td>
<td><ul><li>Nombre: Escriba un nombre para la red virtual, como WestUSVNet.</li><li>Region: Esta es la ubicación de Azure donde se ubicará la red virtual, por ejemplo, oeste de EE. UU. No se puede cambiar esta ubicación una vez creada la red virtual.</li></ul>
</td>
</tr>
<tr>
<td><b>Servidores DNS y conectividad VPN</b></td>
<td><ul><li>Servidores DNS: Especifique el nombre y la dirección IP de uno o más servidores DNS locales.</li><li>Conectividad: Seleccione <b>Configurar una VPN de sitio a sitio</b>.</li><li>Red local: Especifique una nueva red local.</li></ul>Si está utilizando ExpressRoute en lugar de una VPN, consulte <a href="http://msdn.microsoft.com/library/azure/dn606306.aspx">Configurar una conexión ExpressRoute a través de un proveedor de Exchange</a>.</td>
</tr>
<tr>
<td><b>Conectividad de sitio a sitio</b></td>
<td><ul><li>Nombre: Escriba un nombre para la red local.</li><li>VPN DEVICE IP ADDRESS: Especifique la dirección IP pública del dispositivo que se conectará a la red virtual. El dispositivo VPN no se encuentra detrás de un NAT.</li><li>Dirección: Especifique los intervalos de direcciones de la red local (por ejemplo, 192.168.0.0/16 en el diagrama del escenario).</li></ul></td>
</tr>
<tr>
<td><b>Espacios de direcciones de la red virtual</b></td>
<td><ul><li>Address Space: Especifique el intervalo de direcciones IP para las máquinas virtuales que desea ejecutar en la red virtual (por ejemplo, 10.1.0.0/16 en el diagrama del escenario). Este intervalo de direcciones no puede solaparse con los intervalos de direcciones de la red local.</li><li>Subredes: Especifique un nombre y una dirección para una subred para los servidores de aplicaciones (como front-end, 10.1.1.0/24) y para los controladores de dominio (como back-end, 10.1.2.0/24).</li><li>Haga clic en <b>Agregar subred de puerta de enlace</b>.</li></ul></td>
</tr>
</table>
</li>
<li><p>A continuación, configurará la puerta de enlace de red virtual para crear una conexión VPN de sitio a sitio segura. Vea <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Configurar una puerta de enlace de red virtual en el Portal de administración</a> para obtener instrucciones.</p>
</li>
<li><p>Cree la conexión VPN de sitio a sitio entre la nueva red virtual y un dispositivo VPN local. Vea <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Configurar una puerta de enlace de red virtual en el Portal de administración</a> para obtener instrucciones.</p>
</li>
</ol>

## Paso 3: Crear máquinas virtuales de Azure para los roles de controlador de dominio

<p>Repita los pasos siguientes para crear máquinas virtuales para hospedar el rol de controlador de dominio según sea necesario. Debe implementar al menos dos controladores de dominio virtuales para proporcionar redundancia y tolerancia a errores. Si la red virtual de Azure incluye al menos dos controladores de dominio que están configurados de manera similar (es decir, son ambos catálogos globales, servidor DNS de ejecución y no contienen ningún rol FSMO, etc.), a continuación, coloque las máquinas virtuales que ejecutan los controladores de dominio en un conjunto de disponibilidad para aumentar la tolerancia a errores.</p>


<ol><li><p>En el portal de administración de Azure, haga clic en <b>Nuevo</b> > <b>Calcular</b> > <b>Máquina virtual</b> > <b>De la galería</b>. Utilice los valores siguientes para completar el asistente. Acepte el valor predeterminado para una configuración a menos que se sugiera o requiera otro valor.</p>
<table style="width:100%">
<tr>
<td><b>En esta página del asistente</b></td>
<td><b>Especifique estos valores</b></td>
</tr>
<tr>
<td><b>Elegir una imagen</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Configuración de la máquina virtual</b></td>
<td><ul><li>Nombre de la máquina virtual: Escriba un nombre de etiqueta única (por ejemplo, AzureDC2).</li><li>Nuevo nombre de usuario: Escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta integrada Administrador no funcionará.</li><li>Nueva contraseña/Confirmar: Escriba una contraseña</li></ul></td>
</tr>
<tr>
<td><b>Configuración de la máquina virtual</b></td>
<td><ul><li>Servicio en la nube: Elija <b>Crear un nuevo servicio de nube</b> para la primera máquina virtual y seleccione ese mismo nombre de servicio de nube al crear más máquinas virtuales que hospedan el rol de controlador de dominio.</li><li>Nombre de DNS del servicio en la nube: Especifique un nombre único global</li><li>Región/grupo de afinidad/red virtual: Especifique el nombre de red virtual (por ejemplo, WestUSVNet).</li><li>Cuenta de almacenamiento: Elija <b>Use una cuenta de almacenamiento generada de forma automática</b> para la primera máquina virtual y, a continuación, seleccione ese mismo nombre de cuenta de almacenamiento al crear más máquinas virtuales que vayan a hospedar el rol de controlador de dominio.</li><li>Conjunto de disponibilidad: Elija <b>Crear un conjunto de disponibilidad</b>.</li><li>Nombre del conjunto de disponibilidad: Escriba un nombre para el conjunto de disponibilidad al crear la primera máquina virtual y, a continuación, seleccione ese mismo nombre al crear más máquinas virtuales.</li></ul></td>
</tr>
<tr>
<td><b>Configuración de la máquina virtual</b></td>
<td>Seleccione <b>Instale el Agente de máquina virtual</b> y cualquier otra extensión que necesite.</td>
</tr>
</table>
</li>
<li><p>Conecte un disco a cada máquina virtual que ejecutará el rol de servidor de controlador de dominio. Se necesita el disco adicional para almacenar la base de datos, los registros y SYSVOL de AD. Especifique un tamaño para el disco (por ejemplo, 10 GB) y deje la <b>Preferencia de caché de host</b> establecida en <b>Ninguno</b>. Una vez que inicie sesión por primera vez en la máquina virtual, abra <b>Administrador del servidor</b> > <b>Servicios de archivos y almacenamiento</b> para crear un volumen en este disco con NTFS.</p></li>
<li><p>Reserve una dirección IP estática para las máquinas virtuales que ejecutarán el rol de controlador de dominio. Para reservar una dirección IP estática, descargue el instalador de plataforma web de Microsoft, <a href = "http://azure.microsoft.com/documentation/articles/install-configure-powershell/">instale Azure PowerShell</a> y ejecute el cmdlet <a href = "http://msdn.microsoft.com/library/azure/dn630228.aspx">Set-AzureStaticVNetIP</a> .</p></li>

</ol>

## Paso 4: Instalar AD DS en máquinas virtuales de Azure

Inicie sesión en una máquina virtual y compruebe que tiene conectividad a través de la conexión de ExpressRoute o de VPN de sitio a sitio a los recursos de la red local. Después, instale AD DS en las máquinas virtuales de Azure. Puede usar el mismo proceso que se utiliza para instalar un controlador de dominio adicional en la red local (interfaz de usuario, Windows PowerShell o un archivo de respuesta). Cuando instale AD DS, asegúrese de que especifica el nuevo volumen para la ubicación de la base de datos, los registros y SYSVOL de AD. Si necesita actualizar los conocimientos sobre la instalación de AD DS, consulte [Instalar servicios de dominio de Active Directory (nivel 100)](http://technet.microsoft.com/library/hh472162.aspx) o [Instalar un controlador de dominio de réplica de Windows Server 2012 en un dominio existente (nivel 200)](http://technet.microsoft.com/library/jj574134.aspx).

## Paso 5: Volver a configurar el servidor DNS para la red virtual

<ol>
<li><p>En el portal de administración de Azure, haga clic en el nombre de la red virtual y, a continuación, haga clic en la pestaña <b>Configurar</b> para <a href = "http://msdn.microsoft.com/library/azure/dn275925.aspx">volver a configurar las direcciones IP del servidor DNS de la red virtual</a> para usar las direcciones IP estáticas asignadas a los controladores de dominio de réplica en lugar de las direcciones IP de los servidores DNS locales. </p>
</li>
<li><p>Para asegurarse de que todas las máquinas virtuales del controlador de dominio de réplica de la red virtual están configuradas para usar servidores DNS en la red virtual, haga clic en <b>Máquinas virtuales</b>, en la columna de estado para cada máquina virtual y, a continuación, en <b>Reiniciar</b>. Espere hasta que la máquina virtual muestre el estado <b>En ejecución</b> antes de intentar iniciar sesión en ella. 
</p>
</li>
</ol>

## Paso 6: Crear máquinas virtuales para servidores de aplicaciones

<ol><li><p>Repita los pasos siguientes para crear VM que se ejecuten como servidores de aplicaciones. Acepte el valor predeterminado para una configuración a menos que se sugiera o requiera otro valor.</p>

<table style="width:100%">
<tr>
<td><b>En esta página del asistente</b></td>
<td><b>Especifique estos valores</b></td>
</tr>
<tr>
<td><b>Elegir una imagen</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Configuración de la máquina virtual</b></td>
<td><ul><li>Nombre de la máquina virtual: Escriba un nombre de etiqueta única (por ejemplo, TreyAppServer1).</li><li>Nuevo nombre de usuario: Escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta integrada Administrador no funcionará.</li><li>Nueva contraseña/Confirmar:  Escriba una contraseña</li></ul></td>
</tr>
<tr>
<td><b>Configuración de la máquina virtual</b></td>
<td><ul><li>Servicio en la nube: Elija Crear un nuevo servicio de nube para la primera máquina virtual y seleccione ese mismo nombre de servicio de nube al crear más máquinas virtuales que hospedan la aplicación.</li><li>Nombre de DNS del servicio en la nube: Especifique un nombre único global</li><li>Región/grupo de afinidad/red virtual: Especifique el nombre de red virtual (por ejemplo, WestUSVNet).</li><li>Cuenta de almacenamiento: Elija <b>Use una cuenta de almacenamiento generada de forma automática</b> para la primera máquina virtual y, a continuación, seleccione ese mismo nombre de cuenta de almacenamiento al crear más máquinas virtuales que vayan a hospedar el rol de controlador de dominio.</li><li>Conjunto de disponibilidad: Elija <b>Crear un conjunto de disponibilidad</b>.</li><li>Nombre del conjunto de disponibilidad: Escriba un nombre para el conjunto de disponibilidad al crear la primera máquina virtual y, a continuación, seleccione ese mismo nombre al crear más máquinas virtuales.</li></ul></td>
</tr>
<tr>
<td><b>Configuración de la máquina virtual</b></td>
<td>Seleccione <b>Instale el Agente de máquina virtual</b> y cualquier otra extensión que necesite.</td>
</tr>
</table>


</li>
<li><p>Una vez aprovisionada cada máquina virtual, inicie sesión y únalas al dominio. En <b>Administrador de servidores</b>, haga clic en <b>Servidor local</b> > <b>GRUPO DE TRABAJO</b> > <b>Cambiar...</b> y, a continuación, seleccione <b>Dominio</b> y escriba el nombre del dominio local. Proporcione las credenciales de un usuario de dominio y, a continuación, reinicie la máquina virtual para completar la unión al dominio.
</p>
</li>
</ol>
<p>
Como alternativa al uso del portal de administración para aprovisionar máquinas virtuales, puede usar Windows PowerShell para Microsoft Azure. Use objetos <a href = "http://msdn.microsoft.com/library/azure/dn495159.aspx">New-AzureVMConfig</a> y <a href = "http://msdn.microsoft.com/library/azure/dn495299.aspx">Add-AzureProvisioningConfig</a> para aprovisionar una máquina virtual como un equipo unido al dominio cuando se inicia por primera vez y utilice <a href = "http://msdn.microsoft.com/library/azure/dn495254.aspx">New-AzureVM</a> para crear la propia máquina virtual. 
</p>

Para obtener más información acerca del uso de Windows PowerShell, consulte [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx) y [Azure Management Cmdlets](http://msdn.microsoft.com/library/windowsazure/jj152841).


## Recursos adicionales

-  [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Windows Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Cómo cargar los controladores de dominio de Hyper-V locales existentes a Azure con Azure PowerShell](http://support.microsoft.com/kb/2904015)

-  [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)

-  [Red virtual de Azure](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [IaaS para profesionales de TI en Windows Azure: (01) Principios básicos sobre máquinas virtuales](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [IaaS para profesionales de TI en Windows Azure: (05) Creación de redes virtuales y conectividad entre instalaciones](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [PowerShell de Azure](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Azure Management Cmdlets](http://msdn.microsoft.com/library/windowsazure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!--HONumber=47-->
