## Red virtual
Las redes virtuales (VNET) y las subredes ayudan a definir un límite de seguridad para las cargas de trabajo que se ejecutan en Azure. Una red virtual se caracteriza por una colección de espacios de direcciones, también denominados bloques CIDR.

Una subred es un recurso secundario de una red virtual, y ayuda a definir segmentos de espacios de direcciones dentro de un bloque CIDR usando prefijos de direcciones IP. Las NIC pueden agregarse a subredes y conectarse a máquinas virtuales, lo cual proporciona conectividad para varias cargas de trabajo.

![NIC en una sola máquina virtual](./media/resource-groups-networking/Figure4.png)

Entre las principales propiedades de un recurso de red virtual se incluyen las siguientes:

- Espacio de direcciones IP (bloque CIDR) 
- Nombre de red virtual
- subredes
- Servidores DNS

Una red virtual puede asociarse también con los siguientes recursos de red:

- Puerta de enlace de VPN

### Subred

Entre las principales propiedades de una subred se incluyen las siguientes:

- Prefijo de dirección IP
- Nombre de subred

Una subred puede asociarse también con los siguientes recursos de red:

- Grupo de seguridad de red

<!---HONumber=Sept15_HO4-->