# Información general
## [Redes virtuales](virtual-networks-overview.md)
## [Grupos de seguridad de red](virtual-networks-nsg.md)
## [Rutas definidas por el usuario y reenvío IP](virtual-networks-udr-overview.md)
## Direcciones IP
### [Resource Manager](virtual-network-ip-addresses-overview-arm.md)
### [Clásico](virtual-network-ip-addresses-overview-classic.md)
## [Emparejamiento de redes virtuales](virtual-network-peering-overview.md)
## Máquinas virtuales
### [Interfaces de red](virtual-network-network-interface-overview.md)
### [Resolución de nombres](virtual-networks-name-resolution-for-vms-and-role-instances.md)
## [Continuidad del negocio](virtual-network-disaster-recovery-guidance.md)
## [Precios](https://azure.microsoft.com/pricing/details/virtual-network)

# Introducción
## [Crear una red virtual](virtual-networks-create-vnet-arm-pportal.md)
## [Implementación de una VM en una red virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

# Procedimientos
## Planeamiento y diseño
### [Redes virtuales](virtual-network-vnet-plan-design-arm.md)
### [Grupos de seguridad de red](virtual-networks-nsg.md)

## Implementación
### Redes virtuales
#### [Portal](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [Plantilla](virtual-networks-create-vnet-arm-template-click.md)
#### [Portal (clásico)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (clásico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [CLI (clásico)](virtual-networks-create-vnet-classic-cli.md)

### Grupos de seguridad de red
#### [Portal](virtual-networks-create-nsg-arm-portal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [Plantilla](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (clásico)](virtual-networks-create-nsg-classic-ps.md)
#### [CLI (clásico)](virtual-networks-create-nsg-classic-cli.md)

### Rutas definidas por el usuario
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [Plantilla](virtual-network-create-udr-arm-template.md)
#### [PowerShell (clásico)](virtual-network-create-udr-classic-ps.md)
#### [CLI (clásico)](virtual-network-create-udr-classic-cli.md)

### Emparejamiento de redes virtuales de Azure
#### [Portal](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [Plantilla](virtual-networks-create-vnetpeering-arm-template-click.md)

### Máquinas virtuales

#### Direcciones IP públicas estáticas
##### [Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Plantilla](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (clásico)](virtual-networks-reserved-public-ip.md)

#### Direcciones IP privadas estáticas
##### [Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### [Portal (clásico)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (clásico)](virtual-networks-static-private-ip-classic-ps.md)
##### [CLI (clásico)](virtual-networks-static-private-ip-classic-cli.md)

#### Varias interfaces de red
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [CLI](virtual-network-deploy-multinic-arm-cli.md)
##### [Plantilla](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell (clásico)](virtual-network-deploy-multinic-classic-ps.md)
##### [CLI (clásico)](virtual-network-deploy-multinic-classic-cli.md)

#### [Varias direcciones IP](virtual-network-multiple-ip-addresses-powershell.md)

### Escenarios de conectividad
#### [De red virtual (VNet) a red virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [De red virtual (Resource Manager) a red virtual (clásica)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [De red virtual a red local (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [De red virtual a red local (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Arquitectura de red híbrida de alta disponibilidad](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Escenarios de seguridad
#### [Protección de redes con aplicaciones virtuales](virtual-network-scenario-udr-gw-nva.md)
#### [Red perimetral entre Internet y Azure](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Servicio en la nube y seguridad de red](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Red perimetral sencilla con grupos de seguridad de red](virtual-networks-dmz-nsg-asm.md)
##### [Red perimetral con firewall y grupos de seguridad de red](virtual-networks-dmz-nsg-fw-asm.md)
##### [Red perimetral con firewall, enrutamiento definido por el usuario y grupos de seguridad de red](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [Aplicación de ejemplo](virtual-networks-sample-app.md)

## Administrar
### Grupos de seguridad de red
#### [Portal](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [Registros](virtual-network-nsg-manage-log.md)
#### Solución de problemas
##### [Portal](virtual-network-nsg-troubleshoot-portal.md)
##### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Rutas para solución de problemas
#### [Portal](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### Máquinas virtuales
#### [Visión y modificación de nombres de host](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Movimiento de VM a una subred diferente](virtual-networks-move-vm-role-to-subnet.md)

# Referencia
## [Cmdlets de PowerShell (Resource Manager)](https://msdn.microsoft.com/library/mt163510(v=azure.300))
## [Cmdlets de PowerShell (clásico)](https://msdn.microsoft.com/library/mt270335(v=azure.300))
## [API (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [API (clásica)](https://msdn.microsoft.com/library/jj157182.aspx)
## [Blog de redes](http://azure.microsoft.com/blog/topics/networking)
## [Preguntas más frecuentes](virtual-networks-faq.md)

# Temas relacionados
## [Máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines)
## [Puerta de enlace de aplicaciones](https://azure.microsoft.com/documentation/services/application-gateway)
## [DNS de Azure](https://azure.microsoft.com/documentation/services/dns)
## [Administrador de tráfico](https://azure.microsoft.com/documentation/services/traffic-manager)
## [Equilibrador de carga](https://azure.microsoft.com/documentation/services/load-balancer)
## [VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway)
## [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute)



<!--HONumber=Nov16_HO2-->


