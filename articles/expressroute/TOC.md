# Información general
## [¿Qué es ExpressRoute?](expressroute-introduction.md)
## [Preguntas más frecuentes sobre ExpressRoute](expressroute-faqs.md)
## [Modelos de conectividad](expressroute-connectivity-models.md)
## [Circuitos y dominios de enrutamiento](expressroute-circuit-peerings.md)
## [Ubicaciones y asociados](expressroute-locations.md)
### [Proveedores por ubicación](expressroute-locations-providers.md)
### [Ubicaciones por proveedor](expressroute-locations.md)
## [Puertas de enlace de red virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md)

# Introducción
## [Requisitos previos](expressroute-prerequisites.md)
## [Flujos de trabajo](expressroute-workflows.md)
## [Requisitos de enrutamiento](expressroute-routing.md)
## [Requisitos QoS](expressroute-qos.md)
## [Movimiento de circuitos del portal clásico a Resource Manager](expressroute-move.md)

# Procedimientos
## Creación y modificación de un circuito
### [Portal de Azure](expressroute-howto-circuit-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-circuit-arm.md)
### [CLI de Azure](howto-circuit-cli.md)
## Creación y modificación de la configuración de emparejamiento
### [Portal de Azure](expressroute-howto-routing-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-routing-arm.md)
### [CLI de Azure](howto-routing-cli.md)
## Vinculación de una red virtual a un circuito ExpressRoute
### [Portal de Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-linkvnet-arm.md)
### [CLI de Azure](howto-linkvnet-cli.md)
## [Configuración de una VPN de sitio a sitio mediante emparejamiento de Microsoft](site-to-site-vpn-over-microsoft-peering.md)
## Configuración de una puerta de enlace de red virtual para ExpressRoute
### [Portal de Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-add-gateway-resource-manager.md)
## [Configuración de conexiones coexistentes de ExpressRoute y de sitio a sitio](expressroute-howto-coexist-resource-manager.md)
## Configuración de filtros de ruta para el emparejamiento de Microsoft
### [Portal de Azure](how-to-routefilter-portal.md)
### [Azure PowerShell](how-to-routefilter-powershell.md)
### [CLI de Azure](how-to-routefilter-cli.md)
## [Cambio de emparejamiento público a emparejamiento de Microsoft](how-to-move-peering.md)
## [Movimiento de un circuito del portal clásico a Resource Manager](expressroute-howto-move-arm.md)
## [Migración de redes virtuales asociadas del portal clásico a Resource Manager](expressroute-migration-classic-resource-manager.md)
## Configuración de un enrutador para ExpressRoute
### [Configuración de un enrutador](expressroute-config-samples-routing.md)
### [Ejemplos de configuración de enrutador para NAT](expressroute-config-samples-nat.md)
## [Configuración de Network Performance Monitor para ExpressRoute](how-to-npm.md)
## Artículos sobre el modelo de implementación clásica
### [Modificación de un circuito](expressroute-howto-circuit-classic.md)
### [Creación y modificación de la configuración de emparejamiento](expressroute-howto-routing-classic.md)
### [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)
### [Configuración de conexiones coexistentes de ExpressRoute y de sitio a sitio](expressroute-howto-coexist-classic.md)
### [Adición de una puerta de enlace a una red virtual](expressroute-howto-add-gateway-classic.md)

## Prácticas recomendadas
### [Procedimientos recomendados para seguridad de red y servicios en la nube](../best-practices-network-security.md)
### [Optimización del enrutamiento](expressroute-optimize-routing.md)
### [Enrutamiento asimétrica](expressroute-asymmetric-routing.md)
### [NAT para ExpressRoute](expressroute-nat.md)

## Solución de problemas
### [Comprobación de la conectividad de ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
### [Resolución problemas de rendimiento de la red](expressroute-troubleshooting-network-performance.md)
### [Restablecimiento de un circuito erróneo](reset-circuit.md)
### [Obtención de tablas ARP](expressroute-troubleshooting-arp-resource-manager.md)
### [Obtención de tablas ARP (clásico)](expressroute-troubleshooting-arp-classic.md)

# Referencia
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#expressroute)
## [CLI de Azure](/cli/azure/network/express-route)
## [REST](https://msdn.microsoft.com/library/azure/mt586720)
## [REST (clásico)](https://msdn.microsoft.com/library/azure/dn606310)

# Temas relacionados
## [Virtual Network](/azure/virtual-network/)
## [VPN Gateway](/azure/vpn-gateway/)
## [Máquinas virtuales](/azure/virtual-machines/)
## [Load Balancer](/azure/load-balancer/)
## [Traffic Manager](/azure/traffic-manager/)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=networking)
## [Casos prácticos](https://customers.microsoft.com/Pages/advancedsearch.aspx?mrmcproducts=More%20Products)
## [Blog de redes](https://azure.microsoft.com/blog/topics/networking/)
## [Precios](https://azure.microsoft.com/pricing/details/expressroute/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=expressroute)
## [CONTRATO DE NIVEL DE SERVICIO](https://azure.microsoft.com/support/legal/sla/)
## [Límites del servicio y la suscripción](../azure-subscription-service-limits.md?toc=%2fazure%2fexpressroute%2ftoc.json)
## [ExpressRoute para proveedores de soluciones en la nube (CSP)](expressroute-for-cloud-solution-providers.md)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=expressroute)
### [Conexión de una puerta de enlace de red virtual a un circuito](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit/)
### [Creación de una red virtual para ExpressRoute](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-virtual-network/)
### [Creación de una puerta de enlace de red virtual para ExpressRoute](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network/)
### [Creación de un circuito ExpressRoute](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit/)
### [Desarrollo de la infraestructura de red para la conectividad](https://go.microsoft.com/fwlink/p/?LinkId=615124)
### [Configuración del emparejamiento privado para el circuito](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit/)
### [Asociaciones híbridas: habilitación de escenarios locales](https://go.microsoft.com/fwlink/p/?LinkId=615125)
### [Configuración del emparejamiento de Microsoft para el circuito](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit/)
### [Configuración del emparejamiento público para el circuito](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit/)
