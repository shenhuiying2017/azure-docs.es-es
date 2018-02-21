<a name="virtual-networking-limits-classic"></a>Los límites siguientes se aplican solo a los recursos de redes administrados a través del modelo de implementación clásico por suscripción.

| Recurso | Límite predeterminado | Límite máximo |
| --- | --- | --- |
| Redes virtuales |50 |100 |
| Sitios de red local |20  |ponerse en contacto con el servicio de soporte técnico |
| Servidores DNS por red virtual |20  |100 |
| Direcciones IP privadas por red virtual |4096 |4096 |
| Conexiones TCP concurrentes o flujo UDP por NIC de una máquina virtual o instancia de rol |500 KB |500 KB |
| Grupos de seguridad de red (NSG) |100 |200 |
| Reglas de NSG por NSG |200 |400 |
| Tablas de rutas definidas por el usuario |100 |200 |
| Rutas definidas por el usuario por tabla de ruta |100 |400 |
| Direcciones IP públicas (dinámicas) |5 |ponerse en contacto con el servicio de soporte técnico |
| Direcciones IP públicas reservadas |20  |ponerse en contacto con el servicio de soporte técnico |
| VIP pública por implementación |5 |ponerse en contacto con el servicio de soporte técnico |
| VIP privada (ILB) por implementación |1 |1 |
| Listas de control de acceso (ACL) de extremo |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Límites de redes - Azure Resource Manager
Los límites siguientes solo se aplican a los recursos de redes administrados a través de Azure Resource Manager por región y por suscripción.

| Recurso | Límite predeterminado | Límite máximo |
| --- | --- | --- |
| Redes virtuales |50 |1000 |
| Subredes por red virtual |1000 |10000 |
| Emparejamientos de redes virtuales por red virtual |10 |50 |
| Servidores DNS por red virtual |9 |25 |
| Direcciones IP privadas por red virtual |4096 |8192 |
| Direcciones IP privadas por interfaz de red |256 |1024 |
| Conexiones TCP concurrentes o flujo UDP por NIC de una máquina virtual o instancia de rol |500 KB |500 KB |
| Interfaces de red (NIC) |350 |20000 |
| Grupos de seguridad de red (NSG) |100 |5000 |
| Reglas de NSG por NSG |200 |1000 |
| Direcciones IP y rangos especificados para el origen o destino en un grupo de seguridad |2000 |4000 |
| Grupos de seguridad de aplicaciones |200 |500 |
| Grupos de seguridad de aplicaciones por configuración de IP, por NIC |10 |20  |
| Configuraciones de IP por grupo de seguridad de aplicaciones |1000 |4000 |
| Grupos de seguridad de aplicaciones que se pueden especificar en todas las reglas de seguridad de un grupo de seguridad de red |50 |100 |
| Tablas de rutas definidas por el usuario |100 |200 |
| Rutas definidas por el usuario por tabla de ruta |100 |400 |
| Direcciones IP públicas - dinámicas |(Básico) 60 |ponerse en contacto con el servicio de soporte técnico |
| Direcciones IP públicas - estáticas |(Básico) 20 |ponerse en contacto con el servicio de soporte técnico |
| Direcciones IP públicas - estáticas |(Estándar) 20 |ponerse en contacto con el servicio de soporte técnico |
| Certificados raíz de punto a sitio por VPN Gateway |20  |20  |

#### <a name="load-balancer"></a>Límites de Load Balancer

| Recurso | Límite predeterminado | Límite máximo |
| --- | --- | --- |
| Equilibradores de carga | 100 | 1000 |
| Reglas por recurso, básico | 150 | 250 |
| Reglas por recurso, estándar | 1250 | 1.500 |
| Reglas por configuración de dirección IP | 299 |299 |
| Configuraciones de direcciones IP de front-end, básico | 10 | 200 |
| Configuraciones de direcciones IP de front-end, estándar | 10 | 600 |
| Grupo de back-end, básico | 100, conjunto de disponibilidad único | - |
| Grupo de back-end, estándar | 1000, red virtual única | - |
| Puertos de alta disponibilidad, estándar | 1 por front-end interno | - |

Si necesita que los límites sean superiores a lo establecido de manera predeterminada, [póngase en contacto con el soporte técnico](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

