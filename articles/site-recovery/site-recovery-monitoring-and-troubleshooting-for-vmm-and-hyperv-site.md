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
	ms.date="06/16/2015" 
	ms.author="anbacker"/>
	
# Guía de solución de problemas y supervisión de VMM y de la protección de sitios de Hyper-V

Esta guía de solución de problemas y supervisión le permite aprender el seguimiento del estado de replicación y técnicas de solución de recuperación de Azure Site Recovery.

## Descripción de los componentes

### Implementación del sitio de VMM para la replicación entre el sitio local.

Como parte de la configuración de la recuperación ante desastres entre dos ubicaciones locales, es necesario descargar el proveedor de Azure Site Recovery Provider e instalarlo en el servidor VMM. El proveedor necesita conectividad a Internet para asegurarse de que todas las operaciones desencadenadas desde el Portal de Azure se traducen en operaciones locales como habilitar la protección, el apagado de máquinas de lado principal como parte de las conmutaciones por error etc.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image1.png)

### Implementación del sitio de VMM para la replicación entre el sitio local y Azure.

Como parte de la configuración de la recuperación ante desastres entre el sitio local local y Azure; es necesario descargar e instalar el proveedor de Azure Site Recovery Provider en el servidor VMM junto con el agente de Servicios de recuperación de Azure que debe instalarse en cada host de Hyper-V.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image2.png)

### Implementación del sitio de Hyper-V para la replicación entre el sitio local y Azure

Es el mismo que el de la implementación de VMM: solo se diferencia en la instalación de proveedores y agentes en el propio host de Hyper-V.

## Supervisar las operaciones de configuración, protección y recuperación

Cada operación de ASR se audita y se rastre en la ficha "TRABAJOS". En el caso de cualquier error de configuración, protección o recuperación, navegue hasta la ficha TRABAJOS y consulte si hay errores.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image3.png)

Una vez que encuentre errores en la vista de TRABAJOS, seleccione el TRABAJO y haga clic en DETALLES DEL ERROR correspondiente a ese trabajo.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image4.png)

Los detalles del error le ayudarán a identificar la causa posible y la recomendación para el problema.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image5.png)

En el caso anterior parece haber otra operación que está en curso debido a la que la falla la configuración de protección. Asegúrese de resolver el problema según la recomendación: a continuación, haga clic en REINICIAR para volver a iniciar la operación.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image6.png)

La opción de REINICIAR no está disponible para todas las operaciones: para las que no tienen la opción de REINICIAR, regrese al objeto y vuelva a realizar la operación. Cada TRABAJO se puede cancelar en cualquier momento mientras se ejecuta mediante el botón de CANCELAR.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image7.png)

## Supervisar el estado de replicación de la máquina virtual

Supervisión central y remota del proveedor ASR a través del Portal de Azure para cada una de las entidades protegidas. Desplácese hasta los ELEMENTOS PROTEGIDOS. A continuación, seleccione NUBES de VMM o GRUPOS DE PROTECCIÓN. La pestaña NUBES de VMM es solo para implementaciones de VMM y todos los demás escenarios tienen las entidades protegidas en la ficha GRUPOS DE PROTECCIÓN.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image8.png)

A continuación, seleccione la entidad protegida en la nube correspondiente o en el grupo de protección. Una vez seleccionada la entidad protegida, se muestran todas las operaciones permitidas en el panel inferior.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image9.png)

Como se indicó anteriormente en «El ESTADO de la máquina virtual es crítico», puede hacer clic en el botón DETALLES DEL ERROR de la parte inferior para ver el error. Basándose en las "Posibles causas" y "Recomendación" mencionadas, resuelva el problema: aquí en este caso la máquina virtual debe volver a sincronizarse, lo cual se puede hacer desde el propio portal haciendo clic en el botón VOLVER A SINCRONIZAR.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image10.png)

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image11.png)

Nota: Si hay operaciones activas que están en curso o con errores, a continuación, navegue a la vista de TRABAJOS como se mencionó anteriormente, para ver el error específico del TRABAJO.

## Solucionar problemas locales

Conéctese a la consola del administrador de Hyper-V local, seleccione la máquina virtual y visualice el estado de replicación.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image12.png)

En este caso *Estado de replicación* se indica como crítico: *Ver el estado de replicación* para ver los detalles.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image13.png)

#### Visor de eventos

| Escenarios | Orígenes de eventos |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Protección de sitios VMM | VMM Server <ul><li> **Registros de aplicaciones y servicios/Microsoft/VirtualMachineManager/Server/Admin** </li></ul> Hyper-V Host <ul><li> **Registros de aplicaciones y servicios/MicrosoftAzureRecoveryServices/Replication** (para Azure como objetivo)</li><li> **Registros de aplicaciones y servicios/Microsoft/Windows/Hyper-V-VMMS/Admin** </li></ul> |
| Protección de sitios Hyper-V | <ul><li> **Registros de aplicaciones y servicios/MicrosoftAzureRecoveryServices/Replication** </li><li> **Registros de aplicaciones y servicios/Microsoft/Azure Site Recovery/Provider/Operational** </li><li> **Registros de aplicaciones y servicios/Microsoft/Windows/Hyper-V-VMMS/Admin** </li><ul>|

#### Opciones de registro de Replicación de Hyper-V

Todos los eventos relacionados con la Réplica de Hyper-V se registran en el registro de Hyper-V-VMMS\Admin ubicado en **Registros de aplicaciones y servicios\Microsoft\Windows**. Además, puede habilitarse un registro analítico para Hyper-V-VMMS. Para habilitar este registro, asegúrese primero de que los registros analíticos y de depuración puedan verse en el Visor de eventos. Abra el Visor de eventos y, a continuación, en el **menú Ver**, haga clic en **Mostrar registros analíticos y de depuración**.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image14.png)

Puede verse un registro analítico en Hyper-V-VMMS

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image15.png)

En el panel **Acciones**, haga clic en **Habilitar registro**. Una vez habilitado, aparece en **Monitor de rendimiento** como una sesión de seguimiento de eventos situada en **Conjuntos de recopiladores de datos.**

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image16.png)

Para ver la información recopilada, primero detenga la sesión de seguimiento deshabilitando el registro y, a continuación, guarde el registro y vuelva a abrirlo en el Visor de eventos o use otras herramientas para convertirlo según sea necesario.


## Descripción del ciclo de vida de la máquina virtual

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image17.png)


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
    > ](http://www.microsoft.com/es-es/download/details.aspx?id=29016)

<!---HONumber=62-->