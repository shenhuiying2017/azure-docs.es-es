


Un conjunto de disponibilidad ayuda a mantener las máquinas virtuales disponibles durante períodos de inactividad, como por ejemplo mientras se realiza mantenimiento. Colocar dos o más máquinas virtuales similarmente configuradas en un conjunto de disponibilidad crea la redundancia necesaria para mantener la disponibilidad de las aplicaciones o servicios que ejecuta su máquina virtual. Para obtener información detallada acerca del funcionamiento, consulte [Administración de la disponibilidad de las máquinas virtuales][Administración de la disponibilidad de las máquinas virtuales].

Es una buena práctica usar una combinación de conjuntos de disponibilidad y extremos de equilibrio de carga para ayudar a asegurar de que su aplicación está siempre disponible y ejecutándose de forma eficaz. Para obtener información acerca de los puntos de conexión con equilibrio de carga, consulte [Equilibrio de carga para servicios de infraestructura de Azure][Equilibrio de carga para servicios de infraestructura de Azure].

Puede agregar máquinas virtuales clásicas en un conjunto de disponibilidad mediante una de estas dos opciones:

* [Opción 1: Creación de una máquina virtual y un conjunto de disponibilidad al mismo tiempo][Opción 1: Creación de una máquina virtual y un conjunto de disponibilidad al mismo tiempo]. A continuación, agregue nuevas máquinas virtuales al conjunto cuando cree en dichas máquinas virtuales.
* [Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad][Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad].

> [!NOTE]
> En el modelo clásico, las máquinas virtuales que desee poner en el mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.
> 
> 

## <a id="createset"> </a>Opción 1: Creación de una máquina virtual y de un conjunto de disponibilidad simultáneamente.
Puede usar el Portal de Azure o los comandos de Azure PowerShell para ello.

Para utilizar el Portal de Azure:

1. Si aún no lo ha hecho, inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú del concentrador, haga clic en **+ Nuevo** y, luego, en **Máquina virtual**.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)
3. Seleccione la imagen de máquina virtual de Marketplace que desee usar. Puede crear una máquina virtual con Linux o con Windows.
4. En la máquina virtual seleccionada, compruebe que el modelo de implementación está establecido en **Clásico** y, luego, haga clic en **Crear**
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Escriba un nombre de máquina virtual, un nombre de usuario y una contraseña (para máquinas con Windows) o la clave pública SSH (para equipos con Linux). 
6. Elija un tamaño de máquina virtual y, luego, haga clic en **Seleccionar** para continuar.
7. Elija **Configuración opcional > Conjunto de disponibilidad** y seleccione el conjunto de disponibilidad al que desea agregar la máquina virtual.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Revise las opciones de configuración. Cuando haya terminado, haga clic en **Crear**.
9. Mientras Azure crea la máquina virtual, puede realizar el seguimiento del progreso desde el menú del concentrador, en **Máquinas virtuales** .

Para usar los comandos de Azure PowerShell para crear una máquina virtual de Azure y agregarla a un conjunto de disponibilidad nuevo o existente, consulte [Creación de una máquina virtual de Windows con PowerShell y el modelo de implementación clásica](../articles/virtual-machines/virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad.
En el Portal de Azure, puede agregar máquinas virtuales clásicas existentes a un conjunto de disponibilidad existente o crear uno nuevo para ellas. (Recuerde que las máquinas virtuales del mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.) Estos pasos son prácticamente los mismos. Con Azure PowerShell, puede agregar la máquina virtual a un conjunto de disponibilidad existente.

1. Si aún no lo ha hecho, inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú del concentrador, haga clic en **Máquinas virtuales (clásico)**.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. En la lista de máquinas virtuales, seleccione el nombre de la máquina virtual que quiera agregar al conjunto.
4. Elija **Conjunto de disponibilidad** en la opción **Configuración** de la máquina virtual.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Seleccione el conjunto de disponibilidad al que desea agregar la máquina virtual. La máquina virtual debe pertenecer al mismo servicio en la nube que el conjunto de disponibilidad.
   
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Haga clic en **Guardar**.

Para utilizar los comandos de Azure PowerShell, abra una sesión de Azure PowerShell con nivel de administrador y ejecute el siguiente comando. En el caso de los marcadores de posición (como &lt;VmCloudServiceName&gt;), reemplace todo el contenido que esté entre comillas, lo que incluye los caracteres < y >, por los nombres correctos.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> Es posible que haya que reiniciar la máquina virtual para terminar de agregarla al conjunto de disponibilidad.
> 
> 

<!-- LINKS -->
[Opción 1: Creación de una máquina virtual y un conjunto de disponibilidad al mismo tiempo]: #createset
[Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad]: #addmachine

[Equilibrio de carga para servicios de infraestructura de Azure]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Administración de la disponibilidad de las máquinas virtuales]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md

[Creación de una máquina virtual que ejecuta Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Información general sobre Virtual Network]: ../articles/virtual-network/virtual-networks-overview.md



<!--HONumber=Nov16_HO3-->


