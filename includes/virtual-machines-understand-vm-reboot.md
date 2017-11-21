A veces, las máquinas virtuales de Azure se reinician sin motivo aparente, sin signos de que usted haya iniciado la operación. En este artículo se enumeran las acciones y los eventos que pueden hacer que las máquinas virtuales se reinicien y se proporciona información acerca de cómo evitar los problemas de reinicio inesperado o reducir su efecto.

## <a name="configure-the-vms-for-high-availability"></a>Configuración de las máquinas virtuales para que tengan alta disponibilidad
La mejor manera de proteger las aplicaciones que se ejecutan en Azure del reinicio de las máquinas virtuales y el tiempo de inactividad es configurar la alta disponibilidad de las máquinas virtuales.

Para proporcionar este nivel de redundancia a la aplicación, se recomienda agrupar dos máquinas virtuales, o más, en un conjunto de disponibilidad. Esta configuración garantiza que durante un evento de mantenimiento planeado o no planeado haya al menos una máquina virtual disponible que cumpla el 99,95 % del [Acuerdo de Nivel de Servicio de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Para más información acerca de los conjuntos de disponibilidad, consulte los siguientes artículos:

- [Administración de la disponibilidad de las máquinas virtuales Windows en Azure](../articles/virtual-machines/windows/manage-availability.md)
- [Configuración de un conjunto de disponibilidad para máquinas virtuales con Windows en el modelo de implementación clásica](../articles/virtual-machines/windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Información acerca de Resource Health 
Azure Resource Health es un servicio que expone el estado de los recursos individuales de Azure y proporciona instrucciones para solucionar problemas. En un entorno de nube donde no es posible acceder directamente a los servidores o a elementos de la infraestructura, el objetivo de Resource Health es reducir el tiempo que se tarda en solucionar los problemas. En concreto, el objetivo es reducir el tiempo que se tarda en determinar si la raíz del problema se encuentra en la aplicación o en un evento de la plataforma Azure. Para más información, consulte la [introducción al uso de Resource Health](../articles/resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Acciones y eventos que pueden hacer que la máquina virtual se reinicie

### <a name="planned-maintenance"></a>Mantenimiento planeado
Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la confiabilidad, el rendimiento y la seguridad de la infraestructura de host que subyace debajo de las máquinas virtuales. Muchas de esas actualizaciones, entre las que se incluyen las de conservación de memoria, se realizan sin que afecten a las máquinas virtuales ni a los servicios en la nube.

Sin embargo, algunas de ellas requieren un reinicio. En estos casos, las máquinas virtuales se apagan mientras se revisa la infraestructura y luego se reinician.

Para saber qué es el mantenimiento planeado de Azure y cómo afecta a la disponibilidad de las máquinas virtuales Linux, consulte los artículos que se enumeran aquí. En estos artículos se proporciona información general acerca del proceso del mantenimiento planeado de Azure y de cómo programarlo para reducir aún más sus efectos negativos.

- [Mantenimiento planeado de máquinas virtuales en Azure](../articles/virtual-machines/windows/planned-maintenance.md)
- [Programación del mantenimiento planeado en máquinas virtuales de Azure](../articles/virtual-machines/windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Actualizaciones de conservación de memoria   
Con esta clase de actualizaciones de Microsoft Azure, las máquinas virtuales en ejecución de los usuarios no se ven afectadas en absoluto. Muchas de estas actualizaciones son componentes o servicios que se pueden actualizar sin interferir con la instancia en ejecución. Algunas son actualizaciones de la infraestructura de la plataforma en el sistema operativo host que se pueden aplicar sin necesidad de que se reinicien las máquinas virtuales.

Estas actualizaciones que conservan memoria se realizan con tecnología que permite la migración en vivo local. Cuando se está actualizando, el estado de la máquina virtual es *En pausa*, que conserva la memoria RAM mientras el sistema operativo host subyacente recibe las actualizaciones y revisiones necesarias. La máquina virtual se reanuda en menos de 30 segundos después de haberse puesto en pausa. Tras la reanudación, el reloj de la máquina virtual se sincroniza automáticamente.

Debido al período breve de pausa, la implementación de actualizaciones mediante este mecanismo permite que las máquinas virtuales casi no resulten afectadas. Sin embargo, no todas las actualizaciones pueden implementarse de esta manera. 

Las actualizaciones de varias instancias (para las máquinas virtuales de un conjunto de disponibilidad) no se aplican a más de un dominio de actualización a la vez.

> [!NOTE]
> Con este método de actualización, las máquinas Linux que tengan versiones anteriores del kernel se ven afectadas por un fallo de kernel. Para evitar este problema, actualice el kernel a la versión 3.10.0-327.10.1 o a una posterior. Para más información, consulte [Una máquina virtual Linux de Azure en un kernel basado en 3.10, envía un pánico después de actualizar el nodo host](https://support.microsoft.com/help/3212236).     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>Acciones de reinicio o apagado iniciadas por el usuario
 
Si realiza un reinicio desde Azure Portal, Azure PowerShell, la interfaz de línea de comandos o la API de REST, el evento se encontrará en el [registro de actividad de Azure](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Si realiza la acción desde el sistema operativo de la máquina virtual, el evento se encontrará en los registros del sistema.

Otros escenarios que suelen provocar el reinicio de la máquina virtual incluyen varias acciones de cambio de configuración. Generalmente aparece un mensaje de advertencia que indica que la ejecución de una acción determinada provoca el reinicio de la máquina virtual. Por ejemplo, las operaciones de cambio de tamaño de máquina virtual, el cambio de la contraseña de la cuenta administrativa y la configuración de una dirección IP estática.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center y Windows Update
Azure Security Center supervisa diariamente las máquinas virtuales Windows y Linux por si faltan actualizaciones del sistema operativo. Security Center recupera una lista de actualizaciones críticas y de seguridad disponibles desde Windows Update o Windows Server Update Services (WSUS), dependiendo de qué servicio está configurado en una máquina virtual Windows. Security Center también comprueba las últimas actualizaciones de los sistemas Linux. Si falta una actualización del sistema en la máquina virtual, Security Center le recomienda que aplique las actualizaciones del sistema. Este proceso se controla a través de Security Center en Azure Portal. Tras la aplicación de algunas actualizaciones, puede ser necesario reiniciar la máquina virtual. Para más información, consulte [Aplicar actualizaciones del sistema en Azure Security Center](../articles/security-center/security-center-apply-system-updates.md).

Al igual que los servidores locales, Azure no inserta las actualizaciones de Windows Update en las máquinas virtuales de Microsoft Azure, ya que estas máquinas están diseñadas para que las administren los usuarios. No obstante, se recomienda dejar habilitada la configuración automática de Windows Update. La instalación automática de actualizaciones de Windows Update también puede provocar el reinicio tras su aplicación. Para más información, consulte [Windows Update: preguntas frecuentes](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Otras situaciones que afectan a la disponibilidad de la máquina virtual
Hay otros casos en los que Azure puede suspender activamente el uso de una máquina virtual. Antes de que se realice esta acción, se le notificará para que pueda resolver los problemas subyacentes. La vulneración de la seguridad y la expiración de las formas de pago son ejemplos de problemas que afectan a la disponibilidad de las máquinas virtuales.

### <a name="host-server-faults"></a>Errores en el servidor host 
La máquina virtual está hospedada en un servidor físico que se ejecuta en un centro de datos de Azure. El servidor físico ejecuta a un agente denominado al agente del host, además de otros componentes de Azure. Cuando estos componentes de software de Azure en el servidor físico dejan de responder, el sistema de supervisión desencadena un reinicio del servidor host para intentar la recuperación. La máquina suele estar disponible a los cinco minutos en el mismo host que antes.

Los errores en el servidor suelen deberse a errores de hardware, como un disco duro o una unidad de estado sólido. Azure supervisa continuamente si algo de esto sucede, identifica los errores subyacentes e implementa las actualizaciones una vez se ha implementado y probado la mitigación.

Puesto que algunos errores en el servidor host pueden ser específicos de ese servidor, para mejorar una situación en la que una máquina virtual se reinicia de manera repetitiva es preciso implementar la máquina virtual manualmente en otro servidor host. Esta operación se desencadena con la opción **volver a implementar** de la página de detalles de la máquina virtual o al detener y reiniciar la máquina virtual en Azure Portal.

### <a name="auto-recovery"></a>Recuperación automática
Si el servidor host no se pueda reiniciar por algún motivo, la plataforma Azure inicia una acción de recuperación automática para sacar el servidor host defectuoso de la rotación e investigarlo más a fondo. 

Todas las máquinas virtuales de dicho host se reubican automáticamente en otro servidor host que funcione correctamente. Este proceso suele tardar menos de 15 minutos. Para más información sobre el proceso de recuperación automática, consulte el artículo sobre la [recuperación automática de las máquinas virtuales](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Mantenimiento no planeado
En contadas ocasiones, el equipo de operaciones de Azure podría necesitar realizar actividades de mantenimiento para garantizar el buen estado general de la plataforma Azure. Este comportamiento podría afectar a la disponibilidad de las máquinas virtuales y suele generar la misma acción de recuperación automática que se ha descrito.  

El mantenimiento no planeado incluye las siguientes operaciones:

- Desfragmentación urgente de nodos
- Actualizaciones urgentes de los conmutadores de la red

### <a name="vm-crashes"></a>Bloqueos en la máquina virtual
Las máquinas virtuales podrían reiniciarse debido a problemas internos. La carga de trabajo o el rol que se ejecuta en la máquina virtual podría desencadenar una comprobación de errores en el sistema operativo invitado. Para determinar el motivo del bloqueo, consulte los registros de la aplicación y del sistema (máquinas virtuales Windows) y los registros de serie (máquinas virtuales Linux).

### <a name="storage-related-forced-shutdowns"></a>Apagados forzados relacionados con el almacenamiento
Las máquinas virtuales de Azure usan discos virtuales para el sistema operativo y el almacenamiento de datos que se hospeda en la infraestructura de Azure Storage. Cada vez que la disponibilidad o la conectividad entre la máquina virtual y los discos virtuales asociados se ven afectadas durante más de 120 segundos, la plataforma Azure realiza un apagado forzado de las máquinas virtuales para evitar que se dañen los datos. Una vez que se ha restaurado la conectividad del almacenamiento, las máquinas virtuales se vuelven a encender automáticamente. 

Las máquinas puede que solo permanezcan cinco minutos apagadas, pero también puede permanecer así mucho más tiempo. A continuación encontrará uno de los casos concretos que se asocia con apagados forzados relacionados con el almacenamiento: 

**Superación de los límites de E/S**

Las máquinas virtuales pueden apagarse temporalmente cuando las solicitudes de E/S se limitan en sincronía con el volumen de operaciones de entrada/salida por segundo (IOPS) al superar los límites de E/S del disco (el almacenamiento en disco estándar está limitado a 500 IOPS). Para mitigar este problema, utilice el seccionamiento del disco o configure el espacio de almacenamiento dentro de la máquina virtual invitada en función de la carga de trabajo. Para más información, consulte [Configuring Azure Virtual Machines for Optimal Storage Performance](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx) (Configuración de máquinas virtuales de Azure para que el rendimiento del almacenamiento sea óptimo).

Con Azure Premium Storage se consiguen límites de IOPS mayores (hasta 80 000 IOPS). Para más información, consulte el artículo sobre [Premium Storage de alto rendimiento](../articles/virtual-machines/windows/premium-storage.md).

### <a name="other-incidents"></a>Otros incidentes
En circunstancias excepcionales, un problema muy generalizado puede afectar a varios servidores de un centro de datos de Azure. En ese caso, el equipo de Azure envía notificaciones por correo electrónico a las suscripciones afectadas. Verá el estado de las interrupciones actuales y de los incidentes anteriores tanto en el [panel de estado de los servicios de Azure](https://azure.microsoft.com/status/) como en Azure Portal.
