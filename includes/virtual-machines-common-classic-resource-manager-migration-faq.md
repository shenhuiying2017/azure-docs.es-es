# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Preguntas más frecuentes sobre la migración del método clásico al de Azure Resource Manager

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>¿Afecta este plan de migración a alguno de los servicios o las aplicaciones existentes que se ejecutan en máquinas virtuales de Azure? 

Nº Las máquinas virtuales (clásicas) son servicios totalmente compatibles en la disponibilidad general. Puede seguir usando estos recursos para expandir su huella en Microsoft Azure.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>¿Qué ocurre con mis máquinas virtuales si por ahora no tengo planeado migrar? 

Las API y el modelo de recursos clásicos existentes no van a quedar en desuso. Queremos facilitar la migración, dadas las características avanzadas disponibles en el modelo de implementación de Resource Manager. Es muy recomendable que repase [algunos de los avances realizados](../articles/azure-resource-manager/resource-manager-deployment-model.md) como parte de IaaS en Resource Manager.

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>¿Qué supone este plan de migración para las herramientas existentes? 

La actualización de sus herramientas al modelo de implementación de Resource Manager es uno de los cambios más importantes que tendrá que considerar en sus planes de migración.

## <a name="how-long-will-the-management-plane-downtime-be"></a>¿Cuánto tiempo durará el tiempo de inactividad del plano de administración? 

Depende del número de recursos que se vayan a migrar. Para las implementaciones más pequeñas (unas decenas de máquinas virtuales), el proceso completo de migración debería tardar menos de una hora. Para implementaciones a gran escala (cientos de máquinas virtuales), la migración puede tardar unas horas.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>¿Puedo revertir el proceso después de que la migración de los recursos esté confirmada en Resource Manager? 

Puede anular la migración siempre que los recursos se encuentren en el estado preparado. No se puede revertir una vez que los recursos se hayan migrado correctamente mediante la operación de confirmación.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>¿Puedo revertir la migración si se produce un error en la operación de confirmación? 

No se puede anular la migración si se produce un error en la operación de confirmación. Todas las operaciones de migración, incluida la de confirmación, son idempotentes. Por consiguiente, se recomienda que vuelva a intentar la operación en breve. Si sigue apareciendo un error, cree una incidencia de soporte técnico o publique una entrada con la etiqueta ClassicIaaSMigration en el [foro de máquinas virtuales](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>¿Tengo que comprar otro circuito de ExpressRoute para usar IaaS en Resource Manager? 

Nº Hace poco hemos habilitado la [transición de los circuitos ExpressRoute del modelo de implementación clásica al modelo de implementación de Resource Manager](../articles/expressroute/expressroute-move.md). No hace falta que compre un nuevo circuito de ExpressRoute si ya tiene uno.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>¿Qué ocurre si había configurado las directivas de control de acceso basado en rol para mis recursos IaaS clásicos? 

Durante la migración, los recursos se transforman del modelo clásico al de Resource Manager. Por lo tanto, se recomienda que planee las actualizaciones de directiva de RBAC que deben producirse después de la migración.

## <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>He realizado copias de seguridad de mis máquinas virtuales clásicas en un almacén de Backup. ¿Puedo migrar mis máquinas virtuales del modo clásico al modo de Resource Manager y protegerlos en un almacén de Recovery Services?

Los puntos de recuperación de máquinas virtuales <a name="vault">clásicas</a> en un almacén de Backup no migran automáticamente a un almacén de Recovery Services cuando se mueve la máquina virtual del modo clásico al modo de Resource Manager. Siga estos pasos para transferir las copias de seguridad de máquinas virtuales:

1. En el almacén de Backup, vaya a la pestaña **Elementos protegidos** y seleccione la máquina virtual. Haga clic en [Detener protección](../articles/backup/backup-azure-manage-vms.md#stop-protecting-virtual-machines). Deje la opción *Eliminar los datos de copia de seguridad asociados***desactivada**.
2. Elimine la extensión de instantánea o copia de seguridad de la máquina virtual.
3. Migre la máquina virtual del modo clásico al modo de Resource Manager. Asegúrese de que la información de almacenamiento y red correspondiente a la máquina virtual también se migra al modo de Resource Manager.
4. Cree un almacén de Recovery Services y configure la copia de seguridad de la máquina virtual migrada mediante la acción **Copia de seguridad** sobre el panel del almacén. Para más información sobre la copia de seguridad de una máquina virtual en un almacén de Recovery Services, consulte el artículo [Primer análisis: protección de máquinas virtuales con un almacén de Recovery Services](../articles/backup/backup-azure-vms-first-look-arm.md).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>¿Puedo validar mi suscripción o mis recursos para ver si son aptos para la migración? 

Sí. En la opción de migración compatible con la plataforma, el primer paso para preparar la migración consiste en validar si los recursos pueden migrarse. Si se produce un error en la operación de validación, recibe todos los mensajes con todas las razones por las que no se puede completar la migración.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>¿Qué ocurre si se produce un error de cuota mientras preparo los recursos IaaS para la migración? 

Se recomienda que anule la migración y, a continuación, presente una solicitud de soporte técnico para que se aumenten las cuotas en la región donde vaya a migrar las máquinas virtuales. Una vez aprobada la solicitud de cuotas, puede empezar a ejecutar los pasos de la migración.

## <a name="how-do-i-report-an-issue"></a>¿Cómo se informa de un problema? 

Publique sus problemas y preguntas sobre la migración en nuestro [foro de máquinas virtuales](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)con la palabra clave ClassicIaaSMigration. Se recomienda registrar todas sus preguntas en este foro. Si tiene un contrato de soporte técnico, también puede presentar una incidencia de soporte técnico.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>¿Qué hago si no me gustan los nombres que eligió la plataforma para los recursos durante la migración? 

Todos los recursos para los que se proporcionen nombres explícitamente en el modelo de implementación clásica se conservan durante la migración. En algunos casos, se crean recursos nuevos. Por ejemplo, se crea una interfaz de red para cada máquina virtual. Actualmente, no se admite la capacidad de controlar los nombres de estos recursos nuevos creados durante la migración. Registre sus votos para esta característica en el [foro de comentarios de Azure](http://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>¿Puedo migrar circuitos de ExpressRoute utilizados en las suscripciones con vínculos de autorización? 

Los circuitos de ExpressRoute que usan vínculos de autorización entre suscripciones no se pueden migrar automáticamente sin tiempo de inactividad. Contamos con instrucciones sobre cómo se pueden migrar mediante pasos manuales. Para conocer los pasos que deben darse y obtener más información, consulte [Migración de circuitos ExpressRoute y las redes virtuales asociadas del modelo de implementación clásica a Resource Manager](../articles/expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Aparece el mensaje *"La máquina virtual notifica el estado de agente general No preparado. Por lo tanto, la VM no se puede migra. Asegúrese de que el agente de VM notifica el estado de agente general como Preparado"* o *"la máquina virtual contiene la extensión cuyo estado no se notifica desde la VM. Por lo tanto, esta máquina virtual no se puede migrar."*

Este mensaje se recibe cuando la máquina virtual no tiene conectividad saliente a Internet. El agente de VM utiliza conectividad saliente para llegar a la cuenta de almacenamiento de Azure a fin de actualizar el estado del agente cada cinco minutos.
