---
title: "Compatibilidad multiempresa para la replicación de VM de VMware en Azure (programa CSP) | Microsoft Docs"
description: "Describe cómo implementar Azure Site Recovery en un entorno multiinquilino para organizar la replicación, la conmutación por error y la recuperación de máquinas virtuales (VM) de VMware locales en Azure mediante el programa CSP con Azure Portal."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: manayar
ms.openlocfilehash: 273efe0bdef421d753ea51e01060d48351cbe6fc
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Compatibilidad con multiinquilino en Azure Site Recovery para la replicación de máquinas virtuales de VMware en Azure mediante CSP

Azure Site Recovery admite entornos multiinquilino en suscripciones de inquilino. También admite la arquitectura multiinquilino para suscripciones de inquilino que se crean y administran mediante el programa del Proveedor de soluciones en la nube (CSP) de Microsoft. En este artículo se detallan las instrucciones para implementar y administrar escenarios multiinquilino de VMware en Azure. Para más información sobre la creación y administración de suscripciones de los inquilinos, consulte [Administración multiinquilino con CSP](site-recovery-manage-multi-tenancy-with-csp.md).

Tenga en cuenta que esta guía se basa en buena medida en la documentación existente para replicar máquinas virtuales de VMware en Azure. Para más información, consulte [Replicación de máquinas virtuales VMware en Azure con Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Entornos multiinquilino
Existen tres modelos principales multiinquilino:

* **Proveedor de servicios de hospedaje compartidos (HSP)**: el asociado posee la infraestructura física y usa los recursos compartidos (vCenter, centros de datos, almacenamiento físico, etc.) para hospedar máquinas virtuales de varios inquilinos en la misma infraestructura. El asociado puede proporcionar administración de recuperación ante desastres como servicio administrado, o bien el inquilino puede ser propietario de la recuperación ante desastres como solución de autoservicio.

* **Proveedor de servicios de hospedaje dedicados**: el asociado posee la infraestructura física, pero usa recursos dedicados (varios vCenters, almacenes de datos físicos, etc.) para hospedar las máquinas virtuales de cada inquilino en infraestructuras diferentes. El asociado puede proporcionar administración de recuperación ante desastres como servicio administrado, o bien el inquilino puede poseerla como solución de autoservicio.

* **Proveedor de servicios administrados (MSP)**: aquí el cliente posee la infraestructura física que hospeda las máquinas virtuales y el asociado proporciona la habilitación y la administración de la recuperación ante desastres.

## <a name="shared-hosting-multi-tenant-guidance"></a>Guía para entornos multiinquilino: hospedaje compartido
Esta sección trata detalladamente el escenario de hospedaje compartido. Los otros dos escenarios son subconjuntos del escenario de hospedaje compartido y usan los mismos principios. Al final de la guía de hospedaje compartido se describen las diferencias.

El requisito básico en un escenario multiinquilino es aislar los diversos inquilinos. Un inquilino no podrá observar lo que tenga hospedado otro inquilino. En un entorno administrado por un asociado, este requisito no es tan importante como en el entorno de autoservicio, donde puede ser crítico. En esta guía se da por supuesto que se requiere el aislamiento de inquilinos.

La arquitectura se presenta en el diagrama siguiente:

![HSP compartido con un vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Escenario de hospedaje compartido con un vCenter**

Tal como se muestra en el diagrama anterior, cada cliente tiene un servidor de administración independiente. Esta configuración limita el acceso del inquilino a máquinas virtuales específicas de inquilino y permite el aislamiento de inquilinos. En el escenario de replicación de máquinas virtuales de VMware, se usa el servidor de configuración para administrar las cuentas con el fin de detectar máquinas virtuales e instalar agentes. Los mismos principios se siguen para entornos multiinquilino, junto con la restricción de la detección de máquinas virtuales mediante el control de acceso de vCenter.

El requisito de aislamiento de datos precisa que toda la información confidencial de infraestructura (por ejemplo, credenciales de acceso) permanezca sin revelar para los inquilinos. Por este motivo, se recomienda que todos los componentes del servidor de administración permanecen bajo control exclusivo del asociado. Los componentes de servidor de administración son:
* Servidor de configuración (CS)
* Servidor de proceso (PS)
* Servidor de destino maestro (MT)

Un PS de escalado horizontal también está bajo el control del asociado.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Cada CS del escenario multiinquilino emplea dos cuentas:

- **Cuenta de acceso de vCenter**: use esta cuenta para detectar máquinas virtuales del inquilino. Tiene asignados permisos de acceso a vCenter (tal y como se describe en la sección siguiente). Para ayudar a evitar pérdidas de acceso accidentales, se recomienda que los asociados escriban estas credenciales en la herramienta de configuración.

- **Cuenta de acceso a la máquina virtual**: esta cuenta se usa para instalar el agente de movilidad en las máquinas virtuales de inquilino mediante inserción automática. Suele ser una cuenta de dominio que un inquilino puede proporcionar a un asociado o una que el asociado pudiera administrar directamente. Si un inquilino no desea compartir los detalles con el asociado directamente, se le puede permitir escribir las credenciales mediante acceso limitado por tiempo al CS o bien, con la ayuda del asociado, instalar los agentes de movilidad manualmente.

### <a name="requirements-for-a-vcenter-access-account"></a>Requisitos para una cuenta de acceso a vCenter

Como se mencionó en la sección anterior, debe configurar el CS con una cuenta que tenga asignado un rol especial. La asignación de roles se debe aplicar a la cuenta de acceso a vCenter a cada objeto de vCenter y no se propaga a los objetos secundarios. Esta configuración garantiza el aislamiento de los inquilinos, puesto que la propagación de acceso puede dar lugar al acceso accidental a otros objetos.

![La opción de propagación a objetos secundarios](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

La alternativa consiste en asignar la cuenta y el rol de usuario en el objeto de centro de datos y propagarlos a los objetos secundarios. A continuación, conceda a la cuenta un rol *Sin acceso* para cada objeto (como las máquinas virtuales de otros inquilinos) que debe ser inaccesible para un inquilino en particular. Esta configuración es complicada y expone controles de acceso accidental, ya que cada nuevo objeto secundario también concede automáticamente el acceso que se hereda del objeto primario. Por lo tanto, se recomienda usar el primer enfoque.

El procedimiento de acceso a la cuenta de vCenter es el siguiente:

1. Cree un nuevo rol mediante la clonación del rol *Solo lectura* predefinido y proporciónele un nombre adecuado (como Azure_Site_Recovery, que se muestra en este ejemplo).

2. Asigne los siguientes permisos a este rol:

    * **Almacén de datos**: asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual

    * **Red**: asignación de red

    * **Recursos**: asignar máquina virtual al grupo de recursos, migrar máquina virtual apagada, migrar máquina virtual encendida

    * **Tareas**: crear tarea, actualizar tarea

    * **Máquina virtual**:
        * Configuración > Todos
        * Interacción > Responder a pregunta, Conexión de dispositivos, Configurar soporte de CD, Configurar soporte de disquete, Apagar, Encender, Instalación de herramientas de VMware
        * Inventario > Crear a partir de existente, Crear nuevo, Registrar, Anular registro
        * Máquina virtual > Permitir descarga de máquina virtual, Permitir carga de archivos de máquina virtual
        * Administración de instantáneas > Eliminar instantáneas

    ![El cuadro de diálogo Editar rol](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Asigne niveles de acceso a la cuenta de vCenter (usada en el CS del inquilino) para distintos objetos, de la manera siguiente:

>| Objeto | Rol | Comentarios |
>| --- | --- | --- |
>| vCenter | Solo lectura | Solo es necesario para permitir el acceso a vCenter y administrar diferentes objetos. Este permiso se puede quitar si la cuenta nunca se va a proporcionar a un inquilino ni se va a usar para las operaciones de administración en vCenter. |
>| Centro de datos | Azure_Site_Recovery |  |
>| Host y clúster de hosts | Azure_Site_Recovery | Garantiza de nuevo que el acceso es en el nivel de objeto, así que solo los hosts accesibles tienen máquinas virtuales de inquilino antes de la conmutación por error y después de la conmutación por recuperación. |
>| Almacén de datos y clúster de almacenes de datos | Azure_Site_Recovery | Igual que el anterior. |
>| Red | Azure_Site_Recovery |  |
>| Servidor de administración | Azure_Site_Recovery | Incluye el acceso a todos los componentes (CS, PS y MT) fuera de la máquina de CS. |
>| Máquinas virtuales de inquilinos | Azure_Site_Recovery | Asegúrese de que las nuevas máquinas virtuales de inquilino de un inquilino determinado obtengan también este acceso o no se podrán detectar a través de Azure Portal. |

El acceso a la cuenta de vCenter ha finalizado ahora y se cumplen los requisitos mínimos de permisos para realizar las operaciones de conmutación por recuperación. Estos permisos de acceso también pueden usarse con las directivas existentes. Solo tiene que modificar el conjunto de permisos existente para incluir los permisos de rol del paso 2 detallado anteriormente.

Para restringir las operaciones de recuperación ante desastres hasta el estado de conmutación por error (es decir, sin funcionalidades de conmutación por recuperación), siga el procedimiento anterior, pero en lugar de asignar el rol *Azure_Site_Recovery* a la cuenta de acceso de vCenter, asigne solo un rol de *Solo lectura* a esa cuenta. Este conjunto de permisos permite la replicación de máquinas virtuales y la conmutación por error, y no permite la conmutación por recuperación. Todo lo demás del proceso anterior permanece igual. Cada permiso se sigue asignando solamente en el nivel de objeto y no se propaga a los objetos secundarios para garantizar el aislamiento de inquilinos y restringir la detección de máquinas virtuales.

## <a name="other-multi-tenant-environments"></a>Otros entornos multiinquilino

En la sección anterior se ha descrito cómo configurar un entorno multiinquilino para una solución de hospedaje compartido. Las otras dos soluciones principales son hospedaje dedicado y servicio administrado. La arquitectura de estas soluciones se describe en las secciones siguientes.

### <a name="dedicated-hosting-solution"></a>Solución de hospedaje dedicado

Tal como se muestra en el diagrama siguiente, la diferencia de arquitectura en una solución de hospedaje dedicado es que la infraestructura de cada inquilino está configurada para ese inquilino únicamente. Como los inquilinos están aislados en vCenters independientes, el proveedor de hospedaje puede seguir los pasos del CSP proporcionados para el hospedaje compartido, pero no tiene que preocuparse por el aislamiento de los inquilinos. La configuración del CSP permanece sin cambios.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Escenario de hospedaje dedicado con varios vCenters**

### <a name="managed-service-solution"></a>Solución de servicios administrados

Tal como se muestra en el diagrama siguiente, la diferencia de arquitectura en una solución de servicio administrado es que la infraestructura de cada inquilino también está físicamente separada de la infraestructura de los otros inquilinos. Este escenario existe normalmente cuando el inquilino es propietario de la infraestructura y desea un proveedor de soluciones para administrar la recuperación ante desastres. De nuevo, como los inquilinos están aislados físicamente en diferentes infraestructuras, el asociado debe seguir los pasos del CSP proporcionados para el hospedaje compartido, pero no necesita preocuparse por el aislamiento de los inquilinos. El aprovisionamiento del CSP permanece sin cambios.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Escenario de servicios administrados con varios vCenters**

## <a name="next-steps"></a>pasos siguientes
[Más información](site-recovery-role-based-linked-access-control.md) sobre el control de acceso basado en roles para administrar implementaciones de Azure Site Recovery.

[Administración de servicios multiinquilino con CSP](site-recovery-manage-multi-tenancy-with-csp.md)
