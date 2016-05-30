<properties
	pageTitle="Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager"
	description="Este artículo lo guía por las funcionalidades del servicio de migración compatible con la plataforma. Administración de servicios para Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager

Ha pasado casi un año desde que se anunció la compatibilidad con máquinas virtuales en Azure Resource Manager. Puede leer más sobre los avances y las funcionalidades adicionales que admite aquí. Además, también se proporcionó orientación acerca de cómo mejorar la conexión y la coexistencia de recursos de dos modelos de implementación en su suscripción mediante las puertas de enlace de red virtual de sitio a sitio. En este artículo, nos gustaría mostrar cómo se ha hecho posible la migración de recursos IaaS del modelo de implementación clásica al de Resource Manager.

## ¿Cuál es el objetivo de la migración?

Estamos muy emocionados con la eficacia y la funcionalidad que ofrecen la experiencia y las API nuevas disponibles en máquinas virtuales. Creemos que esto va a cambiar en muchos sentidos la manera en que puede usar la nube. Con el lanzamiento del nuevo modelo, le permitimos implementar, administrar y supervisar servicios relacionados en un grupo de recursos. Resource Manager permite implementar aplicaciones complejas con plantillas, configura las máquinas virtuales mediante extensiones de máquina virtual e incorpora la administración de acceso y el etiquetado. También incluye una implementación escalable en paralelo para las máquinas virtuales en conjuntos de disponibilidad. Además, el nuevo modelo proporciona administración del ciclo de vida para procesos, redes y almacenamiento por separado. Por último, también se centra en habilitar la seguridad de forma predeterminada con la aplicación de máquinas virtuales en una red virtual.

En cuanto a las características, se admiten casi todas las del modelo de implementación clásico para procesos, redes y almacenamiento en Azure Resource Manager, con algunas excepciones que estamos esforzándonos por finalizar en los próximos meses. Además, no dejamos de mejorar la experiencia del usuario y de agregar más características al Portal de Azure para aunar las experiencias.

Gracias a esta nueva funcionalidad y al aumento de la base de implementaciones en el nuevo Azure Resource Manager, queremos permitir que los clientes migren las implementaciones existentes en el modelo clásico.

>[AZURE.NOTE] Con este anuncio, se presenta la versión preliminar pública del servicio de migración. Durante la versión preliminar pública, solo se recomienda migrar las cargas de trabajo que no sean de producción en la suscripción de Azure.

## Cambios en la automatización y las herramientas después de la migración

Tenga en cuenta que uno de los cambios importantes que deberá realizar como parte de la migración de los recursos del modelo clásico al de Resource Manager serán las actualizaciones a la automatización o las herramientas existentes para que sigan funcionando incluso después de la migración de los recursos.

## ¿Qué supone migrar los recursos IaaS del modelo clásico al de Resource Manager?

Antes de profundizar en los detalles, explicaremos brevemente la diferencia entre las operaciones en el plano de datos y en el plano de administración de los recursos IaaS. Comprender estas diferencias es fundamental, ya que esto explica cómo se ha planeado la compatibilidad con la migración.

- Plano de administración: describe las llamadas que entran en el plano de administración o la API para modificar recursos. Por ejemplo: crear una máquina virtual, reiniciar una máquina virtual, actualizar una red virtual con una nueva subred, etc. Todas estas operaciones administran los recursos en ejecución pero no afectan directamente a la conexión con las instancias.
- Plano de datos (aplicación): esto describe el "tiempo de ejecución" de la aplicación en sí e incluye la interacción con instancias que no pasan por la API de Azure. Por ejemplo, el acceso a su sitio web o la extracción de datos desde un servidor SQL Server o mongoDB en ejecución se consideran interacciones de aplicación o en el plano de datos. La copia de un blob desde una cuenta de almacenamiento y el acceso de una dirección IP pública a RDP o SSH en la máquina virtual también pertenecen al plano de datos. Estas operaciones mantienen la aplicación en ejecución en procesos, redes y almacenamiento.

>[AZURE.NOTE] En algunos escenarios de migración (más detalles en Configuraciones no admitidas), se detendrán, desasignarán y reiniciarán las máquinas virtuales, lo cual supondrá un breve tiempo de inactividad en el plano de datos.

## ¿Cuáles son los ámbitos de migración compatibles?

Durante la versión preliminar pública, se ofrecen dos ámbitos de migración, destinados principalmente a procesos y redes. La compatibilidad con la migración de cuentas de almacenamiento está planeada y se lanzará muy pronto. Aun así, para lograr una migración sin problemas, se ha permitido que las cuentas de almacenamiento clásicas contengan discos para las máquinas virtuales de Resource Manager. Puede encontrar más detalles sobre esto a continuación.

### Migración de máquinas virtuales (no en una red virtual)

En el modelo de implementación de Resource Manager, la seguridad de las aplicaciones se aplica de forma predeterminada. En consecuencia, en este modelo, todas las máquinas virtuales deben estar en una red virtual. Por lo tanto, se reiniciarán (detendrán, desasignarán e iniciarán) las máquinas virtuales como parte de la migración. Dispondrá de un par de opciones en lo que respecta a las redes virtuales:
- Puede solicitar que la plataforma cree una red virtual y migrar la máquina virtual a ella, o bien
- Puede migrar la máquina virtual a una red virtual existente en Resource Manager.

- Puede solicitar que la plataforma cree una red virtual y migrar la máquina virtual a ella, o bien
- Puede migrar la máquina virtual a una red virtual existente en Resource Manager.

>[AZURE.NOTE] En este ámbito de migración, es posible que haya un determinado período durante la migración en que no se permitan las operaciones en el "plano de administración" y el "plano de datos".

### Migración de máquinas virtuales (en una red virtual)

En este ámbito, para la mayoría de las configuraciones de máquina virtual, solo se migran los metadatos entre el modelo clásico y el de Resource Manager. Las máquinas virtuales subyacentes se ejecutan en el mismo hardware, en la misma red y con el mismo almacenamiento. Por lo tanto, cuando nos referimos a la migración de los metadatos del modelo clásico al de Resource Manager, es posible que las operaciones en el "plano de administración" no estén permitidas un determinado período durante la migración. Sin embargo, el plano de datos seguirá funcionando, es decir, las aplicaciones que se ejecutan en máquinas virtuales (clásicas) no experimentarán tiempo de inactividad durante la migración.

En este momento, no se admiten las siguientes configuraciones. Cuando se agregue compatibilidad para ellas en el futuro, es posible que algunas máquinas virtuales en esta configuración sufran tiempo de inactividad (detener, desasignar y reiniciar):

-	Si tiene más de un conjunto de disponibilidad en un único servicio en la nube
-	Si tiene uno o más conjuntos de disponibilidad y máquinas virtuales que no estén en un conjunto de disponibilidad en un único servicio en la nube

>[AZURE.NOTE] En este ámbito de migración, es posible que haya un determinado período durante la migración en que no se permitan las operaciones en el "plano de administración". Para determinadas configuraciones especiales, descritas antes, se producirá un tiempo de inactividad en el "plano de datos".

### Cuentas de almacenamiento y migración

Actualmente, no se admite la migración de cuentas de almacenamiento con esta versión preliminar pública. Sin embargo, la compatibilidad para la migración de cuentas de almacenamiento está planeada y se debería lanzar muy pronto. Existen dos aspectos importantes en lo que se refiere a la migración y su comportamiento con las cuentas de almacenamiento.

- Permitir que las máquinas virtuales de Resource Manager se implementen en una cuenta de almacenamiento clásica. Para permitir la migración sin problemas, se ha habilitado la funcionalidad de implementar máquinas virtuales de Resource Manager en una cuenta de almacenamiento clásica. Con esta funcionalidad, los recursos de procesos y redes se pueden migrar con independencia de las cuentas de almacenamiento.
- Procedimiento recomendado para la migración de cuentas de almacenamiento
	- Cuando se admita la migración de cuentas de almacenamiento, la plataforma migrará los recursos de procesos y redes de forma independiente de las cuentas de almacenamiento para hacer posible una experiencia perfecta.

## Configuraciones y características no admitidas

En este momento, no se admite un determinado conjunto de características y configuraciones. Aún estamos trabajando para agregar compatibilidad para ellas; en la siguiente sección, se ofrecen nuestra recomendación y nuestros planes en torno a ellas.

### Características no admitidas

Las características de la siguiente lista no se admiten en la versión preliminar pública. Opcionalmente, puede quitar estas configuraciones, migrar las máquinas virtuales y después volver a habilitarlas en el modelo de implementación de Resource Manager. La compatibilidad con la mayoría de estas características está planeada y se lanzará en cuanto esté disponible.

Proveedor de recursos | Característica
---------- | ------------
Proceso | Diagnósticos de arranque
Proceso | Discos de máquinas virtuales no asociados
Proceso | Imágenes de máquina virtual
Red | Direcciones IP reservadas no asociadas (si no están adjuntadas a una máquina virtual). Se admiten direcciones IP reservadas que estén adjuntadas a máquinas virtuales.
Red | Grupos de seguridad de red (NSG) no asociados (si no están adjuntados a una red virtual o una interfaz de red). Se admiten NSG a los que hagan referencia redes virtuales.
Red | Listas ACL de punto de conexión
Red | Puertas de enlace de red virtual (de sitio a sitio, ExpressRoute, de punto a sitio)
Almacenamiento | Cuentas de almacenamiento

### Configuraciones no admitidas

Las configuraciones de la siguiente lista no se admiten en la versión preliminar pública. Puede encontrar nuestras recomendaciones a continuación. La compatibilidad para algunas de estas configuraciones está planeada y se lanzará en cuanto esté disponible.

Servicio | Configuración | Recomendación
---------- | ------------ | ------------
Resource Manager | Control de acceso basado en rol para recursos clásicos | Puesto que se modifica el identificador URI de los recursos después de la migración. Se recomienda que planee con antelación las actualizaciones de la directiva de RBAC que deben producirse después de la migración.
Proceso | Varias subredes asociadas con una máquina virtual | Debería actualizar la configuración de la subred para que solo haga referencia a la subred.
Proceso | Máquinas virtuales que pertenecen a una red virtual, pero no tienen una subred explícita asignada. | Opcionalmente, puede eliminar la máquina virtual. La compatibilidad con esta característica está actualmente planeada.
Proceso | Máquinas virtuales que tienen alertas, directivas de escalado automático | En este momento, la migración se completará, pero estas configuraciones se eliminarán. Por lo tanto, es muy recomendable evaluar el entorno antes de realizar la migración. Como alternativa, también puede reconfigurar los valores de alertas una vez completada la migración.
Proceso | Extensiones de máquina virtual de XML (depurador de VS, WebDeploy y RemoteDebug) | No se admitirán. Se recomienda que quite estas extensiones de la máquina virtual para proceder con la migración.
Proceso | Servicios en la nube que contienen roles web y de trabajo | Esto no se admite actualmente y está en planeamiento.
Red | Redes virtuales que contienen máquinas virtuales y roles web y de trabajo | Esto no se admite actualmente y está en planeamiento.
Red | Subredes que contienen espacios en el nombre | La compatibilidad con esta característica está planeada.
Servicios de aplicaciones | Red virtual que contiene entornos del Servicio de aplicaciones | Esto no se admite actualmente y está en planeamiento.
Servicios de HDInsight | Red virtual que contiene servicios de HDInsight | Esto no se admite actualmente y está en planeamiento.
Dynamics Lifecycle Services | Red virtual que contiene máquinas virtuales administradas por Dynamics Lifecycle Services | Esto no se admite actualmente y está en planeamiento.

## Experiencia de migración

Antes de comenzar la experiencia de migración, se recomienda encarecidamente que siga estos pasos:

1. Asegúrese de que los recursos que ha planeado para la migración no utilicen características o configuraciones no admitidas. En la mayoría de los casos, la plataforma detecta estos problemas y genera un error.
2. Si tiene máquinas virtuales que no están en una red virtual, se detendrán y desasignarán como parte de la operación de preparación. De modo que, si no desea perder la dirección IP pública, considere reservar la dirección IP antes de que se desencadene la preparación. Sin embargo, si las máquinas virtuales están en una red virtual, no se detendrán y desasignarán.
3. Azure recomienda no tratar de migrar los recursos de producción en este momento.
4. Planee la migración fuera del horario de actividad, para dar cabida a errores inesperados que surjan durante ella.
5. Descargue la configuración actual de las máquinas virtuales mediante PowerShell, los comandos de la CLI o las API de REST para facilitar la validación una vez que se finalice el paso de preparación.
6. Actualice los scripts de automatización y operacionalización para controlar el modelo de implementación de Resource Manager antes de iniciar la migración. Además, como opción, también puede realizar las operaciones GET cuando los recursos se encuentren en el estado Preparado.
7. Evalúe las directivas de RBAC configuradas en los recursos IaaS clásicos y cuente con un plan para cuando se haya completado la migración.

Con el anuncio de la versión preliminar pública, se ha agregado compatibilidad con la desencadenamiento de la migración por medio de las API de REST, PowerShell y la CLI de Azure. Actualmente, está planeada la compatibilidad con la migración en el Portal de Azure para que pueda visualizar y recorrer la migración del modelo clásico al de ARM.

![Captura de pantalla que muestra el flujo de trabajo de migración](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

1.	Preparación
	* Es el primer paso del proceso de migración. El objetivo de este paso es simular la transformación de los recursos IaaS de clásicos a recursos de Resource Manager y presentarla en paralelo para que la visualice. A continuación, se describe el flujo detallado de acciones.
  * Seleccionará la red virtual o el servicio hospedado (si no es una red virtual) que desee preparar para la migración.
  *	En primer lugar, la plataforma siempre realizará en segundo plano el análisis de datos de los recursos en proceso de migración y devolverá un resultado correcto o con errores dependiendo de si los recursos son aptos o no.
	*	Si no se puede migrar el recurso, se indicarán las razones de que no sea compatible con la migración.
	* Si el recurso se puede migrar, en primer lugar la plataforma bloquea las operaciones en el plano de administración para los recursos en proceso de migración. Por ejemplo: no podrá agregar un disco de datos a una máquina virtual en proceso de migración.
  *	Después, la plataforma comenzará la migración de los metadatos del modelo clásico al de Resource Manager para los recursos en migración.  
  *	Una vez completada la operación de preparación, tendrá la opción de visualizar los recursos tanto en el modelo clásico como en el de Resource Manager. Para cada servicio en la nube en el modelo de implementación clásica, se creará un nombre de grupo de recursos con este patrón: `cloud-service-name>-migrated`.

2.	Comprobación manual o mediante scripts
  * En este paso, puede optar por usar la configuración que descargó antes para comprobar si esa migración parece correcta. Como alternativa, también puede iniciar sesión en el portal y revisar las propiedades y los recursos para asegurarse de que los metadatos de la migración sean correctos.
	* Si va a migrar una red virtual, no se reiniciará la mayoría de la configuración de las máquinas virtuales. Para las aplicaciones en esas máquinas virtuales, puede confirmar si la aplicación sigue en funcionamiento.
	* Puede probar los scripts operacionales y de supervisión y automatización para ver si las máquinas virtuales operan del modo esperado y si los scripts actualizados funcionan correctamente. Tenga en cuenta que solo se admitirán las operaciones GET cuando los recursos se encuentren en el estado Preparado.
  * No existirá ningún período establecido antes del cual deba ‘confirmar’ la migración. Puede mantenerse el tiempo que desee en este estado. No obstante, tenga en cuenta que el plano de administración quedará bloqueado para estos recursos hasta que lleve a cabo la operación de anulación o confirmación.
  * Si ve algún problema, siempre puede anular la migración y volver al modelo de implementación clásica. Cuando regrese, se abrirán las operaciones en el plano de administración en los recursos para que pueda reanudar las operaciones normales en esas máquinas virtuales en el modelo de implementación clásica.

3. Anulación
  * Se trata de un paso opcional que permite revertir los cambios realizados en el modelo de implementación clásica y anular la migración. Tenga en cuenta que esta operación no se puede ejecutar una vez que se haya desencadenado la operación de confirmación. 	

4.	Confirmación
  * Una vez que haya terminado con la validación, puede "confirmar" la migración; los recursos dejarán de aparecer en el modelo clásico y solo estarán disponibles en el modelo de implementación de Resource Manager. Esto también significa que los recursos migrados solo se pueden administrar en el nuevo portal.
	* Si se produce un error en esta operación, se recomienda que la intente de nuevo un par de veces. Si sigue sin poder completarla, cree una incidencia de soporte técnico o publique una entrada en el foro con una etiqueta ClassicIaaSMigration [aquí](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=WAVirtualMachinesforWindows).

>[AZURE.NOTE] Tenga en cuenta que todas las operaciones que se describen a continuación son idempotentes. Si surge cualquier problema, aparte de un error de configuración o característica no admitida, se recomienda repetir la operación de preparación, anulación o confirmación y la plataforma volverá a intentar la acción.


## Preguntas frecuentes

**¿Afecta este plan de migración a alguno de los servicios o las aplicaciones existentes que se ejecutan en máquinas virtuales de Azure?**

No. Las máquinas virtuales (clásicas) son servicios de GA totalmente compatibles y puede seguir aprovechando estos recursos para expandir su presencia en la nube de Microsoft Azure.

**¿Qué ocurre con mis máquinas virtuales si por ahora no tengo planeado migrar?**

Las API y el modelo de recursos clásicos existentes no van a quedar en desuso. Queremos facilitar lo más posible la migración, dadas las características avanzadas disponibles en el modelo de implementación de Resource Manager. Por lo tanto, es muy recomendable que repase algunos de los avances realizados como parte de IaaS en Resource Manager [aquí](virtual-machines-windows-compare-deployment-models.md).

**¿Qué supone este plan de migración para las herramientas existentes?**

La actualización de sus herramientas al modelo de implementación de Resource Manager es uno de los cambios más importantes que tendrá que considerar en sus planes de migración.

**¿Cuánto tiempo durará el tiempo de inactividad del plano de administración?**

Depende del número de recursos que se vayan a migrar. Para las implementaciones más pequeñas (unas decenas de máquinas virtuales), la migración de principio a fin debería tardar menos de una hora. Sin embargo, para implementaciones a gran escala (cientos de máquinas virtuales), se podría prolongar varias horas. Dado que el servicio está en la versión preliminar pública, es muy recomendable que lo ejecute en su suscripción de prueba o desarrollo para evaluar el impacto.

**¿Puedo revertir el proceso después de que la migración de los recursos esté confirmada en Resource Manager?**

Puede anular la migración siempre que los recursos se encuentren en el estado "Preparado". No se puede revertir una vez que los recursos se hayan migrado correctamente mediante la operación de confirmación.

**¿Puedo revertir la migración si se produce un error en la operación de confirmación?**

No se puede anular la migración si se produce un error en la operación de confirmación. Todas las operaciones de migración, incluida la de confirmación, son idempotentes. Por consiguiente, se recomienda que vuelva a intentar la operación en breve. Si sigue apareciendo un error, cree una incidencia de soporte técnico o publique una entrada en el foro con la etiqueta ClassicIaaSMigration [aquí](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=WAVirtualMachinesforWindows).

**¿Tengo que comprar otro circuito de ExpressRoute para aprovechar IaaS en Resource Manager?**

No. Hace poco se ha habilitado la [coexistencia de un circuito de ExpressRoute en el modelo clásico y el de Resource Manager](../expressroute/expressroute-howto-coexist-resource-manager.md). No hace falta que compre un nuevo circuito de ExpressRoute si ya tiene uno.

**¿Hay un plan de cuándo se agregarán los escenarios no admitidos a la lista de migración?**

Ahora mismo, el plan es lanzar el primer conjunto de escenarios durante el primer semestre de 2016. Se seguirá agregando compatibilidad con las características no admitidas después de ese primer lanzamiento.

**¿Qué ocurre si había configurado las directivas de control de acceso basado en rol para mis recursos IaaS clásicos?**

Durante la migración, los recursos se transforman del modelo clásico al de Resource Manager. Por lo tanto, se recomienda que planee con antelación las actualizaciones de directiva de RBAC que deben producirse después de la migración.

**¿Qué pasa si estoy usando Azure Site Recovery o servicios de copia de seguridad en Azure?**

Recientemente se agregó compatibilidad con Azure Site Recovery y la copia de seguridad para máquinas virtuales en Resource Manager. Se está colaborando con esos equipos para hacer posible también la funcionalidad que admite la migración de máquinas virtuales a Resource Manager. En este momento, se recomienda no ejecutar la migración si se están usando estas funcionalidades.

**¿Puedo validar mi suscripción o recursos para ver si son aptos para la migración?**

En este momento, la operación de preparación lleva implícita una validación de los recursos que se estén preparando para la migración. En la opción de migración compatible con la plataforma, el primer paso para preparar la migración consiste en validar si los recursos pueden migrarse. Si la validación genera un error, los recursos no se tocan en absoluto. De todos modos, también se está planeando el lanzamiento de una nueva acción de validación que resultará mucho más sencilla.

**¿Qué ocurre si se produce un error de cuota mientras preparo los recursos IaaS para la migración?**

Se recomienda anular la migración y después presentar una solicitud de soporte técnico para que se aumenten las cuotas en la región donde vaya a migrar las máquinas virtuales. Una vez aprobada la solicitud de cuota, vuelva a empezar a ejecutar los pasos de la migración.

**¿Cómo notifico un problema si carezco de contrato de soporte técnico?**

Publique sus problemas y preguntas sobre la migración en nuestro foro de máquinas virtuales con la palabra clave ClassicIaaSMigration [aquí](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=WAVirtualMachinesforWindows). Se recomienda que publique en este foro todas sus preguntas; por supuesto, si tiene un contrato de soporte técnico, también puede abrir una incidencia de soporte técnico.

**¿Qué hago si no me gustan los nombres que eligió la plataforma para los grupos de recursos durante la migración?**

En breve, se va a anunciar la compatibilidad con el traslado de recursos entre grupos de recursos. Una vez que se admita esta funcionalidad, podrá mover los recursos al grupo de recursos de su elección.

**¿Qué hago si no me gustan los nombres que eligió la plataforma para los recursos durante la migración?**

En el caso de los recursos para los que se proporcionen nombres explícitamente en el modelo de implementación clásica, se conservarán durante la migración. En algunos casos, se crearán recursos nuevos. Por ejemplo, se creará una interfaz de red para cada máquina virtual. En este momento, no se admite la capacidad de controlar los nombres de estos recursos nuevos creados durante la migración. Puede votar por esta característica [aquí](http://feedback.azure.com).


## Pasos siguientes
Ahora que comprende mejor la migración de recursos IaaS del modelo clásico al de Resource Manager, puede empezar a migrar los recursos.

- [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Migración de recursos IaaS del modelo clásico al de Azure Resource Manager con Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Migración de recursos IaaS del modelo clásico al de Azure Resource Manager mediante la CLI de Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonación de una máquina virtual clásica en Azure Resource Manager con scripts de PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0518_2016-->