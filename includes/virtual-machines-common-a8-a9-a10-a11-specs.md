

## <a name="deployment-considerations"></a>Consideraciones de la implementación
* **Suscripción de Azure**: para implementar más que algunas instancias de proceso intensivo, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.

* **Disponibilidad y precios**: los tamaños de máquina virtual se ofrecen solo en el plan de tarifa estándar. Compruebe [Productos disponibles por región] (https://azure.microsoft.com/regions/services/) para ver la disponibilidad en regiones de Azure. 
* **Cuota de núcleos**: quizás tenga que aumentar la cuota de núcleos de su suscripción de Azure partiendo del valor predeterminado. La suscripción también podría limitar el número de núcleos que se pueden implementar en ciertas familias de tamaño de máquina virtual, como la serie H. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../articles/azure-supportability/how-to-create-azure-support-request.md) sin cargo alguno. (Los límites predeterminados pueden variar según la categoría de suscripción).
  
  > [!NOTE]
  > Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure. Las cuotas de Azure son límites de crédito, no garantías de capacidad. Independientemente de la cuota, solamente se le cobrarán los núcleos que use.
  > 
  > 
* **Red virtual** : no se necesita una [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) de Azure para usar instancias de proceso intensivo. Sin embargo, para muchas implementaciones necesita al menos una red virtual de Azure basada en la nube o una conexión de sitio a sitio si necesita acceder a recursos locales. Si es necesario, cree una red virtual para implementar las instancias. No se admite la adición de máquinas virtuales de proceso intensivo a las redes virtuales de grupos de afinidad.
* **Cambio de tamaño**: debido a su hardware especializado, solo se puede cambiar el tamaño de las instancias de proceso intensivo dentro de la misma familia de tamaño (serie H o serie A de proceso intensivo). Por ejemplo, una máquina virtual de la serie H solo se puede cambiar de un tamaño de serie H a otro. Además, no se admite el cambio de tamaño de un tamaño que no sea de proceso intensivo a un tamaño que sí lo sea.  
