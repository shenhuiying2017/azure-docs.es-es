## Equilibrador de carga
Se utiliza un equilibrador de carga cuando desea escalar sus aplicaciones. Entre los escenarios de implementación habituales se encuentran las aplicaciones que se ejecutan en varias instancias de máquina virtual. Las instancias de máquina virtual están representadas por un equilibrador de carga que ayuda a distribuir el tráfico de red a las diversas instancias.

![NIC en una sola máquina virtual](./media/resource-groups-networking/Figure5.png)

Los equilibradores de carga contienen los siguientes recursos secundarios:

- **Configuración de dirección IP de front-end**: un equilibrador de carga puede incluir una o varias direcciones IP de front-end, conocidas también como IP virtuales (VIP). Estas direcciones IP sirven como entrada para el tráfico. 
- **Grupo de direcciones de back-end**: se trata de direcciones IP asociadas a las NIC de máquina virtual a la que se distribuirá la carga.
- **Reglas de equilibrio de carga**: una propiedad de regla asigna una combinación de una determinada dirección IP de front-end y puerto a un conjunto compuesto por una combinación de direcciones IP de back-end y puerto. Con una única definición de un recurso de equilibrador de carga, puede definir varias reglas de equilibrio de carga, donde cada regla refleja una combinación de dirección IP de front-end y puerto y de dirección IP de back-end y puerto asociada con las máquinas virtuales. 
- **Sondeos**: los sondeos permiten realizar un seguimiento del estado de las instancias de máquina virtual. Si se produce un error en un sondeo de estado, la instancia de VM se sacará automáticamente de la rotación.
- **Reglas NAT entrantes**: las reglas NAT que definen el tráfico entrante que fluye a través de la dirección IP de front-end y se distribuye a la dirección IP de back-end.

<!---HONumber=Sept15_HO4-->