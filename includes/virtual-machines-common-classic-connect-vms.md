

![Máquinas virtuales en un servicio en la nube independiente](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Si coloca las máquinas virtuales en una red virtual, puede decidir cuántos servicios en la nube desea utilizar para aprovechar el equilibrio de cargas y los conjuntos de disponibilidad. Además, puede organizar las máquinas virtuales en subredes de la misma manera que en red local y conectar la red virtual a su red local. Este es un ejemplo:

![Máquinas virtuales en una red virtual](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Las redes virtuales son el método recomendado para conectar máquinas virtuales en Azure. La práctica recomendada es configurar cada nivel de la aplicación en un servicio de nube diferente. Sin embargo, puede que necesite combinar algunas máquinas virtuales de diferentes niveles de aplicaciones en el mismo servicio en la nube para no superar el número máximo de 200 servicios en la nube por suscripción. Para revisar este y otros límites, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Conexión de máquinas virtuales en una red virtual
Para conectar máquinas virtuales en una red virtual:

1. Crear la red virtual en [Azure Portal](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) y especifique "implementación clásica".
2. Cree el conjunto de servicios en la nube para la implementación de forma que refleje el diseño de los conjuntos de disponibilidad y del equilibrio de carga. En Azure Portal, haga clic en **Nuevo > Compute > Servicio en la nube** en cada servicio en la nube.

  Cuando rellene los detalles de servicio en la nube, elija el mismo _grupo de recursos_ utilizado con la red virtual.

3. Para crear una máquina virtual nueva, haga clic en **Nuevo > Proceso** y, después, seleccione la imagen de máquina virtual apropiada en **Aplicaciones destacadas**.

  En la hoja **Básico** de la máquina virtual, elija el mismo _grupo de recursos_ que se ha utilizado con la red virtual.

  ![Hoja Básico de la máquina virtual cuando se usa una red virtual](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. Cuando rellene los datos de **Configuración** de la máquina virtual, elija el valor correcto _Servicio en la nube_ o _Red virtual_ para la máquina virtual.

  Azure seleccionará el otro elemento en función de lo que haya seleccionado.

  ![Hoja Configuración de la máquina virtual cuando se usa una red virtual](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Conexión de máquinas virtuales en un servicio en la nube independiente
Para conectar máquinas virtuales en un servicio en la nube independiente:

1. Cree el servicio en la nube en [Azure Portal](http://portal.azure.com). Haga clic en **Nuevo > Proceso > Servicio en la nube**. Como alternativa, puede crear el servicio en la nube para su implementación cuando cree la primera máquina virtual.
2. Cuando cree las máquinas virtuales, elija el mismo grupo de recursos utilizado con el servicio en la nube.

  ![Agregar una máquina virtual a un servicio de nube existente](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Cuando rellene los detalles de la máquina virtual, elija el nombre del servicio en la nube que creó en el primer paso.

  ![Selección de un servicio en la nube para una máquina virtual](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
