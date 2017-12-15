# Información general
## [Acerca de VPN Gateway](vpn-gateway-about-vpngateways.md)
## [Preguntas más frecuentes sobre VPN Gateway](vpn-gateway-vpn-faq.md)
## [Límites del servicio y la suscripción](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Introducción
## [Planeamiento y diseño de VPN Gateway](vpn-gateway-plan-design.md)
## [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md)
## [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md)
## [Acerca de los requisitos de cifrado](vpn-gateway-about-compliance-crypto.md)
## [Acerca de VPN Gateway y BGP](vpn-gateway-bgp-overview.md)
## [Acerca de la conectividad de alta disponibilidad](vpn-gateway-highlyavailable.md)
## [Acerca de las conexiones de punto a sitio](point-to-site-about.md)

# Procedimientos
## Configuración de conexiones de punto a sitio
### [Portal de Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [CLI de Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Portal de Azure clásico](vpn-gateway-howto-site-to-site-classic-portal.md)

## Configuración de conexiones de punto a sitio: autenticación nativa de certificados de Azure
### Configuración de una VPN de punto a sitio
#### [Portal de Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Portal de Azure clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Generación de certificados autofirmados
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Creación e instalación de los archivos de configuración de cliente de VPN](point-to-site-vpn-client-configuration-azure-cert.md)
### [Instalación de certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Configuración de conexiones de punto a sitio: autenticación RADIUS
### Configuración de una VPN de punto a sitio
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Creación e instalación de los archivos de configuración de cliente de VPN](point-to-site-vpn-client-configuration-radius.md)

## Configuración de conexiones de red virtual a red virtual
### [Portal de Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [CLI de Azure](vpn-gateway-howto-vnet-vnet-cli.md)
### [Portal de Azure clásico](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Configuración de una conexión de red virtual a red virtual entre distintos modelos de implementación
### [Portal de Azure](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Configuración de conexiones coexistentes de ExpressRoute y de sitio a sitio
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Configuración de varias conexiones de sitio a sitio
### [Portal de Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (modelo clásico)](vpn-gateway-multi-site.md)
## Conexión de varios dispositivos VPN basados en directivas
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Configuración de directivas de IPsec/IKE en las conexiones
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Configuración de conexiones activo-activo de alta disponibilidad
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Configuración de BGP para una instancia de VPN Gateway
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [CLI de Azure](bgp-how-to-cli.md)
## Configuración de la tunelización forzada
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (modelo clásico)](vpn-gateway-about-forced-tunneling.md)
## Modificación de la configuración de puerta de enlace de red local
### [Portal de Azure](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [CLI de Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Comprobación de una conexión de VPN Gateway](vpn-gateway-verify-connection-resource-manager.md)
## [Restablecimiento de una instancia de VPN Gateway](vpn-gateway-resetgw-classic.md)
## Eliminación de una instancia de VPN Gateway
### [Portal de Azure](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (modelo clásico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Configuración de una instancia de VPN Gateway (clásico)](vpn-gateway-configure-vpn-gateway-mp.md)
## [SKU de puerta de enlace (heredado)](vpn-gateway-about-skus-legacy.md)
## Configuración de dispositivos VPN de terceros
### [Introducción y configuración de Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Ejemplo: dispositivo Cisco ASA (IKEv2/no BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Migración del modelo clásico al de Resource Manager](vpn-gateway-classic-resource-manager-migration.md)
## Solución de problemas
### [Validación del rendimiento de la VPN en una red virtual](vpn-gateway-validate-throughput-to-vnet.md)
### [Configuración de dispositivos de firewall o VPN sugeridos por la comunidad](vpn-gateway-third-party-settings.md)
### [Problema de conexión de punto a sitio](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [La conexión de sitio a sitio se desconecta de forma intermitente](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [La conexión de sitio a sitio no puede conectarse](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 
### [Configuración y validación de las conexiones de red virtual o VPN](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# Referencia
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (modelo clásico)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (clásico)](https://msdn.microsoft.com/library/jj154113)
## [CLI de Azure](/cli/azure/network/vnet-gateway)

# Temas relacionados
## [Virtual Network](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [DNS de Azure](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Equilibrador de carga](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Foro](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Precios](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [CONTRATO DE NIVEL DE SERVICIO](https://azure.microsoft.com/support/legal/sla)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
