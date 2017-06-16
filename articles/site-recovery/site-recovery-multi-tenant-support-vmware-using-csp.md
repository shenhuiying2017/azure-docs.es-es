---
title: "Compatibilidad multiempresa para la replicación de VM de VMware en Azure (programa CSP) | Microsoft Docs"
description: "Se describe cómo implementar Azure Site Recovery en un entorno multiinquilino para organizar la replicación, la conmutación por error y la recuperación de máquinas virtuales de VMware locales en Azure mediante el programa CSP con Azure Portal."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: manayar
ms.translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: ed484afc59bbf48490e3ff4389e8e28c71a5e471
ms.contentlocale: es-es
ms.lasthandoff: 01/30/2017


---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-the-csp-program"></a>Compatibilidad con multiinquilino en Azure Site Recovery para la replicación de máquinas virtuales de VMware en Azure mediante el programa CSP.

Azure Site Recovery admite entornos multiinquilino en suscripciones de inquilino. El entorno multiinquilino se admite también suscripciones de inquilino creadas y administradas mediante el programa CSP. En este artículo se detallan las instrucciones para implementar y administrar escenarios multiinquilino de VMware en Azure. También se describe la creación y la administración de suscripciones de inquilino mediante CSP.

Tenga en cuenta que esta guía se basa en buena medida en la documentación existente para replicar máquinas virtuales de VMware en Azure, y debe usarse en combinación con esa [documentación](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Entornos multiinquilino
Existen tres modelos principales multiinquilino:

1.  **Proveedor de servicios de hospedaje compartidos (HSP)**: en este caso el asociado posee la infraestructura física y usa los recursos compartidos (vCenter, centros de datos, almacenamiento físico, etc.) para hospedar máquinas virtuales de varios inquilinos en la misma infraestructura. El asociado puede proporcionar la administración de la recuperación ante desastres como un servicio administrado o ser propiedad esta del inquilino como una solución de recuperación ante desastres de autoservicio.
2.  **Proveedor de servicios de hospedaje dedicados**: en este caso, el asociado posee la infraestructura física, pero usa recursos dedicados (varios vCenters, almacenes de datos físicos, etc.) para hospedar las máquinas virtuales de cada inquilino en infraestructuras diferentes. De nuevo, el asociado puede proporcionar la administración de la recuperación ante desastres o ser esta un autoservicio que administra el inquilino.
3.  **Proveedor de servicios administrados (MSP)**: aquí el cliente posee la infraestructura física que hospeda las máquinas virtuales y los asociados habilitan y administran la recuperación ante desastres.

## <a name="shared-hosting-multi-tenant-guidance"></a>Guía para entornos multiinquilino: hospedaje compartido
Esta guía trata detalladamente el escenario de hospedaje compartido. Los otros dos escenarios son subconjuntos del escenario de hospedaje compartido y usan los mismos principios. Al final de la guía de hospedaje compartido se describen las diferencias.

El requisito básico en un escenario multiinquilino es aislar los diferentes inquilinos; es decir, un inquilino no podrá observar lo que otro inquilino haya hospedado. En un entorno completamente administrado por un asociado, este requisito no es tan importante como en el entorno de autoservicio, donde puede ser crítico. En esta guía se da por supuesto que se requiere el aislamiento de inquilinos.

La arquitectura es la siguiente:

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)

**Figura 1: Escenario de hospedaje compartido con un vCenter**

Tal como se puede ver por la presentación anterior, cada cliente tendrá un servidor de administración independiente. Esto se hace para limitar el acceso del inquilino a máquinas virtuales específicas de inquilino a fin de habilitar el aislamiento de inquilinos. En el escenario de replicación de máquinas virtuales de VMware, se usa el servidor de configuración para administrar las cuentas con el fin de detectar máquinas virtuales y agentes de instalación. Los mismos principios se siguen para entornos multiinquilinos, junto con la restricción de la detección de máquinas virtuales mediante el control de acceso a vCenter.

El requisito de aislamiento de datos necesita que toda la información confidencial de infraestructura (por ejemplo, credenciales de acceso) siga siendo relevada por los inquilinos. Por este motivo, es recomendable que todos los componentes del servidor de administración (servidor de configuración [CS], servidor de proceso [PS] y servidor de destino maestro [MT]) permanezcan bajo el estricto el control del asociado. Esto incluye el PS de escalado horizontal.

### <a name="every-cs-under-the-multi-tenant-scenario-uses-two-accounts"></a>Cada CS en el escenario multiinquilino emplea dos cuentas:

- **Cuenta de acceso de vCenter**: esta cuenta se usa para detectar máquinas virtuales de inquilino y es la cuenta que tiene asignados permisos de acceso a vCenter (se describe en la siguiente sección). Se recomienda que el asociado inserte estas credenciales en la herramienta de configuración él mismo con el fin de evitar pérdidas de acceso accidentales.
- **Cuenta de acceso de máquina virtual**: esta cuenta se usa para instalar el agente de movilidad en las máquinas virtuales de inquilino mediante inserción automática. Suele ser una cuenta de dominio que un inquilino puede proporcionar al asociado; también puede administrarla directamente el asociado. En caso de que el inquilino no quiera compartir directamente los detalles con el asociado, se le puede dar permiso para que inserte las credenciales mediante un acceso con tiempo limitado al CS; como alternativa, el inquilino puede instalar agentes de movilidad manualmente con ayuda del asociado.

### <a name="requirements-for-vcenter-access-account"></a>Requisitos para la cuenta de acceso de vCenter

Como se ha detallado en la sección anterior, es necesario que el CS esté configurado con una cuenta que tenga asignado un rol especial. Es importante advertir que esta asignación de rol se debe realizar a la cuenta de acceso de vCenter en cada objeto de vCenter y no propagarse a los objetos secundarios. El objetivo es garantizar el aislamiento de los inquilinos puesto que la propagación de acceso puede dar lugar también al acceso accidental a otros objetos.

![permissions-without-propagation](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

La alternativa consiste en asignar la cuenta de usuario y el rol en el objeto Datacenter y propagarlos a los objetos secundarios; tras lo cual se tiene que dar a esa cuenta un rol "Sin acceso" para cada objeto (por ejemplo, las máquinas virtuales de otros inquilinos) que no debe ser accesible a un inquilino determinado. Esto es complejo y expone controles de acceso accidentales, ya que a cada nuevo objeto secundario creado se le concede automáticamente acceso heredado desde el objeto primario. De ahí que se recomiende seguir con el primer enfoque.

El procedimiento de acceso a la cuenta de vCenter es el siguiente:

1.  Cree un nuevo rol mediante la clonación del rol de "Solo lectura" predefinido y proporciónele un nombre adecuado (como Azure_Site_Recovery, que se usa en este ejemplo).
2.  Asigne los siguientes permisos a este rol:
 *  Almacén de datos -> Asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual
 *  Red -> Asignación de red
 *  Recursos-> Asignar máquina virtual al grupo de recursos, migrar apagado de máquina virtual, migrar máquinas virtuales encendidas
 *  Tareas -> Crear tarea, actualizar tarea
 *  Máquina virtual -> Configuración
 *  Máquina virtual -> Interactuar -> Responder a pregunta, conexión de dispositivos, configurar soporte de CD, configurar soporte de disquete, apagar, encender, instalación de herramientas de VMware
 *  Máquina virtual -> Inventario -> Crear, registrar, anular registro
 *  Máquina virtual -> Aprovisionamiento -> Permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual
 *  Máquina virtual -> Instantáneas -> Quitar instantáneas

    ![role-permissions](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3.  Asigne el nivel de acceso a la cuenta de vCenter (usada en el CS del inquilino) para los distintos objetos, de la manera siguiente:

| **Object** | **Rol** | **Comentarios** |
| --- | --- | --- |
| vCenter | Solo lectura | Solo es necesario para permitir el acceso a vCenter y administrar diferentes objetos. Este permiso se puede quitar si la cuenta nunca se va a proporcionar a un inquilino ni se va a usar para las operaciones de administración en el vCenter. |
| Centro de datos | Azure_Site_Recovery |  |
| Host y clúster de hosts | Azure_Site_Recovery | De nuevo, asegúrese de que el acceso esté en el nivel de objeto de modo que solo esos hosts que son accesibles tengan máquinas virtuales de inquilino antes de la conmutación por error y después de la conmutación por recuperación. |
| Almacén de datos y clúster de almacén de datos | Azure_Site_Recovery | Lo mismo que antes. |
| Red | Azure_Site_Recovery |  |
| Servidor de administración | Azure_Site_Recovery | Incluye el acceso a todos los componentes, CS, PS y MT, si alguno está fuera de la máquina de CS. |
| Máquinas virtuales de inquilinos | Azure_Site_Recovery | Asegúrese de que las nuevas máquinas virtuales de inquilino de un inquilino determinado obtengan también este acceso o no se podrán detectar a través de Azure Portal. |

El acceso a la cuenta de vCenter ha finalizado ahora y se cumplen los requisitos mínimos de permisos para realizar las operaciones de conmutación por recuperación. Tenga en cuenta que estos permisos de acceso también pueden usarse con las directivas existentes. Solo tiene que modificar el conjunto de permisos existente para incluir los permisos de rol del punto 2 detallado anteriormente.

Para restringir las operaciones de recuperación ante desastres hasta el estado de conmutación por error, es decir, sin funcionalidades de conmutación por recuperación, siga el procedimiento anterior, pero en lugar de asignar el rol "Azure_Site_Recovery" a la cuenta de acceso de vCenter, asigne solo un rol de "Solo lectura" a esa cuenta. Este conjunto de permisos permite la replicación de máquinas virtuales y la conmutación por error no permite la conmutación por recuperación. Tenga en cuenta que todo lo demás del proceso anterior permanece igual. Cada permiso se sigue asignando solamente en el nivel de objeto y no se propaga a los objetos secundarios para garantizar el aislamiento de inquilinos y restringir la detección de máquinas virtuales.

## <a name="other-multi-tenant-environments"></a>Otros entornos multiinquilino

En la guía anterior se ha descrito de forma detallada cómo configurar un entorno multiinquilino para una solución de hospedaje compartido. Las otras dos soluciones principales son hospedaje dedicado y servicio administrado. Su arquitectura es como se muestra a continuación:

### <a name="dedicated-hosting-solution"></a>Solución de hospedaje dedicado

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)

**Figura 2: Escenario de hospedaje dedicado con varios vCenters**

Aquí la diferencia en la arquitectura es que la infraestructura de cada inquilino solo se aprovisiona para ese inquilino. El proveedor de hospedaje aún debe seguir los pasos del CSP detallados para el hospedaje compartido, pero no tiene que preocuparse por el aislamiento de los inquilinos, ya que los inquilinos están aislados mediante distintos vCenters. El aprovisionamiento del CSP permanece sin cambios.

### <a name="managed-service-solution"></a>Solución de servicios administrados

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)

**Figura 3: Escenario de servicios administrados con varios vCenters**

Aquí la diferencia en la arquitectura es que la infraestructura de cada inquilino también está separada físicamente de la de los demás. Este escenario existe normalmente cuando el inquilino es propietario de la infraestructura y simplemente desea un proveedor de soluciones para administrar la recuperación ante desastres. De nuevo, el asociado debe seguir los pasos del CSP detallados para el hospedaje compartido, pero no tiene que preocuparse por el aislamiento de los inquilinos, ya que los inquilinos están aislados físicamente mediante distintas infraestructuras. El aprovisionamiento del CSP permanece sin cambios.


## <a name="csp-program-overview"></a>Información general del programa CSP
El [programa](https://partner.microsoft.com/en-US/cloud-solution-provider) del Proveedor de soluciones en la nube (CSP) de Microsoft promueve los casos de trabajo conjunto con los asociados en la oferta de todos los servicios en la nube de Microsoft, como O365, EMS y Microsoft Azure. Permite que nuestros asociados tengan una relación completa con los clientes y se ha convertido en el principal punto de contacto de la relación. Mediante CSP, un asociado puede implementar suscripciones de Azure para los clientes y combinarlas con sus propias ofertas personalizadas de valor añadido.

En el caso de Azure Site Recovery, los asociados pueden administrar la solución completa de recuperación ante desastres para los clientes directamente a través de CSP o usar CSP para configurar los entornos de Azure Site Recovery y permitir que los clientes administren sus propias necesidades de recuperación ante desastres en modo de autoservicio. En ambos escenarios, el asociado es el enlace entre Azure Site Recovery y los clientes finales, se ocupa de las relaciones con los clientes y les factura por el uso de Azure Site Recovery.

## <a name="creating-and-managing-tenant-accounts"></a>Creación y administración de cuentas de inquilino

### <a name="step-0-prerequisite-check"></a>Paso 0: Comprobación de requisitos previos

Los requisitos previos de la máquina virtual son los mismos que los descritos en la [documentación](site-recovery-vmware-to-azure.md) de Azure Site Recovery. Además de esos requisitos previos, deben estar implementados los controles de acceso anteriores antes de proceder a la administración de inquilinos mediante CSP. Cree un servidor de administración diferente para cada inquilino que se pueda comunicar con las máquinas virtuales de inquilino y el vCenter del asociado. Solo el asociado tiene derechos de acceso a este servidor.

### <a name="step-1-create-tenant-account"></a>Paso 1: Creación de la cuenta de inquilino

1.  Mediante el [Centro de partners](https://partnercenter.microsoft.com/), inicie sesión en su cuenta de CSP. En el menú del panel de la izquierda, seleccione la opción "Customers" (Clientes).

    ![csp-dashboard](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

2.  En la página que se abre, haga clic en el botón "Agregar cliente".

    ![add-customer](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

3.  En la página New Customer (Nuevo cliente), rellene todos los detalles de la información de la cuenta del inquilino y haga clic en "Next:Subscriptions" (Siguiente:Suscripciones).

    ![fill-details](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

4.  En la página de selección de las suscripciones, desplácese hacia abajo para agregar la suscripción de "Microsoft Azure". Puede agregar en este momento otras suscripciones o dejarlo para otro momento.

    ![add-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

5.  Continúe y, en la siguiente página, revise todos los detalles especificados para el inquilino y haga clic en el botón Enviar.

    ![customer-summary](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)

6.  Después de crear el cliente, llegará a una página de confirmación con los detalles de la cuenta predeterminada y la contraseña para esa suscripción. Guarde la información y cambie la contraseña más adelante si es necesario mediante el inicio de sesión de Azure Portal. Esta información se puede compartir tal y como está con el inquilino, o también se puede crear una cuenta aparte y compartirse en caso necesario.

### <a name="step-2-access-tenant-account"></a>Paso 2: Acceso a la cuenta de inquilino

1.  Puede acceder a la suscripción del inquilino desde la página "Customers" (Clientes) mediante el Panel, como se describe en el paso 1. Vaya hasta aquí y haga clic en el nombre de la cuenta de inquilino que acaba de crear.
2.  Se abre la sección de suscripciones de dicha cuenta y, desde aquí, puede supervisar las suscripciones existentes de la cuenta y agregar más suscripciones si es necesario. Para administrar las operaciones de recuperación ante desastres del inquilino, seleccione la ' opción All de recursos (portal de Azure) en el lado derecho de la página.

    ![all-resources](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)

3.  Al hacer clic en el botón "All resources" (Todos los recursos) se le concede acceso a las suscripciones de Azure del inquilino; esto mismo lo puede comprobar si marca la instancia de AAD que se muestra en la esquina superior derecha de Azure Portal.

    ![aad-admin](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

Ahora puede realizar todas las operaciones de Site Recovery para el inquilino mediante Azure Portal y administrar las operaciones de recuperación ante desastres. Cada vez que acceda a la suscripción del inquilino mediante CSP para la recuperación ante desastres administrada, debe seguir el proceso detallado anteriormente.

### <a name="step-3-deploy-resources-to-tenant-subscription"></a>Paso 3: Implementación de los recursos en la suscripción de inquilino
1.  En Azure Portal, cree un grupo de recursos e implemente un almacén de Recovery Services por el proceso habitual. Descargue la clave de registro del almacén.
2.  Registre el CS para el inquilino con la clave de registro del almacén.
3.  Escriba las credenciales de las dos cuentas de acceso: la cuenta de acceso de vCenter y la cuenta de acceso de la máquina virtual.

    ![config-accounts](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-recovery-services-vault"></a>Paso 4: Registro de la infraestructura de Site Recovery en el almacén de Recovery Services
1.  Abra Azure Portal y, en el almacén creado anteriormente, registre el servidor de vCenter en el CS registrado en el paso anterior. Para ello, use la cuenta de acceso de vCenter.
2.  Finalice el proceso de preparación de la infraestructura de Site Recovery del modo habitual.
3.  Las máquinas virtuales ahora están listas para replicarse. Compruebe que solo están visibles las máquinas virtuales del inquilino en la hoja de selección de máquinas virtuales situada bajo la opción de replicación.

    ![tenant-vms](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-subscription"></a>Paso 5: Asignación de acceso al inquilino a la suscripción

En el caso de que se use el modo de autoservicio para la recuperación ante desastres, se deben proporcionar al inquilino los detalles de la cuenta mencionados en el punto 6 del paso 1. Esto debe hacerse una vez que el asociado configure la infraestructura de recuperación ante desastres. Con independencia del tipo de recuperación ante desastres (administrada o autoservicio), el asociado debe acceder a las suscripciones de inquilino únicamente mediante el portal de CSP y configurar el almacén y registrar la infraestructura de su propiedad en las suscripciones de inquilino.

El asociado también puede agregar un nuevo usuario a la suscripción de inquilino mediante el portal de CSP, de la manera siguiente:

1.  Vaya a la página de suscripción de CSP del inquilino y seleccione la opción "Users and licenses" (Usuarios y licencias).

    ![user-licenses](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    Ahora puede crear un nuevo usuario; para ello, escriba los datos pertinentes y seleccione los permisos, o bien cargue la lista de usuarios mediante un archivo CSV.
2.  Una vez que se crean los usuarios, vuelva a Azure Portal y, en la hoja Suscripción, seleccione la suscripción correspondiente.
3.  En la hoja nueva que se abre, seleccione Access Control (IAM) y haga clic en + Agregar para agregar un usuario con el nivel de acceso pertinente. Los usuarios creados mediante el portal de CSP se muestran automáticamente en la hoja que se abre al hacer clic en un nivel de acceso.

    ![user-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    Para la mayoría de las operaciones de administración, el rol de Colaborador es suficiente. Un usuario con este nivel de acceso puede hacerlo todo en una suscripción, excepto cambiar los niveles de acceso (para lo cual se necesita el nivel de acceso de Propietario). También puede ajustar los niveles de acceso según sea necesario.

