Las máquinas virtuales siempre se colocan en un servicio en la nube, que actúa como contenedor y proporciona un único nombre DNS público, una dirección IP pública y un conjunto de extremos para tener acceso a la máquina virtual a través de Internet. El servicio en la nube opcionalmente puede estar en una red virtual.

Si no es un servicio en la nube en una red virtual, se denomina un servicio en la nube *independiente*. Las máquinas virtuales de dicho servicio en la nube solo pueden comunicarse con otras máquinas virtuales mediante el uso de nombres DNS públicos de las otras máquinas virtuales, y ese tráfico viajará a través de Internet. Si un servicio en la nube está en una red virtual, las máquinas virtuales de ese servicio en la nube pueden comunicarse con todas las otras máquinas virtuales de la red virtual sin enviar tráfico a través de Internet.

Si coloca las máquinas virtuales en el mismo servicio en la nube independiente, aún puede usar los equilibrios de cargas y los conjuntos de disponibilidad. Para obtener más información, consulte [Máquinas virtuales de equilibrio de carga](../articles/load-balance-virtual-machines.md) y [Administración de la disponibilidad de las máquinas virtuales](../articles/manage-availability-virtual-machines.md). Sin embargo, no se pueden organizar las máquinas virtuales en subredes ni conectar un servicio en la nube independiente a la red local. Aquí tiene un ejemplo.

![Máquinas virtuales en un servicio en la nube independiente](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
Si coloca las máquinas virtuales en una red virtual, puede decidir cuántos servicios en la nube desea utilizar para aprovechar el equilibrio de cargas y los conjuntos de disponibilidad. Además, puede organizar las máquinas virtuales en subredes de la misma manera que en red local y conectar la red virtual a su red local. Aquí tiene un ejemplo.

![Máquinas virtuales en una red virtual](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Las redes virtuales son el método recomendado para conectar máquinas virtuales en Azure. La práctica recomendada es configurar cada nivel de la aplicación en un servicio de nube diferente. Esto permite la delegación de derechos de usuario avanzado a través de Control de acceso basado en roles (RBAC). Para obtener más información, consulte [Control de acceso basado en rol en el Portal de vista previa de Azure](../articles/role-based-access-control-configure.md). Sin embargo, puede que necesite combinar algunas máquinas virtuales de diferentes niveles de aplicaciones en el mismo servicio en la nube para no superar el número máximo de 200 servicios en la nube por suscripción.

## Conexión de máquinas virtuales en una red virtual

Para conectar máquinas virtuales en una red virtual:

1.	Cree la red virtual en el portal de administración de Azure. Para obtener más información, consulte [Tareas de configuración de la red virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx).
2.	Cree el conjunto de servicios en la nube para su implementación en el Portal de administración de Azure a fin de reflejar su diseño para conjuntos de disponibilidad y el equilibrio de cargas con **Nuevo > Proceso > Servicio en la nube > Creación personalizada**.
3.	Cuando cree cada nueva máquina virtual, especifique el servicio de nube y la red virtual correctos. Si el servicio de nube se ha asociado previamente a la red virtual, su nombre ya se seleccionará automáticamente.

A continuación le facilitamos un ejemplo de uso del portal de administración de Azure.

![Selección de un servicio en la nube para una máquina virtual](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## Conexión de máquinas virtuales en un servicio en la nube independiente
 
Para conectar máquinas virtuales en un servicio en la nube independiente:

1.	Cree el servicio en la nube para su implementación en el Portal de administración de Azure. Haga clic en **Nuevo > Proceso > Servicio en la nube > Creación personalizada**.
2.	Cuando cree la máquina virtual, elija el nombre del servicio en la nube creado en el paso anterior. Como alternativa, puede crear el servicio en la nube para su implementación cuando cree la primera máquina virtual.

A continuación le facilitamos un ejemplo de uso del portal de administración de Azure para el servicio en la nube existente denominado EndpointTest.
 
![Agregar una máquina virtual a un servicio de nube existente](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Recursos
[Máquinas virtuales de equilibrio de carga](../articles/load-balance-virtual-machines.md)

[Administración de la disponibilidad de las máquinas virtuales](../articles/manage-availability-virtual-machines.md)

[Tareas de configuración de red virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx)

Después de haber creado una máquina virtual, es conveniente agregar un disco de datos para que los servicios y las cargas de trabajo tengan una ubicación donde almacenar los datos. Consulte alguno de los recursos siguientes:

[Acoplamiento de un disco de datos a una máquina virtual de Linux](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[Acoplamiento de un disco de datos a una máquina virtual de Windows](../articles/virtual-machines/storage-windows-attach-disk.md)

<!---HONumber=July15_HO1-->