Puede realizar fácilmente el [escalado automático](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) de sus [máquinas virtuales (VM)](../articles/virtual-machines/windows/overview.md) si usa los [conjuntos de escalado de máquinas virtuales](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) y la [característica de escalado automático de Azure Monitor](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). Las máquinas virtuales tienen que ser miembros del conjunto de escalado que se va a escalar automáticamente. Este artículo le proporciona información que le permitirá entender mejor cómo escalar las máquinas virtuales tanto vertical como horizontalmente mediante métodos automáticos y manuales.

## <a name="horizontal-or-vertical-scaling"></a>Escalado horizontal o escalado vertical

La característica de escalado automático de Azure Monitor solo escala horizontalmente, es decir, aumenta o reduce el número de instancias de máquina virtual. El escalado horizontal es más flexible en un entorno en la nube ya que permite ejecutar miles de máquinas virtuales para administrar la carga. Puede escalar horizontalmente cambiando automática o manualmente la capacidad (o recuento de instancias) del conjunto de escalado. 

El escalado vertical mantiene el mismo número de máquinas virtuales, pero hace que estas sean más o menos potentes. La potencia se mide por atributos como la memoria, la velocidad de la CPU o el espacio en disco. El escalado vertical depende de la disponibilidad de hardware de mayor tamaño, que supera el límite rápidamente y puede variar según la región. El escalado vertical también suele requerir que se detenga y reinicie una máquina virtual. Puede escalar verticalmente estableciendo un nuevo tamaño en la configuración de las máquinas virtuales del conjunto de escalado.

Mediante el uso de runbooks en [Azure Automation](../articles/automation/automation-intro.md), le resultará muy fácil [escalar o reducir las máquinas virtuales de un conjunto de escalado](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) verticalmente.

## <a name="create-a-virtual-machine-scale-set"></a>Crear un conjunto de escalado de máquinas virtuales

Los conjuntos de escalado facilitan la implementación y administración de máquinas virtuales idénticas como conjunto. Puede crear conjuntos de escalado de Linux o Windows mediante [Azure Portal](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md) o la [CLI de Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). También puede crear y administrar conjuntos de escalado con SDK como [Python](/develop/python) o [Node.js](/nodejs/azure), o directamente con las [API de REST](/rest/api/compute/virtualmachinescalesets). El escalado automático de las máquinas virtuales se logra aplicando las métricas y las reglas al conjunto de escalado.

## <a name="configure-autoscale-for-a-scale-set"></a>Configuración del escalado automático de un conjunto de escalado

El escalado automático proporciona el número correcto de máquinas virtuales para administrar la carga en la aplicación. Permite agregar máquinas virtuales para controlar el aumento de la carga y ahorrar dinero mediante la eliminación de aquellas máquinas que estén inactivas. Puede especificar un número mínimo y máximo de máquinas virtuales en ejecución basado en un conjunto de reglas. Tener un mínimo garantiza la ejecución de la aplicación aunque no exista carga. Tener un valor máximo limita el posible costo total por hora.

Puede habilitar el escalado automático cuando cree el conjunto de escalado mediante [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) o la [CLI de Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). También puede habilitarlo una vez creado el conjunto de escalado. Puede crear un conjunto de escalado, instalar la extensión y configurar el escalado automático mediante una [plantilla de Azure Resource Manager](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). En Azure Portal, habilite el escalado automático en Azure Monitor o habilítelo desde la configuración del conjunto de escalado.

![Habilitar escalado automático](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Métricas

La característica de escalado automático de Azure Monitor le permite escalar o reducir verticalmente el número de máquinas virtuales en ejecución en función de las [métricas](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). De forma predeterminada, las máquinas virtuales proporcionan métricas básicas en el nivel de host para el disco, la red y el uso de la CPU. Cuando configura la recopilación de datos de diagnóstico con la extensión de diagnóstico, los contadores de rendimiento adicionales del sistema operativo invitado pasan a estar disponibles para el disco, la CPU y la memoria.

![Criterios de las métricas](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Si necesita escalar la aplicación en función de métricas que no están disponibles en el host, las máquinas virtuales del conjunto de escalado deberán tener instalada la [extensión de diagnóstico de Linux](../articles/virtual-machines/linux/diagnostic-extension.md) o la [extensión de diagnóstico de Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md). Si crea un conjunto de escalado mediante Azure Portal, deberá usar también Azure PowerShell o la CLI de Azure para instalar la extensión con la configuración de diagnóstico que necesita.
 
### <a name="rules"></a>Reglas

Las [reglas](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) combinan una métrica con una acción que debe realizarse. Cuando se cumplen las condiciones de las reglas, se desencadenan una o varias acciones de escalado automático. Por ejemplo, puede tener una regla definida que aumenta el número de máquinas virtuales en 1 si el uso medio de la CPU supera el 85 por ciento.

![Acciones de escalado automático](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Notificaciones

También puede [configurar desencadenadores](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) para que se realicen llamadas a direcciones URL web específicas o se envíen correos electrónicos en función de las reglas de escalado automático que cree. Los Webhook permiten enrutar las notificaciones de alerta de Azure a otros sistemas para procesarlas posteriormente o notificaciones personalizadas.

## <a name="manually-scale-vms-in-a-scale-set"></a>Escalado manual de máquinas virtuales en un conjunto de escalado

### <a name="horizontal"></a>Horizontal

Puede agregar o quitar máquinas virtuales cambiando la capacidad del conjunto de escalado. En Azure Portal, puede reducir o aumentar el número de máquinas virtuales (que aparece como **número de instancias**) del conjunto de escalado desplazando la barra de condición Invalidar en la pantalla Escalado hacia la izquierda o la derecha.

Con Azure PowerShell, tendrá que obtener el objeto del conjunto de escalado mediante [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). A continuación, establezca la propiedad **sku.capacity** en el número de máquinas virtuales que desee y actualice el conjunto de escalado mediante [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Mediante la CLI de Azure, cambie la capacidad con el parámetro **--new-capacity** del comando [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale).

### <a name="vertical"></a>Vertical

Puede cambiar manualmente el tamaño de las máquinas virtuales en Azure Portal en la pantalla Tamaño del conjunto de escalado. Puede usar Azure PowerShell con Get-AzureRmVmss, mediante la configuración de la propiedad de la sku de referencia de imagen y, a continuación, mediante el uso de [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) y [Update-AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca de los conjuntos de escalado en [Consideraciones de diseño para conjuntos de escalado](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

