<properties
	pageTitle="Guía de solución de problemas y supervisión de VMM y de la protección de sitios de Hyper-V" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas en servidores locales de Azure o en un centro de datos secundario. Use este artículo para supervisar y solucionar problemas de protección de sitios VMM o Hyper-V." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/20/2015" 
	ms.author="anbacker"/>
	
# Protección de supervisión y solución de problemas para los VMware, VMM, Hyper-V y sitios físicos

Esta guía de solución de problemas y supervisión le permite aprender el seguimiento del estado de replicación y técnicas de solución de recuperación de Azure Site Recovery.

## Descripción de los componentes

### Implementación del sitio de VMware/físico para la replicación entre una ubicación local y Azure.
Para configurar la recuperación ante desastres entre la máquina de VMware/físicas local; es necesario configurar el servidor de configuración, el destino maestro y el servidor en proceso. Al habilitar la protección del servidor de origen, Azure Site Recovery instalará el servicio de movilidad. Tras la interrupción local, en cuanto el servidor de origen efectúa la conmutación por error en Azure, el cliente necesita configurar un servidor en proceso en Azure y un servidor de destino maestro localmente para proteger al servidor de origen para que se reconstruya localmente.

![Implementación del sitio de Hyper-V para la replicación entre el sitio local y Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### Implementación del sitio de VMM para la replicación entre el sitio local.

Como parte de la configuración de la recuperación ante desastres entre dos ubicaciones locales, es necesario descargar el proveedor de Azure Site Recovery Provider e instalarlo en el servidor VMM. El proveedor necesita conectividad a Internet para asegurarse de que todas las operaciones desencadenadas desde el Portal de Azure se traducen en operaciones locales como habilitar la protección, el apagado de máquinas de lado principal como parte de las conmutaciones por error etc.

![Implementación del sitio de VMM para la replicación entre el sitio local](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### Implementación del sitio de VMM para la replicación entre el sitio local y Azure.

Como parte de la configuración de la recuperación ante desastres entre el sitio local local y Azure; es necesario descargar e instalar el proveedor de Azure Site Recovery Provider en el servidor VMM junto con el agente de Servicios de recuperación de Azure que debe instalarse en cada host de Hyper-V.

![Implementación del sitio de VMM para la replicación entre el sitio local y Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### Implementación del sitio de Hyper-V para la replicación entre el sitio local y Azure

Es el mismo que el de la implementación de VMM: solo se diferencia en la instalación de proveedores y agentes en el propio host de Hyper-V.

## Supervisar las operaciones de configuración, protección y recuperación

Cada operación de ASR se audita y se rastre en la ficha "TRABAJOS". En el caso de cualquier error de configuración, protección o recuperación, navegue hasta la ficha TRABAJOS y consulte si hay errores.

![Supervisar las operaciones de configuración, protección y recuperación](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Una vez que encuentre errores en la vista de TRABAJOS, seleccione el TRABAJO y haga clic en DETALLES DEL ERROR correspondiente a ese trabajo.

![Supervisar las operaciones de configuración, protección y recuperación](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Los detalles del error le ayudarán a identificar la causa posible y la recomendación para el problema.

![Supervisar las operaciones de configuración, protección y recuperación](media/site-recovery-monitoring-and-troubleshooting/image5.png)

En el caso anterior parece haber otra operación que está en curso debido a la que la falla la configuración de protección. Asegúrese de resolver el problema según la recomendación: a continuación, haga clic en REINICIAR para volver a iniciar la operación.

![Supervisar las operaciones de configuración, protección y recuperación](media/site-recovery-monitoring-and-troubleshooting/image6.png)

La opción de REINICIAR no está disponible para todas las operaciones: para las que no tienen la opción de REINICIAR, regrese al objeto y vuelva a realizar la operación. Cada TRABAJO se puede cancelar en cualquier momento mientras se ejecuta mediante el botón de CANCELAR.

![Supervisar las operaciones de configuración, protección y recuperación](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## Supervisar el estado de replicación de la máquina virtual

Supervisión central y remota del proveedor ASR a través del Portal de Azure para cada una de las entidades protegidas. Desplácese hasta los ELEMENTOS PROTEGIDOS. A continuación, seleccione NUBES de VMM o GRUPOS DE PROTECCIÓN. La pestaña NUBES de VMM es solo para implementaciones de VMM y todos los demás escenarios tienen las entidades protegidas en la ficha GRUPOS DE PROTECCIÓN.

![Supervisar el estado de replicación de la máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image8.png)

A continuación, seleccione la entidad protegida en la nube correspondiente o en el grupo de protección. Una vez seleccionada la entidad protegida, se muestran todas las operaciones permitidas en el panel inferior.

![Supervisar el estado de replicación de la máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Como se indicó anteriormente en «El ESTADO de la máquina virtual es crítico», puede hacer clic en el botón DETALLES DEL ERROR de la parte inferior para ver el error. Basándose en las "Posibles causas" y "Recomendación" mencionadas, resuelva el problema: aquí en este caso la máquina virtual debe volver a sincronizarse, lo cual se puede hacer desde el propio portal haciendo clic en el botón VOLVER A SINCRONIZAR.

![Supervisar el estado de replicación de la máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Supervisar el estado de replicación de la máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Nota: Si hay operaciones activas que están en curso o con errores, a continuación, navegue a la vista de TRABAJOS como se mencionó anteriormente, para ver el error específico del TRABAJO.

## Visor de eventos

| Escenarios | Orígenes de eventos |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Protección de sitios VMM | VMM Server <ul><li> **Registros de aplicaciones y servicios/Microsoft/VirtualMachineManager/Server/Admin** </li></ul> Hyper-V Host <ul><li> **Registros de aplicaciones y servicios/MicrosoftAzureRecoveryServices/Replication** (para Azure como objetivo)</li><li> **Registros de aplicaciones y servicios/Microsoft/Windows/Hyper-V-VMMS/Admin** </li></ul> |
| Protección de sitios Hyper-V | <ul><li> **Registros de aplicaciones y servicios/MicrosoftAzureRecoveryServices/Replication** </li><li> **Registros de aplicaciones y servicios/Microsoft/Azure Site Recovery/Provider/Operational** </li><li> **Registros de aplicaciones y servicios/Microsoft/Windows/Hyper-V-VMMS/Admin** </li><ul>|


## Solucionar problemas de Hyper-V locales

Conéctese a la consola del administrador de Hyper-V local, seleccione la máquina virtual y visualice el estado de replicación.

![Solucionar problemas de Hyper-V locales](media/site-recovery-monitoring-and-troubleshooting/image12.png)

En este caso *Estado de replicación* se indica como crítico: *Ver el estado de replicación* para ver los detalles.

![Solucionar problemas de Hyper-V locales](media/site-recovery-monitoring-and-troubleshooting/image13.png)

### Opciones de registro de Replicación de Hyper-V

Todos los eventos relacionados con la Réplica de Hyper-V se registran en el registro de Hyper-V-VMMS\Admin ubicado en **Registros de aplicaciones y servicios\Microsoft\Windows**. Además, puede habilitarse un registro analítico para Hyper-V-VMMS. Para habilitar este registro, asegúrese primero de que los registros analíticos y de depuración puedan verse en el Visor de eventos. Abra el Visor de eventos y, a continuación, en el **menú Ver**, haga clic en **Mostrar registros analíticos y de depuración**.

![Solucionar problemas de Hyper-V locales](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Puede verse un registro analítico en Hyper-V-VMMS

![Solucionar problemas de Hyper-V locales](media/site-recovery-monitoring-and-troubleshooting/image15.png)

En el panel **Acciones**, haga clic en **Habilitar registro**. Una vez habilitado, aparece en **Monitor de rendimiento** como una sesión de seguimiento de eventos situada en **Conjuntos de recopiladores de datos.**

![Solucionar problemas de Hyper-V locales](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Para ver la información recopilada, primero detenga la sesión de seguimiento deshabilitando el registro y, a continuación, guarde el registro y vuelva a abrirlo en el Visor de eventos o use otras herramientas para convertirlo según sea necesario.


## Descripción del ciclo de vida de protección y recuperación de la máquina virtual de Hyper-V

![Descripción del ciclo de vida de protección y recuperación de la máquina virtual de Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image17.png)


## Contacto con el soporte técnico de Microsoft

### Recopilación de registros

Para Protección de sitios VMM, consulte [Recopilación del registro de ASR usando la herramienta de diagnóstico de soporte técnico (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) para recopilar los registros necesarios.

Para la protección de sucursal de Hyper-V y de sitio de SMB, descargue la [herramienta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) y ejecútela en el host de Hyper-V para recopilar los registros.

Para escenarios de VMware/físicos, consulte [Colección de registros de Azure Site Recovery para la protección de sitios de VMware y físicos](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) para recopilar los registros necesarios.

La herramienta SDP recopila localmente el archivo de registro, que también puede encontrar en una subcarpeta con nombre aleatorio situada en **%LocalAppData%\ElevatedDiagnostics**

### Apertura de una incidencia de soporte técnico

Para elevar la incidencia de soporte técnico para ASR, contacte con el soporte técnico de Azure a través de la dirección URL <http://aka.ms/getazuresupport>

## Artículos de Knowledge Base

-   [Cómo mantener la letra de la unidad para máquinas virtuales protegidas
    > http://support.microsoft.com/kb/3031135

-   [Cómo solucionar problemas de Servicios de recuperación
    > de Azure](http://support.microsoft.com/kb/3005185)

-   [Cómo habilitar el registro de depuración para Azure Site Recovery en
    > la Protección de sitios VMM](http://support.microsoft.com/kb/3033922)

-   [ASR: se muestra el error "no se encontró el recurso de clúster" error al intentar
    > habilitar la protección de una máquina virtual](http://support.microsoft.com/kb/3010979)
    
-   [Guía de descripción y solución de problemas de Réplica de Hyper-V
    > ](http://www.microsoft.com/en-in/download/details.aspx?id=29016)
	
	
	
## Errores de ASR comunes y sus soluciones

A continuación se muestran los errores comunes con los que se puede encontrar y sus soluciones. Cada uno de los errores se documenta en una página WIKI independiente.

### Configuración

-   [No se puede validar el certificado seleccionado. Seleccione un certificado diferente.](http://social.technet.microsoft.com/wiki/contents/articles/25569.the-selected-certificate-cannot-be-validated-please-select-a-different-certificate.aspx)

-   [No se puede registrar el servidor VMM debido a un error interno. Consulte la vista de trabajos en el Portal de Site Recovery para obtener más detalles sobre el error. Ejecute el programa de instalación para registrar el servidor.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)

-   [No se puede establecer una conexión en el almacén de Hyper-V Recovery Manager. Compruebe la configuración de proxy o inténtelo de nuevo más tarde.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### Configuración

-   [El clúster de hosts de Hyper-V contiene al menos un adaptador de red estático o no hay adaptadores conectados configurados para usar DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)

-   [El perfil de Hyper-V no está habilitado en los perfiles de capacidad para la nube](http://social.technet.microsoft.com/wiki/contents/articles/25499.the-hyper-v-profile-isn-t-enabled-in-the-capability-profiles-for-cloud.aspx)

-   [No se pudo aplicar la configuración de protección para '% CloudName;'. No se pudo configurar un host de Hyper-V o un clúster recién agregado porque no está configurada la protección de la nube.](http://social.technet.microsoft.com/wiki/contents/articles/25500.protection-configuration-for-cloudname-couldn-t-be-applied-a-newly-added-hyper-v-host-or-cluster-couldn-t-be-configured-because-cloud-protection-isn-t-configured.aspx)

-   [VMM no dispone de permisos para completar una acción](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)

### Protección

-   [No se admite el sistema operativo](http://social.technet.microsoft.com/wiki/contents/articles/31103.the-operating-system-is-not-supported.aspx)

-   [Error en la habilitación de la protección, ya que el agente no está instalado en el equipo host](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)

-   [No se puede encontrar un host adecuado para la máquina virtual de réplica debido a unos recursos de proceso bajos](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)

-   [No se puede encontrar un host adecuado para la máquina virtual de réplica debido a que no se conectó una red lógica](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)

-   [No se puede conectar a la máquina host de réplica: no se pudo establecer la conexión](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### Recuperación

-   VMM no puede completar la operación de host:

    -   [Conmutación por error en el punto de recuperación seleccionado para la máquina virtual: error de acceso general denegado.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)

    -   [No se ha podido conmutar por error Hyper-V a un punto de recuperación seleccionado para la máquina virtual: operación anulada, pruebe un punto de recuperación más reciente. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)

    -   No se pudo establecer una conexión con el servidor (0x00002EFD)

        -   [Error de Hyper-V al habilitar la replicación inversa para la máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)

        -   [Error de Hyper-V al habilitar la replicación para la máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)

    -   [No se puede confirmar la conmutación por error de la máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)

-   [El plan de recuperación contiene máquinas virtuales que no están listas para la conmutación por error planeada](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)

-   [La máquina virtual no está lista para la conmutación por error planeada](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)

-   [La máquina virtual no se está ejecutando y no está apagada](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)

-   [Se produjo una operación de fuera de banda en una máquina virtual y ](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)se produjo un error de conmutación por error

-   Test Failover

    -   [No se pudo iniciar la conmutación por error ya que la conmutación por error de prueba está en curso](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)

-   Específico de la recuperación de Azure

    -   [La conmutación por error no se pudo iniciar debido a caracteres no admitidos en el nombre de VM](http://social.technet.microsoft.com/wiki/contents/articles/31107.failover-could-not-be-started-due-to-unsupported-characters-in-the-vm-name.aspx)

### Servidor de configuración, Servidor de proceso y Destino principal

Servidor de configuración (CS), Servidor de proceso (PS) y Destino principal (MT)

-   [El host ESXi en el que se hospeda PS/CS, ya que se produce un error en VM con una pantalla morada de la muerte.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

<!---HONumber=July15_HO4-->