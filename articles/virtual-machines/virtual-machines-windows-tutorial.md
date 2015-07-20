<properties
	pageTitle="Creación de una máquina virtual con Windows en el Portal de vista previa de Azure"
	description="Aprenda a crear máquinas virtuales (VM) de Azure que usen Windows con Azure Marketplace en el Portal de vista previa de Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/03/2015"
	ms.author="kathydav"/>

# Creación de una máquina virtual que ejecuta Windows en el Portal de vista previa de Azure#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

Este tutorial muestra lo fácil que resulta crear una máquina virtual (VM) de Azure en el Portal de vista previa de Azure. Vamos a usar una imagen de Windows Server como ejemplo, pero es solo una de las muchas imágenes que Azure ofrece. Tenga en cuenta que las opciones de imagen dependen de su suscripción. Por ejemplo, las imágenes de escritorio pueden estar disponibles para los suscriptores MSDN.

También se pueden crear máquinas virtuales con [imágenes propias](virtual-machines-create-upload-vhd-windows-server.md). Para obtener información sobre este y otros métodos, consulte [Diferentes formas de crear una máquina virtual de Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Creación de la máquina virtual

Con Windows Server 2012 R2 Datacenter como ejemplo, puede crear una máquina virtual para probar en solo unos minutos. Puede usar la configuración predeterminada de Azure para la mayoría de la configuración.

1. Inicie sesión en el [Portal de vista previa](https://portal.azure.com).

2. En el menú Concentrador, haga clic en **Nuevo**.

3. En el **nuevo** blade, haga clic en **Proceso** **>** **Windows Server 2012 R2 Datacenter**.

	![Seleccione una imagen de máquina virtual de Marketplace](./media/virtual-machines-windows-tutorial/marketplace_portal.png)

4. En el cuadro **Crear VM**, rellene los campos **Nombre de host**, **Nombre de usuario** y el campo **Contraseña**. El campo **Nombre de usuario** hace referencia a la cuenta administrativa que usa para administrar el servidor. Cree una contraseña que sea difícil de adivinar, pero que pueda recordar. **Necesitará el nombre de usuario y la contraseña para iniciar sesión en la máquina virtual**. Si olvida la contraseña, puede restablecerla con [estas instrucciones](virtual-machines-windows-reset-password.md)

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial/create_vm_name_pwd_portal.png)

5. Revise la configuración predeterminada, como el **nivel de precios** y la **configuración opcional**. Estas opciones afectan al tamaño de la máquina virtual, así como las opciones de red, como la pertenencia al dominio. Por ejemplo, para probar el almacenamiento Premium en una máquina virtual, necesitará seleccionar una región y el tamaño que admite. Para la primera máquina virtual, los valores predeterminados suelen funcionar correctamente.

	>[AZURE.NOTE]El almacenamiento Premium está disponible para las máquinas virtuales de la serie DS en determinadas regiones. El almacenamiento Premium es la mejor opción de almacenamiento para cargas de trabajo intensivas de datos como una base de datos. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage-preview-portal.md)

6. Cuando haya terminado de revisar o actualizar la configuración, haga clic en **Crear**.

7. Mientras Azure crea la máquina virtual, puede realizar un seguimiento del progreso en **Notificaciones**, en el menú Concentrador. Después de que Azure cree la máquina virtual, la verá en el Panel de inicio a menos que se desactive **Anclar al Panel de inicio** en la hoja **Crear VM**.

## Iniciar sesión en la nueva máquina virtual

Una vez creada la máquina virtual, podrá iniciar sesión en esta para poder administrar su configuración y las aplicaciones que va a ejecutar en ella.

>[AZURE.NOTE]Para obtener consejos acerca de los requisitos y la solución de problemas, consulte [Conectar a una máquina virtual de Azure con RDP o SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Si aún no lo ha hecho, inicie sesión en el [Portal de vista previa](https://portal.azure.com).

2. Haga clic en la máquina virtual en el panel de inicio. Si necesita buscarlo, haga clic en **Examinar** > **Máquinas virtuales**. A continuación, seleccione la máquina virtual de la lista.

3. En la hoja de la máquina virtual, haga clic en **Conectar**.

	![Iniciar sesión en la nueva máquina virtual](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Haga clic en **Abrir** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.

5. Haga clic en **Conectar**.

6. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y, a continuación, haga clic en **Aceptar**.

7. Haga clic en **Sí** para comprobar la identidad de la máquina virtual.

	Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

## Pasos siguientes

Para obtener más información sobre la configuración de máquinas virtuales con Windows en Azure, consulte estos artículos:

[Conectar máquinas virtuales con una red virtual o un servicio en la nube](cloud-services-connect-virtual-machine.md)

[Acoplamiento de discos de datos a una máquina virtual](storage-windows-attach-disk.md)

[Administración de la disponibilidad de las máquinas virtuales](../manage-availability-virtual-machines.md)

[Acerca de los ajustes de configuración de máquinas virtuales en Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)
 

<!---HONumber=July15_HO2-->