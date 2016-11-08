

![Máquinas virtuales en un servicio en la nube independiente](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Si coloca las máquinas virtuales en una red virtual, puede decidir cuántos servicios en la nube desea utilizar para aprovechar el equilibrio de cargas y los conjuntos de disponibilidad. Además, puede organizar las máquinas virtuales en subredes de la misma manera que en red local y conectar la red virtual a su red local. Este es un ejemplo:

![Máquinas virtuales en una red virtual](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Las redes virtuales son el método recomendado para conectar máquinas virtuales en Azure. La práctica recomendada es configurar cada nivel de la aplicación en un servicio de nube diferente. Sin embargo, puede que necesite combinar algunas máquinas virtuales de diferentes niveles de aplicaciones en el mismo servicio en la nube para no superar el número máximo de 200 servicios en la nube por suscripción. Para revisar este y otros límites, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../articles/azure-subscription-service-limits.md).

## Conexión de máquinas virtuales en una red virtual
Para conectar máquinas virtuales en una red virtual:

1. Cree la red virtual en el [Portal de Azure](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md).
2. Cree el conjunto de servicios en la nube para la implementación de forma que refleje el diseño de los conjuntos de disponibilidad y del equilibrio de carga. En el portal de Azure clásico, haga clic en **Nuevo > Proceso > Servicio en la nube > Creación personalizada** para cada servicio en la nube.
3. Para crear cada máquina virtual, haga clic en **Nuevo > Proceso > Máquina virtual > Desde la galería**. Elija el servicio en la nube y la red virtual correctos para la máquina virtual. Si el servicio en la nube ya está unido a una red virtual, su nombre se seleccionará automáticamente.

![Selección de un servicio en la nube para una máquina virtual](./media/virtual-machines-common-classic-connect-vms/VMConfig1.png)

## Conexión de máquinas virtuales en un servicio en la nube independiente
Para conectar máquinas virtuales en un servicio en la nube independiente:

1. Cree el servicio en la nube en el [Portal de Azure clásico](http://manage.windowsazure.com). Haga clic en **Nuevo > Proceso > Servicio en la nube > Creación personalizada**. Como alternativa, puede crear el servicio en la nube para su implementación cuando cree la primera máquina virtual.
2. Cuando cree las máquinas virtuales, elija el nombre del servicio en la nube que creó en el paso anterior.
   
   ![Agregar una máquina virtual a un servicio de nube existente](./media/virtual-machines-common-classic-connect-vms/Connect-VM-to-CS.png)

<!---HONumber=AcomDC_0330_2016-->