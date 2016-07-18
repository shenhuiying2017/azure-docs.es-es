<properties
	pageTitle="Versión preliminar de Servicios de dominio de Azure Active Directory: guía de administración | Microsoft Azure"
	description="Una la máquina virtual de Windows a un dominio administrado con Azure PowerShell y el modelo de implementación clásica."
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>


# Creación de máquinas virtuales Windows con PowerShell y el modelo de implementación clásico

> [AZURE.SELECTOR]
- [Portal de Azure clásico - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md). Este artículo trata del modelo de implementación clásico. Servicios de dominio de Azure AD no admite actualmente el modelo de Resource Manager.

En estos pasos se muestra cómo personalizar un conjunto de comandos de Azure PowerShell que creen y preconfiguren una máquina virtual de Azure basada en Windows mediante el uso de un enfoque de bloque de creación. Estos pasos ayudan a crear una máquina virtual de Azure basada en Windows y unirla a un dominio administrado de Servicios de dominio de Azure AD.

En estos pasos se sigue un enfoque consistente en atar cabos para crear conjuntos de comandos de Azure PowerShell. Este enfoque puede ser útil si está familiarizado con PowerShell o desea conocer los valores que debe especificar para una configuración correcta. Los usuarios avanzados de PowerShell pueden tomar los comandos y sustituir sus propios valores de las variables (las líneas que comienzan con "$").

Si aún no lo ha hecho, siga las instrucciones de [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para instalar Azure PowerShell en un equipo local. Después, abra el símbolo del sistema de Windows PowerShell.

## Paso 1: agregar la cuenta

1. En el símbolo del sistema de Powershell, escriba **Add-AzureAccount** y haga clic en **Entrar**.
2. Escriba la dirección de correo electrónico asociada a su suscripción de Azure y haga clic en **Continuar**.
3. Escriba la contraseña de su cuenta.
4. Haga clic en **Iniciar sesión**.

## Paso 2: Establecimiento de la cuenta de suscripción y almacenamiento

Establezca su cuenta de suscripción y almacenamiento de Azure mediante la ejecución de estos comandos en el símbolo del sistema de Windows PowerShell. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >, por los nombres correctos.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Puede obtener el nombre de suscripción correcto con la propiedad SubscriptionName de la salida del comando **Get-AzureSubscription**. Puede obtener el nombre de cuenta de almacenamiento correcto de la propiedad “Label” de la salida del comando **Get-AzureStorageAccount**, una vez haya ejecutado el comando **Select-AzureSubscription**.


## Paso 3: Tutorial paso a paso - Aprovisionamiento de la máquina virtual y unión al dominio administrado
Este es el comando de Azure PowerShell correspondiente para crear esta máquina virtual, con líneas en blanco entre cada bloque para mejorar la legibilidad.

Especifique la información sobre la máquina virtual de Windows que desea aprovisionar.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Para los valores de InstanceSize de las máquinas virtuales de las series D, DS o G, consulte [Máquina virtual y tamaños de servicios en la nube de Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Proporcione información sobre el dominio administrado.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Especifique el nombre del servicio en la nube.

    $svcname="Contoso100-test"

Especifique el nombre de la red virtual a la que se debe unir la máquina virtual. Asegúrese de que el dominio administrado de Servicios de dominio de Azure AD está disponible en esta red virtual.

    $vnetname="MyPreviewVnet"

Seleccione la imagen de máquina virtual que se usará para aprovisionar la máquina virtual.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configure la máquina virtual: nombre del conjunto de máquina virtual, tamaño de instancia e imagen que se usará.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Obtenga las credenciales de administrador local para la máquina virtual. Elija una contraseña segura de administrador local. Para comprobar su robustez, consulte [Comprobador de contraseñas: uso de contraseñas seguras](https://www.microsoft.com/security/pc-security/password-checker.aspx).

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Obtenga las credenciales para una cuenta de usuario que pertenezca al grupo de administradores de controlador de dominio de AAD para unir la máquina virtual al dominio administrado. No especifique el nombre de dominio; fíjese en nuestro ejemplo, donde "bob" es el nombre de usuario.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configure la máquina virtual: especifique el requisito de la unión a dominio y las credenciales necesarias.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Establezca una subred para la máquina virtual.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Opcional: señale a la dirección IP del dominio. Si establece las direcciones IP del dominio administrado de Servicios de dominio de Azure AD para que sean los servidores DNS de la red virtual, este paso no es necesario.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Ahora, aprovisione la máquina virtual de Windows unida a un dominio.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## Script para aprovisionar una máquina virtual de Windows y unirla automáticamente a un dominio administrado de Servicios de dominio de AAD
Este conjunto de comandos de PowerShell crea una máquina virtual para un servidor de línea de negocio que:

- Utilice la imagen de Windows Server 2012 R2 Datacenter.
- Es una máquina virtual extra pequeña.
- Tiene como nombre contoso-test.
- Se une automáticamente al dominio administrado contoso100.
- Se agrega a la misma red virtual que el dominio administrado.

Ese es el script de ejemplo completo para crear la máquina virtual de Windows y unirla automáticamente al dominio administrado de Servicios de dominio de Azure AD.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<!---HONumber=AcomDC_0706_2016-->