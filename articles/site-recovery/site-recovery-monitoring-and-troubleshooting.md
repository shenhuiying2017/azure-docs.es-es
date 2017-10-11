---
title: "Protección de supervisión y solución de problemas para las máquinas virtuales y los servidores físicos | Microsoft Docs"
description: "Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas en servidores locales en Azure o en un centro de datos secundario. Use este artículo para supervisar y solucionar problemas de protección de sitios Virtual Machine Manager o Hyper-V."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 0fc8e368-0c0e-4bb1-9d50-cffd5ad0853f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: 5bcb5dcb6afc3909e34dde31f845e014e7c539e3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Protección de supervisión y solución de problemas para las máquinas virtuales y los servidores físicos
Con esta guía de supervisión y solución de problemas aprenderá a realizar el seguimiento del estado de mantenimiento de la replicación y técnicas de solución de problemas de Azure Site Recovery.

## <a name="understand-the-components"></a>Descripción de los componentes
### <a name="vmware-virtual-machine-or-physical-server-site-deployment-for-replication-between-on-premises-and-azure"></a>Implementación de sitios de servidores físicos o máquinas virtuales de VMware para su replicación entre una ubicación local y Azure.
Para configurar la recuperación de bases de datos entre un servidor físico o una máquina virtual de VMware local y Azure, debe configurar el servidor de configuración, el servidor de destino maestro y los componentes del servidor de procesos en su máquina virtual o servidor. Cuando se habilitar la protección del servidor de origen, Azure Site Recovery instala la característica Mobile Apps de Microsoft Azure App Service. En caso de interrupción local, tan pronto como el servidor de origen efectúa la conmutación por error en Azure, el cliente debe configurar un servidor de procesos en Azure y un servidor de destino maestro local para que el servidor de origen se reconstruya localmente.

![Implementación del sitio de VMware o físico para la replicación entre una ubicación local y Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-sites"></a>Implementación del sitio de Virtual Machine Manager para la replicación entre sitios locales
Para configurar la recuperación de bases de datos entre dos ubicaciones locales, debe descargar el proveedor de Azure Site Recovery e instalarlo en el servidor de Virtual Machine Manager. El proveedor requiere conexión a Internet para que todas las operaciones que se desencadenen en Azure Portal se traduzcan en operaciones locales.

![Implementación del sitio de Virtual Machine Manager para la replicación entre sitios locales](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Implementación de sitios de Virtual Machine Manager para su replicación entre ubicaciones locales y Azure.
Cuando se configura la recuperación de bases de datos entre dos ubicaciones locales y Azure, es preciso descargar el proveedor de Azure Site Recovery e instalarlo en el servidor de Virtual Machine Manager. También hay que instalar el agente de Azure Recovery Services en cada host de Hyper-V. Haga clic aquí para [obtener más información](site-recovery-hyper-v-azure-architecture.md).

![Implementación de sitios de Virtual Machine Manager para su replicación entre ubicaciones locales y Azure.](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Implementación de sitios de Hyper-V para la replicación entre ubicaciones locales y Azure
Este proceso es similar a la implementación de Virtual Machine Manager. La única diferencia es que el proveedor de Azure Site Recovery y el agente de Azure Recovery Services se instalan en el host de Hyper-V propiamente dicho. [Más información](site-recovery-hyper-v-azure-architecture.md). .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Supervisión de las operaciones de configuración, protección y recuperación
Cada operación de Azure Site Recovery se audita y somete a seguimiento en la pestaña **TRABAJOS**. En caso de cualquier error de configuración, protección o recuperación, búsquelo en la pestaña **TRABAJOS**.

![Filtro de error en la pestaña TRABAJOS](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Si encuentra algún error en la pestaña **TRABAJOS**, haga clic en el trabajo y en la opción **DETALLES DEL ERROR** correspondiente a ese trabajo.

![Botón DETALLES DEL ERROR](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Los detalles del error lo ayudarán a identificar la causa posible y la recomendación para solucionar el problema.

![Cuadro de diálogo que muestra los detalles del error para un trabajo específico](media/site-recovery-monitoring-and-troubleshooting/image5.png)

En el ejemplo anterior, hay otra operación en curso que parece ser la causante del error en la configuración de protección. Siga la recomendación para solucionar el problema y, a continuación, haga clic en **REINICIAR** para volver a iniciar la operación.

![Botón de REINICIO de la pestaña TRABAJOS](media/site-recovery-monitoring-and-troubleshooting/image6.png)

La opción **REINICIAR** no está disponible para todas las operaciones. Si una operación no tiene la opción **REINICIAR**, vuelva al objeto y repita la operación. Puede cancelar cualquier trabajo en curso mediante el botón **CANCELAR**.

![Botón CANCELAR](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machines"></a>Supervisión del estado de mantenimiento de la replicación de las máquinas virtuales
Puede utilizar Azure Portal para supervisar de forma remota los proveedores de Azure Site Recovery de cada una de las entidades protegidas. Haga clic en **ELEMENTOS PROTEGIDOS** y, a continuación, en **NUBES VMM** o **GRUPOS DE PROTECCIÓN**. La pestaña **NUBES VMM** solo está disponible para las implementaciones basadas en Virtual Machine Manager. Para otros escenarios, las entidades protegidas se encuentran en la pestaña **GRUPOS DE PROTECCIÓN**.

![Opciones Nubes VMM y GRUPOS DE PROTECCIÓN](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Haga clic en una entidad protegida en la nube o en el grupo de protección correspondiente para ver todas las operaciones disponibles, que se muestran en el panel inferior.

![Opciones disponibles para una entidad protegida seleccionada](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Tal y como se muestra en la captura de pantalla anterior, el estado de la máquina virtual es **crítico**. Puede hacer clic en el botón **DETALLES DEL ERROR** en la parte inferior para ver el error. Resuelva el problema en función de lo indicado en **Causas posibles** y **Recomendaciones**.

![Botón Detalles del error](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Errores y recomendaciones que aparecen en el cuadro de diálogo Detalles del error](media/site-recovery-monitoring-and-troubleshooting/image11.png)

> [!NOTE]
> Si hay operaciones activas en curso o con errores, vaya a la vista **TRABAJOS** como se mencionó anteriormente para ver el error específico de un trabajo.
>
>

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Solucionar problemas de Hyper-V locales
Conéctese a la consola del administrador de Hyper-V local, seleccione la máquina virtual y vea el estado de mantenimiento de la replicación.

![Opción para ver el estado de mantenimiento de la replicación en la consola del administrador de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image12.png)

En este caso, **Mantenimiento de la replicación** es **Crítico**. Haga clic con el botón derecho en la máquina virtual y, a continuación, en **Replicación** > **Ver mantenimiento de la replicación** para ver los detalles.

![Estado de mantenimiento de la replicación de una máquina virtual concreta](media/site-recovery-monitoring-and-troubleshooting/image13.png)

En los casos en los que la replicación se ponga en pausa para la máquina virtual, haga clic con el botón derecho en ella y, a continuación, en **Replicación** > **Reanudar replicación**.

![Opción para reanudar la replicación en la consola del administrador de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image19.png)

En el caso de que la máquina virtual se migre a un nuevo host de Hyper-V (dentro del clúster o en una máquina independiente) que se configuró a través de Azure Site Recovery, la replicación de la máquina virtual no se verá afectada. Asegúrese de que el nuevo host de Hyper-V cumpla todos los requisitos previos y se configure mediante Azure Site Recovery.

### <a name="event-log"></a>Registro de eventos
| Orígenes de eventos | Detalles |
| --- |:--- |
| **Registros de aplicaciones y servicios/Microsoft/VirtualMachineManager/Server/Admin** (Servidor Virtual Machine Manager) |Ofrece un registro útil para solucionar numerosos problemas distintos de Virtual Machine Manager. |
| **Registros de aplicaciones y servicios/MicrosoftAzureRecoveryServices/Replication** (Host de Hyper-V) |Ofrece un registro útil para solucionar muchos problemas del agente de Microsoft Azure Recovery Services. <br/> ![Ubicación de replicación del origen de eventos para el host de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Registros de aplicaciones y servicios/Microsoft/Azure Site Recovery/Provider/Operational** (Host de Hyper-V) |Ofrece un registro útil para solucionar muchos problemas del agente de Microsoft Azure Site Recovery Service. <br/> ![Ubicación del origen de eventos operativo para el host de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Registros de aplicaciones y servicios/Microsoft/Windows/Hyper-V-VMMS/Admin** (Host de Hyper-V) |Ofrece un registro útil para solucionar muchos problemas de administración de máquinas virtuales de Hyper-V. <br/> ![Ubicación del origen de eventos de Virtual Machine Manager para el host de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |

### <a name="hyper-v-replication-logging-options"></a>Opciones de registro de replicación de Hyper-V
Todos los eventos relacionados con la replicación de Hyper-V se registran en el registro Hyper-V-VMMS\\Admin ubicado en Registros de aplicaciones y servicios\\Microsoft\\Windows. Además, puede habilitar un registro analítico para el Servicio Administración de máquinas virtuales de Hyper-V. Para habilitar este registro, asegúrese primero de que los registros analíticos y de depuración puedan verse en el Visor de eventos. Abra el Visor de eventos y, a continuación, haga clic en **Ver** > **Mostrar registros analíticos y de depuración**.

![Opción Mostrar registros analíticos y de depuración](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Puede verse un registro analítico en **Hyper-V-VMMS**.

![Registro analítico en el árbol del Visor de eventos](media/site-recovery-monitoring-and-troubleshooting/image15.png)

En el panel **Acciones**, haga clic en **Habilitar registro**. Una vez habilitado, aparece en **Monitor de rendimiento** como **sesión de seguimiento de eventos** situada en **Conjuntos de recopiladores de datos**.

![Sesiones de seguimiento de eventos en el árbol del Monitor de rendimiento](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Para ver la información recopilada, primero debe deshabilitar el registro para detener la sesión de seguimiento. Guardar el registro y vuelva a abrirlo en el Visor de eventos o bien use otras herramientas para convertirlo, según proceda.

## <a name="reach-out-for-microsoft-support"></a>Contacto con el soporte técnico de Microsoft
### <a name="log-collection"></a>Recopilación de registros
Para la protección de sitios Virtual Machine Manager, consulte [Azure Site Recovery log collection using Support Diagnostics Platform (SDP) Tool](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) (Recopilación de registros de Azure Site Recovery usando la herramienta de diagnóstico de soporte técnico) para recopilar los registros necesarios.

Para la protección de sitios de Hyper-V, descargue la [herramienta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) y ejecútela en el host de Hyper-V para recopilar los registros.

Para escenarios de VMware y físicos, consulte [Azure Site Recovery log collection for VMware and physical site protection](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) (Colección de registros de Azure Site Recovery para la protección de sitios de VMware y físicos) para recopilar los registros necesarios.

La herramienta recopila los registros localmente en una subcarpeta de nombre aleatorio que se encuentra en %LocalAppData%\ElevatedDiagnostics.

![Pasos de ejemplo que se muestran en la protección del sitio de Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="open-a-support-ticket"></a>Abrir una incidencia de soporte técnico
Para generar una incidencia de soporte técnico para Azure Site Recovery, póngase en contacto con el soporte técnico de Azure a través de la dirección URL <http://aka.ms/getazuresupport>.

## <a name="kb-articles"></a>Artículos de Knowledge Base
* [Conservación de la letra de la unidad para máquinas virtuales protegidas que se conmutaron por error o se migraron a Azure](http://support.microsoft.com/kb/3031135)
* [Administración del uso de ancho de banda de red de protección de instalaciones locales a Azure](https://support.microsoft.com/kb/3056159)
* [Azure Site Recovery: error "No se encontró el recurso de clúster" al tratar de habilitar la protección de una máquina virtual](http://support.microsoft.com/kb/3010979).
* [Understand & Troubleshoot Hyper-V Replica Guide](http://social.technet.microsoft.com/wiki/contents/articles/21948.hyper-v-replica-troubleshooting-guide.aspx) (Guía de descripción y solución de problemas de Réplica de Hyper-V)

## <a name="common-azure-site-recovery-errors-and-their-resolutions"></a>Errores comunes de Azure Site Recovery y sus soluciones
A continuación, se muestran los errores comunes y sus soluciones. Cada uno de los errores se documenta en una página Wiki independiente.

### <a name="general"></a>General
* <span style="color:green;">NUEVO</span> [Trabajos que presentan el error "Operación en curso". Error 505, 514, 532.](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
* <span style="color:green;">NUEVO</span> [Trabajos que presentan el error "Servidor no conectado a Internet". Error 25018.](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Configuración
* [El servidor Virtual Machine Manager no se puede registrar debido a un error interno. Consulte la vista de trabajos en el portal de Site Recovery para obtener más detalles sobre el error. Vuelva a ejecutar la configuración para registrar el servidor.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
* [No se puede establecer una conexión con el almacén del Administrador de recuperación de Hyper-V. Compruebe la configuración de proxy o inténtelo de nuevo más tarde.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configuración
* [No se puede crear el grupo de protección: hubo un error al recuperar la lista de servidores.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
* [El clúster de hosts de Hyper-V contiene al menos un adaptador de red estático o no hay adaptadores conectados configurados para usar DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
* [Virtual Machine Manager no dispone de permisos para completar una acción.](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
* [No se puede seleccionar la cuenta de almacenamiento dentro de la suscripción durante la configuración de la protección.](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Protección
* <span style="color:green;">NUEVO</span> [Habilitar protección presenta el error "No se pudo configurar la protección para la máquina virtual". Error 60007, 40003.](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
* <span style="color:green;">NUEVO</span> [Habilitar protección presenta el error "No se pudo habilitar la protección para la máquina virtual". Error 70094.](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
* <span style="color:green;">NEW</span> [Error de migración en vivo 23848: La máquina virtual se va a mover con el tipo Live. Esto podría afectar al estado de protección de la recuperación de la máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
* [Error al habilitar la protección, ya que el agente no está instalado en el equipo host.](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
* [No se encuentra un host adecuado para la máquina virtual de réplica debido a unos recursos de ejecución bajos.](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
* [No se puede encontrar un host adecuado para la máquina virtual de réplica debido a que no se conectó una red lógica.](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
* [No se pudo conectar a la máquina host de réplica: no se pudo establecer la conexión.](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### <a name="recovery"></a>Recuperación
* Virtual Machine Manager no puede completar la operación de host:
  * [Conmutación por error en el punto de recuperación seleccionado para la máquina virtual: error de acceso general denegado.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
  * [No se pudo conmutar por error Hyper-V al punto de recuperación seleccionado para la máquina virtual: operación anulada.  Pruebe con un punto de recuperación más reciente. (0x80004004).](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
  * No se pudo establecer una conexión con el servidor (0x00002EFD).
    * [Error de Hyper-V al habilitar la replicación inversa para la máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
    * [Error de Hyper-V al habilitar la replicación para la máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
  * [No se pudo confirmar la conmutación por error de la máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
* [El plan de recuperación contiene máquinas virtuales que no están listas para la conmutación por error planeada.](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
* [La máquina virtual no está lista para la conmutación por error planeada.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* [La máquina virtual no se está ejecutando y no está apagada.](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
* [Se produjo una operación fuera de banda en una máquina virtual y no se realizó la conmutación por error.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* Probar conmutación por error
  * [No se pudo iniciar la conmutación por error ya que la conmutación por error de prueba está en curso.](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
* <span style="color:green;">NUEVO</span> La conmutación por error agota el tiempo con "PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout" debido a la configuración en el grupo de seguridad de red asociado a la máquina virtual o la subred a la que pertenece el equipo. Consulte [PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) para obtener más información.

### <a name="configuration-server-process-server-master-target"></a>Servidor de configuración, servidor de procesos y destino principal
* [El host ESXi en el que se hospeda PS/CS, ya que se produce un error en VM con una pantalla morada de la muerte.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Solución de problemas del escritorio remoto después de la conmutación por error
* Muchos clientes tienen que afrontar problemas para conectarse a la máquina virtual con conmutación por error en Azure. [Use el documento de solución de problemas para RDP en la máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Adición de una dirección IP pública en una máquina virtual de administrador de recursos
Si el botón **Conectar** del portal está atenuado y no está conectado a Azure a través de una conexión Express Route o VPN de sitio a sitio, deberá crear y asignar a la máquina virtual una dirección IP pública antes de poder usar Escritorio remoto/shell compartido. A continuación, puede agregar una dirección IP pública en la interfaz de red de la máquina virtual.  

![Adición de una dirección IP pública en la interfaz de red de la máquina virtual que ha conmutado por error](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)
