<properties 
	pageTitle="Cómo configurar un conjunto de disponibilidad para máquinas virtuales" 
	description="Proporciona los pasos para configurar un conjunto de disponibilidad para una máquina virtual nueva o existente en Azure con el Portal de administración de Azure y los comandos de Azure PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="kathydav"/>

#Cómo configurar un conjunto de disponibilidad para máquinas virtuales#


Un conjunto de disponibilidad ayuda a mantener las máquinas virtuales disponibles durante períodos de inactividad, como por ejemplo mientras se realiza mantenimiento. Colocar dos o más máquinas virtuales similarmente configuradas en un conjunto de disponibilidad crea la redundancia necesaria para mantener la disponibilidad de las aplicaciones o servicios que ejecuta su máquina virtual. Para obtener información detallada acerca del funcionamiento, consulte [Administración de la disponibilidad de las máquinas virtuales][].

Es una buena práctica usar una combinación de conjuntos de disponibilidad y extremos de equilibrio de carga para ayudar a asegurar de que su aplicación está siempre disponible y ejecutándose de forma eficaz. Para obtener información acerca de los extremos de equilibrio de carga, consulte [Equilibrio de carga para servicios de infraestructura de Azure][].

Puede poner máquinas virtuales en un conjunto de disponibilidad usando una de estas dos opciones:

- [Opción 1: Creación de una máquina virtual y de un conjunto de disponibilidad simultáneamente][]. A continuación, agregue nuevas máquinas virtuales al conjunto cuando cree en dichas máquinas virtuales.
- [Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad][].


>[AZURE.NOTE]Las máquinas virtuales que desee poner en el mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.

## <a id="createset"> </a>Opción 1: Creación de una máquina virtual y de un conjunto de disponibilidad simultáneamente.##

Puede usar el Portal de administración o los comandos de Azure PowerShell para ello.

Para utilizar el Portal de administración:

1. Si aún no lo ha hecho, inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2. En la barra de comandos, haga clic en **Nuevo**.

3. Haga clic en **Máquina Virtual**, y, a continuación, en **Desde la galería**.

4. Utilice las dos primeras pantallas para elegir una imagen, un nombre de usuario, una contraseña, etc. Para obtener más detalles, consulte [Creación de una máquina virtual que ejecuta Windows][].
 
5. La tercera pantalla permite configurar los recursos relacionados con las redes, el almacenamiento y la disponibilidad. Haga lo siguiente:
	 
	1. Seleccione la opción apropiada para el servicio la nube. Establézcalo en **Crear un nuevo servicio en la nube** (a menos que esté agregando esta nueva máquina virtual a un servicio en la nube de máquinas virtuales existente). A continuación, en **Nombre DNS de servicio en la nube**, escriba un nombre. El nombre DNS pasará a ser parte del URI que se usa para establecer contacto con la máquina virtual. El servicio en la nube se comporta como un grupo de comunicaciones y aislamiento. Todas las máquinas virtuales del mismo servicio en la nube se pueden comunicar entre sí, se pueden configurar para equilibrio de carga y se pueden colocar en el mismo grupo de disponibilidad. 

	2. En **Región/Grupo de afinidad/Red virtual**, especifique una red virtual si pretende usar una. **Importante**: si desea que una máquina virtual use una red virtual, debe incorporar dicha máquina virtual a la red virtual cuando cree la máquina virtual. No puede incorporar una máquina virtual a una red virtual después de crear la VM. Para obtener más información, consulte [Información general sobre redes virtuales de Azure][].
	
	3. Cree el conjunto de disponibilidad. En **Conjunto de disponibilidad**, déjelo establecido en **Crear un conjunto de disponibilidad**. A continuación, escriba un nombre para el conjunto.

	4. Cree los extremos predeterminados y agregue más extremos si es necesario. También puede agregar extremos posteriormente.

	![Crear un conjunto de disponibilidad para una nueva máquina virtual](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png)

6. En la cuarta pantalla, elija las extensiones que desee instalar. Las extensiones proporcionan características que facilitan la administración de la máquina virtual, como por ejemplo la ejecución de antimalware o el restablecimiento de contraseñas. Para obtener detalles, consulte [Agente de máquina virtual de Azure y extensiones de máquina virtual](http://go.microsoft.com/fwlink/p/?LinkId=XXX).

7.	Haga clic en la fecha para crear la máquina virtual y el conjunto de disponibilidad.

	En el panel de la máquina virtual nueva, puede hacer clic en **Configurar** y ver que la máquina virtual pertenece al conjunto de disponibilidad nuevo.

Para utilizar los comandos de PowerShell de Azure para crear una máquina virtual de Azure y agregarla a un conjunto de disponibilidad nuevo o existente, vea lo siguiente:

- [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Linux](virtual-machines-ps-create-preconfigure-linux-vms.md)


## <a id="addmachine"> </a>Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad.##

En el Portal de administración, puede agregar máquinas virtuales existentes a un conjunto de disponibilidad existente o crear uno nuevo para ellas. (Recuerde que las máquinas virtuales del mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.) Estos pasos son prácticamente los mismos. Con Azure PowerShell, puede agregar la máquina virtual a un conjunto de disponibilidad existente.

1. Si no lo ha hecho todavía, inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2. En la barra de navegación, haga clic en **Máquinas virtuales**.

3. En la lista de máquinas virtuales, haga clic en el nombre de las máquinas virtuales que desea agregar al conjunto.

4. En las pestañas debajo del nombre de la máquina virtual, haga clic en **Configurar**.

5. En la sección Configuración, busque **Conjunto de disponibilidad**. Realice una de las operaciones siguientes:

	R: Elija **Crear un conjunto de disponibilidad** y, a continuación, escriba un nombre para el conjunto.

	B. Elija **Seleccionar un conjunto de disponibilidad** y, a continuación, seleccione un conjunto de la lista.

	![Crear un conjunto de disponibilidad para una máquina virtual existente](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png)

6. Haga clic en **Guardar**.

Para utilizar los comandos de Azure PowerShell, abra una sesión de Azure PowerShell con nivel de administrador y ejecute el siguiente comando. Para los marcadores de posición (como &lt;VmCloudServiceName&gt;), reemplace todo el contenido de las comillas, incluyendo los caracteres < and >, por los nombres correctos.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE]La máquina virtual podría reiniciarse para terminar de agregarla al conjunto de disponibilidad.


##Recursos adicionales
[Acerca de los ajustes de configuración de máquinas virtuales en Azure]

<!-- LINKS -->
[Opción 1: Creación de una máquina virtual y de un conjunto de disponibilidad simultáneamente]: #createset
[Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad]: #addmachine
[Equilibrio de carga para servicios de infraestructura de Azure]: virtual-machines-load-balance.md
[Administración de la disponibilidad de las máquinas virtuales]: virtual-machines-manage-availability.md
[Creación de una máquina virtual que ejecuta Windows]: virtual-machines-windows-tutorial.md
[Información general sobre redes virtuales de Azure]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[Acerca de los ajustes de configuración de máquinas virtuales en Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx

<!---HONumber=58--> 