---
title: Introducción a la compatibilidad multiinquilino para la replicación de máquinas virtuales de VMware en Azure (CSP) con Azure Site Recovery | Microsoft Docs
description: Introducción al soporte técnico de Azure Site Recovery para las suscripciones de inquilino en un entorno de varios inquilinos, mediante el programa CSP.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 0168849c01add3f714b139c7984e3def74f40a5b
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071770"
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>Introducción a la compatibilidad multiinquilino para la replicación de VMware en Azure con CSP

[Azure Site Recovery](site-recovery-overview.md) admite entornos multiinquilino para las suscripciones de inquilino. También admite la arquitectura multiinquilino para suscripciones de inquilino que se crean y administran mediante el programa del Proveedor de soluciones en la nube (CSP) de Microsoft.

Este artículo es una introducción a la implementación y la administración de la replicación de VMware multiinquilino en Azure.

## <a name="multi-tenant-environments"></a>Entornos multiinquilino

Existen tres modelos principales multiinquilino:

* **Proveedor de servicios de hospedaje compartido (HSP)**: el asociado posee la infraestructura física y usa los recursos compartidos (vCenter, centros de datos, almacenamiento físico, etc.) para hospedar máquinas virtuales multiinquilino en la misma infraestructura. El asociado puede proporcionar administración de recuperación ante desastres como servicio administrado, o bien el inquilino puede ser propietario de la recuperación ante desastres como solución de autoservicio.

* **Proveedor de servicios de hospedaje dedicados**: el asociado posee la infraestructura física, pero usa recursos dedicados (varios vCenters, almacenes de datos físicos, etc.) para hospedar las máquinas virtuales de cada inquilino en infraestructuras diferentes. El asociado puede proporcionar administración de recuperación ante desastres como servicio administrado, o bien el inquilino puede poseerla como solución de autoservicio.

* **Proveedor de servicios administrados (MSP)**: aquí el cliente posee la infraestructura física que hospeda las máquinas virtuales y el asociado proporciona la habilitación y la administración de la recuperación ante desastres.

## <a name="shared-hosting-services-provider-hsp"></a>Proveedor de servicios de hospedaje compartido (HSP)

Los otros dos escenarios son subconjuntos del escenario de hospedaje compartido y usan los mismos principios. Al final de la guía de hospedaje compartido se describen las diferencias.

El requisito básico en un escenario multiinquilino es que los inquilinos estén aislados. Un inquilino no podrá observar lo que tenga hospedado otro inquilino. En un entorno administrado por un asociado, este requisito no es tan importante como en el entorno de autoservicio, donde puede ser crítico. En este artículo se da por supuesto que se requiere el aislamiento de inquilinos.

La arquitectura se muestra en el diagrama siguiente.

![HSP compartido con un vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hospedaje compartido con un servidor vCenter**

En el diagrama, cada cliente tiene un servidor de administración independiente. Esta configuración limita el acceso del inquilino a máquinas virtuales específicas de inquilino y permite el aislamiento de inquilinos. La replicación de máquinas virtuales de VMware usa el servidor de configuración para detectar las máquinas virtuales e instalar agentes. Los mismos principios se aplican a los entornos multiinquilino, además de la restricción de la detección de máquinas virtuales mediante el control de acceso de vCenter.

El requisito de aislamiento de datos significa que toda la información confidencial de infraestructura (por ejemplo, las credenciales de acceso) permanezca oculta para los inquilinos. Por este motivo, se recomienda que todos los componentes del servidor de administración permanecen bajo control exclusivo del asociado. Los componentes de servidor de administración son:

* Servidor de configuración
* Servidor de proceso
* Servidor de destino principal

También hay un servidor de proceso con escalado horizontal independiente bajo el control del asociado.

## <a name="configuration-server-accounts"></a>Cuentas de los servidores de configuración

Cada servidor de configuración del escenario multiinquilino emplea dos cuentas:

- **Cuenta de acceso de vCenter**: sirve para detectar las máquinas virtuales del inquilino. Tiene permisos de acceso de vCenter asignados. Para ayudar a evitar pérdidas de acceso, se recomienda que los asociados escriban estas credenciales en la herramienta de configuración.

- **Cuenta de acceso de máquina virtual**: sirve para instalar el agente de Mobility Service en las máquinas virtuales de inquilino con inserción automática. Suele ser una cuenta de dominio que un inquilino proporciona a un asociado o una que el asociado pueda administrar directamente. Si un inquilino no desea compartir los detalles con el asociado directamente, puede escribir las credenciales mediante acceso limitado por tiempo al servidor de configuración. O bien, con la ayuda del asociado, instalar al agente de Mobility Service manualmente.

## <a name="vcenter-account-requirements"></a>Requisitos de la cuenta de vCenter

Configure el servidor de configuración con una cuenta que tenga asignado un rol especial.

- La asignación de roles se debe aplicar a la cuenta de acceso a vCenter a cada objeto de vCenter y no se propaga a los objetos secundarios. Esta configuración garantiza el aislamiento de los inquilinos, puesto que la propagación de acceso puede dar lugar al acceso accidental a otros objetos.

    ![La opción de propagación a objetos secundarios](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- La alternativa consiste en asignar la cuenta y el rol de usuario al objeto de centro de datos y propagarlos a los objetos secundarios. A continuación, conceda a la cuenta un rol **Sin acceso** para cada objeto (como las máquinas virtuales de otros inquilinos) que debe ser inaccesible para un inquilino en particular. Esta configuración es complicada. Expone controles de acceso accidental, ya que cada nuevo objeto secundario también concede automáticamente el acceso que se hereda del objeto primario. Por lo tanto, se recomienda usar el primer enfoque.

### <a name="create-a-vcenter-account"></a>Creación de una cuenta de vCenter

1. Cree un nuevo rol mediante la clonación del rol *Solo lectura* predefinido y proporciónele un nombre adecuado (como Azure_Site_Recovery, que se muestra en este ejemplo).
2. Asigne los siguientes permisos a este rol:

    * **Almacén de datos**: asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual
    * **Red**: asignación de red
    * **Recursos**: asignar máquina virtual al grupo de recursos, migrar máquina virtual apagada, migrar máquina virtual encendida
    * **Tareas**: crear tarea, actualizar tarea
    * **Máquina virtual: configuración**: todos
    - **Máquina virtual: interacción**: responder a pregunta, conexión de dispositivos, configurar soporte de CD, configurar soporte de disquete, apagar, encender, instalar herramientas de VMware
    - **Máquina virtual: inventario**: crear a partir de existente, crear nuevo, registrar, anular registro
    - **Máquina virtual: aprovisionamiento**: permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual
    - **Máquina virtual: administración de instantáneas**: eliminar instantáneas

        ![El cuadro de diálogo Editar rol](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Asigne niveles de acceso a la cuenta de vCenter (usada en el servidor de configuración del inquilino) para distintos objetos, de la manera siguiente:

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

### <a name="failover-only"></a>Solo el modo de conmutación por error
Para restringir las operaciones de recuperación ante desastres hasta solo conmutación por error (es decir, sin las funcionalidades de conmutación por recuperación), utilice el procedimiento anterior, con las siguientes excepciones:

- En lugar de asignar el rol *Azure_Site_Recovery* a la cuenta de acceso a vCenter, asígnele solo uno de*solo lectura*. Este conjunto de permisos permite la replicación de máquinas virtuales y la conmutación por error, y no permite la conmutación por recuperación.
- Todo lo demás del proceso anterior permanece igual. Los permisos se asignan solamente en el nivel de objeto y no se propagan a los objetos secundarios para garantizar el aislamiento de inquilinos y restringir la detección de máquinas virtuales.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Implementación de recursos en la suscripción de inquilino

1. En Azure Portal, cree un grupo de recursos e implemente un almacén de Recovery Services por el proceso habitual.
2. Descargue la clave de registro del almacén.
3. Registre el CS en el inquilino con la clave de registro del almacén.
4. Escriba las credenciales de las dos cuentas de acceso, la de acceso al servidor vCenter y la de acceso a la máquina virtual.

    ![Cuentas de servidor de Configuration Manager](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registro de servidores en el almacén

1. En Azure Portal, en el almacén que creó anteriormente, registre el servidor vCenter como servidor de configuración, con la cuenta de vCenter que creó.
2. Finalice el proceso de preparación de la infraestructura de Site Recovery del modo habitual.
3. Las máquinas virtuales ahora están listas para replicarse. Verifique que solo se muestran las máquinas virtuales del inquilino en **Replicar** > **Seleccionar máquinas virtuales**.

## <a name="dedicated-hosting-solution"></a>Solución de hospedaje dedicado

Tal como se muestra en el diagrama siguiente, la diferencia de arquitectura en una solución de hospedaje dedicado es que la infraestructura de cada inquilino está configurada para ese inquilino únicamente.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Escenario de hospedaje dedicado con varios vCenters**

## <a name="managed-service-solution"></a>Solución de servicios administrados

Tal como se muestra en el diagrama siguiente, la diferencia de arquitectura en una solución de servicio administrado es que la infraestructura de cada inquilino también está físicamente separada de la infraestructura de los otros inquilinos. Este escenario existe normalmente cuando el inquilino es propietario de la infraestructura y desea un proveedor de soluciones para administrar la recuperación ante desastres.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Escenario de servicios administrados con varios vCenters**

## <a name="next-steps"></a>Pasos siguientes
- [Más información](site-recovery-role-based-linked-access-control.md) sobre control de acceso basado en rol en Site Recovery.
- Aprenda a [configurar la recuperación ante desastres de máquinas virtuales de VMware en Azure](vmware-azure-tutorial.md).
- Más información acerca de los [servicios multiinquilino con CSP para máquinas virtuales de VMWare](vmware-azure-multi-tenant-csp-disaster-recovery.md).
