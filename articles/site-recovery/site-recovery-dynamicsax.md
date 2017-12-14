---
title: "Replicación de una implementación de Dynamics AX en varios niveles múltiples mediante Azure Site Recovery | Microsoft Docs"
description: "En este artículo se explica cómo replicar y proteger Dynamics AX mediante Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: asgang
ms.openlocfilehash: 8ffc4a5a573b1c5951fab98fb766aed36f626fe4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Replicación de una implementación de Dynamics AX en varios niveles múltiples mediante Azure Site Recovery

## <a name="overview"></a>Información general


 Dynamics AX es una de las soluciones de ERP más utilizadas por las empresas para estandarizar procesos en diferentes ubicaciones, administrar recursos y simplificar el cumplimiento. Dado que la aplicación es crítica para una organización, si se produce un desastre, la aplicación debe poder ponerse en funcionamiento en un tiempo mínimo.

En la actualidad, Dynamics AX no cuenta con ninguna funcionalidad de recuperación ante desastres de forma estándar. Dynamics AX consta de muchos componentes de servidor, como Windows Application Object Server, Azure Active Directory, Azure SQL Database, SharePoint Server y Reporting Services. La administración manual de la recuperación ante desastres de estos componentes no solo es cara, sino también propensa a errores.

En este artículo se explica cómo crear una solución de recuperación ante desastres para la aplicación Dynamics AX mediante [Azure Site Recovery](site-recovery-overview.md). También se describen las conmutaciones por error de prueba (tanto planeadas como no planeadas) mediante el plan de recuperación con un solo clic, las configuraciones admitidas y los requisitos previos.



## <a name="prerequisites"></a>Requisitos previos

La implementación de la recuperación ante desastres para aplicaciones de Dynamics AX mediante Site Recovery requiere los siguientes requisitos previos:

• Instale una implementación de Dynamics AX local.

• Cree un almacén de Site Recovery en una suscripción de Azure.

• Si Azure es el sitio de recuperación, ejecute la herramienta Evaluación de preparación de máquinas virtuales de Azure en las máquinas virtuales. Deben ser compatibles con los servicios Azure Virtual Machines y Site Recovery.

## <a name="site-recovery-support"></a>Compatibilidad de Site Recovery

Para elaborar este artículo, se utilizaron máquinas virtuales de VMware con Dynamics AX 2012 R3 en Windows Server 2012 R2 Enterprise. Dado que la replicación de la recuperación del sitio no depende de la aplicación, se pretende que estas recomendaciones sirvan también para los escenarios siguientes.

### <a name="source-and-target"></a>Origen y destino

**Escenario** | **En un sitio secundario** | **En Azure**
--- | --- | ---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí
**Servidor físico** | Sí | Sí

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Habilitación de la recuperación ante desastres de la aplicación Dynamics AX mediante Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Proteger la aplicación Dynamics AX
Para habilitar la recuperación y replicación completas de la aplicación, es preciso proteger todos los componentes de Dynamics AX. 

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Configuración de la replicación de DNS y Active Directory

Para que la aplicación Dynamics AX funcione, Active Directory debe encontrarse en el sitio de recuperación ante desastres. Hay dos opciones recomendadas en función de la complejidad del entorno local del cliente.

**Opción 1**

El cliente tiene un número pequeño de aplicaciones y un solo controlador de dominio para todo el sitio local y planea realizar una conmutación por error de todo el sitio. Se recomienda utilizar la replicación de Site Recovery para replicar la máquina del controlador de dominio en un sitio secundario (se puede aplicar a escenarios de sitio a sitio y de sitio a Azure).

**Opción 2**

El cliente tiene muchas aplicaciones, ejecuta un bosque de Active Directory y planea realizar una conmutación por error de unas pocas simultáneamente. Se recomienda configurar un controlador de dominio adicional en el sitio de recuperación ante desastres (un sitio secundario o en Azure).

 Para más información, consulte [Protección de Active Directory y DNS con Azure Site Recovery](site-recovery-active-directory.md). En el resto del documento, se asume que hay un controlador de dominio disponible en el sitio de recuperación ante desastres.

### <a name="2-set-up-sql-server-replication"></a>2. Configuración de la replicación de SQL Server
Para obtener orientación técnica acerca de la opción recomendada para proteger el nivel de SQL, consulte [Replicación de aplicaciones con SQL Server y Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Habilitación de la protección del cliente de Dynamics AX de las y máquinas virtuales de Application Object Server
Realice la configuración pertinente de Site Recovery en función de si las máquinas virtuales están implementadas en [Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Se recomienda configurar la frecuencia coherente con los bloqueos en 15 minutos.
>

La siguiente instantánea muestra el estado de protección de las máquinas virtuales del componente de Dynamics en un escenario de protección de sitio de VMware a Azure.

![Elementos protegidos](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configuración de las redes
**Configuración del proceso y las opciones de red de máquinas virtuales**

Tanto en el cliente de Dynamics AX y las máquinas virtuales de Application Object Server, configure las opciones de red en Site Recovery para que las redes de máquinas virtuales se conecten a la red de recuperación ante desastres adecuada después de la conmutación por error. Asegúrese de que la red de recuperación ante desastres para estos niveles pueda enrutarse al nivel de SQL.

Puede seleccionar la máquina virtual en los elementos replicados para configurar las opciones de red como se muestra en la siguiente instantánea:

* En el caso de los servidores de Application Object Server, seleccione el conjunto de disponibilidad correcto.

* Si utiliza una IP estática, especifique en el campo **IP de destino** la dirección IP que desea que use la máquina virtual.

    ![Configuración de red ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png).


### <a name="5-create-a-recovery-plan"></a>5. Creación de un plan de recuperación

En Site Recovery se puede crear un plan de recuperación para automatizar el proceso de conmutación por error. Agregue un nivel de aplicación y un nivel de web al plan de recuperación. Ordénelos en grupos diferentes, con el fin de que el front-end se apague antes que el nivel de aplicación.

1. Seleccione el almacén de Azure Site Recovery en su suscripción y haga clic en el icono **Planes de recuperación**.

2. Seleccione **Plan de recuperación** y especifique un nombre.

3. Seleccione los valores apropiados en **Origen** y **Destino**. El destino puede ser Azure o un sitio secundario. Si elige Azure, debe especificar el modelo de implementación.

    ![Creación de un plan de recuperación](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Seleccione las máquinas virtuales cliente y de Application Object Server para el plan de recuperación y seleccione el ✓.

    ![Selección de elementos](./media/site-recovery-dynamics-ax/selectvms.png)

    Ejemplo de plan de recuperación:

    ![Detalles de plan de recuperación](./media/site-recovery-dynamics-ax/recoveryplan.png)

Para personalizar el plan de recuperación de la aplicación Dynamics AX, agregue los siguientes pasos. La instantánea anterior muestra el plan de recuperación completo una vez que se han agregado todos los pasos.


* **Pasos de conmutación por error de SQL Server**: para obtener información acerca de los pasos de recuperación específicos para SQL Server, consulte [Replicación de aplicaciones con SQL Server y Azure Site Recovery](site-recovery-sql.md).

* **Grupo de conmutación por error 1**: conmutar por error las máquinas virtuales de Application Object Server.
Asegúrese de que el punto de recuperación seleccionado esté lo más cercano posible a la base de datos PIT, sin adelantarla.

* **Script**: agregar un equilibrador de carga (solo E-A).
Agregue un script (a través de Azure Automation) después de que se active el grupo de máquinas virtuales de Application Object Server para agregarle un equilibrador de carga. Puede usar un script para realizar esta tarea. Para más información, consulte el artículo [Cloud migration and disaster recovery of load balanced multi-tier applications](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/) (Migración a la nube y recuperación ante desastres de aplicaciones de niveles múltiples).

* **Grupo de conmutación por error 2**: conmutar por error las máquinas virtuales del cliente de Dynamics AX. Conmute por error las máquinas virtuales de nivel web como parte del plan de recuperación.


### <a name="perform-a-test-failover"></a>Realización de una conmutación por error de prueba

Para más información específica acerca de Active Directory durante la conmutación por error de prueba, consulte la guía complementaria de "solución de recuperación ante desastres de Active Directory". 

Para más información específica acerca de Active Directory durante la conmutación por error de prueba, consulte [Replicación de aplicaciones con SQL Server y Azure Site Recovery](site-recovery-sql.md).

1. Vaya a Azure Portal, seleccione su almacén de Site Recovery.

2. Seleccione el plan de recuperación creado para Dynamics AX.

3. Seleccione **Conmutación por error de prueba**.

4. Seleccione la red virtual para iniciar el proceso de conmutación por error de prueba.

5. Cuando el entorno secundario esté activo, podrá realizar las validaciones.

6. Una vez que se hayan completado las validaciones, seleccione **Validations complete** (Validaciones completas) para que el entorno de la conmutación por error de prueba quede limpio.

Para más información acerca de la realización de una conmutación por error de prueba, consulte [Conmutación por error de prueba a Azure en Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Realización de una conmutación por error

1. Vaya a Azure Portal, seleccione su almacén de Site Recovery.

2. Seleccione el plan de recuperación creado para Dynamics AX.

3. Seleccione **Conmutación por error** y haga clic en **Conmutación por error**.

4. Seleccione la red de destino y haga clic en **✓** para iniciar el proceso de conmutación por error.

Para más información acerca de cómo realizar una conmutación por error, consulte [Conmutación por error en Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Realización de una conmutación por recuperación

Para ver consideraciones específicas acerca de Active Directory durante la conmutación por recuperación, consulte [Replicación de aplicaciones con SQL Server y Azure Site Recovery](site-recovery-sql.md).

1. Vaya a Azure Portal, seleccione su almacén de Site Recovery.

2. Seleccione el plan de recuperación creado para Dynamics AX.

3. Seleccione **Conmutación por error** y haga clic en **Conmutación por error**.

4. Seleccione **Cambiar dirección**.

5. Seleccione las opciones adecuadas: sincronización de datos y creación de máquinas virtuales.

6. Seleccione **✓** para iniciar el proceso de conmutación por recuperación.


Para más información acerca de cómo realizar una conmutación por recuperación, consulte [Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos al sitio local](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Resumen
Con Azure Site Recovery puede crear un plan completo de recuperación ante desastres automatizado para su aplicación Dynamics AX. Si se produce una interrupción, puede iniciar la conmutación por error en cuestión de segundos desde cualquier lugar y poner en funcionamiento la aplicación en pocos minutos.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de cómo proteger las cargas de trabajo empresariales con Azure Site Recovery, consulte [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](site-recovery-workload.md) .
