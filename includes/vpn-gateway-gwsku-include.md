Al crear una puerta de enlace de red virtual, debe especificar la SKU de la puerta de enlace que desea usar. Cuando se selecciona una SKU de puerta de enlace superior, se asignan más CPU y mayor ancho de banda de red a la puerta de enlace y. como resultado, esta admite un mayor rendimiento de red a la red virtual.

VPN Gateway puede usar las SKU siguientes:

* Básica
* Estándar
* HighPerformance

La VPN Gateway no utiliza la SKU de puerta de enlace de UltraPerformance. Para obtener información acerca de la SKU de UltraPerformance, consulte la documentación de [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Al seleccionar un SKU, considere los siguientes factores:

* Si desea utilizar un tipo de VPN PolicyBased, debe utilizar la SKU de nivel Básico. Las VPN PolicyBased (que anteriormente se denominaba enrutamiento estático) no se admiten en otra SKU.
* BGP no es compatible con la SKU de nivel Básico.
* Las configuraciones de la coexistencia de ExpressRoute-VPN Gateway no se admiten en la SKU de nivel Básico.
* Las conexiones de VPN Gateway S2S activo/activo solo pueden configurarse en la SKU HighPerformance.



<!--HONumber=Nov16_HO2-->


