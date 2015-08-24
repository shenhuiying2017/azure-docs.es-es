<properties
	pageTitle="Creación de una máquina virtual con Windows en el Portal de vista previa de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo crear recursos de máquinas virtuales de Azure que usen Windows con Azure Marketplace en el Portal de vista previa de Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/13/2015"
	ms.author="kathydav"/>

# Creación de una máquina virtual que ejecuta Windows en el Portal de vista previa de Azure#

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource manager](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service management](virtual-machines-ps-create-preconfigure-windows-vms.md)

Este tutorial muestra lo fácil que resulta crear una máquina virtual de Azure en unos minutos en el Portal de vista previa. Usaremos una imagen de Windows Server 2012 R2 Datacenter como ejemplo para crear la máquina virtual en el Administrador de recursos de Azure, pero esa es solo una de las muchas imágenes que Azure ofrece. Tenga en cuenta que las opciones de imagen dependen de su suscripción. Por ejemplo, las imágenes de escritorio pueden estar disponibles para los suscriptores MSDN.

También puede crear máquinas virtuales con sus propias imágenes, con plantillas de Administrador de recursos o con herramientas de automatización. Para obtener información sobre los diferentes métodos, consulte [Diferentes formas de crear una máquina virtual de Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Selección de la imagen

Vaya a Azure Marketplace en el Portal de vista previa para buscar la imagen de la máquina virtual de Windows Server que desea.

1. Conéctese al [Portal de vista previa](https://portal.azure.com).

2. En el menú Concentrador, haga clic en **Nuevo** > **Proceso** > **Windows Server 2012 R2 Datacenter**.

	![Marketplace](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]Para buscar imágenes adicionales, haga clic en **Marketplace** y, a continuación, búsquelas o filtre las que haya disponibles.

3. En la página **Windows Server 2012 R2 Datacenter**, seleccione **Usar la pila del Administrador de recursos** para crear la máquina virtual en el Administrador de recursos de Azure. (Para la mayoría de las cargas de trabajo nuevas, se recomienda la pila del Administrador de recursos. Para obtener más información, consulte [Proveedores de procesos, redes y almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)). A continuación, haga clic en **Crear**.

	![Buscar en Marketplace](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## Creación de la máquina virtual

Después de seleccionar la imagen, puede usar los valores predeterminados de Azure en la mayor parte de la configuración y crear rápidamente la máquina virtual.

1. En la hoja **Crear máquina virtual**, haga clic en **Datos básicos**. Escriba un **Nombre** que desee para la máquina virtual, el **Nombre de usuario** administrativo y una **contraseña** segura. Si tiene más de una suscripción, especifique una de ellas para la nueva máquina virtual, así como un nuevo **grupo de recursos**, o uno existente, y la **ubicación** de un centro de datos de Azure.

	![Configurar los aspectos básicos de la máquina virtual](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE]El campo **Nombre de usuario** hace referencia a la cuenta administrativa que usa para administrar el servidor. Cree una contraseña que sea difícil de adivinar, pero que pueda recordar. **Necesitará el nombre de usuario y la contraseña para iniciar sesión en la máquina virtual**.

2. Haga clic en **Tamaño** y seleccione un tamaño de VM apropiado para sus necesidades. Cada tamaño especifica el número de núcleos de proceso, la memoria y otras características, como la compatibilidad con el Almacenamiento premium, lo que afectará al precio. Azure recomienda automáticamente determinados tamaños según la imagen que se elija.

	![Configurar el tamaño de la máquina virtual](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]El almacenamiento Premium está disponible para las máquinas virtuales de la serie DS en determinadas regiones. El almacenamiento Premium es la mejor opción de almacenamiento para cargas de trabajo intensivas de datos como una base de datos. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage-preview-portal.md)

3. Haga clic en **Configuración** para ver la configuración de red y de almacenamiento de la nueva máquina virtual. En la primera máquina virtual, generalmente podrá aceptar la configuración predeterminada. Si ha seleccionado un tamaño de máquina virtual que lo admita, puede probar Almacenamiento premium, para lo que debe seleccionar **Premium (SSD)** en **Tipo de disco**.

	![Configurar la máquina virtual](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. Haga clic en **Resumen** para revisar la configuración elegida. Cuando haya terminado de revisar o actualizar la configuración, haga clic en **Crear**.

	![Configurar la máquina virtual](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Mientras Azure crea la máquina virtual, puede realizar un seguimiento del progreso en **Notificaciones**, en el menú Concentrador. Después de que Azure cree la máquina virtual, la verá en el Panel de inicio, a menos que haya desactivado **Anclar a Panel de inicio** en la hoja **Crear máquina virtual**.

## Iniciar sesión en la nueva máquina virtual

Una vez creada la máquina virtual, podrá iniciar sesión en esta para poder administrar su configuración y las aplicaciones que va a ejecutar en ella.

>[AZURE.NOTE]Para obtener consejos acerca de los requisitos y la solución de problemas, vea [Conectar a una máquina virtual de Azure con RDP o SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Si aún no lo ha hecho, conéctese al [Portal de vista previa](https://portal.azure.com).

2. Haga clic en la máquina virtual en el panel de inicio. Si necesita encontrarlo, haga clic en **Examinar todo** > **Reciente** o **Examinar todo** > **Máquinas virtuales**. A continuación, seleccione la máquina virtual de la lista.

3. En la hoja de la máquina virtual, haga clic en **Conectar**.

	![Iniciar sesión en la nueva máquina virtual](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Haga clic en **Abrir** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual de Windows Server.

5. Haga clic en **Conectar**.

6. Escriba el nombre de usuario y la contraseña que estableció al crear la máquina virtual y, a continuación, haga clic en **Aceptar**.

7. Haga clic en **Sí** para comprobar la identidad de la máquina virtual.

	Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

## Pasos siguientes

* Use Azure PowerShell y la CLI de Azure para [buscar y seleccionar imágenes de máquina virtual](resource-groups-vm-searching.md).
* Automatice la implementación y administración de máquinas virtuales y carga de trabajo mediante [Administrador de recursos de Azure](virtual-machines-how-to-automate-azure-resource-manager.md) y [Plantillas de Administrador de recursos de Azure](http://azure.microsoft.com/es-es/documentation/templates/).

<!---HONumber=August15_HO7-->