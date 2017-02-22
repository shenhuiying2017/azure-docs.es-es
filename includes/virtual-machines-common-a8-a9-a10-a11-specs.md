
## <a name="key-features"></a>Principales características
* **Hardware de alto rendimiento** : estas instancias están diseñadas y optimizadas para aplicaciones de proceso intensivo y uso de red intensivo, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HCP) y de lotes, el modelado y las simulaciones a gran escala. 
  
    En el sitio web Intel.com encontrará detalles acerca de los procesadores de Intel Xeon E5-2667 v3 (que se usa en las series H) y los procesadores de Intel Xeon E5-2670 (en A8 - A11), incluidas las extensiones del conjunto de instrucciones compatibles. 
* **Diseñados para clústeres HPC** : implemente varias instancias de proceso intensivo en Azure para crear un clúster HPC independiente o para agregar más capacidad a un clúster local. Si quiere, implemente herramientas de administración del clúster y de programación de trabajos. O bien, usar las instancias para el trabajo de proceso intensivo en otro servicio de Azure, como Azure Batch.
* **Conexión de red RDMA para aplicaciones MPI** : un subconjunto de las instancias de proceso intensivo (H16r, H16mr, A8 y A9) incluyen una segunda interfaz de red para la conectividad de acceso directo a memoria remota (RDMA). Esta interfaz se añade a la interfaz de red estándar de Azure disponible para otros tamaños de máquina virtual. 
  
    Esta interfaz permite que las instancias compatibles con RDMA se comuniquen entre sí a través de una red InfiniBand, funcionando a velocidades FDR para máquinas virtuales H16r y H16mr y QDR para máquinas virtuales A8 y A9. Las funcionalidades RDMA expuestas en estas máquinas virtuales pueden mejorar la escalabilidad y el rendimiento de determinadas aplicaciones de interfaz de paso de mensajes (MPI) de Windows y Linux. Para conocer los requisitos, consulte [Acceso a la red RDMA](#access-to-the-rdma-network) en este artículo.

## <a name="deployment-considerations"></a>Consideraciones de la implementación
* **Suscripción de Azure** : si desea implementar más de un pequeño número de instancias de proceso intensivo, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. Si usa una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), solo puede usar un número limitado de núcleos de proceso de Azure.
* **Disponibilidad y precios** : los tamaños de máquina virtual de proceso intensivo se ofrecen solo en el plan de tarifa estándar. Para ver la disponibilidad en las regiones de Azure, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/) . 
* **Cuota de núcleos** : es posible que necesite aumentar la cuota de núcleos de su suscripción de Azure desde el valor predeterminado de 20 núcleos por suscripción (si se usa el modelo de implementación clásica) o 20 núcleos por región (si utiliza el modelo de implementación de Resource Manager). La suscripción también podría limitar el número de núcleos que se pueden implementar en ciertas familias de tamaño de máquina virtual, como la serie H. Para solicitar un aumento de cuota, [abra una solicitud de soporte técnico al cliente en línea](../articles/azure-supportability/how-to-create-azure-support-request.md) sin cargo alguno. (Los límites predeterminados pueden variar según la categoría de suscripción).
  
  > [!NOTE]
  > Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure. Las cuotas de Azure son límites de crédito, no garantías de capacidad. Independientemente de la cuota, solamente se le cobrarán los núcleos que use.
  > 
  > 
* **Red virtual** : no se necesita una [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) de Azure para usar instancias de proceso intensivo. Sin embargo, puede que necesite al menos una red virtual Azure basada en la nube para muchos escenarios de implementación o una conexión de sitio a sitio si necesita acceder a recursos locales, como un servidor de licencias de aplicaciones. Si necesita una, cree una nueva red virtual para implementar las instancias. No se admite la adición de máquinas virtuales de proceso intensivo a las redes virtuales de grupos de afinidad.
* **Servicio en la nube o conjunto de disponibilidad** : para usar la red RDMA de Azure, implemente las máquinas virtuales compatibles con RDMA en el mismo servicio en la nube (si utiliza el modelo de implementación clásica) o en el mismo conjunto de disponibilidad (si utiliza el modelo de implementación de Azure Resource Manager). Si utiliza Azure Batch, las máquinas virtuales compatibles con RDMA deben ser del mismo grupo.
* **Cambio de tamaño** : debido al hardware especializado que se usa en las instancias de proceso intensivo, solo se pueden cambiar dentro de la misma familia de tamaño (serie H o serie A de proceso intensivo). Por ejemplo, una máquina virtual de la serie H solo se puede cambiar de un tamaño de serie H a otro. Además, no se admite el cambio de tamaño de un tamaño que no sea de proceso intensivo a un tamaño que sí lo sea.  
* **Espacio de direcciones de red RDMA** : la red RDMA en Azure reserva el espacio de direcciones 172.16.0.0/16. Para ejecutar aplicaciones MPI en instancias implementadas en una red virtual Azure, asegúrese de que el espacio de direcciones de la red virtual no se superpone a la red RDMA.



<!--HONumber=Nov16_HO4-->


