El enfoque de los puntos de conexión de Azure funciona de forma diferente en los modelos de implementación clásico y de Resource Manager. En el modelo de implementación de Resource Manager, ahora puede crear filtros de red que controlan el flujo de tráfico dentro y fuera de las máquinas virtuales. Estos filtros permiten crear complejos entornos de redes más allá de un punto de conexión simple como en el modelo de implementación clásica. En este artículo se proporciona información general sobre los grupos de seguridad de red y se explican sus diferencias con respecto a los puntos de conexión del modelo clásica, para lo que se crean reglas de filtrado y escenarios de implementación de ejemplo.

## <a name="overview-of-resource-manager-deployments"></a>Información general acerca de las implementaciones de Resource Manager
Los puntos de conexión del modelo de implementación clásico se reemplazan por grupos de seguridad de red y reglas de la lista de control de acceso (ACL). Estos son unos rápidos para implementar las reglas de ACL del grupo de seguridad de red:

* Crear un grupo de seguridad de red.
* Para permitir o denegar el tráfico, defina reglas de ACL del grupo de seguridad de red.
* Asigne el grupo de seguridad de red a una interfaz de red o a una subred de una red virtual.

Si también desea realizar también el enrutamiento de puertos, es preciso que coloque un equilibrador de carga delante de la máquina virtual y utilice reglas NAT. Estos serían los pasos rápidos para implementar un equilibrador de carga y reglas NAT:

* Cree un equilibrador de carga.
* Cree un grupo de back-end y agregue las máquinas virtuales a dicho grupo.
* Defina las reglas NAT para el enrutamiento de puertos requerido.
* Asigne las reglas NAT a las máquinas virtuales.

## <a name="network-security-group-overview"></a>Introducción a los grupo de seguridad de red
Los grupos de seguridad de red proporcionan una capa de seguridad que permite que determinados puertos y subredes accedan a las máquinas virtuales. Normalmente siempre hay un grupo de seguridad de red que proporciona este nivel de seguridad entre las máquinas virtuales y el mundo exterior. Los grupos de seguridad de red se pueden aplicar a una subred de una red virtual o a una interfaz de red específica de una máquina virtual. En lugar de crear reglas de ACL de punto de conexión, ahora se crean reglas de ACL del grupo de seguridad de red. Dichas reglas proporcionan un control mucho mayor que la mera creación de un punto de conexión que enrute un puerto determinado. Para más información, consulte [¿Qué es un grupo de seguridad de red?](../articles/virtual-network/virtual-networks-nsg.md)

> [!TIP]
> Los grupos de seguridad de red se pueden asignar a varias subredes o interfaces de red. No hay una asignación 1:1. Puede crear un grupo de seguridad de red con un conjunto común de reglas de ACL y aplicarlo a varias subredes o interfaces de red. Además, el grupo de seguridad de red se puede aplicar a los recursos de la suscripción (basado en los [controles de acceso basados en rol](../articles/active-directory/role-based-access-control-what-is.md)).

## <a name="load-balancers-overview"></a>Información general de los equilibradores de carga
En el modelo de implementación Clásico, Azure realizaría automáticamente toda la traducción de direcciones de red (NAT) y el enrutamiento de puertos reenvío en un servicio en la nube. Al crear un punto de conexión, debe especificar el puerto externo que se va a exponer junto con el puerto interno al que se va a dirigir el tráfico. Por sí mismos, los grupos de seguridad de red no realizan el mismo enrutamiento de puertos ni la misma NAT. 

Para que pueda crear reglas NAT para realizar dicho enrutamiento de puertos, cree una instancia de Azure Load Balancer en el grupo de recursos. Nuevamente, este equilibrador de carga es lo suficientemente pormenorizado como para aplicarlo solo a máquinas virtuales específicas, en caso de que sea necesario. Las reglas NAT de Azure Load Balancer funcionan junto a las reglas de ACL del grupo de seguridad de red para proporcionar mucha más flexibilidad y control de la que se lograba con los puntos de conexión del servicio en la nube. Para más información, consulte [Información general sobre Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md).

## <a name="network-security-group-acl-rules"></a>Reglas de ACL del grupo de seguridad de red
Las reglas de ACL permiten definir qué tráfico puede entrar y salir en una máquina virtual en función de determinados puertos, intervalos de puerto o protocolos. Las reglas se asignan a máquinas virtuales individuales o a una subred. La siguiente captura de pantalla es un ejemplo de reglas de ACL para un servidor web común:

![Lista de reglas de ACL del grupo de seguridad de red](./media/virtual-machines-common-endpoints-in-resource-manager/example-acl-rules.png)

Las reglas de ACL se aplican en función de la métrica de prioridad que especifique (cuanto mayor sea el valor, menor será la prioridad). Todos los grupos de seguridad de red tienen tres reglas predeterminadas diseñadas para controlar el flujo de tráfico de red de Azure, con un `DenyAllInbound` explícito como regla final. A las reglas de ACL predeterminadas se les da una prioridad baja, para que no interfieran con las reglas que se creen.

## <a name="assigning-network-security-groups"></a>Asignación de grupos de seguridad de red
Los grupos de seguridad de red se asignan a una subred o a una interfaz de red. Este enfoque le permite ser tan pormenorizado como sea necesario al aplicar las reglas de ACL solo a una máquina virtual específica o garantizar que un conjunto de común de reglas de ACL se aplican a todas las máquinas virtuales que forman parte de una subred:

![Aplicación de NSG a interfaces de red o subredes](./media/virtual-machines-common-endpoints-in-resource-manager/apply-nsg-to-resources.png)

El comportamiento del grupo de seguridad de red no cambia, independientemente de que se asigne a una subred o a una interfaz de red. En un escenario común de implementación, el grupo de seguridad de red está asignado a una subred, con el fin de garantizar el cumplimiento de todas las máquinas virtuales conectadas a dicha subred. Para más información, consulte la [aplicación de grupos de seguridad de red a recursos](../articles/virtual-network/virtual-networks-nsg.md#associating-nsgs).

## <a name="default-behavior-of-network-security-groups"></a>Comportamiento predeterminado de grupos de seguridad de red
En función de la forma y el momento en que cree un grupo de seguridad de red, se pueden crear reglas predeterminadas para máquinas virtuales de Windows que permitan el acceso RDP en el puerto TCP 3389. Las reglas predeterminadas para las máquinas virtuales Linux permiten el acceso de SSH al puerto TCP 22. Estas reglas de ACL automáticas se crean con las siguientes condiciones:

* Si crea una máquina virtual Windows a través del portal y acepta la acción predeterminada para crear un grupo de seguridad de red, se crea una regla de ACL para permitir el puerto TCP 3389 (RDP).
* Si crea una máquina virtual Linux a través del portal y acepta la acción predeterminada para crear un grupo de seguridad de red, se crea una regla de ACL para permitir el puerto TCP 22 (SSH).

Bajo las demás condiciones, estas reglas de ACL predeterminadas no se crean. No se puede conectar a la máquina virtual sin crear las reglas de ACL adecuadas. Estas condiciones incluyen las siguientes acciones comunes:

* Creación de un grupo de seguridad de red a través del portal como acción independiente de la creación de la máquina virtual.
* Creación de un grupo de seguridad de red mediante programación a través de PowerShell, la CLI de Azure, API de REST, etc.
* Creación de una máquina virtual y su asignación a un grupo de seguridad de red existente que aún no tenga definida la regla de ACL adecuada.

En todos los casos anteriores, tiene que crear reglas de ACL para la máquina virtual, con el fin de permitir las conexiones de administración remota adecuadas.

## <a name="default-behavior-of-a-vm-without-a-network-security-group"></a>Comportamiento predeterminado de una máquina virtual sin un grupo de seguridad de red
Es posible crear una máquina virtual sin necesidad de crear un grupo de seguridad de red. En estas situaciones, es posible conectarse a la máquina mediante RDP o SSH sin necesidad de crear ninguna regla de ACL. De forma similar, si se ha instalado un servicio web en el puerto 80, es posible acceder automáticamente a él de forma remota. La máquina virtual tiene todos los puertos abiertos.

> [!NOTE]
> Para poder establecer conexiones remotas, será preciso tener una dirección IP pública asignada a una máquina virtual. No tener un grupo de seguridad de red para la interfaz de red o la subred no expone la máquina virtual al tráfico externo. La acción predeterminada cuando se crea una máquina virtual a través del portal es crear una nueva dirección IP pública. En el caso de las demás formas de crear una máquina virtual como PowerShell, la CLI de Azure o una plantilla de Resource Manager, no se crea automáticamente una dirección IP pública, a menos que se solicite explícitamente. La acción predeterminada a través del portal también es crear un grupo de seguridad de red. Esta acción predeterminada significa que no debe terminar en una situación con una máquina virtual expuesta que no tenga el filtrado de redes instalado.

## <a name="understanding-load-balancers-and-nat-rules"></a>Descripción de las reglas NAT y los equilibradores de carga
En el modelo de implementación Clásico, se pueden crear puntos de conexión que también realizan el enrutamiento de puertos. Cuando crea una máquina virtual en el modelo de implementación Clásico, se crearán automáticamente reglas de ACL para RDP o SSH. Estas reglas no expondrán el puerto TCP 3389 ni el puerto TCP 22 respectivamente al mundo exterior. En su lugar, se expondrá un valor TCP con un valor alto que se asigna al puerto interno apropiado. También puede crear sus propias reglas de ACL de forma similar, como por ejemplo, puede exponer un servidor web en el puerto TCP 4280 al mundo exterior. Todas estas reglas de ACL y asignaciones de puerto se pueden ver en la siguiente captura de pantalla del portal clásico:

![Enrutamiento de puertos con punto de conexión del modelo de implementación Clásico](./media/virtual-machines-common-endpoints-in-resource-manager/classic-endpoints-port-forwarding.png)

Con los grupos de seguridad de red, la función de enrutamiento de puertos la controla un equilibrador de carga. Para más información, consulte los [equilibradores de carga de Azure](../articles/load-balancer/load-balancer-overview.md). El siguiente ejemplo muestra un equilibrador de carga con una regla NAT que realiza el enrutamiento de puertos del puerto TCP 4222 al puerto TCP 22 interno de una máquina virtual:

![Reglas NAT del equilibrador para el enrutamiento de puertos](./media/virtual-machines-common-endpoints-in-resource-manager/load-balancer-nat-rules.png)

> [!NOTE]
> Cuando se implementa un equilibrador de carga, lo habitual es no asignar una dirección IP pública a la propia máquina virtual. En su lugar, el equilibrador de carga tiene una dirección IP pública asignada. Aún así será preciso crear las reglas de ACL y el grupo de seguridad de red para definir el flujo de tráfico que entra y sale de la máquina virtual. Las reglas NAT del equilibrador de carga se usan simplemente para definir qué puertos se permiten a través del equilibrador de carga y cómo se distribuyen entre el máquinas virtuales de back-end. Por lo tanto, debe crear una regla NAT para que el tráfico fluya a través del equilibrador de carga. Para permitir que el tráfico llegue a la máquina virtual, cree una regla de ACL de grupo de seguridad de red.
