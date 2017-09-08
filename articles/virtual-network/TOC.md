# Información general
## [Redes virtuales](virtual-networks-overview.md)
## [Rutas definidas por el usuario y reenvío IP](virtual-networks-udr-overview.md)
## [Emparejamiento de redes virtuales](virtual-network-peering-overview.md)
## [Continuidad del negocio](virtual-network-disaster-recovery-guidance.md)
## [Preguntas más frecuentes](virtual-networks-faq.md)
## [Direccionamiento IP](virtual-network-ip-addresses-overview-arm.md)
## Clásico
### [Direccionamiento IP](virtual-network-ip-addresses-overview-classic.md)
### [Listas de control de acceso](virtual-networks-acl.md)

# Introducción
## [Creación de su primera red virtual](virtual-network-get-started-vnet-subnet.md)

# Procedimientos
## Planeamiento y diseño
### [Redes virtuales](virtual-network-vnet-plan-design-arm.md)
### [Grupos de seguridad de red](virtual-networks-nsg.md)

## Implementación
### [Redes virtuales](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [Plantilla](virtual-networks-create-vnet-arm-template-click.md)

### Grupos de seguridad de red
#### [Portal](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [Plantilla](virtual-networks-create-nsg-arm-template.md)
#### Clásico
##### [PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [CLI](virtual-networks-create-nsg-classic-cli.md)

### Rutas definidas por el usuario
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [Plantilla](virtual-network-create-udr-arm-template.md)
#### Clásico
##### [PowerShell](virtual-network-create-udr-classic-ps.md)
##### [CLI](virtual-network-create-udr-classic-cli.md)

### Emparejamiento de redes virtuales de Azure
#### [Mismo modelo de implementación, misma suscripción](virtual-network-create-peering.md)
#### [Mismo modelo de implementación, diferentes suscripciones](create-peering-different-subscriptions.md)
#### [Diferentes modelos de implementación, misma suscripción](create-peering-different-deployment-models.md)
#### [Diferentes modelos de implementación, diferentes suscripciones](create-peering-different-deployment-models-subscriptions.md)

### Máquinas virtuales
#### Creación de una máquina virtual con una dirección IP pública estática
##### [Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Plantilla](virtual-network-deploy-static-pip-arm-template.md)
##### Clásico
###### [PowerShell](virtual-networks-reserved-public-ip.md)

#### Creación de una máquina virtual con una dirección IP privada estática
##### [Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### Clásico
###### [Portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [CLI](virtual-networks-static-private-ip-classic-cli.md)

#### Creación de una máquina virtual con varias interfaces de red
##### [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### Clásico
###### [PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [CLI](virtual-network-deploy-multinic-classic-cli.md)

#### Creación de una máquina virtual con varias direcciones IP
##### [Portal de Azure](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [Plantilla](virtual-network-multiple-ip-addresses-template.md)

#### [Creación de una máquina virtual con redes aceleradas](virtual-network-create-vm-accelerated-networking.md)

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

### Clásico
#### [Red virtual](create-virtual-network-classic.md)
##### [Portal](virtual-networks-create-vnet-classic-pportal.md)
##### [PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [CLI](virtual-networks-create-vnet-classic-cli.md)

## Configuración
### Máquinas virtuales
#### [Adición o eliminación de interfaces de red](virtual-network-network-interface-vm.md)
#### [Resolución de nombres para las máquinas virtuales y servicios en la nube](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Optimización del rendimiento de la red](virtual-network-optimize-network-bandwidth.md)
#### [Visión y modificación de nombres de host](virtual-networks-viewing-and-modifying-hostnames.md)
### Clásico
#### Listas de control de acceso
##### [Portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [PowerShell](virtual-networks-acl-powershell.md)

## administración
### [Redes virtuales](virtual-network-manage-network.md)
#### [Subredes](virtual-network-manage-subnet.md)
#### [Emparejamientos](virtual-network-manage-peering.md)
#### Clásico
##### [Archivo de configuración de red](virtual-networks-using-network-configuration-file.md)
##### [Migración de un grupo de afinidad a una región](virtual-networks-migrate-to-regional-vnet.md)
### Grupos de seguridad de red
#### [Portal](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [Registros](virtual-network-nsg-manage-log.md)
### Interfaces de red (NIC)
#### [Creación, cambio o eliminación de interfaces de red](virtual-network-network-interface.md)
#### [Adición, cambio o eliminación de direcciones IP](virtual-network-network-interface-addresses.md)
### Máquinas virtuales
#### [Movimiento de VM a una subred diferente](virtual-networks-move-vm-role-to-subnet.md)
### [Direcciones IP públicas](virtual-network-public-ip-address.md)

## Solución de problemas
### Grupos de seguridad de red
#### [Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Rutas
#### [Portal](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Pruebas de rendimiento](virtual-network-bandwidth-testing.md)
### [No se pueden eliminar redes virtuales](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [Problemas de conectividad entre máquinas virtuales](virtual-network-troubleshoot-connectivity-problem-between-vms.md)

# Referencia
## [Ejemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=virtual-network)
## [PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [PowerShell (clásico)](/powershell/module/azure/)
## [CLI de Azure](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (clásico)](https://msdn.microsoft.com/library/jj157182.aspx)


# Temas relacionados
## [Máquinas virtuales](/azure/virtual-machines/)
## [Puerta de enlace de aplicaciones](/azure/application-gateway/)
## [DNS de Azure](/azure/dns/)
## [Administrador de tráfico](/azure/traffic-manager/)
## [Equilibrador de carga](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog de redes](http://azure.microsoft.com/blog/topics/networking)
## [Foro de redes](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Precios](https://azure.microsoft.com/pricing/details/virtual-network)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
