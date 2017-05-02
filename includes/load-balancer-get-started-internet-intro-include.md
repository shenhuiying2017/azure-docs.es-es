Azure Load Balancer es un equilibrador de carga de nivel 4 (TCP y UDP) que distribuye proporcionando una alta disponibilidad el tráfico entrante entre las instancias de servicio correctas de los servicios en la nube o las máquinas virtuales de un conjunto de carga equilibrada. Azure Load Balancer también pueden presentar prestar servicios en varios puertos, varias direcciones IP o ambos.

Puede configurar un equilibrador de carga para los siguientes objetivos:

* Equilibrar la carga del tráfico entrante de Internet a las máquinas virtuales (VM). En este escenario hacemos referencia a un equilibrador de carga como un [equilibrador de carga con conexión a Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* El tráfico de equilibrio de carga entre máquinas virtuales de una red virtual (VNet), las máquinas virtuales de los servicios en la nube o entre equipos locales y máquinas virtuales en una red virtual entre entornos. En este escenario hacemos referencia a un equilibrador de carga como un [equilibrador de carga interno (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Enrutar el tráfico externo a una instancia específica de máquina virtual.
