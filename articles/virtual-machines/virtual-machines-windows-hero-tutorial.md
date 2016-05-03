<properties
	pageTitle="Creación de una máquina virtual de Windows en el Portal de Azure | Microsoft Azure"
	description="Aprenda cómo crear una máquina virtual de Windows con el Portal de Azure."
	keywords="máquina virtual de Windows, crear una máquina virtual, equipo virtual, configurar una máquina virtual"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2016"
	ms.author="cynthn"/>

# Creación de una máquina virtual de Windows en el Portal de Azure.

En este tutorial se muestra lo fácil que resulta crear una máquina virtual de Windows en unos minutos con el Portal de Azure. Vamos a usar una imagen de Windows Server 2012 R2 Datacenter como ejemplo, pero es solo una de las muchas imágenes que Azure ofrece. Las imágenes disponibles dependen de su suscripción. Por ejemplo, las imágenes de escritorio pueden estar disponibles para los [suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

Si carece de una suscripción de Azure, puede crear una [cuenta gratis](https://azure.microsoft.com/free/) en tan solo unos minutos.

## Tutorial en vídeo

Se trata del [tutorial en formato de vídeo](https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Create-A-Virtual-Machine-Running-Windows-In-The-Azure-Preview-Portal).


## Seleccione la imagen de máquina virtual de Windows 2012 R2 desde Marketplace.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú Base de datos central, haga clic en **Nuevo** > **Proceso** > **Windows Server 2012 R2 Datacenter**.

	![Captura de pantalla que muestra las imágenes de las máquinas virtuales de Azure disponibles en el portal](./media/virtual-machines-windows-hero-tutorial/marketplace_new.png)


3. En la página **Windows Server 2012 R2 Datacenter**, en **Seleccionar un modelo de implementación**, seleccione **Administrador de recursos**. Haga clic en **Crear**.

	![Captura de pantalla que muestra el modelo de implementación seleccionado para una máquina virtual de Azure](./media/virtual-machines-windows-hero-tutorial/marketplace_search_select.png)

## Creación de la máquina virtual de Windows

Después de seleccionar la imagen, puede usar los valores predeterminados de Azure en la mayor parte de la configuración y crear rápidamente la máquina virtual.

1. En la hoja **Crear máquina virtual**, haga clic en **Conceptos básicos**.

2. Escriba el **nombre** que desee para la máquina virtual. El nombre no puede contener caracteres especiales.

3. Escriba un **nombre de usuario** administrador y una **contraseña** segura. La contraseña debe tener entre 8 y 123 caracteres y al menos tres de los siguientes valores: una minúscula, una mayúscula, un número y un carácter especial. **Necesitará el nombre de usuario y la contraseña para conectarse a la máquina virtual**.


4. Si tiene más de una suscripción, especifique la de la nueva máquina virtual. Seleccione un [grupo de recursos](../resource-group-overview/#resource-groups) nuevo o existente y una **ubicación** del centro de datos de Azure, como **oeste de EE. UU**.

	![Captura de pantalla que muestra la configuración básica para una máquina virtual de Azure](./media/virtual-machines-windows-hero-tutorial/create_vm_basics.PNG)

	
2. Haga clic en **Tamaño** y seleccione un tamaño de máquina virtual apropiado para sus necesidades. Cada tamaño especifica el número de núcleos de proceso, la memoria y otras características, como la compatibilidad con el Almacenamiento premium, lo que afecta al precio. Azure recomienda automáticamente determinados tamaños según la imagen que se elija.

	![Captura de pantalla que muestra los tamaños de máquinas virtuales de Azure que puede seleccionar](./media/virtual-machines-windows-hero-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE] El almacenamiento Premium está disponible para las máquinas virtuales de la serie DS en determinadas regiones. El almacenamiento Premium es la mejor opción de almacenamiento para cargas de trabajo intensivas de datos como una base de datos. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md)

3. Haga clic en **Configuración** para ver la configuración de red y de almacenamiento de la nueva máquina virtual. En la primera máquina virtual, por lo general podrá aceptar la configuración predeterminada. Si ha seleccionado un tamaño de máquina virtual que lo admita, puede probar Almacenamiento premium, para lo que debe seleccionar **Premium (SSD)** en **Tipo de disco**.

	![Captura de pantalla que muestra las características opciones que puede configurar en una máquina virtual de Azure](./media/virtual-machines-windows-hero-tutorial/create_vm_settings.PNG)

6. Haga clic en **Resumen** para revisar las opciones de configuración. Cuando haya terminado de revisar o actualizar la configuración, haga clic en **Crear**.

	![Captura de pantalla que muestra el resumen de las opciones de configuración realizadas para la máquina virtual de Azure](./media/virtual-machines-windows-hero-tutorial/create_vm_summary.PNG)

8. Mientras Azure crea la máquina virtual, puede realizar un seguimiento del progreso en **Máquinas virtuales**, en el menú central.


## Conexión a la máquina virtual e inicio de sesión

1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2.	En el menú central, haga clic en **Máquinas virtuales**.

3.	Seleccione la máquina virtual en la lista.

4. En la hoja de la máquina virtual, haga clic en **Conectar**.

	![Captura de pantalla del Portal de Azure que muestra cómo conectarse a la máquina virtual.](./media/virtual-machines-windows-connect-logon/preview-portal-connect.png)


[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

Si surgen problemas al intentar conectarse, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

## Pasos siguientes

* También puede [crear una máquina virtual Windows con Powershell](virtual-machines-windows-ps-create.md) o [crear una máquina virtual Linux](virtual-machines-linux-quick-create-cli.md) mediante la CLI de Azure.

<!---HONumber=AcomDC_0427_2016-->