

## <a name="what-is-happening"></a>¿Qué pasa?

[El 3 de enero se divulgó](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html) una vulnerabilidad de seguridad basada en hardware de todo el sector. Mantener a los clientes seguros siempre es nuestra máxima prioridad y estamos adoptando medidas activas para que ningún cliente de Azure esté expuesto a estas vulnerabilidades.

Con la divulgación pública de la vulnerabilidad de seguridad, [aceleramos el tiempo de mantenimiento planeado](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) y empezamos a reiniciar automáticamente las máquinas virtuales que aún precisaban de actualizaciones.


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>¿Cómo puedo ver qué máquinas virtuales ya están actualizadas? 

Puede ver el estado de las máquinas virtuales y también si el reinicio se completó en la [lista de máquinas virtuales en Azure Portal](https://aka.ms/T08tdc). Las máquinas virtuales se muestran como "Ya actualizada" si se ha aplicado la actualización o "Programada" si la actualización sigue siendo necesaria. Si desea ver solo las máquinas virtuales "Programadas", consulte [Azure Service Health](https://portal.azure.com/).

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>¿Puedo saber exactamente cuándo se reiniciarán mis máquinas virtuales?

La mejor manera de obtener una alerta sobre el reinicio consiste en configurar [Eventos programados](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events). De esta forma, se envía una notificación indicando que, en 15 minutos, la máquina virtual dejará de funcionar debido al mantenimiento.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>¿Puedo volver a implementar manualmente ahora para realizar el mantenimiento necesario? 

No se puede garantizar que una máquina virtual que se ha vuelto a implementar se asigne a un host actualizado. Siempre que sea posible, el tejido de Azure intentará asignar máquinas virtuales a hosts que ya están actualizados. Puede que volver a implementar una máquina virtual conlleve la asignación a un host no actualizado, en cuyo caso puede estar sujeta a un segundo reinicio, forzado como parte del mantenimiento programado. Como resultado, no se recomiendan nuevas implementaciones manuales como una solución alternativa.

## <a name="how-long-will-the-reboot-take"></a>¿Cuánto tardará el reinicio? 

La mayoría de los reinicios tardan aproximadamente **treinta minutos**.

## <a name="does-the-guest-os-need-to-be-updated"></a>¿Debe actualizarse el sistema operativo invitado? 

Esta actualización de la infraestructura de Azure soluciona la vulnerabilidad divulgada en el nivel de hipervisor y no requiere una actualización de las imágenes de máquinas virtuales Windows o Linux. Sin embargo, como siempre, debe continuar aplicando los procedimientos recomendados de seguridad para las imágenes de máquina virtual. Póngase en contacto con el proveedor de sus sistemas operativos para obtener actualizaciones e instrucciones, según proceda. Ya se ha publicado una guía para los clientes de máquinas virtuales Windows Server, que está disponible [aquí](../articles/virtual-machines/windows/mitigate-se.md).

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>¿La resolución de esta actualización afectará al rendimiento?

La mayoría de los clientes de Azure no han observado un impacto perceptible en el rendimiento con esta actualización. Hemos trabajado para optimizar la CPU y la ruta de acceso de E/S de disco y no prevemos un impacto perceptible en el rendimiento después de haber aplicado la revisión. Unos pocos clientes podría experimentar algún impacto de rendimiento de red. Para solucionarlo, se puede usar Azure Accelerated Networking para [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) o [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), que es una funcionalidad gratuita disponible para todos los clientes de Azure.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>Sigo las recomendaciones para lograr alta disponibilidad, así que, ¿mi entorno seguirá teniendo alta disponibilidad durante el reinicio?

Sí, las máquinas virtuales implementadas en un conjunto de disponibilidad o en conjuntos de escalado de máquinas virtuales tienen la construcción de dominios de actualización (UD). Al realizar mantenimiento, Azure respeta la restricción de UD, por lo que no reiniciará máquinas virtuales de diferentes UD (del mismo conjunto de disponibilidad). Para más información sobre la alta disponibilidad, vea [Administración de la disponibilidad de las máquinas virtuales Windows en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) o [Administración de la disponibilidad de las máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>He diseñado mi plan de recuperación ante desastres y de continuidad empresarial con pares de regiones. ¿Se producirán reinicios en mis máquinas virtuales en pares de regiones al mismo tiempo?

Normalmente, los eventos de mantenimiento planeado de Azure se implementan en regiones emparejadas de una en una para minimizar el riesgo de interrupción en ambas regiones. Sin embargo, debido a la naturaleza urgente de esta actualización de seguridad, estamos implementando la actualización en todas las regiones simultáneamente.

## <a name="what-about-paas-services-on-azure"></a>¿Qué ocurre con los servicios PaaS de Azure?  

Los servicios de la Plataforma Azure, entre otros, web y móviles, servicios de datos, IoT y sin servidor, han solucionado la vulnerabilidad. No es necesaria ninguna acción para los clientes que usan estos servicios.

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>Intel publicó instrucciones adicionales el 22 de enero de 2018 relacionadas con las vulnerabilidades de seguridad.  ¿Estas instrucciones requerirán que Azure realice actividades de mantenimiento adicionales?  

Las [instrucciones actualizadas](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/) de Intel no afectan a las mitigaciones de Azure que se anunciaron el 3 de enero de 2018. No habrá ninguna actividad de mantenimiento adicional en las máquinas virtuales del cliente como resultado de esta nueva información.
 

## <a name="next-steps"></a>pasos siguientes

Para más información, consulte [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Proteger a los clientes de Azure de las vulnerabilidades de CPU).
