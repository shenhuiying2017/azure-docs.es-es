

## <a name="multi-and-single-instance-vms"></a>Máquinas virtuales de instancia única o instancias múltiples
Muchos clientes que ejecutan en Azure consideran fundamental que puedan programar cuándo sus máquinas virtuales deben someterse a un mantenimiento planeado debido al período de inactividad (unos 15 minutos) que se produce durante el mantenimiento. Puede usar los conjuntos de disponibilidad para ayudar a controlar cuándo las máquinas virtuales aprovisionadas reciben el mantenimiento planeado.

Hay dos configuraciones posibles para las máquinas virtuales que se ejecutan en Azure. Las máquinas virtuales se pueden configurar como instancia única o instancias múltiples. Si las máquinas virtuales están en un conjunto de disponibilidad, se configuran como instancias múltiples. Tenga en cuenta que incluso las máquinas virtuales únicas se pueden implementar en un conjunto de disponibilidad, que se tratarán como instancias múltiples. Si las máquinas virtuales NO están en un conjunto de disponibilidad, se configurarán como una instancia única.  Para más información sobre los conjuntos de disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales Windows](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Administración de la disponibilidad de las máquinas virtuales Linux](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Las actualizaciones de mantenimiento planeado para máquinas virtuales de instancia única e instancias múltiples se realizan por separado. Si vuelve a configurar las máquinas virtuales para que sean de instancia única (si son de instancias múltiples) o de instancias múltiples (si son de instancia única), puede controlar cuándo las máquinas virtuales recibirán el mantenimiento planeado. Consulte [Mantenimiento planeado de máquinas virtuales Linux en Azure](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [Mantenimiento planeado de máquinas virtuales Windows en Azure](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para más detalles sobre el mantenimiento planeado de máquinas virtuales de Azure.

## <a name="for-multi-instance-configuration"></a>Para la configuración de instancias múltiples
Puede seleccionar la hora del mantenimiento planeado que afecta a las máquinas virtuales que se implementan en una configuración de conjunto de disponibilidad mediante la eliminación de estas máquinas virtuales de los conjuntos de disponibilidad.

1. Se le enviará un correo electrónico siete días naturales antes del mantenimiento planeado para las máquinas virtuales en una configuración de instancias múltiples. Los identificadores de suscripción y los nombres de las máquinas virtuales de instancias múltiples afectados se incluyen en el cuerpo del correo electrónico.
2. Durante estos siete días, puede elegir la hora en la que se actualizarán las instancias mediante la eliminación de las máquinas virtuales de instancias múltiples en esa región de su conjunto de disponibilidad. Este cambio de configuración causa un reinicio, ya que la máquina virtual se traslada desde un host físico, destinado para el mantenimiento, a otro host físico que no está destinado al mantenimiento.
3. Puede quitar la máquina virtual del conjunto de disponibilidad en Azure Portal.

   1. En el portal, seleccione la máquina virtual que se va a quitar del conjunto de disponibilidad.  

   2. En **Configuración**, haga clic en **Conjunto de disponibilidad**.

      ![Selección del conjunto de disponibilidad](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. En el menú desplegable del conjunto de disponibilidad, seleccione “No forma parte de un conjunto de disponibilidad”.

      ![Eliminación del conjunto](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. En la parte superior, haga clic en **Guardar**. Seleccione **Sí** para confirmar que esta acción reinicia la máquina virtual.

   >[!TIP]
   >Puede volver a configurar la máquina virtual para varias instancias más adelante mediante la selección de uno de los conjuntos de disponibilidad enumerados.

4. Las máquinas virtuales que se han quitado de los conjuntos de disponibilidad se trasladarán a hosts de instancia única y no se actualizarán durante el mantenimiento planeado para las configuraciones del conjunto de disponibilidad.
5. Una vez completada la actualización de máquinas virtuales del conjunto de disponibilidad (según la programación que se describe en el correo electrónico original), debe agregar las máquinas virtuales en sus conjuntos de disponibilidad y volver a configurar como máquinas virtuales de instancias múltiples. Formar parte de un conjunto de disponibilidad vuelve a configurar las máquinas virtuales como de instancias múltiples y da como resultado un reinicio. Normalmente, una vez que se completan todas las actualizaciones de instancias múltiples en todo el entorno de Azure, se realiza el mantenimiento de una instancia única.

También puede lograrse la eliminación de una máquina virtual de un conjunto de disponibilidad mediante Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Para la configuración de una instancia única
Puede seleccionar la hora del mantenimiento planeado que afecta a las máquinas virtuales en una configuración de una instancia única mediante la adición de estas máquinas virtuales en conjuntos de disponibilidad.

Paso a paso

1. Se le enviará un correo electrónico siete días naturales antes del mantenimiento planeado para las máquinas virtuales en una configuración de instancia única. Los identificadores de suscripción y los nombres de las máquinas virtuales de instancia única afectados se incluyen en el cuerpo del correo electrónico.
2. Durante estos siete días, puede elegir la hora en la que se reinicia la instancia. Para ello, debe agregar las máquinas virtuales de instancia única a un conjunto de disponibilidad que se encuentre en la misma región. Este cambio de configuración causa un reinicio, ya que la máquina virtual se traslada desde un host físico, destinado para el mantenimiento, a otro host físico que no está destinado al mantenimiento.
3. Siga las instrucciones siguientes para agregar las máquinas virtuales existentes a conjuntos de disponibilidad mediante Azure Portal y Azure PowerShell. (Consulte el ejemplo de Azure PowerShell en la nota siguiente).
4. Una vez que se hayan vuelto a configurar estas máquinas virtuales como de instancias múltiples, se excluyen del mantenimiento planeado para máquinas virtuales de instancia única.
5. Cuando se complete la actualización de las máquinas virtuales de instancia única (según la programación del correo electrónico original), puede devolver las máquinas virtuales a la instancia única mediante su eliminación de los conjuntos de disponibilidad.

También puede lograrse la adición de una máquina virtual a un conjunto de disponibilidad mediante Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
