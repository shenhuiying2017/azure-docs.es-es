---
title: "Supervisión y solución de problemas de Azure Site Recovery | Microsoft Docs"
description: "Supervisión y solución de problemas de replicación y de operaciones de Azure Site Recovery mediante el portal"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/06/2017
ms.author: bsiva
ms.openlocfilehash: 6dca032c8611ac4f7e66eb6f7e22e53f49209143
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Supervisión y solución de problemas de Azure Site Recovery

En este artículo, aprenderá a usar las características de supervisión integradas de Azure Site Recovery para la supervisión y solución de problemas. Obtenga información sobre cómo:
> [!div class="checklist"]
> - Usar el panel de Azure Site Recovery (página de información general del almacén)
> - Supervisión y solución de problemas de replicación
> - Supervisión de trabajos/operaciones de Azure Site Recovery
> - Suscribirse a las notificaciones por correo electrónico

## <a name="using-the-azure-site-recovery-dashboard"></a>Uso del panel de Azure Site Recovery

El panel de Azure Site Recovery en la página de información general del almacén consolida toda información de supervisión del almacén en una sola ubicación. Empiece en el panel del almacén y, para profundizar y obtener más detalles, desplácese por los elementos del panel. Los elementos principales del panel de Azure Site Recovery son los siguientes:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Cambio entre los paneles de Azure Backup y Azure Site Recovery

El conmutador para alternar de la parte superior de la página de información general le permite alternar entre las páginas de los paneles de Site Recovery y Backup. Esta selección, una vez realizada, se recuerda y se establece como valor predeterminado para la próxima vez que abra la página de información general del almacén. Seleccione la opción Site Recovery para ver el panel de Site Recovery. 

Los distintos elementos de la página del panel de Azure Site Recovery se actualizan automáticamente cada 10 minutos, de forma que el panel refleja la información más reciente disponible.

![Características de supervisión en la página de información general de Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Elementos replicados

La sección de los elementos replicados del panel presenta información general sobre el estado de replicación de servidores protegidos en el almacén. 

<table>
<tr>
    <td>Healthy</td>
    <td>La replicación está progresando normalmente para estos servidores y no se han detectado síntomas de error ni de advertencia.</td>
</tr>
<tr>
    <td>Warning (Advertencia) </td>
    <td>Se han detectado uno o varios síntomas de advertencia que pueden afectar a la replicación o indicar que la replicación no está progresando normalmente para estos servidores.</td>
</tr>
<tr>
    <td>Crítico</td>
    <td>Se han detectado uno o varios síntomas de errores críticos de replicación para estos servidores. Estos síntomas de error suelen ser indicadores de que la replicación se ha quedado bloqueada, o de que no está progresando a la misma velocidad que el cambio de datos de estos servidores.</td>
</tr>
<tr>
    <td>No aplicable</td>
    <td>Servidores de los que, actualmente, no se espera que se estén replicando, como los servidores que se han conmutado por error.</td>
</tr>
</table>

Para ver una lista de los servidores protegidos filtrados por el estado de replicación, haga clic en la descripción de estado de replicación que aparece junto al anillo. El vínculo Ver todo, situado cerca del título de sección, es un acceso directo a la página de elementos replicados para el almacén. Use el vínculo Ver todo para ver la lista de todos los servidores del almacén.

### <a name="3-failover-test-success"></a>3. Prueba de conmutación por error correcta

La sección de prueba de conmutación por error correcta del panel clasifica las máquinas virtuales del almacén en función del estado de la prueba de conmutación por error. 

<table>
<tr>
    <td>Prueba recomendada</td>
    <td>Máquinas virtuales que no han tenido un resultado correcto de la prueba de conmutación por error desde que alcanzaron un estado protegido.</td>
</tr>
<tr>
    <td>Realizada correctamente</td>
    <td>Máquinas virtuales que han tenido una o varias pruebas correctas de conmutación por error.</td>
</tr>
<tr>
    <td>No aplicable</td>
    <td>Máquinas virtuales que, actualmente, no son aptas para una conmutación por error de prueba. Algunos ejemplos son: servidores conmutados por error, servidores para los que la replicación inicial está en curso, servidores para los que hay una conmutación por error en curso, o servidores para los que ya está en curso una conmutación por error de prueba.</td>
</tr>
</table>

Haga clic en el estado de conmutación por error de prueba, junto al anillo, para ver la lista de servidores protegidos basándose en su estado de conmutación por error de prueba.
 
> [!IMPORTANT]
> Como procedimiento recomendado, es aconsejable realizar una conmutación por error de prueba en los servidores protegidos al menos una vez cada seis meses. Realizar una conmutación por error de prueba es una forma fluida de probar sin interrupciones la conmutación por error de los servidores y de las aplicaciones en un entorno aislado, y le ayuda a evaluar la preparación de continuidad de su negocio.

 Una operación de conmutación por error de prueba en un servidor o un plan de recuperación se considera correcta si tanto la operación de conmutación por error de prueba como la operación de conmutación por error de prueba de limpieza finalizan correctamente.

### <a name="4-configuration-issues"></a>4. Problemas de configuración

La sección Problemas de configuración muestra una lista de problemas que pueden afectar a su capacidad para conmutar por error correctamente las máquinas virtuales. Las clases de problemas que se enumeran en esta sección son los siguientes:
 - **Falta de configuraciones:** falta de configuraciones necesarias en servidores protegidos, como una red de recuperación o un grupo de recursos de recuperación.
 - **Falta de recursos:** recursos configurados de destino/recuperación no encontrados o no disponibles en la suscripción. Por ejemplo, el recurso se eliminó o se migró a una suscripción o a un grupo de recursos diferente. Se supervisa la disponibilidad de las siguientes configuraciones de destino/recuperación: grupo de recursos de destino, red virtual de destino y subred, cuenta de almacenamiento de destino/registro, conjunto de disponibilidad de destino, dirección IP de destino.
 - **Cuota de suscripción:** el saldo disponible de la cuota de recursos de suscripción se compara con el saldo necesario para poder conmutar por error todas las máquinas virtuales en el almacén. Si el saldo disponible resulta insuficiente, se notifica como saldo de cuota insuficiente. Se supervisan las cuotas de los siguientes recursos de Azure: recuento de núcleos de máquinas virtuales, recuento de núcleos de familia de máquinas virtuales, recuento de tarjetas de interfaz de red (NIC).
 - **Actualizaciones de software:** disponibilidad de nuevas actualizaciones de software, y versiones de software que van a caducar.

Los problemas de configuración (distintos de la disponibilidad de las actualizaciones de software) se detectan mediante una operación periódica de validador que se ejecuta cada 12 horas de forma predeterminada. Puede forzar la operación de validador para que se ejecute inmediatamente; para ello, haga clic en el icono de actualización junto al encabezado de la sección *Problemas de configuración*.

Haga clic en los vínculos para obtener más detalles sobre los problemas indicados y las máquinas virtuales a las que afectan. Para obtener información sobre los problemas que afectan a máquinas virtuales específicas, puede obtener más detalles haciendo clic en el vínculo **Requiere atención** que aparece en la columna de las configuraciones de destino para la máquina virtual. Los detalles incluyen recomendaciones sobre cómo puede corregir los problemas detectados.

### <a name="5-error-summary"></a>5. Resumen de errores

La sección Resumen de errores muestra los síntomas de los errores de replicación actualmente activos que pueden afectar a la replicación de servidores en el almacén, junto con el número de entidades afectadas por cada error.

Los síntomas de los errores de replicación para los servidores en un estado de replicación crítico o de advertencia pueden verse en la tabla de resumen del error. 

- Los errores que afectan a los componentes de la infraestructura local, como la falta de recepción de latido del proveedor de Azure Site Recovery que se ejecuta en el servidor de configuración local, el servidor VMM o el host de Hyper-V, se enumeran al principio de la sección Resumen de errores.
- Los síntomas de errores de replicación que afectan a los servidores protegidos se incluyen a continuación. Las entradas de la tabla de resumen de errores se ordenan por orden decreciente de gravedad del error y, a continuación, por orden decreciente de recuento de los servidores afectados.
 

> [!NOTE]
> 
>  Se pueden observar varios síntomas de error de replicación en un único servidor. Si hay varios síntomas de error en un único servidor, cada síntoma de error contaría ese servidor en la lista de sus servidores afectados. Una vez solucionado el problema subyacente que genera un síntoma de error, los parámetros de replicación mejoran y se borra el error de la máquina virtual.
>
> > [!TIP]
> El recuento de los servidores afectados es una forma útil de conocer si un único problema subyacente puede afectar a varios servidores. Por ejemplo, un problema de red puede afectar potencialmente a todos los servidores que se replican desde un sitio local a Azure. En esta vista se puede ver rápidamente que corregir ese problema subyacente solucionará la replicación para varios servidores.
>

### <a name="6-infrastructure-view"></a>6. Vista de la infraestructura

La vista de la infraestructura proporciona una práctica representación visual del escenario de los componentes de infraestructura implicados en la replicación. También muestra visualmente el estado de la conectividad entre los distintos servidores, así como entre los servidores y los servicios de Azure implicados en la replicación. 

Una línea verde indica que la conexión es correcta, mientras que una línea roja con el icono de error superpuesto indica la existencia de uno o varios síntomas de error que afectan a la conectividad entre los componentes implicados. Al mantener el puntero del mouse sobre el icono del error en la línea, se muestra el error y el número de entidades afectadas. 

Al hacer clic en el icono de error, se muestra una lista filtrada de las entidades afectadas por los errores.

![Vista de la infraestructura de Site Recovery (almacén)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Asegúrese de que los componentes de infraestructura local (servidor de configuración, servidores de procesos adicionales, máquinas virtuales de VMware de replicación, hosts de Hyper-V, servidores VMM) estén ejecutando la versión más reciente del software de Azure Site Recovery. Para poder usar todas las características de la vista de infraestructura, debe ejecutar el [paquete acumulativo de actualizaciones 22](https://support.microsoft.com/help/4072852) o una versión posterior para Azure Site Recovery.

Para usar la vista de infraestructura, seleccione el escenario de replicación correspondiente (máquinas virtuales de Azure, servidor físico/de máquinas virtuales de VMware o Hyper-V) en función de su entorno de origen. La vista de infraestructura que aparece en la página de información general del almacén es una vista agregada del almacén. Puede profundizar más en los componentes individuales haciendo clic en los cuadros.

En la página de información general del elemento replicado se ofrece una vista de infraestructura con el ámbito del contexto de una única máquina de replicación. Para ir a la página de información general para un servidor de replicación, diríjase a los elementos replicados desde el menú del almacén y seleccione el servidor para ver los detalles.

### <a name="infrastructure-view---faq"></a>Vista de infraestructura: preguntas más frecuentes

**P.** ¿Por qué no veo la vista de infraestructura para mi VM? </br>
**R.** La característica de vista de infraestructura solo está disponible para máquinas virtuales que se replican en Azure. Actualmente, la característica no está disponible para máquinas virtuales que se replican entre sitios locales.

**P.** ¿Por qué el número de máquinas virtuales de la vista de la infraestructura de almacén se muestra diferente del recuento total del anillo de elementos replicados?</br>
**R.** La vista de la infraestructura de almacén tiene un ámbito por escenarios de replicación. Solo las máquinas virtuales que participan en el escenario de replicación seleccionado actualmente se incluyen en el recuento de máquinas virtuales que se muestra en la vista de la infraestructura. Además, para el escenario seleccionado, solo las máquinas virtuales que están actualmente configuradas para replicarse en Azure se incluyen en el recuento de máquinas virtuales que se muestra en la vista de infraestructura (por ejemplo: las máquinas virtuales conmutadas por error o las máquinas virtuales que se vuelven a replicar en un sitio local no se incluyen en la vista de la infraestructura).

**P.** ¿Por qué el número de elementos replicados que se muestra en el espacio de información esencial en la página de información general es diferente al número total de elementos replicados que se muestra en el gráfico de anillos en el panel?</br>
**R.** Solo las máquinas virtuales para las que se ha completado la replicación inicial se incluyen en el recuento que se muestra en el espacio de información esencial. El total del anillo de elementos replicados incluye todas las máquinas virtuales del almacén, incluidos los servidores para los que la replicación inicial está en curso.

**P.** ¿Para qué escenarios de replicación está disponible la vista de infraestructura? </br>
**R.**
>[!div class="mx-tdBreakAll"]
>|Escenario de replicación  | Estado de VM  | Vista de infraestructura disponible  |
>|---------|---------|---------|
>|Máquinas virtuales que se replican entre dos sitios locales     | -        | Sin       |
>|Todo     | Conmutada por error         |  Sin        |
>|Máquinas virtuales que se replican entre dos regiones de Azure     | Replicación inicial en curso o en estado protegido         | Sí         |
>|Máquinas virtuales de VMware que se replican en Azure     | Replicación inicial en curso o en estado protegido        | Sí        |
>|Máquinas virtuales de VMware que se replican en Azure     | Máquinas virtuales conmutadas por error que se vuelven a replicar en un sitio local de VMware         | Sin         |
>|Máquinas virtuales de Hyper-V que se replican en Azure     | Replicación inicial en curso o en estado protegido        | Sí       |
>|Máquinas virtuales de Hyper-V que se replican en Azure     | Conmutadas por error/conmutación por recuperación en curso        |  Sin        |


### <a name="7-recovery-plans"></a>7. Planes de recuperación

La sección Planes de recuperación muestra el número de planes de recuperación del almacén. Haga clic en el número para ver la lista de planes de recuperación, crear nuevos planes de recuperación o editar los existentes. 

### <a name="8-jobs"></a>8. Trabajos

Los trabajos de Azure Site Recovery realizan un seguimiento del estado de las operaciones de Azure Site Recovery. La mayoría de las operaciones de Azure Site Recovery se ejecutan de forma asincrónica, con un trabajo de seguimiento que se usa para realizar un seguimiento del progreso de la operación.  Para obtener información sobre cómo supervisar el estado de una operación, consulte la sección [Supervisión de trabajos/operaciones de Azure Site Recovery](#monitor-azure-site-recovery-jobsoperations).

Esta sección de trabajos del panel le ofrece la siguiente información:

<table>
<tr>
    <td>Con error</td>
    <td>Trabajos de Azure Site Recovery con error en las últimas 24 horas</td>
</tr>
<tr>
    <td>En curso</td>
    <td>Trabajos de Azure Site Recovery que están actualmente en curso</td>
</tr>
<tr>
    <td>Esperando entrada</td>
    <td>Trabajos de Azure Site Recovery que se encuentran en pausa actualmente a la espera de una entrada del usuario.</td>
</tr>
</table>

El vínculo Ver todo que aparece junto al encabezado de la sección es un acceso directo para ir a la página de la lista de trabajos.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Supervisión y solución de problemas de replicación

Además de la información disponible en la página del panel de almacén, puede obtener detalles adicionales e información de solución de problemas en la página de la lista de máquinas virtuales, así como en la página de detalles de la máquina virtual. Puede ver la lista de máquinas virtuales protegidas en el almacén seleccionando la opción **Elementos replicados** en el menú del almacén. Como alternativa, puede obtener a una lista filtrada de los elementos protegidos haciendo clic en cualquiera de los accesos directos con ámbito disponibles en la página del panel del almacén.

![Vista de lista de elementos replicados de Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

La opción de filtro de la página de lista de elementos replicados permite aplicar varios filtros, como el estado de la replicación y la directiva de replicación. 

La opción de selector de columnas permite especificar columnas adicionales para mostrarlas, como RPO, problemas de configuración de destino y errores de replicación. Puede iniciar operaciones en una máquina virtual, o bien ver los errores que afectan a la máquina virtual, haciendo clic con el botón derecho en una fila determinada de la lista de máquinas.

Para explorar en profundidad aún más, seleccione una máquina virtual haciendo clic en ella. Esto abrirá la página de detalles de la máquina virtual. La página de información general de los detalles de la máquina virtual contiene un panel donde encontrará información adicional relacionada con la máquina. 

En la página de información general de la máquina de replicación, encontrará lo siguiente:
- RPO (objetivo de punto de recuperación): el RPO actual de la máquina virtual y la hora a la que se calculó por última vez el RPO.
- Puntos de recuperación disponibles más recientes de la máquina
- Los problemas de configuración, en caso de que los haya, que pueden afectar a la preparación para la conmutación por error de la máquina. Haga clic en el vínculo para obtener más detalles.
- Detalles del error: lista de síntomas de errores de replicación observados actualmente en la máquina junto con las causas posibles y correcciones recomendadas.
- Eventos: una lista cronológica de eventos recientes que afectan a la máquina. Aunque los detalles del error muestran los síntomas de error observables actualmente en la máquina, los eventos son un registro histórico de diversos eventos que pueden haber afectado a la máquina, incluidos los síntomas de error que pueden haberse observado previamente en la máquina.
- Vista de infraestructura de máquinas que se replican en Azure

![Información general/detalles de elementos replicados de Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

El menú de acción de la parte superior de la página proporciona opciones para realizar varias operaciones, como probar la conmutación por error en la máquina virtual. El botón de detalles de error del menú Acción le permite ver todos los errores activos actualmente, incluidos los errores de replicación, los problemas de configuración y las advertencias basadas en procedimientos recomendados de configuración para la máquina virtual.

> [!TIP]
> ¿En qué se diferencia el objetivo de punto de recuperación o RPO del último punto de recuperación disponible?
> 
>Azure Site Recovery utiliza un proceso asincrónico de varios pasos para replicar máquinas virtuales en Azure. En el penúltimo paso de la replicación, los cambios recientes en la máquina virtual junto con los metadatos se copian en una cuenta de registro/almacenamiento en caché. Una vez que estos cambios, junto con la etiqueta para identificar un punto recuperable, se escriben en la cuenta de almacenamiento en la región de destino, Azure Site Recovery tiene la información necesaria para generar un punto recuperable para la máquina virtual. En este momento, se ha cumplido el RPO para los cambios cargados en la cuenta de almacenamiento hasta entonces. En otras palabras, el RPO de la máquina virtual en este momento expresado en unidades de tiempo equivale a la cantidad de tiempo transcurrido desde la marca de tiempo correspondiente al punto recuperable.
>
>El servicio Azure Site Recovery, que funciona en segundo plano, toma los datos cargados de la cuenta de almacenamiento y los aplica en los discos de réplica creados para la máquina virtual. A continuación, genera un punto de recuperación y hace que este punto esté disponible para la recuperación en la conmutación por error. El último punto de recuperación disponible indica la marca de tiempo correspondiente al punto de recuperación más reciente que ya se ha procesado y aplicado a los discos de réplica.
>> [!WARNING]
> Un reloj manipulado o una hora del sistema incorrecta en la máquina de origen de replicación o en los servidores de infraestructura local distorsionará el valor calculado de RPO. Para garantizar unos informes precisos de los valores de RPO, asegúrese de que el reloj del sistema de los servidores implicados en la replicación sea preciso. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Supervisión de trabajos/operaciones de Azure Site Recovery

Azure Site Recovery ejecuta las operaciones que especifica de forma asincrónica. Algunos ejemplos de operaciones que puede realizar son habilitar la replicación, crear un plan de recuperación, probar la conmutación por error, actualizar la configuración de replicación, etc. Cada operación de este tipo tiene un trabajo correspondiente que se crea para realizar un seguimiento y una auditoría de la operación. El objeto de trabajo tiene toda la información necesaria para que pueda realizar un seguimiento del estado y el progreso de la operación. Puede realizar un seguimiento del estado de las distintas operaciones de Site Recovery para el almacén desde la página Trabajos. 

Para ver la lista de trabajos de Site Recovery para el almacén, vaya a la sección **Supervisión e informes** del menú de almacén y seleccione Trabajos > Trabajos de Site Recovery. Seleccione un trabajo de la lista de trabajos en la página haciendo clic en él para obtener más información acerca del trabajo especificado. Si no se ha completado correctamente el trabajo o tiene errores, puede ver más información sobre el error y posibles soluciones haciendo clic en el botón de detalles del error, situado en la parte superior de la página de detalles del trabajo (también se puede obtener acceso desde la página de la lista de trabajos haciendo clic con el botón derecho en el trabajo que ha generado el error). Puede usar la opción de filtro del menú de acción en la parte superior de la página de la lista de trabajos para filtrar la lista en función de criterios específicos y usar el botón de exportación para exportar los detalles de los trabajos seleccionados a Excel. También puede tener acceso a la vista de la lista de trabajos desde el acceso directo de la página del panel de Site Recovery. 

 Para las operaciones que realiza desde Azure Portal, también puede realizar un seguimiento del trabajo creado y su estado actual desde la sección de notificaciones (el icono de la campana situado en la parte superior derecha) de Azure Portal.

## <a name="subscribe-to-email-notifications"></a>Suscribirse a las notificaciones por correo electrónico

La característica integrada de notificación por correo electrónico le permite suscribirse para recibir notificaciones por correo electrónico para los eventos críticos. Si está suscrito, las notificaciones por correo electrónico se envían para los siguientes eventos:
- Cambio del estado de replicación de una máquina en replicación a crítico.
- Ausencia de conectividad entre los componentes de infraestructura local y el servicio Azure Site Recovery. Mediante un mecanismo de latido, se detecta la conectividad con el servicio Site Recovery desde los componentes de la infraestructura local, como el servidor de configuración (VMware) o System Center Virtual Machine Manager (Hyper-V) registrados en el almacén.
- Errores de operación de conmutación por error, si existen.

Para suscribirse para recibir notificaciones por correo electrónico para Azure Site Recovery, vaya a la sección **Supervisión e informes** del menú de almacén y haga lo siguiente:
1. Seleccione Alertas y eventos > Eventos de Site Recovery.
2. Seleccione "Notificaciones de correo electrónico" en el menú de la parte superior de la página de eventos que se abre.
3. Utilice el asistente para la notificación de correo electrónico para activar o desactivar las notificaciones de correo electrónico y seleccionar destinatarios para las notificaciones. Puede especificar que todos los administradores de la suscripción reciban notificaciones, o bien proporcionar una lista de direcciones de correo electrónico a las que enviar las notificaciones. 