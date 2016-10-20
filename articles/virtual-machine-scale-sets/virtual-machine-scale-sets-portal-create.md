<properties
	pageTitle="Creación de conjuntos de escalado con la CLI en el portal | Microsoft Azure"
	description="Implementación de conjuntos de escalado mediante Azure Portal."
	keywords="conjuntos de escalado de máquinas virtuales" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="gatneil"/>

# Creación de un conjunto de escalado con Azure Portal

En este tutorial se muestra lo fácil que resulta crear un conjunto de escalado de máquinas virtuales en unos minutos con Azure Portal. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## Selección de la imagen de la máquina virtual de Marketplace

En el portal, puede implementar fácilmente una escala establecida con CentOS, CoreOS, Debian, Open Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server o imágenes de Ubuntu Server.

En el explorador, vaya a [Azure Portal](https://portal.azure.com). Haga clic en `New`, busque `scale set` y seleccione la entrada `Virtual machine scale set`:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## Creación de la máquina virtual de Windows

Ahora puede usar los valores predeterminados para crear rápidamente la máquina virtual.

* En la hoja `Basics`, escriba un nombre para el conjunto de escalado. Este nombre se convierte en la base del FQDN del equilibrador de carga delante del conjunto de escalado, así que asegúrese de que el nombre es único en todo Azure.

* Seleccione su tipo preferido de sistema operativo, escriba el nombre de usuario que quiera y seleccione el tipo de autenticación que desee. Si elige una contraseña, debe tener 12 caracteres como mínimo y reunir, al menos, tres de los cuatro requisitos de complejidad siguientes: una minúscula, una mayúscula, un número y un carácter especial. Obtenga más información acerca de los [requisitos de usuario y la contraseña](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Si elige `SSH public key`, asegúrese de que solo pega la clave pública, no la privada:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Escriba el nombre del grupo de recursos y la ubicación que desee y haga clic en `OK`.

* En la hoja `Virtual machine scale set service settings`: escriba la etiqueta de nombre de dominio que quiera (la base del FQDN del equilibrador de carga delante del conjunto de escalado). Esta etiqueta debe ser única en todo Azure.

* Elija la imagen de disco del sistema operativo que desee, el recuento de instancias y el tamaño de máquina.

* Habilite o deshabilite el escalado automático y, si lo habilita, configúrelo:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Una vez realizada la validación, haga clic en `OK` en la hoja `Summary`.

* Por último, en la hoja `Purchase`, haga clic en `Purchase` para iniciar la implementación del conjunto de escalado.

## Conexión a una máquina virtual en el conjunto de escalado

Una vez implementado el conjunto de escalado, vaya a la pestaña `Inbound NAT Rules` del equilibrador de carga para el conjunto de escalado:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Puede conectarse a las máquinas virtuales del conjunto de escalado con estas reglas NAT. Por ejemplo, para un conjunto de escalado de Windows, si hay una regla NAT en el puerto entrante 50000, se pudo conectar a esa máquina a través de RDP en `<load-balancer-ip-address>:50000`. Para un conjunto de escalado de Linux, tendría que conectarse con el comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## Pasos siguientes

Para información sobre cómo implementar conjuntos de escalado desde la CLI, consulte [esta documentación](./virtual-machine-scale-sets-cli-quick-create.md).

Para información sobre cómo implementar conjuntos de escalado desde Powershell, consulte [esta documentación](./virtual-machine-scale-sets-windows-create.md).

Para información sobre cómo implementar conjuntos de escalado desde Visual Studio, consulte [esta documentación](./virtual-machine-scale-sets-vs-create.md).

Para la documentación general, consulte la [página de documentación principal de los conjuntos de escalado](./virtual-machine-scale-sets-overview.md).

Para información general, consulte la [página de destino principal de los conjuntos de escalado](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0928_2016-->