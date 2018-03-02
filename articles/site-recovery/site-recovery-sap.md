---
title: "Protección de la implementación de una aplicación SAP NetWeaver de niveles múltiples mediante Azure Site Recovery | Microsoft Docs"
description: "En este artículo se explica cómo proteger las implementaciones de aplicaciones SAP NetWeaver mediante Azure Site Recovery."
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
ms.date: 02/27/2018
ms.author: manayar
<<<<<<< HEAD
ms.openlocfilehash: b6ab734186f23d51d60e51bd0946329d5209097b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
=======
ms.openlocfilehash: 1ee472498bdefc4eeb9863670e5480326b5ba6d8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
>>>>>>> 2b7eaf6c4cfe5413d9b581a669c96a2527a1b2ba
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Protección de la implementación de una aplicación SAP NetWeaver de niveles múltiples mediante Site Recovery

La mayor parte de las implementaciones SAP grandes y medianas usan algún tipo de solución de recuperación ante desastres. La importancia de las soluciones de recuperación ante desastres sólidas y verificables ha aumentado a medida que cada vez más procesos empresariales básicos se trasladan a aplicaciones como SAP. Azure Site Recovery se probó e integró con aplicaciones SAP. Site Recovery supera las funcionalidades de la mayoría de las soluciones de recuperación ante desastres locales, con un costo total de propiedad (TCO) menor que las soluciones de la competencia.

Con Site Recovery, puede:
* **Habilitar la protección de aplicaciones de protección de SAP NetWeaver y no NetWeaver que se ejecutan de manera local**, mediante la replicación de componentes en Azure.
* **Habilitar la protección de aplicaciones de producción de SAP NetWeaver y no NetWeaver que se ejecutan en Azure**, mediante la replicación de componentes en otro centro de datos de Azure.
* **Simplificar la migración a la nube** mediante Site Recovery para migrar la implementación de SAP a Azure.
* **Simplificar las actualizaciones, las pruebas y la creación de prototipos de los proyectos de SAP**, mediante la creación de una clon en producción a petición para probar las aplicaciones SAP.

En este artículo se explica cómo proteger las implementaciones de aplicaciones SAP NetWeaver mediante [Azure Site Recovery](site-recovery-overview.md). En este artículo se describen los procedimientos recomendados para proteger una implementación de SAP NetWeaver de tres niveles en Azure mediante la replicación en otro centro de datos de Azure a través de Site Recovery. Describe los escenarios y las configuraciones compatibles y cómo realizar conmutaciones por error de prueba (maniobras de recuperación ante desastres) y conmutaciones ante desastres reales.

## <a name="prerequisites"></a>requisitos previos
Antes de comenzar, asegúrese de que sabe cómo realizar las tareas siguientes:

* [Replicar una máquina virtual en Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Diseñar una red de recuperación](site-recovery-azure-to-azure-networking-guidance.md)
* [Realizar una conmutación por error de prueba en Azure](azure-to-azure-walkthrough-test-failover.md)
* [Realizar una conmutación por error en Azure](site-recovery-failover.md)
* [Replicar un controlador de dominio](site-recovery-active-directory.md)
* [Replicación de SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Escenarios admitidos
Puede usar Site Recovery para implementar una solución de recuperación ante desastres en los escenarios siguientes:
* Sistemas SAP que se ejecutan en un centro de datos de Azure que se replican en otro centro de datos de Azure (recuperación ante desastres de Azure en Azure). Para más información, consulte [Arquitectura de replicación de Azure en Azure](https://aka.ms/asr-a2a-architecture).
* Sistemas SAP que se ejecutan en servidores VMware (o físicos) locales que se replican en un sitio de recuperación ante desastres de un centro de datos de Azure (recuperación ante desastres de VMware en Azure). Se requieren componentes adicionales para este escenario. Para más información, consulte el artículo sobre la [arquitectura de replicación de VMware en Azure](https://aka.ms/asr-v2a-architecture).
* Sistemas SAP que se ejecutan en una instancia local de Hyper-V que se replican en un sitio de recuperación ante desastres de un centro de datos de Azure (recuperación ante desastres de Hyper-V en Azure). Se requieren componentes adicionales para este escenario. Para más información, consulte [Arquitectura de replicación de Hyper-V en Azure](https://aka.ms/asr-h2a-architecture).

En este artículo se usa un escenario de recuperación ante desastres de Azure en Azure para demostrar las funcionalidades de recuperación ante desastres de SAP de Site Recovery. Debido a que la replicación de Site Recovery no es específica para la aplicación, el proceso descrito también se puede aplicar a otros escenarios.

### <a name="required-foundation-services"></a>Servicios básicos requeridos
En el escenario que se analiza en este artículo, se implementan los servicios básicos siguientes:
* Azure ExpressRoute o Azure VPN Gateway
* Al menos un servidor DNS y un controlador de dominio de Active Directory que se ejecutan en Azure

Se recomienda establecer esta infraestructura antes de implementar Site Recovery.

## <a name="typical-sap-application-deployment"></a>Implementación típica de la aplicación SAP
Los clientes de SAP grandes habitualmente implementan entre 6 y 20 aplicaciones SAP individuales. La mayoría de estas aplicaciones se basa en los motores Java o ABAP de SAP NetWeaver. Muchos motores menores e independientes SAP específicos que no son NetWeaver y, por lo general, algunas aplicaciones que no son SAP, admiten estas aplicaciones principales de NetWeaver.  

Es fundamental hacer un inventario de todas las aplicaciones SAP que se ejecutan en el entorno. Luego, determine el modo de implementación (ya sea de dos o de tres niveles), los requisitos de persistencia en disco, las versiones, las revisiones, los tamaños y las tasas de renovación.

![Diagrama de un modelo de implementación de SAP típico](./media/site-recovery-sap/sap-typical-deployment.png)

Proteja el nivel de persistencia de la base de datos de SAP mediante el uso de las herramientas nativas de DBMS, como SQL Server AlwaysOn, Oracle Data Guard o la replicación del sistema de SAP HANA. Al igual que lo que ocurre con el nivel de la base de datos de SAP, Site Recovery no protege el nivel de cliente. Resulta importante considerar los factores que afectan este nivel. Los factores incluyen el retraso de propagación de DNS, la seguridad y el acceso remoto al centro de datos de recuperación ante desastres.

Site Recovery es la solución recomendada para el nivel de aplicación, incluido SCS y ASCS de SAP. Otras aplicaciones, como las aplicaciones SAP NetWeaver y las que no son NetWeaver, forman parte del entorno de implementación general de SAP. Debe protegerlas con Site Recovery.

## <a name="replicate-virtual-machines"></a>Replicación de máquinas virtuales
Para comenzar a replicar todas las máquinas virtuales de la aplicación SAP en el centro de datos de recuperación ante desastres de Azure, siga las instrucciones que aparecen en el artículo sobre la [replicación de una máquina virtual en Azure](azure-to-azure-walkthrough-enable-replication.md).

Si usa una dirección IP estática, puede especificar la dirección IP que desea que use la máquina virtual. Para establecer la dirección IP, vaya a **Configuración de proceso y red** > **Adaptador de red**.

![Captura de pantalla que muestra cómo establecer una dirección IP privada en el panel del adaptador de red de Site Recovery](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación
Un plan de recuperación admite la secuenciación de distintas capas en una aplicación de varios niveles durante una conmutación por error. La secuenciación ayuda a mantener la coherencia de la aplicación. Cuando cree un plan de recuperación para una aplicación web de varios niveles, complete los pasos descritos en [Creación de un plan de recuperación mediante Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-scripts-to-the-recovery-plan"></a>Incorporación de scripts al plan de recuperación
Para que las aplicaciones funciones correctamente, es posible que tenga que realizar algunas operaciones en las máquinas virtuales de Azure después de la conmutación por error o durante una conmutación por error de prueba. Algunas de las operaciones posteriores a la conmutación por error se pueden automatizar. Por ejemplo, puede actualizar la entrada DNS y modificar los enlaces y las conexiones mediante la incorporación de los scripts correspondientes en el plan de recuperación.

### <a name="dns-update"></a>Actualización de DNS
Si el DNS está configurado para que se actualice de forma dinámica, por lo general, las máquinas virtuales lo actualizarán con la nueva dirección IP al iniciarse. Si quiere incorporar un paso explícito para actualizar DNS con las nuevas direcciones IP de las máquinas virtuales, agregue un [script para actualizar la dirección IP en DNS](https://aka.ms/asr-dns-update) como una acción posterior a la conmutación por error en los grupos de planes de recuperación.  

## <a name="example-azure-to-azure-deployment"></a>Ejemplo de implementación de Azure a Azure
En el diagrama siguiente se muestra el escenario de recuperación ante desastres de Azure en Azure de Site Recovery:

![Diagrama de un escenario de replicación de Azure en Azure](./media/site-recovery-sap/sap-replication-scenario.png)

* El centro de datos principal se encuentra en Singapur (Asia Suroriental de Azure). El centro de datos de recuperación ante desastres, en Hong-Kong (Asia Pacífico de Azure). En este escenario, se proporciona alta disponibilidad local mediante dos máquinas virtuales que ejecutan SQL Server AlwaysOn en modo sincrónico en Singapur.
* El recurso compartido de archivos ASCS de SAP proporciona alta disponibilidad para los únicos puntos de error de SAP. El recurso compartido de archivos ASCS no requiere un disco compartido de clúster. No se requieren aplicaciones como SIOS.
* La protección de la recuperación ante desastres para el nivel DBMS se logra mediante la replicación asincrónica.
* Este escenario muestra la "recuperación ante desastres simétrica". Este término describe una solución de recuperación ante desastres que es una réplica de producción exacta. La solución SQL Server de recuperación ante desastres tiene alta disponibilidad local. La recuperación ante desastres simétrica es obligatoria para el nivel de la base de datos. Muchos clientes aprovechan la flexibilidad de las implementaciones de nube para compilar rápidamente un nodo de alta disponibilidad local después de un evento de recuperación ante desastres.
* El diagrama muestra el nivel de servidor de aplicación y ASCS de SAP NetWeaver que Site Recovery replica.

## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

1.  En Azure Portal, seleccione el almacén de Recovery Services.
2.  Seleccione el plan de recuperación que creó para las aplicaciones SAP.
3.  Seleccione **Conmutación por error de prueba**.
4.  Para iniciar el proceso de conmutación por error de prueba, seleccione el punto de recuperación y la red virtual de Azure.
5.  Cuando el entorno secundario esté activo, realice las validaciones.
6.  Cuando se completen las validaciones, seleccione **Limpiar conmutación por error de prueba** para limpiar el entorno de conmutación por error.

Para más información, consulte [Conmutación por error de prueba a Azure en Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Ejecución de la conmutación por error

1.  En Azure Portal, seleccione el almacén de Recovery Services.
2.  Seleccione el plan de recuperación que creó para las aplicaciones SAP.
3.  Seleccione **Failover** (Conmutación por error).
4.  Para iniciar el proceso de conmutación por error, seleccione el punto de recuperación.

Para más información, consulte [Conmutación por error en Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>pasos siguientes
* Para más información sobre cómo compilar una solución de recuperación ante desastres para las implementaciones de SAP NetWeaver mediante Site Recovery, consulte las notas del producto [SAP NetWeaver: Building a Disaster Recovery Solution with Azure Site Recovery](http://aka.ms/asr-sap) (SAP NetWeaver: compilación de una solución de recuperación ante desastres con Azure Site Recovery) que se pueden descargar. En las notas del producto se analizan las recomendaciones para distintas arquitecturas SAP, se muestran las aplicaciones y los tipos de máquinas virtuales compatibles con SAP en Azure y se describen las opciones del plan de pruebas para la solución de recuperación ante desastres.
* Obtenga más información sobre cómo [replicar otras cargas de trabajo](site-recovery-workload.md) con Site Recovery.
