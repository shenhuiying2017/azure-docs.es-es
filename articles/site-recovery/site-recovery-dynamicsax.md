---
title: "Replicación de una implementación de Dynamics AX de niveles múltiples mediante Azure Site Recovery | Microsoft Docs"
description: "En este artículo se explica cómo se replica y protege Dynamics AX mediante Azure Site Recovery."
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
ms.date: 8/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 03127c8f4841b67436c4819628319705af0b2cd5
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---
# <a name="replicate-a-multi-tier-dynamics-ax-application-using-azure-site-recovery"></a>Replicación de una aplicación Dynamics AX de niveles múltiples mediante Azure Site Recovery

## <a name="overview"></a>Información general


Microsoft Dynamics AX es una de las soluciones de ERP más populares entre las empresas para estandarizar procesos en diferentes ubicaciones, administrar recursos y simplificar el cumplimiento. Considerar que la aplicación es esencial para la actividad empresarial de una organización es muy importante para asegurarse de que, en el caso de un desastre, la aplicación debe volver a estar activa y en funcionamiento en un tiempo mínimo.

En la actualidad, la aplicación Microsoft Dynamics AX no tiene integrada ninguna funcionalidad de recuperación ante desastres. Microsoft Dynamics AX consta de muchos componentes de servidor, como Application Object Server, Active Directory (AD), SQL Database Server, SharePoint Server, servidor de informes, etc. Administrar manualmente la recuperación ante desastres de todos estos componentes no solo es costoso, sino también propenso a errores.

En este artículo se explica en detalle cómo crear una solución de recuperación ante desastres para la aplicación Dynamics AX mediante [Azure Site Recovery](site-recovery-overview.md). También se describen las conmutaciones por error planeadas, no planeadas o de prueba con el plan de recuperación de un solo clic, las configuraciones admitidas y los requisitos previos.
La solución de recuperación ante desastres de Azure Site Recovery está totalmente probada, certificada y recomendada por Microsoft Dynamics AX.



## <a name="prerequisites"></a>Requisitos previos

La implementación de la recuperación ante desastres para aplicaciones de Dynamics AX mediante Azure Site Recovery requiere completar primero los siguientes requisitos previos.

• Se ha instalado una implementación local de Dynamics AX

• Se ha creado un almacén de Azure Recovery Services en una suscripción de Microsoft Azure

• Además, si Azure es su sitio de recuperación, debe ejecutar la herramienta de evaluación de la disponibilidad de máquinas virtuales de Azure en las máquinas virtuales para asegurarse de que son compatibles con las máquinas virtuales de Azure y Azure Site Recovery Services.


## <a name="site-recovery-support"></a>Compatibilidad de Site Recovery

Para elaborar este artículo, se utilizaron máquinas virtuales de VMware con Dynamics AX 2012 R3 en Windows Server 2012 R2 Enterprise. Como la replicación de la recuperación del sitio no depende de la aplicación, se pretende que estas recomendaciones sirvan también para los escenarios siguientes.

### <a name="source-and-target"></a>Origen y destino

**Escenario** | **En un sitio secundario** | **En Azure**
--- | --- | ---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí
**Servidor físico** | Sí | Sí

## <a name="enable-dr-of-dynamics-ax-application-using-azure-site-recovery"></a>Habilitar la recuperación ante desastres de la aplicación Dynamics AX mediante Azure Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Proteger la aplicación Dynamics AX
Cada componente de Dynamics AX debe protegerse para permitir una replicación y recuperación completas de la aplicación. En esta sección se describe:

**1. Protección de Active Directory**

**2. Protección del nivel de SQL**

**3. Protección de los niveles de aplicación y web**

**4. Configuración de red**

**5. Plan de recuperación**

### <a name="1-setup-ad-and-dns-replication"></a>1. Configuración de la replicación de DNS y AD

Se requiere Active Directory en el sitio de recuperación ante desastres para que funcione la aplicación Dynamics AX. Hay dos opciones recomendadas según la complejidad del entorno local del cliente.

**Opción 1**

Si el cliente tiene un número pequeño de aplicaciones, un único controlador de dominio para todo el sitio local y realizará conmutaciones por error en todo el sitio conjuntamente, se recomienda utilizar la replicación de Azure Site Recovery para replicar la máquina del controlador de dominio en un sitio secundario (esto se aplica tanto de sitio a sitio como de sitio a Azure).

**Opción 2**

Si el cliente tiene un gran número de aplicaciones, ejecuta un bosque de Active Directory y realizará conmutaciones por error de unas pocas aplicaciones a la vez, se recomienda configurar un controlador de dominio adicional en el sitio de recuperación ante desastres (ya sea un sitio secundario o Azure).

Consulte la [guía complementaria sobre la realización de un controlador de dominio disponible en el sitio de recuperación ante desastres](site-recovery-active-directory.md). Para el resto de este documento, supondremos que habrá un controlador de dominio disponible en el sitio de recuperación ante desastres.

### <a name="2-setup-sql-server-replication"></a>2. Configuración de la replicación de SQL Server
Consulte la guía complementaria para obtener orientación técnica detallada sobre la opción recomendada para proteger el [nivel SQL](site-recovery-sql.md).

### <a name="3-enable-protection-for-dynamics-ax-client-and-aos-vms"></a>3. Habilitar la protección del cliente de Dynamics AX y las máquinas virtuales de AOS
Realice la configuración pertinente de Azure Site Recovery en función de si las máquinas virtuales están implementadas en [Hyper-V](site-recovery-hyper-v-site-to-azure.md) o en [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Se recomienda configurar una frecuencia coherente de bloqueo de 15 minutos.
>

La siguiente instantánea muestra el estado de protección de las máquinas virtuales del componente de Dynamics en un escenario de protección de sitio de VMware a Azure.
![Elementos protegidos ](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configuración de red
Configuración de procesos de máquina virtual y configuración de red

En el cliente de AX y las máquinas virtuales de AOS, configure las opciones de red en Azure Site Recovery para que las redes de máquinas virtuales se conecten a la red de recuperación ante desastres adecuada después de la conmutación por error. Asegúrese de que la red de recuperación ante desastres para estos niveles pueda enrutarse en el nivel de SQL.

Puede seleccionar la máquina virtual en los elementos replicados para configurar las opciones de red como se muestra en la siguiente instantánea.

* En servidores de AOS, seleccione el conjunto de disponibilidad correcto.

* Si utiliza una dirección IP estática, especifique la dirección IP que desea utilizar con la máquina virtual en el campo **IP de destino** ![Configuración de red ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)



### <a name="5-creating-a-recovery-plan"></a>5. Creación de un plan de recuperación

Puede crear un plan de recuperación en Azure Site Recovery para automatizar el proceso de conmutación por error. Agregue el nivel de aplicación y el nivel web en el plan de recuperación. Ordénelas en diferentes grupos para que el front-end se apague antes del nivel de aplicación.

1)  Seleccione el almacén de Azure Site Recovery en su suscripción y haga clic en el icono "Planes de recuperación".

2)  Haga clic en Plan de recuperación y especifique un nombre.

3)  Seleccione el origen y el destino. El destino puede ser el sitio secundario o de Azure. Si elige Azure, debe especificar el modelo de implementación.

![Creación del plan de recuperación](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4)  Seleccione el AOS y las máquinas virtuales cliente del plan de recuperación y haga clic en ✓.
![Crear plan de recuperación](./media/site-recovery-dynamics-ax/selectvms.png)


![Plan de recuperación](./media/site-recovery-dynamics-ax/recoveryplan.png)

Puede personalizar el plan de recuperación de la aplicación Dynamics AX añadiendo varios pasos, tal como se detalla a continuación. La instantánea anterior muestra el plan de recuperación completo después de agregar todos los pasos.

*Pasos:*

*1. Pasos de conmutación por error de SQL Server*

Consulte la guía complementaria [Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery](site-recovery-sql.md) para obtener más información sobre los pasos de recuperación específicos de SQL Server.

*2. Grupo 1 de conmutación por error: Conmutación por error de las máquinas virtuales de AOS*

Asegúrese de que el punto de recuperación seleccionado esté lo más cercano posible a la base de datos PIT, sin adelantarla.

*3. Script: Agregue el equilibrador de carga (solo E-A)* Agregue un script (a través de Azure Automation) después del grupo de máquinas virtuales de AOS para agregarle un equilibrador de carga. Puede usar un script para realizar esta tarea. Consulte el artículo sobre [cómo agregar el equilibrador de carga en una recuperación ante desastres con aplicaciones de varios niveles](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)

*4. Grupo 2 de conmutación por error: Conmutación por error de las máquinas virtuales de AX*
Conmute por error las máquinas virtuales de nivel web como parte del plan de recuperación.


### <a name="doing-a-test-failover"></a>Realización de una conmutación por error de prueba

Consulte las guías complementarias Solución de recuperación ante desastres de AD y Solución de recuperación ante desastres de SQL Server para obtener consideraciones específicas de AD y SQL server respectivamente durante una conmutación por error de prueba.

1.  En Azure Portal, seleccione su almacén de recuperación del sitio.
2.  Haga clic en el plan de recuperación creado para Dynamics AX.
3.  Haga clic en Probar conmutación por error.
4.  Seleccione la red virtual para iniciar el proceso de conmutación por error de prueba.
5.  Cuando el entorno secundario esté activo, podrá realizar las validaciones.
6.  Una vez que se hayan completado las validaciones, puede seleccionar ‘Validations complete’ (Validaciones completas) para que el entono de la conmutación por error de prueba quede limpio.

Siga [estas directrices](site-recovery-test-failover-to-azure.md) para llevar a cabo una conmutación por error de prueba.

### <a name="doing-a-failover"></a>Realización de una conmutación por error

1.  En Azure Portal, seleccione su almacén de recuperación del sitio.
2.  Haga clic en el plan de recuperación creado para Dynamics AX.
3.  Haga clic en Conmutación por error y seleccione Conmutación por error.
4.  Seleccione la red de destino y haga clic en ✓ para iniciar el proceso de conmutación por error.

Siga [estas directrices](site-recovery-failover.md) cuando realice una conmutación por error.

### <a name="perform-a-failback"></a>Realización de una conmutación por recuperación

Consulte la guía complementaria Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery para obtener información específica de SQL Server durante una conmutación por recuperación.

1.  En Azure Portal, seleccione su almacén de recuperación del sitio.
2.  Haga clic en el plan de recuperación creado para Dynamics AX.
3.  Haga clic en Conmutación por error y seleccione Conmutación por error.
4.  Haga clic en Cambiar dirección.
5.  Seleccione las opciones adecuadas de sincronización de datos y de creación de máquinas virtuales.
6.  Haga clic en ✓ para iniciar el proceso de conmutación por recuperación.


Siga [estas directrices](site-recovery-failback-azure-to-vmware.md) cuando realice una conmutación por recuperación.

##<a name="summary"></a>Resumen
Con Azure Site Recovery, puede crear un plan completo de recuperación ante desastres automatizado para su aplicación Dynamics AX. Puede iniciar la conmutación por error en cuestión de segundos desde cualquier lugar si se produce una interrupción y poner en funcionamiento la aplicación en unos minutos.

## <a name="next-steps"></a>Pasos siguientes
Consulte [qué cargas de trabajo puede proteger](site-recovery-workload.md) para más información sobre cómo proteger las cargas de trabajo empresariales con Azure Site Recovery.

