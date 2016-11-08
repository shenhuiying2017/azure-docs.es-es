Al crear una puerta de enlace de red virtual, debe especificar la SKU de la puerta de enlace que desea usar. Cuando se selecciona una SKU de puerta de enlace superior, se asignan más CPU y mayor ancho de banda de red a la puerta de enlace y. como resultado, esta admite un mayor rendimiento de red a la red virtual.

VPN Gateway puede usar las SKU siguientes:

* Básica
* Estándar
* HighPerformance

Al seleccionar un SKU, tenga en cuenta las siguientes limitaciones:

* Si desea utilizar un tipo de VPN PolicyBased, debe utilizar la SKU de puerta de enlace de nivel Básico. Las VPN PolicyBased (que anteriormente se denominaba enrutamiento estático) no se admiten en otra SKU.
* BGP no es compatible con la SKU de nivel Básico.
* Las configuraciones de la coexistencia de ExpressRoute-VPN Gateway no se admiten en la SKU de nivel Básico.

<!--HONumber=Oct16_HO2-->


