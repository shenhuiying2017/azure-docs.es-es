---
title: "Active Directory Domain Services: guía de administración | Microsoft Docs"
description: "Una la máquina virtual de Windows a un dominio administrado con Azure PowerShell y el modelo de implementación clásica."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9143b843-7327-43c3-baab-6e24a18db25e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 1bb1546fb616131a1e1868a0d0610c4cad5d73e2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Unión de una máquina virtual con Windows Server a un dominio administrado mediante PowerShell
> [!div class="op_single_selector"]
> * [Portal de Azure clásico - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). Este artículo trata del modelo de implementación clásico. Servicios de dominio de Azure AD no admite actualmente el modelo de Resource Manager.
>
>

En estos pasos se muestra cómo personalizar un conjunto de comandos de Azure PowerShell que creen y preconfiguren una máquina virtual de Azure basada en Windows mediante el uso de un enfoque de bloque de creación. Estos pasos ayudan a crear una máquina virtual de Azure basada en Windows y unirla a un dominio administrado de Servicios de dominio de Azure AD.

En estos pasos se sigue un enfoque consistente en atar cabos para crear conjuntos de comandos de Azure PowerShell. Este enfoque puede ser útil si es la primera vez que se usa PowerShell o se desea saber qué valores se deben especificar para una configuración correcta. Los usuarios avanzados de PowerShell pueden tomar los comandos y sustituir sus propios valores de las variables (las líneas que comienzan con "$").

Si aún no lo ha hecho, siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/overview) para instalar Azure PowerShell en un equipo local. Después, abra el símbolo del sistema de Windows PowerShell.

## <a name="step-1-add-your-account"></a>Paso 1: agregar la cuenta
1. En el símbolo del sistema de Powershell, escriba **Add-AzureAccount** y haga clic en **Entrar**.
2. Escriba la dirección de correo electrónico asociada a su suscripción de Azure y haga clic en **Continuar**.
3. Escriba la contraseña de su cuenta.
4. Haga clic en **Iniciar sesión**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Paso 2: establecimiento de la cuenta de suscripción y almacenamiento
Para establecer su cuenta de suscripción y almacenamiento de Azure, ejecute estos comandos en el símbolo del sistema de Windows PowerShell. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >, por los nombres correctos.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

El nombre de suscripción correcto se puede obtener de la propiedad SubscriptionName de la salida del comando **Get-AzureSubscription** . Puede obtener el nombre de cuenta de almacenamiento correcto de la propiedad Label de la salida del comando **Get-AzureStorageAccount**, una vez haya ejecutado el comando **Select-AzureSubscription**.

## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Paso 3: Tutorial paso a paso - Aprovisionamiento de la máquina virtual y unión al dominio administrado
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

Obtenga las credenciales de administrador local para la máquina virtual. Elija una contraseña segura de administrador local.

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

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script para aprovisionar una máquina virtual de Windows y unirla automáticamente a un dominio administrado de Servicios de dominio de AAD
Este conjunto de comandos de PowerShell crea una máquina virtual para un servidor de línea de negocio que:

* Utilice la imagen de Windows Server 2012 R2 Datacenter.
* Es una máquina virtual extra pequeña.
* Tiene el nombre Contoso100-test.
* Se une automáticamente al dominio administrado contoso100.
* Se agrega a la misma red virtual que el dominio administrado.

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

<br>

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
