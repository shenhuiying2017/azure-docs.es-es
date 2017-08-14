## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Implicaciones de la migración de los recursos de IaaS del modelo clásico al de Resource Manager
Antes de profundizar en los detalles, veamos la diferencia entre las operaciones en el plano de datos y en el plano de administración de los recursos de IaaS.

* El *plano de administración o control* describe las llamadas que entran en el plano de administración o control, o la API para modificar los recursos. Por ejemplo, operaciones como crear una máquina virtual, reiniciar una máquina virtual y actualizar una red virtual con una nueva subred administran los recursos en ejecución. No afectan directamente a la conexión con las instancias.
* *plano de datos* (aplicación) describe el tiempo de ejecución de la aplicación en sí e incluye la interacción con instancias que no pasan por la API de Azure. Por ejemplo, el acceso a su sitio web o la extracción de datos desde una instancia de SQL Server o un servidor MongoDB en ejecución se consideran interacciones de aplicación o en el plano de datos. La copia de un blob desde una cuenta de almacenamiento y el acceso de una dirección IP pública a RDP o SSH en la máquina virtual también pertenecen al plano de datos. Estas operaciones mantienen la aplicación en ejecución en procesos, redes y almacenamiento.

![Captura de pantalla que muestra la diferencia entre el plano de administración o control, y el plano de datos](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)

> [!NOTE]
> En algunos escenarios de migración, la plataforma Azure se detiene, desasigna y reinicia las máquinas virtuales. Esto provoca un corto período de inactividad en el plano de datos.
>
>

## <a name="the-migration-experience"></a>Experiencia de migración
Antes de comenzar la experiencia de migración, se recomienda realizar lo siguiente:

* Asegúrese de que los recursos que desea migrar no usan las características o configuraciones no admitidas. Normalmente, la plataforma detecta estos problemas y genera un error.
* Si tiene máquinas virtuales que no están en una red virtual, se detendrán y desasignarán como parte de la operación de preparación. Si no desea perder la dirección IP pública, considere la posibilidad de reservar la dirección IP antes de comenzar la operación de preparación. Sin embargo, si las máquinas virtuales están en una red virtual, estas no se detienen ni desasignan.
* Planee la migración fuera del horario de actividad, para dar cabida a errores inesperados que surjan durante ella.
* Descargue la configuración actual de las máquinas virtuales mediante PowerShell, los comandos de la interfaz de la línea de comandos (CLI) o las API de REST para facilitar la validación una vez que finalice el paso de preparación.
* Actualice los scripts de automatización y operacionalización para controlar el modelo de implementación de Resource Manager antes de iniciar la migración. También tiene la opción de realizar las operaciones GET cuando los recursos se encuentren en el estado preparado.
* Evalúe las directivas de RBAC configuradas en los recursos de IaaS clásicos y cuente con un plan para cuando se haya completado la migración.

El flujo de trabajo de migración es el siguiente.

![Captura de pantalla que muestra el flujo de trabajo de migración](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Todas las operaciones que se describen en las siguientes secciones son idempotentes. Si tiene un problema diferente de una función no admitida o un error de configuración, se recomienda que vuelva a intentar la operación de preparación, anulación o confirmación. La plataforma Azure intenta la acción de nuevo.
>
>

### <a name="validate"></a>Validación
La operación de validación es el primer paso del proceso de migración. El objetivo de este paso es analizar en segundo plano los datos de los recursos en proceso de migración y devolverá un resultado correcto o con errores dependiendo de si los recursos son aptos o no.

Seleccione la red virtual o el servicio hospedado (si no es una red virtual) que desee validar para la migración.

* Si no se puede migrar el recurso, la plataforma de Azure indica todas las razones de que no sea compatible con la migración.

Al validar los servicios de almacenamiento, encontrará la cuenta migrada en un grupo de recursos que tiene el mismo nombre que su cuenta de almacenamiento con "-Migrated" anexado.  Por ejemplo, si la cuenta de almacenamiento se denomina "mystorage", encontrará el recurso habilitado para Azure Resource Manager en un grupo de recursos denominado "mystorage-Migrated" y contendrá una cuenta de almacenamiento denominada "mystorage".

### <a name="prepare"></a>Preparación
La operación de preparación es el secundo paso del proceso de migración. El objetivo de este paso es simular la transformación de los recursos de IaaS de clásicos a recursos de Resource Manager y presentarla en paralelo para que la visualice.

Seleccione la red virtual o el servicio hospedado (si no es una red virtual) que desee preparar para la migración.

* Si el recurso no puede realizar la migración, la plataforma de Azure detiene el proceso de migración y muestra el motivo de error de la operación de preparación.
* Si el recurso se puede migrar, en primer lugar la plataforma de Azure bloquea las operaciones en el plano de administración para los recursos en proceso de migración. Por ejemplo, no puede agregar un disco de datos a una máquina virtual en proceso de migración.

Después, la plataforma de Azure comienza la migración de los metadatos del modelo clásico al de Resource Manager para los recursos en migración.

Una vez completada la operación de preparación, tiene la opción de visualizar los recursos tanto en el modelo clásico como en el de Resource Manager. Para cada servicio en la nube en el modelo de implementación clásica, la plataforma de Azure crea un nombre de grupo de recursos con el patrón `cloud-service-name>-Migrated`.

> [!NOTE]
> No es posible seleccionar el nombre del grupo de recursos creado para los recursos migrados (es decir, "migrados"), pero una vez completada la migración, puede usar la característica de movimiento de Azure Resource Manager para mover recursos a cualquier grupo de recursos que desee. Para leer más sobre esto, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../articles/resource-group-move-resources.md).

Estas son dos pantallas que muestran el resultado después de una correcta operación de preparación. La primera pantalla muestra un grupo de recursos que contiene el servicio en la nube original. La segunda pantalla muestra el nuevo grupo de recursos "migrado" que contiene los recursos equivalentes de Azure Resource Manager.

![Captura de pantalla que muestra el servicio en la nube clásico de Portal](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Captura de pantalla que muestra los recursos de Azure Resource Manager del portal en la preparación](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

> [!NOTE]
> Las máquinas virtuales que no se encuentran en una red virtual clásica se detienen y desasignan en esta fase de la migración.
>
>

### <a name="check-manual-or-scripted"></a>Comprobación (manual o mediante scripts)
En este paso de comprobación, puede optar por usar la configuración que descargó antes para comprobar si esa migración parece correcta. También tiene la posibilidad de iniciar sesión en el portal y revisar las propiedades y los recursos para asegurarse de que los metadatos de la migración sean correctos.

Si están migrando una red virtual, no se reinicia la mayoría de la configuración de las máquinas virtuales. Para las aplicaciones en esas máquinas virtuales, puede confirmar si la aplicación sigue en funcionamiento.

Puede probar los scripts operacionales y de supervisión y automatización para ver si las máquinas virtuales operan del modo esperado y si los scripts actualizados funcionan correctamente. Tenga en cuenta que solo se admiten operaciones GET cuando los recursos se encuentran en el estado preparado.

No existirá ningún período establecido antes del cual deba confirmar la migración. Puede mantenerse el tiempo que desee en este estado. No obstante, el plano de administración queda bloqueado para estos recursos hasta que lleve a cabo la operación de anulación o confirmación.

Si ve algún problema, siempre puede anular la migración y volver al modelo de implementación clásica. Si lo hace, la plataforma de Azure abrirá las operaciones en el plano de administración en los recursos para que pueda reanudar las operaciones normales en esas máquinas virtuales en el modelo de implementación clásica.

### <a name="abort"></a>Anulación
Se trata de un paso opcional que puede usar para revertir los cambios realizados en el modelo de implementación clásica y detener la migración.

> [!NOTE]
> Esta operación no se puede ejecutar una vez que se haya desencadenado la operación de confirmación.     
>
>

### <a name="commit"></a>Confirmación
Después de finalizar la validación, puede confirmar la migración. Los recursos ya no aparecen en el modelo clásico y están disponibles solo en el modelo de implementación de Resource Manager. Los recursos migrados solo se pueden administrar en el nuevo portal.

> [!NOTE]
> Se trata de una operación idempotente. Si se produce un error, se recomienda que vuelva a intentar la operación. Si sigue sin poder completarla, cree una incidencia de soporte técnico o publique una entrada con la etiqueta ClassicIaaSMigration en el [foro de máquinas virtuales](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>
<br>
Este es un diagrama de flujo de los pasos durante el proceso de migración

![Captura de pantalla que muestra los pasos de migración](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-classic-to-azure-resource-manager-resources"></a>Traducción de recursos clásicos a recursos de Azure Resource Manager
Puede encontrar las representaciones del modelo clásico y de Resource Manager de los recursos en la tabla siguiente. Actualmente no se admiten otras funciones y recursos.

| Representación clásica | Representación de Resource Manager | Notas detalladas |
| --- | --- | --- |
| Nombre del servicio en la nube |Nombre DNS |Durante la migración, se crea un nuevo grupo de recursos para cada servicio en la nube con el patrón de nomenclatura `<cloudservicename>-migrated`. Este grupo de recursos contiene todos los recursos. El nombre del servicio en la nube se convierte en un nombre DNS que está asociado a la dirección IP pública. |
| Máquina virtual |Máquina virtual |Las propiedades específicas de la máquina virtual se migran sin cambios. Determinada información de osProfile, como el nombre de equipo, no se almacena en el modelo de implementación clásica y permanecerá vacía después de la migración. |
| Recursos de disco conectados a la máquina virtual |Implícitos discos conectados a la máquina virtual |Los discos no se modelan como recursos de nivel superior en el modelo de implementación de Resource Manager. Se migran como discos implícitos en la máquina virtual. Actualmente, se admiten solo los discos que están conectados a una VM. Las VM de Resource Manager pueden usar ahora cuentas de almacenamiento clásico, que permite que los discos se migren fácilmente sin necesidad de las actualizaciones. |
| Extensiones de máquina virtual |Extensiones de máquina virtual |Todas las extensiones de recursos, excepto las extensiones XML, se migran desde el modelo de implementación clásica. |
| Certificados de la máquina virtual |Certificados en Azure Key Vault |Si un servicio en la nube contiene certificados de servicio, la plataforma crea una nueva instancia de Azure Key Vault por cada servicio en la nube y mueve los certificados al almacén de claves. Las máquinas virtuales se actualizan para hacer referencia a los certificados del almacén de claves. <br><br> **NOTA:** No elimine el valor de keyvault ya que puede provocar que la VM entre en un estado de error. Estamos trabajando para mejorar las cosas en el back-end para que los Key Vault se puedan eliminar de forma segura o se muevan junto con la VM a una nueva suscripción. |
| Configuración de WinRM |Configuración de WinRM en osProfile |La configuración de Administración remota de Windows se traslada tal cual como parte de la migración. |
| Propiedad de conjunto de disponibilidad |Recurso de conjunto de disponibilidad | La especificación del conjunto de disponibilidad era una propiedad en la máquina virtual en el modelo de implementación clásica. Los conjuntos de disponibilidad se convierten en un recurso de nivel superior como parte de la migración. Las siguientes configuraciones no son compatibles: varios conjuntos de disponibilidad por servicio en la nube o uno o varios conjuntos de disponibilidad junto con máquinas virtuales que no están en ningún conjunto de disponibilidad de un servicio en la nube. |
| Configuración de red en una máquina virtual |Interfaz de red principal |La configuración de red en una máquina virtual se representa como el recurso de la interfaz de red principal después de la migración. En el caso de las máquinas virtuales que no están en una red virtual, la dirección IP interna cambia durante la migración. |
| Varias interfaces de red en una máquina virtual |Interfaces de red |Si una VM tiene varias interfaces de red asociadas, cada interfaz de red se convierte en un recurso de nivel superior como parte de la migración en el modelo de implementación de Resource Manager, junto con todas las propiedades. |
| Conjunto de punto de conexión de carga equilibrada |Equilibrador de carga |En el modelo de implementación clásica, la plataforma asignaba un equilibrador de carga implícito para cada servicio en la nube. Durante la migración, se crea un nuevo recurso de equilibrador de carga, y el conjunto de puntos de conexión con equilibrio de carga se convierte en reglas de equilibrador de carga. |
| Reglas NAT de entrada |Reglas NAT de entrada |Los puntos de conexión de entrada definidos en la VM se convierten en reglas de traducción de direcciones de red de entrada en el equilibrador de carga durante la migración. |
| Dirección VIP |Dirección IP pública con nombre DNS |La dirección IP virtual se convierte en una dirección IP pública y se asocia con el equilibrador de carga. Solo se puede migrar una dirección IP virtual si hay un punto de conexión de entrada asignado a ella. |
| Red virtual |Red virtual |La red virtual se migra con todas sus propiedades al modelo de implementación de Resource Manager. Se crea un nuevo grupo de recursos con el nombre `-migrated`. |
| Direcciones IP reservadas |Dirección IP pública con método de asignación estático |Las direcciones IP reservadas asociadas con el equilibrador de carga se migran junto con la migración del servicio en la nube o de la máquina virtual. Actualmente, no se admite la migración de direcciones IP reservadas no asociadas. |
| Dirección IP pública por máquina virtual |Dirección IP pública con método de asignación dinámico |La dirección IP pública asociada a la máquina virtual se convierte como un recurso de dirección IP público con el método de asignación establecido en estático. |
| Grupos de seguridad de red |Grupos de seguridad de red |Los grupos de seguridad de red asociados a una subred se clonan como parte de la migración al modelo de implementación de Resource Manager. El grupo de seguridad de red no se quita en el modelo de implementación clásica durante la migración. Sin embargo, las operaciones de plano de administración para el grupo de seguridad de red se bloquean cuando la migración está en curso. |
| Servidores DNS |Servidores DNS |Los servidores DNS asociados con la máquina virtual o una red virtual se migran como parte de la migración de recursos correspondiente junto con todas las propiedades. |
| Rutas definidas por el usuario |Rutas definidas por el usuario |Las rutas definidas por el usuario asociadas a una subred se clonan como parte de la migración al modelo de implementación de Resource Manager. Las rutas definidas por el usuario no se quitan en el modelo de implementación clásica durante la migración. Las operaciones de plano de administración para las rutas definidas por el usuario se bloquean cuando la migración está en curso. |
| Propiedad de reenvío de IP en una configuración de red de máquina virtual |Propiedad de reenvío de IP en la NIC |La propiedad de reenvío de IP en una máquina virtual se convierte en una propiedad en la interfaz de red durante la migración. |
| Equilibrador de carga con varias IP |Equilibrador de carga con varios recursos de dirección IP pública |Todas las direcciones IP públicas asociadas con el equilibrador de carga se convierten a un recurso de dirección IP pública y se asocian con el equilibrador de carga después de la migración. |
| Nombres DNS internos en la máquina virtual |Nombres DNS internos en la NIC |Durante la migración, se migran los sufijos DNS internos para la máquina virtual a una propiedad de solo lectura denominada "InternalDomainNameSuffix" en la NIC. El sufijo no cambia después de la migración y la resolución de la máquina virtual debería seguir funcionando como antes. |
| Puerta de enlace de red virtual |Puerta de enlace de red virtual |Las propiedades de puerta de enlace de red virtual se migran sin cambios. La VIP asociada a la puerta de enlace no cambia. |
| Sitio de red local |Puerta de enlace de red local |Las propiedades del sitio de red local se migran sin cambios a un nuevo recurso denominado puerta de enlace de red local. Este representa prefijos de direcciones locales y la dirección IP de la puerta de enlace remota. |
| Referencias de conexiones |Conexión |Las referencias de conectividad entre la puerta de enlace y el sitio de red local en la configuración de red se representan mediante un recurso recién creado denominado Conexión en el administrador de recursos después de la migración. Todas las propiedades de referencia de conectividad en archivos de configuración de red se copian sin cambios en el recurso Conexión recién creado. La conectividad entre redes virtuales en el modelo clásico se logra creando dos túneles IPsec a sitios de red local que representan las redes virtuales. Esto se transforma en tipo de conexión de red virtual a red virtual en el modelo del administrador de recursos sin necesidad de puertas de enlace de red local. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Cambios en la automatización y las herramientas después de la migración
Como parte del proceso de migración de los recursos del modelo de implementación clásica al de Resource Manager, tiene que actualizar la automatización o las herramientas existentes a fin de asegurarse de que siguen funcionando después de la migración.
