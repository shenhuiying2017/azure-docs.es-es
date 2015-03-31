<properties 
	pageTitle="Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Linux" 
	description="Aprenda a usar Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Linux en Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="josephd"/>

#Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Linux

En estos pasos se muestra cómo personalizar un conjunto de comandos de Azure PowerShell que creen y preconfiguren una máquina virtual de Azure basada en Linux mediante el uso de un enfoque de bloque de creación. Puede utilizar este proceso para crear un conjunto de comandos para una nueva máquina virtual basada en Linux rápidamente y expandir una implementación existente, o para crear varios conjuntos de comandos que creen rápidamente un entorno de profesionales de TI, o de desarrollo o pruebas.

En estos pasos se sigue un enfoque consistente en atar cabos para crear conjuntos de comandos de Azure PowerShell. Este enfoque puede ser útil si está familiarizado con PowerShell o desea conocer los valores que debe especificar para una configuración correcta. Los usuarios avanzados de PowerShell pueden tomar los comandos y sustituir sus propios valores de las variables (las líneas que comienzan con "$").

Para consultar el tema suplementario sobre máquinas virtuales basadas en Windows, vea [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines-ps-create-preconfigure-windows-vms/).

##Paso 1: Azure PowerShell

Si aún no lo ha hecho, siga las instrucciones de [Instalación y configuración de Azure PowerShell](../install-configure-powershell/) para instalar Azure PowerShell en un equipo local. A continuación, abra un símbolo del sistema de Azure PowerShell.

##Paso 2: Establecimiento de la cuenta de suscripción y almacenamiento

Establezca su cuenta de almacenamiento y suscripción de Azure mediante la ejecución de estos comandos en el símbolo del sistema de Azure PowerShell. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >, por los nombres correctos.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr -Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Puede obtener el nombre de suscripción correcta con la propiedad SubscriptionName de la salida del comando **Get-AzureSubscription**. Puede obtener el nombre correcto de la cuenta de almacenamiento desde la propiedad Label de la salida del comando **Get-AzureStorageAccount** cuando se emite el comando **Select-AzureSubscription**. También puede almacenar estos comandos en un archivo de texto para un uso futuro.

##Paso 3: Determinación de ImageFamily

A continuación, deberá determinar el valor de ImageFamily para la imagen concreta que corresponda a la máquina virtual de Azure que desea crear. Puede obtener la lista de los valores de ImageFamily disponibles con este comando.

	Get-AzureVMImage | select ImageFamily -Unique

A continuación, se muestran algunos ejemplos de valores de ImageFamily de equipos basados en Linux:

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Abra una nueva instancia del editor de texto que prefiera y copie lo siguiente en el nuevo archivo de texto, sustituyendo el valor de ImageFamily.
 
	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

##Paso 4: Creación del conjunto de comandos

Cree el resto del conjunto de comandos copiando el conjunto de bloques adecuado en el nuevo archivo de texto y, a continuación, rellene los valores de las variables y quite los caracteres < y >. Consulte los dos [ejemplos](#examples) del final de este artículo para obtener una idea del resultado final.

Inicie el conjunto de comandos eligiendo uno de estos dos bloques de comandos (obligatorio).

Opción 1: Especifique un nombre de máquina virtual y un tamaño.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Opción 2: Especifique un nombre, un tamaño y un nombre del conjunto de disponibilidad.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Para los valores de InstanceSize de las máquinas virtuales de las series D, DS o G, consulte [Máquina virtual y tamaños de servicios en la nube de Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Especifique el nombre de usuario y la contraseña iniciales de Linux (obligatorio). Elija una contraseña segura. Para comprobar su robustez, consulte [Comprobador de contraseñas: uso de contraseñas seguras](https://www.microsoft.com/security/pc-security/password-checker.aspx).

	$username="<user account name>"
	$pass="<user account password>"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

Si va a guardar el conjunto de comandos resultante en un archivo, almacénelo en una ubicación segura para proteger el nombre de cuenta y la contraseña.

Opcionalmente, puede especificar un conjunto de pares de claves SSH que ya estén implementados en la suscripción.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Para obtener más información, consulte [Uso de SSH con Linux en Azure](../virtual-machines-linux-use-ssh-key/).

Opcionalmente, especifique una lista de claves públicas SSH que ya estén implementadas en la suscripción.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Para ver opciones adicionales de configuración previa de máquinas virtuales basadas en Linux, consulte la sintaxis del conjunto de parámetros **Linux** establecido en [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Si lo desea, asígnele una dirección IP concreta, conocida como una DIP estática.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Puede comprobar que una dirección IP concreta está disponible con:

	Test-AzureStaticVNetIP -VNetName <VNet name> -IPAddress <IP address>

De forma opcional, asigne la máquina virtual a una subred concreta en una red virtual de Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Opcionalmente, agregue un único disco de datos a la máquina virtual. 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Si lo desea, puede agregar la máquina virtual a un conjunto de equilibrio de carga existente para el tráfico externo.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Por último, inicie el proceso de creación de la máquina virtual mediante la elección de uno de estos bloques de comandos (obligatorio).

Opción 1: Cree la máquina virtual en un nuevo servicio en la nube. 

	New-AzureVM -Location "<An Azure location, such as US West>" -VMs $vm1

Puede obtener la lista de ubicaciones de Azure con:

	Get-AzureLocation | Select DisplayName

Opción 2: Cree la máquina virtual en un servicio en la nube existente. 

	New-AzureVM -ServiceName "<short name of the cloud service>" -VMs $vm1

El nombre corto del servicio en la nube es el nombre que aparece en la lista de servicios en la nube en el Portal de administración de Azure o en la lista de grupos de recursos en el Portal de vista previa de Azure. 

Opción 3: Cree la máquina virtual en un servicio en la nube y la red virtual.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

##Paso 5: Ejecución del conjunto de comandos

Revise el conjunto de comandos de Azure PowerShell creado en el editor de texto, que consta de varios bloques de comandos del paso 4. Asegúrese de que ha especificado todas las variables necesarias y de que tengan los valores correctos. También asegúrese de que ha quitado todos los caracteres < y >.

Copie el conjunto de comandos en el Portapapeles y haga clic en el símbolo del sistema de Azure PowerShell abierto. Así, se emitirá el conjunto de comandos como una serie de comandos de PowerShell y se creará la máquina virtual de Azure. Si crea la máquina virtual en una suscripción, una cuenta de almacenamiento, un servicio en la nube, un conjunto de disponibilidad, una red virtual o una subred incorrectos, elimine la máquina virtual, corrija la sintaxis del bloque de comandos y, a continuación, ejecute el conjunto de comandos corregido. 

Después de crear la máquina virtual, consulte [Inicio de sesión en una máquina virtual con Linux](../virtual-machines-linux-how-to-log-on/). 

Si va a crear esta máquina virtual de nuevo o una similar, puede: 

- Guardar este conjunto de comandos como un archivo de texto o como un archivo de script de PowerShell (*.ps1)
- Guardar este conjunto de comandos como un Runbook de automatización de Azure en la sección **Automatización** del Portal de administración de Azure 

##<a id="examples"></a>Ejemplos

A continuación se muestran dos ejemplos de uso de los pasos anteriores para crear conjuntos de comandos de Azure PowerShell que creen máquinas virtuales basadas en Linux en Azure.

###Ejemplo 1

Se necesita un conjunto de comandos de PowerShell que cree la máquina virtual de Linux inicial de un servidor de MySQL que:

- Use la imagen de Ubuntu Server 12.10
- Tenga el nombre AZMYSQL1 
- Tenga un disco de datos adicional de 500 GB
- Tenga la dirección IP estática 192.168.244.4
- Se encuentre en la subred BackEnd de la red virtual AZDatacenter
- Se encuentre en el servicio en la nube Azure-TailspinToys

Este es el comando de Azure PowerShell correspondiente para crear esta máquina virtual, con líneas en blanco entre cada bloque para mejorar la legibilidad.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin397A"
	$pass="3A#q291{Y"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

###Ejemplo 2

Se necesita un conjunto de comandos de PowerShell que cree una máquina virtual de Linux para un servidor Apache que:

- Use la imagen de SUSE Linux Enterprise Server 12
- Tenga el nombre LOB1
- Tenga un disco de datos adicional de 50 GB 
- Sea un miembro del conjunto de equilibrador de carga de LOBServers para el tráfico web estándar
- Se encuentre en la subred FrontEnd de la red virtual AZDatacenter
- Se encuentre en el servicio en la nube Azure-TailspinToys

Este es el comando de Azure PowerShell correspondiente para crear esta máquina virtual.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin261Z"
	$pass="9Z2:3Wqp1~"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

##Recursos adicionales

[Documentación de las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Preguntas más frecuentes sobre las máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Información general sobre las máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell/)

[Inicio de sesión en una máquina virtual con Linux](../virtual-machines-linux-how-to-log-on/)

[Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines-ps-create-preconfigure-windows-vms/)


<!--HONumber=47-->
