---
title: "Migración de recursos clásicos a Azure Resource Manager: información general | Microsoft Docs"
description: "Este artículo le guía a través de la migración compatible con la plataforma de recursos del modelo clásico a Azure Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 2c96a3ca5fd72a4a3c992206aeb93f201342dd6a
ms.openlocfilehash: aafaacea59c2c7fc463fb84207417d2c4e1d81ff


---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager
En este artículo, se describe cómo se permite la migración de recursos de infraestructura como servicio (IaaS) del modelo de implementación clásica al de Resource Manager. Se puede leer más información sobre [características y ventajas de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Se explica detalladamente cómo conectar los recursos de los dos modelos de implementación en su suscripción mediante las puertas de enlace de red virtual de sitio a sitio.

## <a name="goal-for-migration"></a>Objetivo para la migración
Resource Manager permite implementar aplicaciones complejas a través de plantillas, configura las máquinas virtuales mediante extensiones de máquina virtual e incorpora la administración de acceso y el etiquetado. Azure Resource Manager incluye una implementación escalable en paralelo para máquinas virtuales en conjuntos de disponibilidad. El nuevo modelo de implementación también proporciona administración del ciclo de vida para procesos, redes y almacenamiento por separado. Por último, también se centra en habilitar la seguridad de forma predeterminada con la aplicación de máquinas virtuales en una red virtual.

En Azure Resource Manager, se admiten casi todas las características del modelo de implementación clásica en cuanto a proceso, red y almacenamiento. Para sacar partido de las nuevas funcionalidades de Resource Manager, puede migrar las implementaciones existentes desde el modelo de implementación clásica.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Cambios en la automatización y las herramientas después de la migración
Como parte del proceso de migración de los recursos del modelo de implementación clásica al de Resource Manager, tiene que actualizar la automatización o las herramientas existentes a fin de asegurarse de que siguen funcionando después de la migración.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Implicaciones de la migración de los recursos de IaaS del modelo clásico al de Resource Manager
Antes de profundizar en los detalles, veamos la diferencia entre las operaciones en el plano de datos y en el plano de administración de los recursos de IaaS.

* *plano de administración* describe las llamadas que entran en el plano de administración o la API para modificar recursos. Por ejemplo, operaciones como crear una máquina virtual, reiniciar una máquina virtual y actualizar una red virtual con una nueva subred administran los recursos en ejecución. No afectan directamente a la conexión con las instancias.
* *plano de datos* (aplicación) describe el tiempo de ejecución de la aplicación en sí e incluye la interacción con instancias que no pasan por la API de Azure. Por ejemplo, el acceso a su sitio web o la extracción de datos desde una instancia de SQL Server o un servidor MongoDB en ejecución se consideran interacciones de aplicación o en el plano de datos. La copia de un blob desde una cuenta de almacenamiento y el acceso de una dirección IP pública a RDP o SSH en la máquina virtual también pertenecen al plano de datos. Estas operaciones mantienen la aplicación en ejecución en procesos, redes y almacenamiento.

> [!NOTE]
> En algunos escenarios de migración, la plataforma Azure se detiene, desasigna y reinicia las máquinas virtuales. Esto provoca un corto período de inactividad en el plano de datos.
>
>

## <a name="supported-scopes-of-migration"></a>Ámbitos admitidos de la migración
Existen tres ámbitos de migración que tienen como destino principalmente los procesos, las redes y el almacenamiento.

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migración de máquinas virtuales (no en una red virtual)
En el modelo de implementación de Resource Manager, la seguridad de las aplicaciones se aplica de forma predeterminada. En este modelo, todas las máquinas virtuales deben estar en una red virtual. La plataforma Azure reinicia (`Stop`, `Deallocate` y `Start`) las máquinas virtuales como parte de la migración. Tiene dos opciones para las redes virtuales:

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

## <a name="unsupported-features-and-configurations"></a>Configuraciones y características no admitidas
Actualmente, no se admiten determinadas características y configuraciones. En las secciones siguientes se describen nuestras recomendaciones a este respecto.

### <a name="unsupported-features"></a>Características no admitidas
Actualmente no se admiten las siguientes características. Opcionalmente, puede quitar estas configuraciones, migrar las máquinas virtuales y después volver a habilitar dichas configuraciones en el modelo de implementación de Resource Manager.

| Proveedor de recursos | Característica |
| --- | --- |
| Proceso |Discos de máquinas virtuales no asociados. |
| Proceso |Imágenes de máquina virtual. |
| Red |ACL de puntos de conexión. |
| Red |Puertas de enlace de red virtual (puertas de enlace de Azure ExpressRoute, puerta de enlace de aplicaciones). |
| Red |Redes virtuales que usan el emparejamiento de VNET. (Migre la red virtual a ARM y luego emparéjela). Más información sobre el [emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md). |
| Red |Perfiles del Administrador de tráfico. |

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
| Proceso |Extensiones de Azure Security Center con una red virtual que tiene VPN Gateway en conectividad de tránsito o una puerta de enlace de ExpressRoute con servidor DNS local |Azure Security Center instala automáticamente las extensiones en las máquinas virtuales para supervisar la seguridad y generar alertas. Si está habilitada la directiva de Azure Security Center en la suscripción, estas extensiones se suelen instalar automáticamente. La migración de la puerta de enlace de ExpressRoute no se admite actualmente y las puertas de enlace de VPN con conectividad de tránsito pierde el acceso local. La eliminación de la puerta de enlace de ExpressRoute o la migración de la puerta de enlace VPN con conectividad de tránsito provoca que el acceso de Internet a la cuenta de almacenamiento de VM se pierda cuando se realiza la confirmación de la migración. Si esto ocurre, la migración no continúa, ya que no se puede rellenar el blob de estado del agente invitado. Se recomienda deshabilitar la directiva de Azure Security Center en la suscripción 3 horas antes de continuar con la migración. |

## <a name="the-migration-experience"></a>Experiencia de migración
Antes de comenzar la experiencia de migración, se recomienda realizar lo siguiente:

* Asegúrese de que los recursos que desea migrar no usan las características o configuraciones no admitidas. Normalmente, la plataforma detecta estos problemas y genera un error.
* Si tiene máquinas virtuales que no están en una red virtual, se detendrán y desasignarán como parte de la operación de preparación. Si no desea perder la dirección IP pública, considere la posibilidad de reservar la dirección IP antes de comenzar la operación de preparación. Sin embargo, si las máquinas virtuales están en una red virtual, estas no se detienen ni desasignan.
* Planee la migración fuera del horario de actividad, para dar cabida a errores inesperados que surjan durante ella.
* Descargue la configuración actual de las máquinas virtuales mediante PowerShell, los comandos de la interfaz de la línea de comandos (CLI) o las API de REST para facilitar la validación una vez que finalice el paso de preparación.
* Actualice los scripts de automatización y operacionalización para controlar el modelo de implementación de Resource Manager antes de iniciar la migración. También tiene la opción de realizar las operaciones GET cuando los recursos se encuentren en el estado preparado.
* Evalúe las directivas de RBAC configuradas en los recursos de IaaS clásicos y cuente con un plan para cuando se haya completado la migración.

El flujo de trabajo de migración es el siguiente.

![Captura de pantalla que muestra el flujo de trabajo de migración](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Todas las operaciones que se describen en las siguientes secciones son idempotentes. Si tiene un problema diferente de una función no admitida o un error de configuración, se recomienda que vuelva a intentar la operación de preparación, anulación o confirmación. La plataforma Azure intenta la acción de nuevo.
>
>

### <a name="validate"></a>Validación
La operación de validación es el primer paso del proceso de migración. El objetivo de este paso es analizar en segundo plano los datos de los recursos en proceso de migración y devolverá un resultado correcto o con errores dependiendo de si los recursos son aptos o no.

Seleccione la red virtual o el servicio hospedado (si no es una red virtual) que desee validar para la migración.

* Si no se puede migrar el recurso, la plataforma de Azure indica todas las razones de que no sea compatible con la migración.

Al validar los servicios de almacenamiento, encontrará la cuenta migrada en un grupo de recursos que tiene el mismo nombre que su cuenta de almacenamiento con "-Migrated" anexado.  Por ejemplo, si la cuenta de almacenamiento se denomina "mystorage", encontrará el recurso habilitado para ARM en un grupo de recursos denominado "mystorage-Migrated" y contendrá una cuenta de almacenamiento denominada "mystorage".

### <a name="prepare"></a>Preparación
La operación de preparación es el secundo paso del proceso de migración. El objetivo de este paso es simular la transformación de los recursos de IaaS de clásicos a recursos de Resource Manager y presentarla en paralelo para que la visualice.

Seleccione la red virtual o el servicio hospedado (si no es una red virtual) que desee preparar para la migración.

* Si el recurso no puede realizar la migración, la plataforma de Azure detiene el proceso de migración y muestra el motivo de error de la operación de preparación.
* Si el recurso se puede migrar, en primer lugar la plataforma de Azure bloquea las operaciones en el plano de administración para los recursos en proceso de migración. Por ejemplo, no puede agregar un disco de datos a una máquina virtual en proceso de migración.

Después, la plataforma de Azure comienza la migración de los metadatos del modelo clásico al de Resource Manager para los recursos en migración.

Una vez completada la operación de preparación, tiene la opción de visualizar los recursos tanto en el modelo clásico como en el de Resource Manager. Para cada servicio en la nube en el modelo de implementación clásica, la plataforma de Azure crea un nombre de grupo de recursos con el patrón `cloud-service-name>-migrated`.

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
> Se trata de una operación idempotente. Si se produce un error, se recomienda que vuelva a intentar la operación. Si sigue sin poder completarla, cree una incidencia de soporte técnico o publique una entrada con la etiqueta ClassicIaaSMigration en el [foro de máquinas virtuales](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).
>
>

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
**¿Afecta este plan de migración a alguno de los servicios o las aplicaciones existentes que se ejecutan en máquinas virtuales de Azure?**

No. Las máquinas virtuales (clásicas) son servicios totalmente compatibles en la disponibilidad general. Puede seguir usando estos recursos para expandir su huella en Microsoft Azure.

**¿Qué ocurre con mis máquinas virtuales si por ahora no tengo planeado migrar?**

Las API y el modelo de recursos clásicos existentes no van a quedar en desuso. Queremos facilitar la migración, dadas las características avanzadas disponibles en el modelo de implementación de Resource Manager. Es muy recomendable que repase [algunos de los avances realizados](../azure-resource-manager/resource-manager-deployment-model.md) como parte de IaaS en Resource Manager.

**¿Qué supone este plan de migración para las herramientas existentes?**

La actualización de sus herramientas al modelo de implementación de Resource Manager es uno de los cambios más importantes que tendrá que considerar en sus planes de migración.

**¿Cuánto tiempo durará el tiempo de inactividad del plano de administración?**

Depende del número de recursos que se vayan a migrar. Para las implementaciones más pequeñas (unas decenas de máquinas virtuales), el proceso completo de migración debería tardar menos de una hora. Para implementaciones a gran escala (cientos de máquinas virtuales), la migración puede tardar unas horas.

**¿Puedo revertir el proceso después de que la migración de los recursos esté confirmada en Resource Manager?**

Puede anular la migración siempre que los recursos se encuentren en el estado preparado. No se puede revertir una vez que los recursos se hayan migrado correctamente mediante la operación de confirmación.

**¿Puedo revertir la migración si se produce un error en la operación de confirmación?**

No se puede anular la migración si se produce un error en la operación de confirmación. Todas las operaciones de migración, incluida la de confirmación, son idempotentes. Por consiguiente, se recomienda que vuelva a intentar la operación en breve. Si sigue apareciendo un error, cree una incidencia de soporte técnico o publique una entrada con la etiqueta ClassicIaaSMigration en el [foro de máquinas virtuales](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**¿Tengo que comprar otro circuito de ExpressRoute para usar IaaS en Resource Manager?**

No. Hace poco hemos habilitado la [transición de los circuitos ExpressRoute del modelo de implementación clásica al modelo de implementación de Resource Manager](../expressroute/expressroute-move.md). No hace falta que compre un nuevo circuito de ExpressRoute si ya tiene uno.

**¿Qué ocurre si había configurado las directivas de control de acceso basado en rol para mis recursos IaaS clásicos?**

Durante la migración, los recursos se transforman del modelo clásico al de Resource Manager. Por lo tanto, se recomienda que planee las actualizaciones de directiva de RBAC que deben producirse después de la migración.

**¿Qué pasa si estoy usando Azure Site Recovery o Copia de seguridad de Azure actualmente?**

Para migrar la máquina virtual habilitada para copia de seguridad, consulte [He realizado copias de seguridad de mis máquinas virtuales clásicas en el almacén de Backup. Ahora deseo migrar mis máquinas virtuales del modo clásico al modo de Resource Manager. ¿Cómo puedo hacer copias de seguridad de ellas en el almacén de servicios de recuperación?](../backup/backup-azure-backup-ibiza-faq.md)He realizado una copia de seguridad de mis VM clásicas en el almacén de copia de seguridad. Ahora deseo migrar mis máquinas virtuales del modo clásico al modo de Resource Manager.  ¿Cómo puedo realizar una copia de seguridad de ellas en el almacén de Servicios de recuperación?

**¿Puedo validar mi suscripción o mis recursos para ver si son aptos para la migración?**

Sí. En la opción de migración compatible con la plataforma, el primer paso para preparar la migración consiste en validar si los recursos pueden migrarse. Si se produce un error en la operación de validación, recibe todos los mensajes con todas las razones por las que no se puede completar la migración.

**¿Qué ocurre si se produce un error de cuota mientras preparo los recursos IaaS para la migración?**

Se recomienda que anule la migración y, a continuación, presente una solicitud de soporte técnico para que se aumenten las cuotas en la región donde vaya a migrar las máquinas virtuales. Una vez aprobada la solicitud de cuotas, puede empezar a ejecutar los pasos de la migración.

**¿Cómo se informa de un problema?**

Publique sus problemas y preguntas sobre la migración en nuestro [foro de máquinas virtuales](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows)con la palabra clave ClassicIaaSMigration. Se recomienda registrar todas sus preguntas en este foro. Si tiene un contrato de soporte técnico, también puede presentar una incidencia de soporte técnico.

**¿Qué hago si no me gustan los nombres que eligió la plataforma para los recursos durante la migración?**

Todos los recursos para los que se proporcionen nombres explícitamente en el modelo de implementación clásica se conservan durante la migración. En algunos casos, se crean recursos nuevos. Por ejemplo, se crea una interfaz de red para cada máquina virtual. Actualmente, no se admite la capacidad de controlar los nombres de estos recursos nuevos creados durante la migración. Registre sus votos para esta característica en el [foro de comentarios de Azure](http://feedback.azure.com).

**Aparece el mensaje *"La máquina virtual notifica el estado de agente general No preparado. Por lo tanto, la VM no se puede migra. Asegúrese de que el agente de VM notifica el estado de agente general como Preparado"* o *"la máquina virtual contiene la extensión cuyo estado no se notifica desde la VM. Por lo tanto, esta VM no se puede migrar."***

Este mensaje se recibe cuando la máquina virtual no tiene conectividad saliente a Internet. El agente de VM utiliza conectividad saliente para llegar a la cuenta de almacenamiento de Azure a fin de actualizar el estado del agente cada cinco minutos.

## <a name="next-steps"></a>Pasos siguientes
Ahora que comprende la migración de recursos de IaaS del modelo clásico al de Resource Manager, puede empezar a migrar los recursos.

* [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Migración de recursos de IaaS de la implementación clásica a Resource Manager con Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
* [Clonación de una máquina virtual clásica en Azure Resource Manager con scripts de PowerShell](virtual-machines-windows-migration-scripts.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Revisión de los errores más comunes en la migración](virtual-machines-migration-errors.md)



<!--HONumber=Jan17_HO4-->


