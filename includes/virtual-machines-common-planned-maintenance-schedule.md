

## Máquinas virtuales de instancia única o instancias múltiples
Para muchos clientes que ejecutan Azure es fundamental poder programar cuándo las máquinas virtuales realizarán el mantenimiento planeado, puesto que esto provoca unos 15 minutos de inactividad. Puede aprovechar los conjuntos de disponibilidad para ayudar a controlar cuándo las máquinas virtuales aprovisionadas reciben el mantenimiento planeado.

Hay dos configuraciones posibles para las máquinas virtuales que se ejecutan en Azure. Las máquinas virtuales se pueden configurar como instancia única o instancias múltiples. Si las máquinas virtuales están en un conjunto de disponibilidad, se configuran como instancias múltiples. Tenga en cuenta que incluso las máquinas virtuales únicas se pueden implementar en un conjunto de disponibilidad y se tratarán como instancias múltiples. Si las máquinas virtuales NO están en un conjunto de disponibilidad, se configurarán como una instancia única. Para más información sobre los conjuntos de disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales de Windows](../articles/virtual-machines/virtual-machines-windows-manage-availability.md) o [Administración de la disponibilidad de las máquinas virtuales de Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

Las actualizaciones de mantenimiento planeado para máquinas virtuales de instancia única e instancias múltiples se realizan por separado. Si vuelve a configurar las máquinas virtuales para que sean de instancia única (si son de instancias múltiples) o de instancias múltiples (si son de instancia única), puede controlar cuándo las máquinas virtuales recibirán el mantenimiento planeado. Consulte [Mantenimiento planeado de máquinas virtuales de Linux de Azure](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md) o [Mantenimiento planeado de máquinas virtuales de Windows Azure](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md) para más detalles sobre el mantenimiento planeado de máquinas virtuales de Azure.

## Para la configuración de instancias múltiples
Puede seleccionar la hora del mantenimiento planeado que afecta a las máquinas virtuales que se implementan en una configuración de conjunto de disponibilidad mediante la eliminación de estas máquinas virtuales de los conjuntos de disponibilidad.
1.	Se le enviará un correo electrónico 7 días naturales antes del mantenimiento planeado para las máquinas virtuales en una configuración de instancias múltiples. Los identificadores de suscripción y los nombres de las máquinas virtuales de instancias múltiples afectados se incluirán en el cuerpo del correo electrónico.
2.	Durante estos 7 días, puede elegir la hora en la que se actualizarán las instancias mediante la eliminación de las máquinas virtuales de instancias múltiples en esa región de su conjunto de disponibilidad. Este cambio de configuración ocasionará un reinicio, ya que la máquina virtual se traslada desde un host físico, destinado para el mantenimiento, a otro host físico que no está destinado al mantenimiento. 
3.	Puede quitar la máquina virtual del conjunto de disponibilidad en el portal clásico. 
   
        a.	In the Classic portal, click on the VM and then select “configure.” 
        
        b.	Under “settings”, you can see which Availability Set the VM is in.
        
    ![Selección del conjunto de disponibilidad](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

        c.	In the availability set dropdown menu, select “remove from availability set.”
        
    ![Eliminación del conjunto](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)

        d.	At the bottom, select “save.” Select “yes” to acknowledge that this action will restart the VM.
4.	Estas máquinas virtuales se trasladarán a hosts de instancia única y no se actualizarán durante el mantenimiento planeado para las configuraciones del conjunto de disponibilidad.
5.	Una vez completada la actualización de máquinas virtuales del conjunto de disponibilidad (según la programación que se describe en el correo electrónico original), debe agregar las máquinas virtuales en sus conjuntos de disponibilidad y se volverán a configurar como máquinas virtuales de instancias múltiples. El traslado de las máquinas virtuales de la instancia única de nuevo a las instancias múltiples provocará un reinicio. Normalmente, una vez que se completan todas las actualizaciones de instancias múltiples en todo el entorno de Azure, se realiza el mantenimiento de una instancia única.

Tenga en cuenta que esto también se puede lograr con Azure PowerShell: Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM

## Para la configuración de una instancia única
Puede seleccionar la hora del mantenimiento planeado que afecta a las máquinas virtuales en una configuración de una instancia única mediante la adición de estas máquinas virtuales en conjuntos de disponibilidad. Paso a paso
1.	Se le enviará un correo electrónico 7 días naturales antes del mantenimiento planeado para las máquinas virtuales en una configuración de instancia única. Los identificadores de suscripción y los nombres de las máquinas virtuales de instancia única afectados se incluirán en el cuerpo del correo electrónico. 
2.	Durante estos 7 días, puede elegir la hora en la que se reinicia la instancia trasladando las máquinas virtuales de instancia única a un conjunto de disponibilidad en esa misma región. Este cambio de configuración ocasionará un reinicio, ya que la máquina virtual se traslada desde un host físico, destinado para el mantenimiento, a otro host físico que no está destinado al mantenimiento.
3.	Siga las instrucciones siguientes para agregar las máquinas virtuales existentes a conjuntos de disponibilidad mediante el Portal clásico y Azure PowerShell (consulte el ejemplo de Azure PowerShell en la nota siguiente).
4.	Una vez que se hayan vuelto a configurar estas máquinas virtuales como de instancias múltiples, se excluirán del mantenimiento planeado para máquinas virtuales de instancia única.
5.	Una vez completada la actualización de máquinas virtuales de instancia única (según la programación que se describe en el correo electrónico original), puede quitar las máquinas virtuales de sus conjuntos de disponibilidad y se volverán a configurar como máquinas virtuales de instancia única.

Tenga en cuenta que esto también se puede lograr con Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0323_2016-->