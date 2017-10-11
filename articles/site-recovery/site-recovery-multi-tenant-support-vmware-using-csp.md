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
ms.date: 06/23/2017
ms.author: manayar
ms.openlocfilehash: 97edbe67c25036dc1156f0f0ca5431a617d7a004
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Compatibilidad con multiinquilino en Azure Site Recovery para la replicación de máquinas virtuales de VMware en Azure mediante CSP

Azure Site Recovery admite entornos multiinquilino en suscripciones de inquilino. También admite la arquitectura multiinquilino para suscripciones de inquilino que se crean y administran mediante el programa del Proveedor de soluciones en la nube (CSP) de Microsoft. En este artículo se detallan las instrucciones para implementar y administrar escenarios multiinquilino de VMware en Azure. También se describe la creación y la administración de suscripciones de inquilino mediante CSP.

Tenga en cuenta que esta guía se basa en buena medida en la documentación existente para replicar máquinas virtuales de VMware en Azure. Para más información, consulte [Replicación de máquinas virtuales VMware en Azure con Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Entornos multiinquilino
Existen tres modelos principales multiinquilino:

* **Proveedor de servicios de hospedaje compartidos (HSP)**: en este caso el asociado posee la infraestructura física y usa los recursos compartidos (vCenter, centros de datos, almacenamiento físico, etc.) para hospedar máquinas virtuales de varios inquilinos en la misma infraestructura. El asociado puede proporcionar administración de recuperación ante desastres como servicio administrado, o bien el inquilino puede ser propietario de la recuperación ante desastres como solución de autoservicio.

* **Proveedor de servicios de hospedaje dedicados**: en este caso, el asociado posee la infraestructura física, pero usa recursos dedicados (varios vCenters, almacenes de datos físicos, etc.) para hospedar las máquinas virtuales de cada inquilino en infraestructuras diferentes. El asociado puede proporcionar administración de recuperación ante desastres como servicio administrado, o bien el inquilino puede poseerla como solución de autoservicio.

* **Proveedor de servicios administrados (MSP)**: aquí el cliente posee la infraestructura física que hospeda las máquinas virtuales y el asociado proporciona la habilitación y la administración de la recuperación ante desastres.

## <a name="shared-hosting-multi-tenant-guidance"></a>Guía para entornos multiinquilino: hospedaje compartido
Esta sección trata detalladamente el escenario de hospedaje compartido. Los otros dos escenarios son subconjuntos del escenario de hospedaje compartido y usan los mismos principios. Al final de la guía de hospedaje compartido se describen las diferencias.

El requisito básico en un escenario multiinquilino es aislar los diversos inquilinos. Un inquilino no podrá observar lo que tenga hospedado otro inquilino. En un entorno administrado por un asociado, este requisito no es tan importante como en el entorno de autoservicio, donde puede ser crítico. En esta guía se da por supuesto que se requiere el aislamiento de inquilinos.

La arquitectura se presenta en el diagrama siguiente:

![HSP compartido con un vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Escenario de hospedaje compartido con un vCenter**

Tal como se muestra en el diagrama anterior, cada cliente tiene un servidor de administración independiente. Esta configuración limita el acceso del inquilino a máquinas virtuales específicas de inquilino y permite el aislamiento de inquilinos. En el escenario de replicación de máquinas virtuales de VMware, se usa el servidor de configuración para administrar las cuentas con el fin de detectar máquinas virtuales e instalar agentes. Los mismos principios se siguen para entornos multiinquilinos, junto con la restricción de la detección de máquinas virtuales mediante el control de acceso a vCenter.

El requisito de aislamiento de datos precisa que toda la información confidencial de infraestructura (por ejemplo, credenciales de acceso) permanezca sin revelar para los inquilinos. Por este motivo, se recomienda que todos los componentes del servidor de administración permanecen bajo control exclusivo del asociado. Los componentes de servidor de administración son:
* Servidor de configuración (CS)
* Servidor de proceso (PS)
* Servidor de destino maestro (MT) 

Un PS de escalado horizontal también está bajo el control del asociado.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Cada CS del escenario multiinquilino emplea dos cuentas:

- **Cuenta de acceso de vCenter**: use esta cuenta para detectar máquinas virtuales del inquilino. Tiene asignados permisos de acceso a vCenter (tal y como se describe en la sección siguiente). Para ayudar a evitar pérdidas de acceso accidentales, se recomienda que los asociados escriban estas credenciales en la herramienta de configuración.

- **Cuenta de acceso a la máquina virtual**: esta cuenta se usa para instalar el agente de movilidad en las máquinas virtuales de inquilino mediante inserción automática. Suele ser una cuenta de dominio que un inquilino puede proporcionar a un asociado o que, de forma alternativa, el asociado puede administrar directamente. Si un inquilino no desea compartir los detalles con el asociado directamente, se le puede permitir escribir las credenciales mediante acceso limitado al CS o bien, con la ayuda del asociado, instalar los agentes de movilidad manualmente.

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
>| Servidor de administración | Azure_Site_Recovery | Incluye el acceso a todos los componentes (CS, PS y MT) si alguno está fuera de la máquina de CS. |
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

## <a name="csp-program-overview"></a>Información general del programa CSP
El [programa CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) fomenta escenarios de colaboración que ofrecen a los asociados todos los servicios en la nube de Microsoft, lo que incluye Office 365, Enterprise Mobility Suite y Microsoft Azure. Con CSP, nuestros asociados tienen una relación completa con los clientes y se ha convertido en el principal punto de contacto de la relación. Los asociados pueden implementar suscripciones de Azure para los clientes y combinarlas con sus propias ofertas personalizadas de valor añadido.

Con Azure Site Recovery, los asociados pueden administrar la solución completa de recuperación ante desastres de los clientes directamente a través de CSP. O bien, pueden usar CSP para configurar entornos de Site Recovery y dejar que los clientes administren sus propias necesidades de recuperación ante desastres en modo de autoservicio. En ambos escenarios, los asociados son el enlace entre Site Recovery y sus clientes. Los asociados atienden la relación con los clientes y les facturan por el uso de Site Recovery.

## <a name="create-and-manage-tenant-accounts"></a>Creación y administración de cuentas de inquilino

### <a name="step-0-prerequisite-check"></a>Paso 0: Comprobación de requisitos previos

Los requisitos previos de la máquina virtual son los mismos que los descritos en la [documentación](site-recovery-vmware-to-azure.md) de Azure Site Recovery. Además de los requisitos previos, debe tener instaurados los controles de acceso anteriormente mencionados antes de continuar con la administración de inquilinos mediante CSP. Para cada inquilino, cree un servidor de administración independiente que se pueda comunicar con las máquinas virtuales de inquilino y el vCenter del asociado. Solo el asociado tiene derechos de acceso a este servidor.

### <a name="step-1-create-a-tenant-account"></a>Paso 1: Creación de una cuenta de inquilino

1. Mediante el [Centro de partners de Microsoft](https://partnercenter.microsoft.com/), inicie sesión en su cuenta de CSP. 
 
2. En el menú **Panel**, seleccione **Clientes**.

    ![El vínculo de los clientes al Centro de partners de Microsoft](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

3. En la página que se abre, haga clic en el botón **Agregar cliente**.

    ![El botón Agregar cliente](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

4. En la página **Nuevo cliente**, rellene los detalles de información de la cuenta del inquilino y haga clic en **Siguiente: Suscripciones**.

    ![La página Información de la cuenta](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

5. En la página de selección de suscripciones, active la casilla **Microsoft Azure**. Puede agregar otras suscripciones ahora o en cualquier otro momento.

    ![La casilla de la suscripción de Microsoft Azure](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

6. En la página **Revisar**, confirme los detalles del inquilino y, a continuación, haga clic en **Enviar**.

    ![La página Revisar](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)  

    Después de haber creado la cuenta del inquilino, aparece una página de confirmación que muestra los detalles de la cuenta predeterminada y la contraseña de esa suscripción. 

7. Guarde la información y cambie la contraseña más adelante si es necesario mediante la página de inicio de sesión de Azure Portal.  
 
    Puede compartir esta información con el inquilino tal cual, o puede crear y compartir una cuenta independiente si es necesario.

### <a name="step-2-access-the-tenant-account"></a>Paso 2: Acceso a la cuenta de inquilino

Puede acceder a la suscripción del inquilino mediante el panel del Centro de partners de Microsoft, como se describe en "Paso 1: Creación de una cuenta de inquilino". 

1. Vaya a la página **Clientes** y, a continuación, haga clic en el nombre de la cuenta de inquilino.

2. En la página **Suscripciones** de la cuenta de inquilino, puede supervisar las suscripciones existentes de la cuenta y agregar más suscripciones, según sea necesario. Para administrar las operaciones de recuperación ante desastres del inquilino, seleccione **Todos los recursos (Azure Portal)**.

    ![El vínculo Todos los recursos](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)  
    
    Al hacer clic en **Todos los recursos** se le concede acceso a las suscripciones de Azure del inquilino. Para comprobar el acceso, haga clic en el vínculo de Azure Active Directory en la parte superior derecha de Azure Portal.

    ![Vínculo de Azure Active Directory](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

Ahora puede realizar todas las operaciones de Site Recovery en el inquilino mediante Azure Portal y administrar las operaciones de recuperación ante desastres. Para acceder a la suscripción del inquilino mediante CSP para la recuperación ante desastres administrada, siga el proceso descrito anteriormente.

### <a name="step-3-deploy-resources-to-the-tenant-subscription"></a>Paso 3: Implementación de recursos en la suscripción de inquilino
1. En Azure Portal, cree un grupo de recursos e implemente un almacén de Recovery Services por el proceso habitual. 
 
2. Descargue la clave de registro del almacén.

3. Registre el CS en el inquilino con la clave de registro del almacén.

4. Escriba las credenciales de las dos cuentas de acceso: la cuenta de acceso de vCenter y la cuenta de acceso de máquina virtual.

    ![Cuentas de servidor de Configuration Manager](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Paso 4: Registro de la infraestructura de Site Recovery en el almacén de Recovery Services
1. En Azure Portal, en el almacén que creó anteriormente, registre el servidor de vCenter en el CS que registró en "Paso 3: Implementación de recursos en la suscripción de inquilino". Para ello, use la cuenta de acceso de vCenter.
2. Finalice el proceso de preparación de la infraestructura de Site Recovery del modo habitual.
3. Las máquinas virtuales ahora están listas para replicarse. Compruebe que solo se muestran las máquinas virtuales del inquilino en la hoja **Seleccionar máquinas virtuales** en la opción **Replicar**.

    ![Lista de máquinas virtuales de inquilino en la hoja Seleccionar máquinas virtuales](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-the-subscription"></a>Paso 5: Asignación de acceso al inquilino a la suscripción

Para la recuperación ante desastres en modo de autoservicio, proporcione al inquilino los detalles de la cuenta, como se mencionó en el paso 6 de la sección "Paso 1: Creación de una cuenta de inquilino". Realice esta acción después de que el asociado configure la infraestructura de recuperación ante desastres. Si el tipo de recuperación ante desastres es administrado o autoservicio, los asociados deben acceder a las suscripciones del inquilino mediante el portal de CSP. Ellos configuran el almacén propiedad del asociado y registran la infraestructura en las suscripciones de inquilino.

Los asociados también pueden agregar un nuevo usuario a la suscripción de inquilino mediante el portal de CSP, de la manera siguiente:

1. Vaya a la página de suscripción de CSP del inquilino y seleccione la opción **Users and licenses** (Usuarios y licencias).

    ![Página de suscripción de CSP del inquilino](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    Ahora puede crear un nuevo usuario; para ello, escriba los datos pertinentes y seleccione los permisos, o bien cargue la lista de usuarios en un archivo CSV.

2. Después de crear un nuevo usuario, vuelva a Azure Portal y, luego, en la hoja **Suscripción**, seleccione la suscripción pertinente.

3. En la hoja que se abre, seleccione **Access Control (IAM)** y haga clic en **Agregar** para agregar un usuario con el nivel de acceso pertinente.      
    Los usuarios que se crearon mediante el portal de CSP se muestran automáticamente en la hoja que se abre al hacer clic en un nivel de acceso.

    ![Adición de un usuario](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    Para la mayoría de las operaciones de administración, el rol de *Colaborador* es suficiente. Un usuario con este nivel de acceso puede hacerlo todo en una suscripción, excepto cambiar los niveles de acceso (para lo cual se necesita el nivel de acceso de *Propietario*). También puede ajustar los niveles de acceso según sea necesario.
