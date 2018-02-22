---
title: Herramienta de planeamiento de capacidad de Hyper-V para Azure Site Recovery | Microsoft Docs
description: "En este artículo se describe cómo ejecutar la herramienta de planeamiento de capacidad de Hyper-V para Azure Site Recovery."
services: site-recovery
documentationcenter: na
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 2bc3832f-4d6e-458d-bf0c-f00567200ca0
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: nisoneji
ms.openlocfilehash: 80c3dcb65bd74bcfa3acc5f12dd5c45cd1c06455
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="hyper-v-capacity-planning-tool-for-site-recovery"></a>Herramienta de planeamiento de capacidad de Hyper-V para Site Recovery

Ahora hay disponible una nueva versión mejorada de [Azure Site Recovery Deployment Planner en el escenario de Hyper-V en Azure](site-recovery-hyper-v-deployment-planner.md). Reemplaza a la antigua herramienta. Use la herramienta nueva para el planeamiento de la implementación. La herramienta proporciona las directrices siguientes:

* Evaluación de la idoneidad de las máquinas virtuales en función del número de discos, el tamaño de estos, las IOPS, la actividad de datos y algunas otras características de las máquinas virtuales.
* Necesidad de ancho de banda de red frente a evaluación de RPO.
* Requisitos de infraestructura de Azure.
* Requisitos de la infraestructura local.
* Guía para el procesamiento por lotes de la replicación inicial.
* Costo total estimado de recuperación ante desastres en Azure.

Como parte de la implementación de Azure Site Recovery, debe averiguar los requisitos de ancho de banda y replicación. La herramienta de planeamiento de capacidad de Hyper-V para Site Recovery le ayuda a determinar estos requisitos para la replicación de máquinas virtuales de Hyper-V.

En este artículo se describe cómo ejecutar la herramienta de planeamiento de capacidad de Hyper-V. Esta herramienta puede utilizarse junto con la información que se describe en [Planeamiento de la capacidad para proteger las máquinas virtuales de Hyper-V con Site Recovery](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Antes de comenzar
Ejecute la herramienta en un servidor de Hyper-V o el nodo de un clúster en su sitio principal. Para ejecutar la herramienta, los servidores host de Hyper-V necesitan:

* Sistema operativo: Windows Server 2012 o 2012 R2
* Memoria: 20 MB (mínimo)
* CPU: sobrecarga del 5 por ciento (mínimo)
* Espacio en disco: 5 MB (mínimo)

Antes de ejecutar la herramienta, debe preparar el sitio principal. Si va a replicar entre dos sitios locales y desea comprobar el ancho de banda, debe preparar también un servidor de réplica.

## <a name="step-1-prepare-the-primary-site"></a>Paso 1: Preparar el sitio primario

1. En el sitio primario, haga una lista de todas las máquinas virtuales de Hyper-V que desea replicar. Enumere los hosts de Hyper-V o los clústeres donde se encuentran. Se puede ejecutar la herramienta para varios hosts independientes o para un único clúster, pero no ambos juntos. También debe ejecutarse por separado para cada sistema operativo. Recopile la siguiente información acerca de los servidores de Hyper-V:

   * Servidores independientes de Windows Server 2012
   * Clústeres de Windows Server 2012
   * Servidores independientes de Windows Server 2012 R2
   * Clústeres de Windows Server 2012 R2

2. Habilite el acceso remoto a Instrumental de administración de Windows en todos los hosts de Hyper-V y los clústeres. Ejecute este comando en cada servidor o clúster para asegurarse de que las reglas de firewall y los permisos de usuario estén establecidos:

        netsh firewall set service RemoteAdmin enable
3. Habilite la supervisión de rendimiento en servidores y clústeres de la siguiente manera:

   a. Abra el Firewall de Windows con el complemento **Seguridad avanzada**. 
   
   b. Seleccione la regla de entrada **Acceso a red COM+ (DCOM de entrada)**. Seleccione todas las reglas del grupo **Administración remota de registro de eventos**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Paso 2: Preparar un servidor de réplica (replicación entre dos sitios locales)
No es necesario seguir este paso si va a replicar en Azure.

Se recomienda que configure un único host de Hyper-V como servidor de recuperación para que se pueda replicar una máquina virtual ficticia y así comprobar el ancho de banda. Puede pasar por alto este paso, pero no podrá medir el ancho de banda a menos que lo complete.

1. Si desea usar un nodo de clúster como réplica, configure el agente de réplicas de Hyper-V:

   a. En **Administrador del servidor**, abra **Administrador de clúster de conmutación por error**.

   b. Conéctese al clúster y resalte el nombre del clúster. Seleccione **Acciones** > **Configurar rol** para abrir el Asistente para alta disponibilidad.

   c. En **Seleccionar rol**, seleccione **Agente de réplicas de Hyper-V**. En el asistente, escriba un nombre para **Nombre de NetBIOS**. Escriba la **Dirección IP** que se usará como punto de conexión con el clúster (denominado punto de acceso cliente). Se configura el **Agente de réplicas de Hyper-V**, lo que proporciona un nombre de punto de acceso cliente que debe anotar.

   d. Compruebe que el rol del agente de Réplica de Hyper-V se conecte correctamente y puede conmutar por error entre todos los nodos del clúster. Haga clic con el botón derecho en el rol y seleccione **Mover** > **Seleccionar nodo**. Seleccione un nodo y luego **Aceptar**.

   e. Si usa la autenticación basada en certificados, asegúrese de que todos los nodos del clúster y el punto de acceso cliente tengan instalado el certificado.

2. Para habilitar un servidor de réplica, siga estos pasos:

   a. Para un clúster, abra **Failure Cluster Manager** y conéctese al clúster. Seleccione **Roles**y, a continuación, seleccione un rol. Seleccione **Configuración de la replicación** > **Enable this cluster as a Replica server** (Habilitar este clúster como servidor de réplica). Si usa un clúster como réplica, el rol de Agente de réplicas de Hyper-V debe estar presente también en el clúster del sitio principal.

   b. Para un servidor independiente, abra el **Administrador de Hyper-V**. En el panel **Acciones**, seleccione **Configuración de Hyper-V** para el servidor que desea habilitar. En **Configuración de la replicación**, seleccione **Enable this computer as a Replica server** (Habilitar este equipo como servidor de réplica).

3. Para configurar la autenticación, siga estos pasos:

   a. En **Autenticación y puertos**, seleccione cómo autenticar el servidor principal y los puertos de autenticación. Si usa un certificado, haga clic en **Seleccionar certificado** para seleccionar uno. Use Kerberos si los hosts de Hyper-V principal y de recuperación se encuentran en el mismo dominio o en dominios de confianza. Use certificados si los dominios son diferentes o si la implementación es para un grupo de trabajo.

   b. En la sección **Autorización y almacenamiento**, especifique si se permite que **cualquier** servidor autenticado (principal) envíe datos de replicación a este servidor de réplica.

   ![Configuración de la replicación](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

   c. Para comprobar que el agente de escucha se esté ejecutando para el puerto y el protocolo especificados, ejecute **netsh http show servicestate**. 

4. Configure los firewalls. Durante la instalación de Hyper-V, se crean reglas de firewall para permitir el tráfico en los puertos predeterminados (HTTPS en el 443 y Kerberos en el 80). Habilite estas reglas como se indica:

    - Autenticación de certificados en clústeres (443): ``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
    - Autenticación Kerberos en clústeres (80): ``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
    - Autenticación de certificados en un servidor independiente: ``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
    - Autenticación Kerberos en un servidor independiente:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planning-tool"></a>Paso 3: Ejecutar la herramienta de planeamiento de capacidad
Después de preparar el sitio principal y configurar un servidor de recuperación, puede ejecutar la herramienta.

1. [Descargue](https://www.microsoft.com/download/details.aspx?id=39057) la herramienta del Centro de descarga de Microsoft.

2. Ejecute la herramienta desde uno de los servidores principales o uno de los nodos del clúster principal. Haga clic con el botón derecho del ratón en el archivo .exe y, a continuación, elija **Ejecutar como administrador**.

3. En **Before you begin** (Antes de comenzar), especifique durante cuánto tiempo desea recopilar datos. Se recomienda que ejecute la herramienta durante las horas de producción para asegurarse de que los datos sean representativos. Si solo desea validar la conectividad de red, puede recopilarlos solo durante un minuto.

    ![Antes de empezar](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. En **Primary Site Details** (Detalles del sitio principal), especifique el nombre del servidor o el FQDN para un host independiente. Para un clúster, especifique el FQDN del punto de acceso cliente, el nombre de clúster o cualquier nodo del clúster. Seleccione **Siguiente**. La herramienta detecta automáticamente el nombre del servidor donde se ejecuta. La herramienta detecta las máquinas virtuales que se pueden supervisar para los servidores especificados.

    ![Detalles del sitio principal](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. En **Replica Site Details** (Detalles del sitio de réplica), si va a replicar en Azure o en un centro de datos secundario y no ha configurado un servidor de réplica, seleccione **Skip tests involving replica site** (Omitir pruebas con el sitio de réplica). Si va a replicar en un centro de datos secundario y ha configurado un tipo de réplica, escriba el FQDN del servidor independiente o el punto de acceso cliente para el clúster en **Server name (or) Hyper-V Replica Broker CAP**(Nombre de servidor o punto de acceso cliente del Agente de réplicas de Hyper-V).

    ![Detalles del sitio de réplica](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. En **Extended Replica Details** (Detalles de réplica extendida), seleccione **Skip the tests involving Extended Replica site** (Omitir pruebas con el sitio de réplica extendida). Estas pruebas son compatibles con Site Recovery.

7. En **Choose VMs to Replicate** (Elegir máquinas virtuales para replicar), la herramienta se conecta al servidor o clúster. Muestra las máquinas virtuales y los discos que se ejecutan en el servidor principal, según la configuración que ha especificado en la página **Primary Site Details** (Detalles del sitio principal). Tenga en cuenta que no se mostrarán las máquinas virtuales que ya estén habilitadas para la replicación o que no se estén ejecutando. Seleccione las máquinas virtuales para las que desea recopilar métricas. Si selecciona los discos duros virtuales, se recopilan también los datos de las máquinas virtuales automáticamente.

8. Si configuró un servidor de réplica o un clúster, en **Network information** (Información de red), especifique el ancho de banda WAN aproximado que se utilizará entre los sitios principal y de réplica. Si configuró la autenticación de certificado, seleccione los certificados.

    ![Información de red](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

9. En **Summary** (Resumen), compruebe la configuración. Seleccione **Next** (Siguiente) para empezar a recopilar métricas. El progreso y el estado de la herramienta se muestran en la página **Calculate Capacity** (Calcular capacidad). Cuando se termine de ejecutar la herramienta, seleccione **View Report** (Ver informe) para ver los resultados. De forma predeterminada, los informes y los registros se almacenan en **%systemdrive%\Users\Public\Documents\Capacity Planner**.

   ![Calcular la capacidad](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>Paso 4: Interpretar los resultados

Estas son las métricas importantes. Puede pasar por alto las métricas que no aparezcan aquí, porque no son relevantes para Site Recovery.

### <a name="on-premises-to-on-premises-replication"></a>Replicación entre dos sitios locales

* Impacto de la replicación en el proceso del host principal y la memoria
* Impacto de la replicación en espacio en disco de almacenamiento e IOPS del host principal y del host de recuperación
* Ancho de banda total necesario para la replicación delta (Mbps)
* Ancho de banda de red observado entre el host primario y el host de recuperación (Mbps)
* Sugerencia para el número ideal de transferencias paralelas activas entre dos hosts o clústeres

### <a name="on-premises-to-azure-replication"></a>Replicación desde un sitio local en Azure

* Impacto de la replicación en el proceso del host principal y la memoria
* Impacto de la replicación en espacio en disco de almacenamiento e IOPS del host principal
* Ancho de banda total necesario para la replicación delta (Mbps)

## <a name="more-resources"></a>Más recursos

* Para obtener más información sobre la herramienta, lea el documento que acompaña a la descarga de la herramienta.
* Vea un tutorial de la herramienta en el [blog de TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx) de Keith Mayer.
* [Obtenga los resultados](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) de la prueba de rendimiento para la replicación de Hyper-V entre sitios locales.

## <a name="next-steps"></a>pasos siguientes

Cuando termine el planeamiento de la capacidad, puede implementar Site Recovery:
* [Replicación de máquinas virtuales de Hyper-V de nubes VMM en Azure](site-recovery-vmm-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V (sin VMM) en Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V entre sitios VMM](site-recovery-vmm-to-vmm.md)
