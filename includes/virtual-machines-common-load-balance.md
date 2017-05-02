

Hay dos niveles de equilibrio de carga disponibles para Servicios de infraestructura de Azure:

* **Nivel de DNS**: equilibrio de carga del tráfico para los diferentes servicios en la nube ubicados en distintos centros de datos, para diferentes sitios web de Azure ubicados en distintos centros de datos o para puntos de conexión externos. Esto se realiza mediante el Administrador de tráfico de Azure y el método de equilibrio de carga Round Robin.
* **Nivel de red**: equilibrio de carga del tráfico de Internet entrante para diferentes máquinas virtuales de un servicio en la nube o equilibrio de carga entre las máquinas virtuales de un servicio en la nube o una red virtual. Esto se realiza mediante el equilibrador de carga de Azure.

## <a name="traffic-manager-load-balancing-for-cloud-services-and-websites"></a>Equilibrio de carga del Administrador de tráfico para servicios en la nube y sitios web
El Administrador de tráfico permite controlar la distribución del tráfico de usuarios en los extremos especificados, que pueden incluir servicios en la nube, sitios web, sitios externos y otros perfiles del Administrador de tráfico. El Administrador de tráfico aplica un motor de directivas inteligente a las consultas del Sistema de nombres de dominio (DNS) para los nombres de dominio de los recursos de Internet. Los servicios en la nube o los sitios web pueden ejecutarse en centros de datos diferentes de todo el mundo.

Debe usar REST o Windows PowerShell para configurar los extremos externos o los perfiles de Administrador de tráfico como extremos.

El Administrador de tráfico usa tres métodos de equilibrio de carga para distribuir el tráfico:

* **Conmutación por error**: use este método cuando desee usar un punto de conexión principal para todo el tráfico, pero proporcione copias de seguridad en caso de que el punto de conexión deje de estar disponible.
* **Rendimiento**: use este método cuando tenga puntos de conexión en diferentes ubicaciones geográficas y desee solicitar a los clientes que usen el punto de conexión "más cercano" en términos de menor latencia.
* **Round Robin:** use este método cuando desee distribuir la carga entre un conjunto de servicios en la nube del mismo centro de datos o entre servicios en la nube o sitios web de diferentes centros de datos.

Para más información, consulte [Métodos de enrutamiento de tráfico de Traffic Manager](../articles/traffic-manager/traffic-manager-routing-methods.md).

En el diagrama siguiente se muestra un ejemplo del método de equilibrio de carga Round Robin para la distribución del tráfico entre diferentes servicios en la nube.

![equilibrio de carga](./media/virtual-machines-common-load-balance/TMSummary.png)

El proceso básico es el siguiente:

1. Un cliente de Internet requiere un nombre de dominio que corresponda a un servicio web.
2. DNS reenvía la solicitud de consulta de nombre al Administrador de tráfico.
3. El Administrador de tráfico elige el siguiente servicio en la nube de la lista Round Robin y devuelve el nombre de DNS. El servidor DNS del cliente de Internet resuelve el nombre en una dirección IP y lo envía al cliente de Internet.
4. El cliente de Internet se conecta con el servicio en la nube que ha elegido el Administrador de tráfico.

Para obtener más información, consulte [Administrador de tráfico](../articles/traffic-manager/traffic-manager-overview.md).

## <a name="azure-load-balancing-for-virtual-machines"></a>Equilibrio de carga de Azure para máquinas virtuales
Las máquinas virtuales que están en el mismo servicio en la nube o en la misma red virtual pueden comunicarse directamente entre sí mediante sus direcciones IP privadas. Los equipos y servicios fuera del servicio en la nube o la red virtual solo pueden comunicarse con las máquinas virtuales de un servicio en la nube o red virtual con un extremo configurado. Un extremo es una asignación de una dirección IP pública y un puerto a esa dirección IP privada y un puerto de una máquina virtual o rol web en un servicio en la nube de Azure.

El Equilibrador de carga de Azure distribuye de forma aleatoria un tipo específico de tráfico entrante entre varias máquinas virtuales o servicios en una configuración conocida como conjunto de carga equilibrada. Por ejemplo, puede extender la carga del tráfico de solicitudes web entre varios servidores web o roles web.

En el diagrama siguiente se muestra un extremo con equilibrio de carga para el tráfico web (sin cifrar) estándar que se comparte entre tres máquinas virtuales para el puerto TCP público y privado de 80. Estas tres máquinas virtuales se encuentran en un conjunto con equilibrio de carga.

![equilibrio de carga](./media/virtual-machines-common-load-balance/LoadBalancing.png)

Para obtener más información, consulte [Equilibrador de carga de Azure](../articles/load-balancer/load-balancer-overview.md). Para conocer los pasos para crear un conjunto con equilibrio de carga, consulte [Creación de un equilibrador de carga orientado a Internet en Resource Manager mediante PowerShell](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md).

Azure también puede equilibrar la carga en un servicio en la nube o una red virtual. Esto se conoce como equilibrio de carga interno y se puede usar de las siguientes maneras:

* Para equilibrar la carga entre servidores de distintos niveles de una aplicación multinivel (por ejemplo, entre los niveles web y de base de datos).
* Para equilibrar la carga para las aplicaciones de línea de negocio (LOB) hospedadas en Azure sin requerir hardware ni software adicional de equilibrador de carga.
* Para incluir servidores locales en el conjunto de equipos de cuyo tráfico se va a equilibrar la carga.

Simular al equilibrio de carga de Azure, el equilibrio de carga interno se facilita mediante la configuración de un conjunto interno de carga equilibrada.

En el diagrama siguiente se muestra un ejemplo de un extremo con carga equilibrada de una aplicación de línea de negocios (LOB) que se comparte entre tres máquinas virtuales en una red virtual entre entornos.

![equilibrio de carga](./media/virtual-machines-common-load-balance/LOBServers.png)

## <a name="load-balancer-considerations"></a>Consideraciones sobre el equilibrador de carga
De manera predeterminada, un equilibrador de carga está configurado para tener un tiempo de espera de 4 minutos en una sesión inactiva. Si la aplicación detrás de un equilibrador de carga deja inactiva una conexión durante más de 4 minutos y no tiene una configuración de conexión persistente, se terminará la conexión. Puede cambiar el comportamiento del equilibrador de carga para permitir un [tiempo de espera más prolongado para el equilibrador de carga de Azure](../articles/load-balancer/load-balancer-tcp-idle-timeout.md).

Otra consideración es el tipo del modo de distribución que admite el equilibrador de carga de Azure. Puede configurar la afinidad de dirección IP de origen (dirección de IP de origen, dirección IP de destino) o el protocolo IP de origen (dirección IP de origen, protocolo y dirección IP de destino). Revise el [modo de distribución del equilibrador de carga de Azure (afinidad de dirección IP de origen)](../articles/load-balancer/load-balancer-distribution-mode.md) para más información.

## <a name="next-steps"></a>Pasos siguientes
Para conocer los pasos para crear un conjunto de carga equilibrada, consulte [Creación del equilibrador de carga interno con PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md).

Para obtener más información, consulte [Equilibrio de carga interno](../articles/load-balancer/load-balancer-internal-overview.md).

