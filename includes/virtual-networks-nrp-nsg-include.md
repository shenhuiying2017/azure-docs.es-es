## Grupo de seguridad de red (NSG)
Un recurso de grupo de seguridad de red permite la creación de un límite de seguridad para las cargas de trabajo mediante la implementación de reglas de permiso y denegación. Estas reglas se pueden aplicar en el nivel de NIC (nivel de instancia de máquina virtual) o en el nivel de subred (grupo de máquinas virtuales).

Entre las principales propiedades de un recurso de grupo de seguridad de red se incluyen las siguientes:

- **Regla de seguridad**: un grupo de seguridad de red puede contener varias reglas de seguridad definidas. Cada regla puede permitir o denegar distintos tipos de tráfico.

### Regla de seguridad
Una regla de seguridad es un recurso secundario de un grupo de seguridad de red.

Entre las principales propiedades de una regla de seguridad se incluyen las siguientes:

- **Protocolo**: protocolo de red al que se aplica esta regla.
- **Intervalo de puerto de origen**: puerto de origen, o intervalo, desde 0 a 65535. Se puede utilizar un carácter comodín para que coincida con todos los puertos. 
- **Intervalo de puertos de destino**: puerto de destino, o intervalo, desde 0 a 65535. Se puede utilizar un carácter comodín para que coincida con todos los puertos.
- **Prefijo de dirección de origen**: intervalo de direcciones IP de origen. 
- **Prefijo de dirección de destino**: intervalo de direcciones IP de destino.
- **Acceso** – *permitir* o *denegar* tráfico.
- **Prioridad**: un valor entre 100 y 4096. El número de prioridad debe ser único para cada regla de la colección de reglas de seguridad. Cuanto menor sea el número de prioridad, mayor será la prioridad de la regla.
- **Dirección**: especifica si la regla se aplicará al tráfico en la dirección *entrante* o *saliente*. 

<!---HONumber=Sept15_HO4-->