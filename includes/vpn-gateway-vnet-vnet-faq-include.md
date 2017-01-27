* Las redes virtuales pueden estar en la misma región de Azure o en regiones distintas (ubicaciones).
* Un servicio en la nube o un extremo de equilibrio de carga NO PUEDE abarcar varias redes virtuales aunque estas estén conectadas entre sí.
* La conexión simultánea de varias redes virtuales de Azure no requiere puertas de enlace de VPN locales, a menos que sea necesaria la conectividad entre locales.
* VNet a VNet admite la conexión de redes virtuales. No admite la conexión de máquinas virtuales o servicios en la nube que NO estén en una red virtual.
* La conexión entre dos redes virtuales requiere puertas de enlace de VPN de Azure con tipos de VPN RouteBased (antes denominado enrutamiento dinámico). 
* La conectividad de red virtual se puede usar de forma simultánea con VPN de varios sitios. Existe un máximo de 10 (puertas de enlace predeterminadas o estándar) o 30 (puertas de enlace de alto rendimiento) túneles VPN para una puerta de enlace de VPN de una red virtual que se conecta a otras redes virtuales o sitios locales.
* Los espacios de direcciones de las redes virtuales y de los sitios de red local no se pueden solapar. Los espacios de direcciones solapados provocarán un error al crear conexiones entre dos redes virtuales.
* Los túneles redundantes entre dos redes virtuales se admiten cuando la puerta de enlace de una red virtual está configurada como activa-activa.
* Todos los túneles de VPN de la red virtual comparten el ancho de banda disponible en la puerta de enlace de VPN de Azure y el mismo SLA de tiempo de actividad de puerta de enlace de VPN en Azure.
* El tráfico entre dos redes virtuales viaja por la red de Microsoft, no por Internet.
* El tráfico de red virtual a red virtual dentro de la misma región es gratuito para ambas direcciones. El tráfico de salida de red virtual a red virtual entre regiones se cobra según las tarifas de transferencia de datos de salida entre redes virtuales en función de las regiones de origen. Consulte la página [Precios de Puerta de enlace VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) para más detalles.



<!--HONumber=Jan17_HO3-->


