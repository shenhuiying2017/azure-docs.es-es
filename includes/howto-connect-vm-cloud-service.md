<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Cómo conectar máquinas virtuales con una red virtual o un servicio de nube

Las máquinas virtuales deben estar en un servicio de nube, que actúa como contenedor y proporciona un único nombre DNS público, una dirección IP pública y un conjunto de extremos para tener acceso a la máquina virtual a través de Internet. El servicio de nube opcionalmente puede estar en una red virtual. 

Si un servicio de nube no está en una red virtual, las máquinas virtuales de dicho servicio de nube sólo pueden comunicarse con otras máquinas virtuales mediante el uso de nombres DNS públicos de las otras máquinas virtuales, y ese tráfico viajará a través de Internet. Si un servicio de nube está en una red virtual, las máquinas virtuales de ese servicio de nube pueden comunicarse con todas las otras máquinas virtuales de la red virtual sin enviar tráfico a través de Internet.

Si coloca las máquinas virtuales en el mismo servicio de nube independiente, puede aprovechar los equilibrios de cargas y los conjuntos de disponibilidad. Para obtener más información, consulte [Máquinas virtuales de equilibrio de carga](../../articles/load-balance-virtual-machines/) y [Administrar la disponibilidad de las máquinas virtuales](../../articles/manage-availability-virtual-machines/). Sin embargo, no se pueden organizar las máquinas virtuales en subredes ni conectar un servicio de nube independiente a la red local. Aquí tiene un ejemplo:

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
Si coloca las máquinas virtuales en una red virtual, puede decidir cuántos servicios en la nube desea utilizar para aprovechar el equilibrio de cargas y los conjuntos de disponibilidad. Además, puede organizar las máquinas virtuales en subredes de la misma manera que en red local y conectar la red virtual a su red local. Aquí tiene un ejemplo:

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Las redes virtuales son el método recomendado para conectar máquinas virtuales en Azure. La práctica recomendada es configurar cada nivel de la aplicación en un servicio de nube diferente. Esto permite la delegación de derechos de usuario avanzado a través de Control de acceso basado en roles (RBAC). Para obtener más información, consulte [Control de acceso basado en roles en el portal de vista previa de Azure](../../articles//role-based-access-control-configure/). Sin embargo, puede que necesite combinar algunas máquinas virtuales de diferentes niveles de aplicaciones en el mismo servicio de nube para no superar el número máximo de 200 servicios de nube por suscripción.

Para conectar máquinas virtuales en una red virtual:

1.	Cree la red virtual en el portal de administración de Azure. Para obtener más información, consulte [Tareas de configuración de la red virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx).
2.	Cree el conjunto de servicios de nube para su implementación en el portal de administración de Azure para reflejar su diseño para conjuntos de disponibilidad y el equilibrio de cargas con **Nuevo > Calcular > Servicio de nube > Creación personalizada**.
3.	Cuando cree cada nueva máquina virtual, especifique el servicio de nube y la red virtual correctos. Si el servicio de nube se ha asociado previamente a la red virtual, su nombre ya se seleccionará automáticamente.

A continuación le facilitamos un ejemplo de uso del portal de administración de Azure.

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)
 
Para conectar máquinas virtuales en un servicio de nube independiente:

1.	Cree el servicio de nube para la implementación en el portal de administración de Azure con **Nuevo > Calcular > Servicio de nube > Creación personalizada**.
2.	Cuando cree la máquina virtual, especifique el nombre del servicio de nube creado en el paso 1. 
Como alternativa, puede crear el servicio de nube para la implementación cuando cree la primera máquina virtual.

A continuación le facilitamos un ejemplo de uso del portal de administración de Azure para el servicio de nube existente denominado EndpointTest.
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Recursos
[Equilibrio de carga de máquinas virtuales](../../articles/load-balance-virtual-machines/)

[Administración de la disponibilidad de las máquinas virtuales](../../articles/manage-availability-virtual-machines/)

[Tareas de configuración de red virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx)

Después de haber creado una máquina virtual, es conveniente agregar un disco de datos para que los servicios y las cargas de trabajo tengan una ubicación donde almacenar los datos. Consulte alguno de los recursos siguientes:

[Acoplamiento de un disco de datos a una máquina virtual Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Acoplamiento de un disco de datos a una máquina virtual de Windows](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=45--> 
