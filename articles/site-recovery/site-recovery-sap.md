---
title: "Protección de la implementación de una aplicación SAP NetWeaver de niveles múltiples mediante Azure Site Recovery | Microsoft Docs"
description: "En este artículo se explica cómo proteger las implementaciones de aplicaciones SAP NetWeaver mediante Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 5a47acab598e113ef7ed968dd3a6429ac3bc1ec3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>Protección de la implementación de una aplicación SAP NetWeaver de niveles múltiples mediante Azure Site Recovery

La mayor parte de las implementaciones SAP grandes y medianas incluyen algún tipo de solución de recuperación ante desastres.  La importancia de las soluciones de recuperación ante desastres robustas y verificables ha aumentado a medida que cada vez más procesos empresariales básicos se trasladan a aplicaciones como SAP.  Azure Site Recovery se ha probado e integrado con aplicaciones SAP y supera las funcionalidades de la mayoría de soluciones de recuperación ante desastres locales, con un menor costo total de propiedad (TCO) que las soluciones de la competencia.
Con Azure Site Recovery, puede:
* Habilitar la protección de aplicaciones de producción de SAP NetWeaver y no NetWeaver que se ejecutan de forma local, mediante la replicación de sus componentes en Azure.
* Habilitar la protección de aplicaciones de producción de SAP NetWeaver y no NetWeaver que se ejecutan en Azure, mediante la replicación de componentes en otro centro de datos de Azure.
* Simplifique la migración a la nube con Site Recovery para migrar la implementación de SAP en Azure.
* Simplificar las actualizaciones de los proyectos SAP, las pruebas y la creación de prototipos, mediante la creación de una clon en producción a petición para probar las aplicaciones SAP.

En este artículo se explica cómo proteger las implementaciones de aplicaciones SAP NetWeaver mediante [Azure Site Recovery](site-recovery-overview.md). Este artículo aborda los procedimientos recomendados para proteger una implementación de SAP NetWeaver de tres niveles en Azure mediante la replicación en otro centro de datos Azure con Azure Site Recovery, las configuraciones y los escenarios admitidos, y cómo realizar conmutaciones por error, tanto de prueba (simulacros) como reales.


## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, no olvide informarse sobre las cuestione siguientes:

1. [Replicación de una máquina virtual en Azure](azure-to-azure-walkthrough-enable-replication.md)
2. [Diseño de una red de recuperación](site-recovery-azure-to-azure-networking-guidance.md)
3. [Realización de una conmutación por error de prueba en Azure](azure-to-azure-walkthrough-test-failover.md)
4. [Ejecución de una conmutación por error en Azure](site-recovery-failover.md)
5. [Replicación de un controlador de dominio](site-recovery-active-directory.md)
6. [Replicación de SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Escenarios admitidos
Con Azure Site Recovery se puede implementar una solución de recuperación ante desastres para los escenarios siguientes:
* Sistemas SAP que se ejecutan en un centro de datos Azure que se replican en otro centro de datos Azure (recuperación ante desastres de Azure a Azure), tal y como se define [aquí](https://aka.ms/asr-a2a-architecture).
* Sistemas SAP que se ejecutan en servidores locales VMWare (o físicos) que se replican en un sitio de recuperación ante desastres de un centro de datos Azure (de VMware a Azure), lo que requiere algunos componentes adicionales, tal y como se define [aquí](https://aka.ms/asr-v2a-architecture).
* Sistemas SAP que se ejecutan en servidores locales Hyper-V y se replican en un sitio de recuperación ante desastres de un centro de datos Azure (de Hyper-V a Azure), lo que requiere algunos componentes adicionales, tal y como se define [aquí](https://aka.ms/asr-h2a-architecture).

En este documento se usa el primer caso, la recuperación ante desastres de Azure a Azure, para demostrar las funcionalidades de la recuperación ante desastres de SAP de Azure Site Recovery. La replicación de Azure Site Recovery es independiente de la aplicación, es de esperar que el proceso descrito sirva también para otros escenarios.

### <a name="required-foundation-services"></a>Servicios básicos requeridos
En este escenario de documentación, se parte de la situación en que todos los servicios básicos siguientes están implementados:
* Red de privada virtual (VPN) ExpressRoute o de sitio a sitio
* Al menos un servidor DNS y un controlador de dominio de Active Directory se ejecutan en Azure

Se recomienda que la infraestructura anterior se establezca antes de implementar Azure Site Recovery.


## <a name="typical-sap-application-deployment"></a>Implementación típica de la aplicación SAP
Los clientes de SAP grandes normalmente implementan entre 6 y 20 aplicaciones SAP individuales.  La mayoría de estas aplicaciones se basa en los motores Java o ABAP de SAP NetWeaver.  Sirviendo de soporte para estas aplicaciones NetWeaver básicas se encuentran muchos motores menores e independientes SAP específicos que no son NetWeaver y, por lo general, algunas aplicaciones que no son SAP.  

Es fundamental inventariar todas las aplicaciones SAP que se ejecutan en un entorno y determinar el modo de implementación (ya sea de dos o de tres niveles), los requisitos de persistencia en disco, versiones, revisiones, tamaños y tasas de renovación.

![Modelo de implementación](./media/site-recovery-sap/sap-typical-deployment.png)

El nivel de persistencia de SAP Database debe protegerse mediante las herramientas nativas de DBMS como SQL Server AlwaysOn, Oracle DataGuard o el sistema de replicación de archivos de HANA. El nivel del cliente no está protegido también por Azure Site Recovery, pero es importante tener en cuenta cuestiones que lo afectan como el retraso de propagación de DNS, la seguridad y el acceso remoto al centro de datos de recuperación ante desastres.

Azure Site Recovery es la solución recomendada para el nivel de aplicación, incluido (A)SCS. Otras aplicaciones, como las aplicaciones SAP NetWeaver y las que no son SAP, forman parte del entorno de implementación general de SAP y también deberían protegerse con Azure Site Recovery.

## <a name="replicate-virtual-machines"></a>Replicación de máquinas virtuales
Siga [estas directrices](azure-to-azure-walkthrough-enable-replication.md) para comenzar a replicar todas las máquinas virtuales de aplicaciones SAP en el centro de datos de recuperación ante desastres de Azure.

Si utiliza una dirección IP estática, puede especificar la dirección IP que desea utilizar con la máquina virtual en la sección de tarjetas de interfaz de red de la configuración de Proceso y red.

![IP de destino](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Creación de un plan de recuperación
Los planes de recuperación permiten secuenciar la conmutación por error de los diferentes niveles de una aplicación de niveles múltiples y, por tanto, mantener la coherencia de la aplicación. Siga los pasos que se describen [aquí](site-recovery-create-recovery-plans.md) al crear un plan de recuperación para una aplicación web de múltiples niveles.

### <a name="adding-scripts-to-the-recovery-plan"></a>Incorporación de scripts al plan de recuperación
Para que las aplicaciones funcionen correctamente, es posible que tenga que realizar algunas operaciones en las máquinas virtuales de Azure tras la conmutación por error (real o de prueba). Puede automatizar las operaciones posteriores a la conmutación por error, como la actualización de entradas DNS, la modificación de los enlaces o de las conexiones, incorporando los scripts correspondientes en el plan de recuperación, tal y como se detalla en [este artículo](site-recovery-create-recovery-plans.md#add-scripts).

### <a name="dns-update"></a>Actualización de DNS
Si el DNS está configurado para que se actualice de forma dinámica, por lo general, las máquinas virtuales lo actualizarán con la nueva dirección IP al iniciarse. Si desea incorporar un paso explícito para actualizar DNS con las nuevas direcciones IP de las máquinas virtuales, agregue este [script para actualizar direcciones IP en DNS](https://aka.ms/asr-dns-update) como una acción posterior a los grupos del plan de recuperación.  

## <a name="example-azure-to-azure-deployment"></a>Ejemplo de implementación de Azure a Azure
En el diagrama siguiente se representa el escenario de recuperación ante desastres de Azure a Azure con Azure Site Recovery:
* El centro de datos principal se encuentra en Singapur (Asia occidental de Azure) y el centro de datos de recuperación ante desastres está en Hong Kong (Asia Pacífico de Azure).  En este escenario, se proporciona una alta disponibilidad local al tener en Singapur dos máquinas virtuales que ejecutan SQL Server AlwaysOn en modo sincrónico.
* Para proporcionar una alta disponibilidad para los puntos únicos de error de SAP, se puede usar ASCS de recurso compartido de archivos. ASCS de recurso compartido de archivos no requiere un disco compartido de clúster y no son necesarias las aplicaciones como SIOS.
* La protección de recuperación ante desastres para el nivel DBMS se logra mediante la replicación asincrónica.
* Este escenario muestra la "recuperación ante desastres simétrica", un término que se usa para describir una solución de recuperación ante desastres que es una réplica exacta del entorno de producción. Por lo tanto, esta solución de SQL Server tiene una alta disponibilidad local. El uso de la recuperación ante desastres simétrica no es obligatorio para el nivel de base de datos y muchos clientes aprovechan la flexibilidad de las implementaciones en la nube para crear rápidamente un nodo de alta disponibilidad local después de un evento de recuperación ante desastres.
* El diagrama muestra el nivel de servidor de aplicación y ASCS de SAP NetWeaver replicado por Azure Site Recovery.

![Escenario de replicación](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>Realización de una conmutación por error de prueba
Siga [estas directrices](azure-to-azure-walkthrough-test-failover.md) para llevar a cabo una conmutación por error de prueba.

1.  Vaya a Azure Portal y seleccione el almacén de Recovery Services.
2.  Haga clic en el plan de recuperación creado para las aplicaciones SAP.
3.  Haga clic en 'Probar conmutación por error'.
4.  Seleccione un punto de recuperación y una red virtual de Azure para iniciar el proceso de conmutación por error de prueba.
5.  Cuando el entorno secundario esté activo, podrá realizar las validaciones.
6.  Después de completar las validaciones, haga clic en 'Limpiar conmutación por error de prueba' y limpie el entorno de conmutación por error.

## <a name="doing-a-failover"></a>Realización de una conmutación por error
Siga [estas directrices](site-recovery-failover.md) cuando realice una conmutación por error.

1.  Vaya a Azure Portal y seleccione el almacén de Recovery Services.
2.  Haga clic en el plan de recuperación creado para las aplicaciones SAP.
3.  Haga clic en 'Conmutación por error'.
4.  Seleccione el punto de recuperación en el que se va a iniciar el proceso de conmutación por error.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo crear una solución de recuperación ante desastres para las implementaciones SAP NetWeaver con Azure Site Recovery en [estas notas del producto](http://aka.ms/asr-sap). Las notas del producto también incluyen recomendaciones para distintas arquitecturas SAP, listas de aplicaciones admitidas y los tipos de máquina virtual para SAP en Azure, y describen los posibles planes de pruebas para la solución de recuperación ante desastres.

Obtenga más información sobre la [replicación de otras cargas de trabajo](site-recovery-workload.md) con Site Recovery.
