


Un conjunto de disponibilidad ayuda a mantener las máquinas virtuales disponibles durante períodos de inactividad, como por ejemplo mientras se realiza mantenimiento. Colocar dos o más máquinas virtuales similarmente configuradas en un conjunto de disponibilidad crea la redundancia necesaria para mantener la disponibilidad de las aplicaciones o servicios que ejecuta su máquina virtual. Para obtener información detallada sobre cómo funciona esto, vea [Administración de la disponibilidad de las máquinas virtuales][].

Es una buena práctica usar una combinación de conjuntos de disponibilidad y extremos de equilibrio de carga para ayudar a asegurar de que su aplicación está siempre disponible y ejecutándose de forma eficaz. Para obtener información acerca de los extremos de equilibrio de carga, vea [Equilibrio de carga para servicios de infraestructura de Azure][].

Puede agregar máquinas virtuales clásicas en un conjunto de disponibilidad mediante una de estas dos opciones:

- [Opción 1: Creación de una máquina virtual y de un conjunto de disponibilidad simultáneamente][]. A continuación, agregue nuevas máquinas virtuales al conjunto cuando cree en dichas máquinas virtuales.
- [Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad][].

>[AZURE.NOTE] En el modelo clásico, las máquinas virtuales que desee poner en el mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.

## <a id="createset"> </a>Opción 1: Creación de una máquina virtual y de un conjunto de disponibilidad simultáneamente.##

Puede usar el Portal de Azure o los comandos de Azure PowerShell para ello.

Para utilizar el Portal de Azure:

1. Si aún no lo ha hecho, inicie sesión en el Portal de Azure.

2. En el menú del concentrador, haga clic en **+ Nuevo**, y, a continuación, haga clic en **Máquina virtual**.
    
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)

3. Seleccione la imagen de máquina virtual de Marketplace que desee usar. Puede crear una máquina virtual con Linux o con Windows.

4. Para la máquina virtual seleccionada, compruebe que el modelo de implementación está establecido en **Clásica** y, a continuación, haga clic en **Crear**.
    
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)

5. Escriba un nombre de máquina virtual, un nombre de usuario y una contraseña (para máquinas con Windows) o la clave pública SSH (para equipos con Linux).

6. Elija un tamaño de máquina virtual y, a continuación, haga clic en **Seleccionar** para continuar.

7. Elija **Configuración opcional > Conjunto de disponibilidad** y seleccione el conjunto de disponibilidad al que desea agregar la máquina virtual.
    
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png)

8. Revise las opciones de configuración. Cuando haya terminado, haga clic en **Crear**.

9. Mientras Azure crea la máquina virtual, puede realizar el seguimiento del progreso desde el menú del concentrador, en **Máquinas virtuales**.

Para usar los comandos de Azure PowerShell con el fin de crear una máquina virtual de Azure y agregarla a un conjunto de disponibilidad nuevo o existente, vea [Creación de una máquina virtual de Windows con PowerShell y el modelo de implementación clásica](../articles/virtual-machines/virtual-machines-windows-classic-create-powershell.md).

## <a id="addmachine"> </a>Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad.##

En el Portal de Azure, puede agregar máquinas virtuales clásicas existentes a un conjunto de disponibilidad existente o crear uno nuevo para ellas. (Recuerde que las máquinas virtuales del mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.) Estos pasos son prácticamente los mismos. Con Azure PowerShell, puede agregar la máquina virtual a un conjunto de disponibilidad existente.

1. Si aún no lo ha hecho, inicie sesión en el Portal de Azure.

2. En el menú del concentrador, haga clic en **Máquinas virtuales (clásico)**.
    
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)

3. En la lista de máquinas virtuales, seleccione el nombre de la máquina virtual que quiera agregar al conjunto.

4. Elija **Conjunto de disponibilidad** en la **Configuración** de la máquina virtual.
    
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)

5. Seleccione el conjunto de disponibilidad al que desea agregar la máquina virtual. La máquina virtual debe pertenecer al mismo servicio en la nube que el conjunto de disponibilidad.
    
    ![Texto alternativo de imagen](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)

6. Haga clic en **Guardar**.

Para utilizar los comandos de Azure PowerShell, abra una sesión de Azure PowerShell con nivel de administrador y ejecute el siguiente comando. Para los marcadores de posición (como &lt;VmCloudServiceName&gt;), reemplace todo el contenido de las comillas, incluyendo los caracteres < and >, por los nombres correctos.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] Es posible que haya que reiniciar la máquina virtual para terminar de agregarla al conjunto de disponibilidad.

## Recursos adicionales

[Artículos para máquinas virtuales clásicas][]

<!-- LINKS -->
[Opción 1: Creación de una máquina virtual y de un conjunto de disponibilidad simultáneamente]: #createset
[Opción 2: Incorporación de una máquina virtual existente a un conjunto de disponibilidad]: #addmachine

[Equilibrio de carga para servicios de infraestructura de Azure]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Administración de la disponibilidad de las máquinas virtuales]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md
[Artículos para máquinas virtuales clásicas]: https://azure.microsoft.com/documentation/articles/?tag=azure-service-management&service=virtual-machines

<!---HONumber=AcomDC_0525_2016-->