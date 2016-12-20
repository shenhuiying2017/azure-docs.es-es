## <a name="virtual-network-basics"></a>Conceptos básicos de las redes virtuales
### <a name="what-is-an-azure-virtual-network-vnet"></a>¿Qué es una red virtual de Azure?
Puede usar las redes virtuales para aprovisionar y administrar redes privadas virtuales (VPN) en Azure y, opcionalmente, vincular las redes virtuales con otras redes virtuales en Azure o con sus infraestructura de TI local para crear soluciones híbridas o entre entornos. Cada red virtual que se crea tiene su propio bloque CIDR y se puede vincular a otras redes locales y redes virtuales, siempre que los bloques CIDR no entren en conflicto. También tiene controles de configuración del servidor DNS para redes virtuales y segmentación de la red virtual en subredes.

Use las redes virtuales para:

* Crear una red virtual dedicada solo en la nube privada
  
    A veces no necesita una configuración entre entornos para su solución. Cuando se crea una red virtual, los servicios y las máquinas virtuales de la red virtual pueden comunicarse de forma directa y segura entre ellas en la nube. Esto mantiene el tráfico de forma segura en la red virtual, pero permite configurar conexiones de extremo para las máquinas virtuales y los servicios que requieren una comunicación a través de Internet como parte de la solución.
* Ampliar su centro de datos de forma segura
  
    Con las redes virtuales, puede crear VPN de sitio a sitio (S2S) tradicionales para ampliar la capacidad del centro de datos de forma segura. Las redes virtuales de sitio a sitio (S2S) usan IPSEC para proporcionar una conexión segura entre la puerta de enlace VPN corporativa y Azure.
* Habilitar escenarios de nube híbrida
  
    Las redes virtuales proporcionan la flexibilidad para admitir una variedad de escenarios de nube híbrida. Puede conectar de forma segura aplicaciones basadas en la nube a cualquier tipo de sistema local como grandes sistemas y sistemas Unix.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>¿Cómo sé si necesito una red virtual?
Visite [Información general sobre redes virtuales](../articles/virtual-network/virtual-networks-overview.md) para ver una tabla de decisiones que le ayude a decidir la mejor opción de diseño de red para usted.

### <a name="how-do-i-get-started"></a>¿Cómo empiezo?
Visite [la documentación de las redes virtuales](https://azure.microsoft.com/documentation/services/virtual-network/) para comenzar. Esta página tiene vínculos a pasos de configuración comunes, así como información que le ayudará a comprender los aspectos que debe tener en cuenta al diseñar la red virtual.

### <a name="what-services-can-i-use-with-vnets"></a>¿Qué servicios puedo usar con redes virtuales?
Las redes virtuales pueden usarse con diferentes servicios de Azure, como Aplicaciones web, máquinas virtuales y servicios en la nube (PaaS). Sin embargo, hay algunos servicios que no compatibles en una red virtual. Compruebe el servicio específico que desea usar y compruebe si es compatible.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>¿Puedo usar redes virtuales sin conectividad entre entornos?
Sí. Las redes virtuales se pueden usar sin emplear la conectividad de sitio a sitio. Esto es especialmente útil si desea ejecutar controladores de dominio y granjas de servidores de SharePoint en Azure.

## <a name="virtual-network-configuration"></a>Configuración de redes virtuales
### <a name="what-tools-do-i-use-to-create-a-vnet"></a>¿Qué herramientas debo usar para crear una red virtual?
Puede emplear las siguientes herramientas para crear o configurar una red virtual:

* Portal de Azure (para redes virtuales clásicas y del Administrador de recursos).
* Un archivo de configuración de red (netcfg; solo para redes virtuales clásicas). Consulte [Configuración de una red virtual con un archivo de configuración de red](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).
* PowerShell (para redes virtuales clásicas y del Administrador de recursos).
* CLI de Azure (para redes virtuales clásicas y del Administrador de recursos).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>¿Qué intervalos de direcciones puedo usar en mis redes virtuales?
Puede usar los intervalos de direcciones IP públicas y cualquier intervalo de direcciones IP definido en [RFC 1918](http://tools.ietf.org/html/rfc1918).

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>¿Puedo tener direcciones IP públicas en mis redes virtuales?
Sí. Para obtener más información acerca de los intervalos de direcciones IP públicas, consulte [Espacio de direcciones IP públicas en una red virtual (VNet)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Tenga en cuenta que sus direcciones IP públicas no estarán accesibles directamente desde Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>¿Existe algún límite en el número de subredes de mi red virtual?
No hay ningún límite en el número de subredes que se pueden usar en una red virtual. Todas las subredes deben estar contenidas completamente en el espacio de direcciones de red virtual y no deben solaparse entre sí.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?
Azure reserva algunas direcciones IP dentro de cada subred. La primera y la última dirección IP de las subredes están reservadas para la conformidad con el protocolo, junto con otras 3 direcciones usadas para los servicios de Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>¿Qué tamaños mínimo y máximo pueden tener las redes virtuales y las subredes?
La subred más pequeña que se admite es /29 y la mayor es /8 (según las definiciones de subred de CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>¿Puedo llevar mis VLAN a Azure mediante redes virtuales?
No. Las redes virtuales son superposiciones de nivel 3. Azure no admite ninguna semántica de nivel 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>¿Puedo especificar directivas de enrutamiento personalizadas en mis redes virtuales y subredes?
Sí. Puede usar el enrutamiento definido por usuario (UDR). Para obtener más información acerca de UDR, visite [Rutas definidas por el usuario y reenvío IP](../articles/virtual-network/virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>¿Las redes virtuales admiten la multidifusión o la difusión?
No. No se admite la multidifusión ni la difusión.

### <a name="what-protocols-can-i-use-within-vnets"></a>¿Qué protocolos puedo usar en las redes virtuales?
Dentro de las redes virtuales se admiten protocolos estándar basados en IP. Sin embargo, se bloquean la multidifusión, la difusión, los paquetes encapsulados IP en IP y los paquetes de encapsulación de enrutamiento genérico (GRE). Entre los protocolos estándar que funcionan se incluyen:

* TCP
* UDP
* ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>¿Puedo hacer ping a mis enrutadores predeterminados dentro de una red virtual?
No.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>¿Puedo usar tracert para diagnosticar la conectividad?
No.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>¿Puedo agregar subredes una vez creada la red virtual?
Sí. Se pueden agregar subredes a redes virtuales en cualquier momento siempre y cuando la dirección de la subred no forme parte de otra subred de la red virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>¿Puedo modificar el tamaño de la subred después de crearla?
Puede usar cmdlets de PowerShell o el archivo NETCFG para agregar, quitar, expandir o reducir una subred si no hay máquinas virtuales o servicios implementados en ella. También puede agregar, quitar, ampliar o reducir cualquier prefijo, siempre y cuando las subredes que contengan máquinas virtuales o servicios no se vean afectadas por el cambio.

### <a name="can-i-modify-subnets-after-i-created-them"></a>¿Puedo modificar subredes después de crearlas?
Sí. Puede agregar, quitar y modificar los bloques CIDR usados por una red virtual.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>¿Puedo conectarme a Internet si estoy ejecutando mis servicios en una red virtual?
Sí. Todos los servicios implementados dentro de una red virtual pueden conectarse a Internet. Cada servicio en la nube implementado en Azure tiene asignada una VIP direccionable de forma pública. Tendrá que definir extremos de entrada para los roles PaaS y extremos para las máquinas virtuales para que estos servicios puedan aceptar conexiones de Internet.

### <a name="do-vnets-support-ipv6"></a>¿Las redes virtuales admiten IPv6?
No. No se admite IPv6 con redes virtuales en este momento.

### <a name="can-a-vnet-span-regions"></a>¿Puede una red virtual abarcar varias regiones?
No. Una red virtual está limitada a una única región.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>¿Puedo conectar una red virtual a otra red virtual en Azure?
Sí. Puede crear una comunicación de red virtual a red virtual mediante API de REST o Windows PowerShell. También puede conectar redes virtuales mediante el emparejamiento de redes virtuales. [Aquí](../articles/virtual-network/virtual-network-peering-overview.md)encontrará información más detallada sobre el emparejamiento.

## <a name="name-resolution-dns"></a>resolución de nombres DNS
### <a name="what-are-my-dns-options-for-vnets"></a>¿Qué opciones de DNS hay para las redes virtuales?
Use la tabla de decisiones de la página [Resolución de nombres para las máquinas virtuales e instancias de rol](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ; le guiará por todas las opciones de DNS disponibles.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>¿Puedo especificar servidores DNS para una red virtual?
Sí. Puede especificar direcciones IP de servidor DNS en la configuración de la red virtual. Se aplicarán como los servidores DNS predeterminados para todas las máquinas virtuales de la red virtual.

### <a name="how-many-dns-servers-can-i-specify"></a>¿Cuántos servidores DNS puedo especificar?
Puede especificar hasta 12 servidores DNS.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>¿Puedo modificar mis servidores DNS después de haber creado la red?
Sí. Puede cambiar la lista de servidores DNS de la red virtual en cualquier momento. Si cambia la lista de servidores DNS, deberá reiniciar todas las máquinas virtuales de la red virtual para que puedan elegir el nuevo servidor DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>¿Qué es un DNS proporcionado por Azure? ¿Funciona con las redes virtuales?
Un DNS proporcionado por Azure es un servicio DNS multiempresa ofrecido por Microsoft. Azure registra todas las máquinas virtuales y las instancias de rol en este servicio. Este servicio proporciona la resolución de nombres mediante nombre de host para las máquinas virtuales y las instancias de rol contenidas en el mismo servicio en la nube y mediante FQDN para las máquinas virtuales y las instancias de rol en la misma red virtual.

> [!NOTE]
> En la actualidad hay una limitación a los 100 primeros servicios en la nube de la red virtual para la resolución de nombres entre inquilinos mediante DNS proporcionado por Azure. Si usa  su propio servidor DNS, esta limitación no es aplicable.
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm-service-basis"></a>¿Puedo invalidar mi configuración de DNS por máquina virtual o servicio?
Sí. Puede configurar los servidores DNS por servicio en la nube para invalidar la configuración de red predeterminada. Sin embargo, recomendamos usar DNS en toda la red siempre que sea posible.

### <a name="can-i-bring-my-own-dns-suffix"></a>¿Puedo usar mi propio sufijo DNS?
No. No puede especificar un sufijo DNS personalizado para sus redes virtuales.

## <a name="vnets-and-vms"></a>Redes virtuales y máquinas virtuales
### <a name="can-i-deploy-vms-to-a-vnet"></a>¿Puedo implementar máquinas virtuales en una red virtual?
Sí.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>¿Puedo implementar máquinas virtuales Linux en una red virtual?
Sí. Puede implementar cualquier distribución de Linux compatible con Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>¿Cuál es la diferencia entre una VIP pública y una dirección IP interna?
* Una dirección IP interna es una dirección IP que DHCP asigna a cada máquina virtual dentro de una red virtual. No está orientada al público. Si ha creado una red virtual, la dirección IP interna se asigna en el intervalo especificado en la configuración de subred de la red virtual. Aunque no tenga una máquina virtual, se asignará una dirección IP interna. La dirección IP interna permanecerá con la máquina virtual mientras esta dure, a menos que se desasigne.
* Una VIP pública es la dirección IP pública asignada al servicio en la nube o el equilibrador de carga. No se asigna directamente a la NIC de la máquina virtual. La dirección VIP permanece asignada al servicio en la nube hasta que todas las máquinas virtuales de ese servicio en la nube se desasignen o se eliminen. En ese momento, se libera.

### <a name="what-ip-address-will-my-vm-receive"></a>¿Qué dirección IP recibirá mi máquina virtual?
* **Dirección IP interna** : si implementa una máquina virtual en una red virtual, la máquina virtual recibe una dirección IP interna de un grupo de direcciones IP internas que especifique. Las máquinas virtuales se comunican dentro de las redes virtuales mediante direcciones IP internas. Aunque Azure asigna una dirección IP interna dinámica, puede solicitar una dirección estática para su máquina virtual. Para obtener más información acerca de direcciones IP internas, visite [Establecimiento de una dirección IP privada interna estática](../articles/virtual-network/virtual-networks-reserved-private-ip.md).
* **VIP** : la máquina virtual también está asociada a una VIP, aunque una VIP nunca se asigna directamente a la máquina virtual. Una VIP es una dirección IP pública que se puede asignar al servicio en la nube. Si lo desea, puede reservar a una VIP para el servicio en la nube.
* **ILPIP** : también puede configurar una dirección IP pública de nivel de instancia (ILPIP). Las ILPIP se asocian directamente a la máquina virtual en lugar de al servicio en la nube. Para obtener más información acerca de las ILPIP, visite [Dirección IP pública de nivel de instancia](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>¿Puedo reservar una dirección IP interna para una máquina virtual que crearé más adelante?
No. No puede reservar una dirección IP interna. Si hay una dirección IP interna disponible, el servidor DHCP puede asignarla a una máquina virtual o instancia de rol. Esa máquina virtual puede ser, o no, la que desea asignar a la dirección IP interna. Sin embargo, puede cambiar la dirección IP interna de una máquina virtual ya creada a una dirección IP interna disponible.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>¿Cambian las direcciones IP internas de las máquinas virtuales en una red virtual?
Sí. Las direcciones IP internas permanecen con la máquina virtual mientras dure su vigencia, a menos que la máquina se desasigne. Cuando una máquina virtual se desasigna, se libera la dirección IP interna a menos que haya definido una dirección IP interna estática para la máquina virtual. Si la máquina virtual simplemente se detiene (y no se pone en el estado **Detenido (Desasignado)**la dirección IP seguirá asignada a la máquina virtual.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>¿Puedo asignar manualmente direcciones IP a las NIC en máquinas virtuales?
No. No debe cambiar ninguna propiedad de la interfaz de las máquinas virtuales. Cualquier cambio puede provocar la pérdida de conectividad con la máquina virtual.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>¿Qué ocurre en mis direcciones IP si apago una máquina virtual?
Nada. Las direcciones IP (tanto la VIP pública como la dirección IP interna) permanecerán con el servicio en la nube o con la máquina virtual.

> [!NOTE]
> Si simplemente desea apagar la máquina virtual, no use el Portal de administración para ello. Por el momento, el botón de apagado desasignará la máquina virtual.
> 
> 

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>¿Puedo mover las máquinas virtuales de una subred a otra subred en una red virtual sin volver a implementarla?
Sí. Puede encontrar más información [aquí](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md):

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>¿Puedo configurar una dirección MAC estática para mi máquina virtual?
No. Una dirección MAC no se puede configurar de forma estática.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>¿Seguirá siendo la dirección MAC la misma en mi máquina virtual una vez que se ha creado?
Sí, la dirección MAC seguirá siendo la misma para una máquina virtual, aunque esta se haya detenido (desasignado) y reiniciado.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>¿Puedo conectarme a Internet desde una máquina virtual de una red virtual?
Sí. Todos los servicios implementados dentro de una red virtual pueden conectarse a Internet. Además, cada servicio en la nube implementado en Azure tiene asignada una VIP direccionable de forma pública. Tendrá que definir extremos de entrada para los roles PaaS y extremos para las máquinas virtuales para que estos servicios puedan aceptar conexiones de Internet.

## <a name="vnets-and-services"></a>Redes virtuales y servicios
### <a name="what-services-can-i-use-with-vnets"></a>¿Qué servicios puedo usar con redes virtuales?
Solo se admiten servicios de proceso en las redes virtuales. Los servicios de proceso están limitados a Servicios en la nube (roles web y de trabajo) y máquinas virtuales.

### <a name="can-i-use-web-apps-with-virtual-network"></a>¿¿Puedo usar Aplicaciones web con la red virtual?
Sí. Puede implementar Aplicaciones web dentro de una red virtual con ASE (Entorno del Servicio de aplicaciones). Además, Aplicaciones web puede conectarse con seguridad y tener acceso a recursos de la red virtual de Azure si ha realizado la configuración de la conexión de punto a sitio para la red virtual. Para obtener más información, consulte los temas siguientes:

* [Creación de Aplicaciones web en un entorno del Servicio de aplicaciones](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Integración de la red virtual con Aplicaciones web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)
* [Uso de la integración de la red virtual y de conexiones híbridas con Aplicaciones web](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)
* [Integración de su aplicación web con una red virtual de Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>¿Puedo implementar servicios en la nube con roles web y de trabajo (PaaS) en una red virtual?
Sí. Puede implementar servicios PaaS en las redes virtuales.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>¿Cómo se implementan roles PaaS en una red virtual?
Puede hacerlo si especifica el nombre de la red virtual y las asignaciones de rol/subred en la sección de configuración de red de la configuración del servicio. No es necesario actualizar ninguno de los archivos binarios.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>¿Puedo mover mis servicios dentro y fuera de las redes virtuales?
No. No se pueden mover los servicios dentro y fuera de las redes virtuales. Tendrá que eliminar y volver a implementar el servicio para moverlo a otra red virtual.

## <a name="vnets-and-security"></a>Redes virtuales y seguridad
### <a name="what-is-the-security-model-for-vnets"></a>¿Cuál es el modelo de seguridad de las redes virtuales?
as redes virtuales están completamente aisladas unas de otras y de otros servicios hospedados en la infraestructura de Azure. Una máquina virtual es un límite de confianza.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>¿Puedo definir ACL o NSG en mis redes virtuales?
No. No se puede asociar ACL o NSG a las redes virtuales. Sin embargo, se pueden definir ACL en los extremos de entrada para las máquinas virtuales que se han implementado en una red virtual y NSG se pueden asociar a subredes o NIC.

### <a name="is-there-a-vnet-security-whitepaper"></a>¿Hay notas del producto sobre la seguridad de las redes virtuales?
Sí. Puede descargarlas [aquí](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>API, esquemas y herramientas
### <a name="can-i-manage-vnets-from-code"></a>¿Puedo administrar redes virtuales mediante programación?
Sí. Existen API de REST para administrar redes virtuales y la conectividad entre entornos. Puede encontrar más información [aquí](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>¿Hay compatibilidad con las herramientas para redes virtuales?
Sí. Puede usar herramientas de la línea de comandos y PowerShell para diferentes plataformas. Puede encontrar más información [aquí](http://go.microsoft.com/fwlink/?LinkId=317721).



<!--HONumber=Nov16_HO3-->


