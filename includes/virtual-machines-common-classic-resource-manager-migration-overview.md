# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager
En este artículo, se describe cómo se permite la migración de recursos de infraestructura como servicio (IaaS) del modelo de implementación clásica al de Resource Manager. Se puede leer más información sobre [características y ventajas de Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md). Se explica detalladamente cómo conectar los recursos de los dos modelos de implementación en su suscripción mediante las puertas de enlace de red virtual de sitio a sitio.

## <a name="goal-for-migration"></a>Objetivo para la migración
Resource Manager permite implementar aplicaciones complejas a través de plantillas, configura las máquinas virtuales mediante extensiones de máquina virtual e incorpora la administración de acceso y el etiquetado. Azure Resource Manager incluye una implementación escalable en paralelo para máquinas virtuales en conjuntos de disponibilidad. El nuevo modelo de implementación también proporciona administración del ciclo de vida para procesos, redes y almacenamiento por separado. Por último, también se centra en habilitar la seguridad de forma predeterminada con la aplicación de máquinas virtuales en una red virtual.

En Azure Resource Manager, se admiten casi todas las características del modelo de implementación clásica en cuanto a proceso, red y almacenamiento. Para sacar partido de las nuevas funcionalidades de Resource Manager, puede migrar las implementaciones existentes desde el modelo de implementación clásica.

## <a name="supported-resources-for-migration"></a>Recursos que se admiten en la migración
Estos recursos de IaaS clásicos se admiten durante la migración

* Máquinas virtuales
* Conjuntos de disponibilidad
* Cloud Services
* Cuentas de almacenamiento
* Redes virtuales
* Puertas de enlace de VPN
* Puertas de enlace de ExpressRoute _(en la misma suscripción que solo Virtual Network)_
* Grupos de seguridad de red 
* Tablas de ruta 
* Direcciones IP reservadas 

## <a name="supported-scopes-of-migration"></a>Ámbitos admitidos de la migración
Hay 4 maneras diferentes de completar la migración de los recursos de proceso, red y almacenamiento. Dichas maneras son 

* Migración de máquinas virtuales (NO en una red virtual)
* Migración de máquinas virtuales (en una red virtual)
* Migración de cuentas de almacenamiento
* Recursos sin conectar (grupos de seguridad de red, tablas de ruta e IP reservadas)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migración de máquinas virtuales (NO en una red virtual)
En el modelo de implementación de Resource Manager, la seguridad de las aplicaciones se aplica de forma predeterminada. En este modelo, todas las máquinas virtuales deben estar en una red virtual. La plataforma Azure reinicia (`Stop`, `Deallocate` y `Start`) las máquinas virtuales como parte de la migración. Hay dos opciones para las redes virtuales a las que se Virtual Machines va a migrar:

* Puede solicitar que la plataforma cree una red virtual y migrar la máquina virtual a ella.
* Puede migrar la máquina virtual a una red virtual existente en Resource Manager.

> [!NOTE]
> En este ámbito de migración, es posible que haya un determinado período durante la migración en que no se permitan las operaciones en el plano de administración y el plano de datos.
>
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migración de máquinas virtuales (en una red virtual)
Para la mayoría de las configuraciones de máquina virtual, solo se migran los metadatos entre el modelo de implementación clásica y el de Resource Manager. Las máquinas virtuales subyacentes se ejecutan en el mismo hardware, en la misma red y con el mismo almacenamiento. Es posible que haya un determinado período durante la migración en que no se permitan las operaciones en el plano de administración. Sin embargo, el plano de datos sigue funcionando. Es decir, las aplicaciones que se ejecutan en máquinas virtuales (clásicas) no experimentan tiempo de inactividad durante la migración.

Actualmente no se admiten las siguientes configuraciones. Si se agrega compatibilidad en el futuro, es posible que algunas máquinas virtuales en esta configuración sufran tiempos de inactividad (pasen por operaciones de detención, desasignación y reinicio).

* Tiene más de un conjunto de disponibilidad en un único servicio en la nube.
* Tiene uno o varios conjuntos de disponibilidad y máquinas virtuales que no están en un conjunto de disponibilidad en un único servicio en la nube.

> [!NOTE]
> En este ámbito de migración, es posible que haya un determinado período durante la migración en que no se permitan las operaciones en el plano de administración. Para determinadas configuraciones descritas anteriormente, se producirá un tiempo de inactividad en el plano de datos.
>
>

### <a name="storage-accounts-migration"></a>Migración de cuentas de almacenamiento
Para lograr una migración sin problemas, se pueden implementar máquinas virtuales de Resource Manager en una cuenta de almacenamiento clásico. Con esta funcionalidad, los recursos de procesos y redes se pueden y se deben migrar con independencia de las cuentas de almacenamiento. Una vez migradas las máquinas virtuales y la red virtual, se deben migrar las cuentas de almacenamiento para completar el proceso de migración.

> [!NOTE]
> El modelo de implementación de Resource Manager carece del concepto de discos e imágenes de la implementación clásica. Cuando se migra la cuenta de almacenamiento, los discos e imágenes de la implementación clásica no están visibles en la pila de Resource Manager pero los discos duros virtuales de respaldo permanecen en la cuenta de almacenamiento.
>
>

### <a name="unattached-resources-network-security-groups-route-tables--reserved-ips"></a>Recursos sin conectar (grupos de seguridad de red, tablas de ruta e IP reservadas)
Los grupos de seguridad de red, las tablas de ruta y las IP reservadas que no están conectadas a ninguna máquina virtual y red virtual se pueden migrar de forma independiente.

<br>

## <a name="unsupported-features-and-configurations"></a>Configuraciones y características no admitidas
Actualmente, no se admiten determinadas características y configuraciones. En las secciones siguientes se describen nuestras recomendaciones a este respecto.

### <a name="unsupported-features"></a>Características no admitidas
Actualmente no se admiten las siguientes características. Opcionalmente, puede quitar estas configuraciones, migrar las máquinas virtuales y después volver a habilitar dichas configuraciones en el modelo de implementación de Resource Manager.

| Proveedor de recursos | Característica | Recomendación |
| --- | --- | --- |
| Proceso |Discos de máquinas virtuales no asociados. | Los blobs de VHD que hay detrás de estos discos se migrarán cuando lo haga la cuenta de almacenamiento |
| Proceso |Imágenes de máquina virtual. | Los blobs de VHD que hay detrás de estos discos se migrarán cuando lo haga la cuenta de almacenamiento |
| Red |ACL de puntos de conexión. | Quitar las ACL de los puntos de conexión y vuelva a intentar la migración. |
| Red |Red virtual con ExpressRoute Gateway y VPN Gateway  | Quite VPN Gateway antes de comenzar la migración y, después, vuelva a crearlo una vez que la migración se complete. Más información acerca de la [migración de ExpressRoute](../articles/expressroute/expressroute-migration-classic-resource-manager.md).|
| Red |ExpressRoute con vínculos de autorización  | Quite el circuito de ExpressRoute en la conexión de red virtual antes de comenzar la migración y, después, vuelva a crear la conexión una vez que se complete la migración. Más información acerca de la [migración de ExpressRoute](../articles/expressroute/expressroute-migration-classic-resource-manager.md). |
| Red |Application Gateway | Quite Application Gateway antes de comenzar la migración y, después, vuelva a crearlo una vez que la migración se complete. |
| Red |Redes virtuales que usan el emparejamiento de VNET. | Migrar Virtual Network a Resource Manager y, después, del mismo nivel. Más información acerca del [emparejamiento de VNET](../articles/virtual-network/virtual-network-peering-overview.md). | 

### <a name="unsupported-configurations"></a>Configuraciones no admitidas
Actualmente no se admiten las siguientes configuraciones.

| Servicio | Configuración | Recomendación |
| --- | --- | --- |
| Resource Manager |Control de acceso basado en rol para recursos clásicos |Puesto que el identificador URI de los recursos se modifica después de la migración, se recomienda planear las actualizaciones de directiva del control de acceso basado en rol que deben producirse después de la migración. |
| Proceso |Varias subredes asociadas con una máquina virtual |Actualice la configuración de la subred para que solo haga referencia a las subredes. |
| Proceso |Máquinas virtuales que pertenecen a una red virtual, pero no tienen una subred explícita asignada |Opcionalmente, puede eliminar la máquina virtual. |
| Proceso |Máquinas virtuales que tienen alertas, directivas de escalado automático |Se efectúa la migración y se descartan estos valores. Es muy recomendable evaluar el entorno antes de realizar la migración. Como alternativa, puede reconfigurar los valores de las alertas una vez completada la migración. |
| Proceso |Extensiones XML de máquina virtual (BGInfo 1.*, depurador de Visual Studio, Web Deploy y depuración remota) |ya que no es compatible. Se recomienda que quite estas extensiones de la máquina virtual para continuar la migración o se quitarán automáticamente durante el proceso. |
| Proceso |Diagnóstico de arranque con Almacenamiento premium |Deshabilite la característica de diagnósticos de arranque para las máquinas virtuales antes de continuar con la migración. Puede volver a habilitar los diagnósticos de arranque en la pila de Resource Manager una vez completada la migración. Además, se deben eliminar los blobs que se utilizan para los registros de captura de pantalla y de serie, por lo que ya no se cobra por los blobs. |
| Proceso |Servicios en la nube que contienen roles web y de trabajo |Actualmente no se admite. |
| Red |Redes virtuales que contienen máquinas virtuales y roles web y de trabajo |Actualmente no se admite. |
| Servicio de aplicaciones de Azure |Redes virtuales que contienen entornos del Servicio de aplicaciones |Actualmente no se admite. |
| HDInsight de Azure |Redes virtuales que contienen servicios de HDInsight |Actualmente no se admite. |
| Dynamics Lifecycle Services |Redes virtuales que contienen máquinas virtuales administradas por Dynamics Lifecycle Services |Actualmente no se admite. |
| Azure AD Domain Services |Redes virtuales que contienen servicios de dominio de Azure AD |Actualmente no se admite. |
| Azure RemoteApp |Redes virtuales que contienen implementaciones de Azure RemoteApp |Actualmente no se admite. |
| Administración de API de Azure |Redes virtuales que contienen implementaciones de Azure API Management |Actualmente no se admite. Para migrar la red virtual de IaaS, cambie la red virtual de la implementación de API Management que no es una operación de tiempo de inactividad. |
| Proceso |Extensiones de Azure Security Center con una red virtual que tiene VPN Gateway en conectividad de tránsito o una puerta de enlace de ExpressRoute con servidor DNS local |Azure Security Center instala automáticamente las extensiones en las máquinas virtuales para supervisar la seguridad y generar alertas. Si está habilitada la directiva de Azure Security Center en la suscripción, estas extensiones se suelen instalar automáticamente. La migración de la puerta de enlace de ExpressRoute no se admite actualmente y las puertas de enlace de VPN con conectividad de tránsito pierde el acceso local. La eliminación de la puerta de enlace de ExpressRoute o la migración de la puerta de enlace VPN con conectividad de tránsito provoca que el acceso de Internet a la cuenta de almacenamiento de VM se pierda cuando se realiza la confirmación de la migración. Si esto ocurre, la migración no continúa, ya que no se puede rellenar el blob de estado del agente invitado. Se recomienda deshabilitar la directiva de Azure Security Center en la suscripción 3 horas antes de continuar con la migración. |

