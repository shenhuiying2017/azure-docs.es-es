---
title: Configuración de la replicación de VMware a Azure en un entorno multiinquilino con Site Recovery y el programa Proveedor de soluciones en la nube (CSP) | Microsoft Docs
description: Se describe cómo crear y administrar suscripciones de inquilinos mediante CSP e implementar Azure Site Recovery en una instalación multiinquilino
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 25591acb3f046744400f5dcf20a7ea651a7bcf54
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configuración de la replicación de VMware en un entorno multiinquilino con el programa Proveedor de soluciones en la nube (CSP)

El [programa CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) fomenta escenarios de colaboración para servicios en la nube de Microsoft, lo que incluye Office 365, Enterprise Mobility Suite y Microsoft Azure. Con CSP, los asociados tienen una relación completa con los clientes y se convierten en el principal punto de contacto de la relación. Los asociados pueden implementar suscripciones de Azure para los clientes y combinarlas con sus propias ofertas personalizadas de valor añadido.

Con [Azure Site Recovery](site-recovery-overview.md), los asociados pueden administrar la recuperación ante desastres de los clientes directamente mediante CSP. O bien, se puede usar CSP para configurar entornos de Site Recovery y dejar que los clientes administren sus propias necesidades de recuperación ante desastres en modo de autoservicio. En ambos escenarios, los asociados son el enlace entre Site Recovery y sus clientes. Los asociados atienden la relación con los clientes y les facturan por el uso de Site Recovery.

En este artículo se describe cómo los asociados pueden crear y administrar las suscripciones de los inquilinos mediante CSP para un escenario de replicación multiinquilino de VMware.

## <a name="prerequisites"></a>requisitos previos

Para configurar la replicación de VMware debe hacer lo siguiente:

- [Preparar](tutorial-prepare-azure.md) recursos de Azure, que incluyan una suscripción de Azure, una red virtual de Azure y una cuenta de almacenamiento.
- [Preparar](vmware-azure-tutorial-prepare-on-premises.md) las máquinas virtuales y los servidores de VMware locales. 
- Para cada inquilino, cree un servidor de administración independiente que se comunique con las máquinas virtuales de inquilino y sus propios servidores vCenter. Solo el asociado debe tener derechos de acceso a este servidor de administración. Más información sobre los [entornos multiinquilino](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Creación de una cuenta de inquilino

1. Mediante el [Centro de partners de Microsoft](https://partnercenter.microsoft.com/), inicie sesión en su cuenta de CSP.
2. En el menú **Panel**, seleccione **Clientes**.
3. En la página que se abre, haga clic en el botón **Agregar cliente**.
4. En la página **Nuevo cliente**, rellene los detalles de la cuenta del inquilino. 

    ![La página Información de la cuenta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. A continuación, haga clic en **Siguiente: suscripciones**.
6. En la página de selección de suscripciones, seleccione la casilla **Microsoft Azure**. Puede agregar otras suscripciones ahora o en cualquier otro momento.
7. En la página **Revisar**, confirme los detalles del inquilino y, a continuación, haga clic en **Enviar**.
8. Después de haber creado la cuenta del inquilino, aparece una página de confirmación que muestra los detalles de la cuenta predeterminada y la contraseña de esa suscripción. Guarde la información y cambie la contraseña más adelante si es necesario mediante la página de inicio de sesión de Azure Portal.

Puede compartir esta información con el inquilino tal cual, o puede crear y compartir una cuenta independiente si es necesario.

## <a name="access-the-tenant-account"></a>Acceso a la cuenta de inquilino

Puede acceder a la suscripción del inquilino mediante el panel del centro de partners de Microsoft.

1. En la página **Clientes**, haga clic en el nombre de la cuenta del inquilino.
2. En la página **Suscripciones** de la cuenta del inquilino, puede supervisar las suscripciones existentes de la cuenta y agregar otras, según sea necesario.
3. Para administrar las operaciones de recuperación ante desastres del inquilino, seleccione **Todos los recursos (Azure Portal)**. Esto concede acceso a las suscripciones de Azure del inquilino.

    ![El vínculo Todos los recursos](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Para verificar el acceso, haga clic en el vínculo de Azure Active Directory de la parte superior derecha de Azure Portal.

    ![Vínculo de Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Ahora puede realizar todas las operaciones de Site Recovery para el inquilino mediante Azure Portal y administrarlas. Para acceder a la suscripción del inquilino mediante CSP para la recuperación ante desastres administrada, siga el proceso descrito anteriormente.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Implementación de recursos en la suscripción de inquilino

1. En Azure Portal, cree un grupo de recursos e implemente un almacén de Recovery Services por el proceso habitual.
2. Descargue la clave de registro del almacén.
3. Registre el CS en el inquilino con la clave de registro del almacén.

4. Escriba las credenciales de las dos cuentas de acceso, la de acceso al servidor vCenter y la de acceso a la máquina virtual.

    ![Cuentas de servidor de Configuration Manager](./media/vmware-azure-multi-tenant-csp-disaster-recovery/config-server-account-display.png)

## <a name="register-servers-in-the-vault"></a>Registro de servidores en el almacén

1. En Azure Portal, en el almacén que creó anteriormente, registre el servidor vCenter como servidor de configuración, con la cuenta de vCenter que creó. 
2. Finalice el proceso de preparación de la infraestructura de Site Recovery del modo habitual.
3. Las máquinas virtuales ahora están listas para replicarse. Verifique que solo se muestran las máquinas virtuales del inquilino en **Replicar** > **Seleccionar máquinas virtuales**.


## <a name="assign-tenant-access-to-the-subscription"></a>Asignación del acceso de inquilino a la suscripción

1. Asegúrese de que esté configurada la infraestructura de recuperación ante desastres. Recuerde que debe acceder a las suscripciones del inquilino mediante el portal de CSP, independientemente de si la recuperación ante desastres es administrada o autoservicio. Debe configurar su propio almacén y registrar la infraestructura en las suscripciones del inquilino.
2. Proporcione al inquilino la [cuenta creada](#create-a-tenant-account)
3. También puede agregar un nuevo usuario a la suscripción de inquilino mediante el portal de CSP, de la manera siguiente:

    a) Vaya a la página de suscripción de CSP del inquilino y seleccione la opción **Users and licenses** (Usuarios y licencias).

        ![The tenant's CSP subscription page](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Ahora cree un nuevo usuario; para ello, escriba los datos pertinentes y seleccione los permisos, o bien cargue la lista de usuarios en un archivo CSV.
    c) Después de crear un usuario, vuelva Azure Portal. En la página **Suscripción**, seleccione la correspondiente.
    d) Seleccione **Control de acceso (IAM)** y haga clic en **Agregar** para agregar un usuario con el nivel de acceso pertinente. Los usuarios que se crearon mediante el portal de CSP se muestran automáticamente en la página que se abre al hacer clic en un nivel de acceso.

        ![Add a user](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Para la mayoría de las operaciones de administración, el rol de *Colaborador* es suficiente. Un usuario con este nivel de acceso puede hacerlo todo en una suscripción, excepto cambiar los niveles de acceso (para lo cual se necesita el nivel de acceso de *Propietario*).
- Site Recovery también tiene tres [roles de usuario predefinidos](site-recovery-role-based-linked-access-control.md) que se pueden usar para restringir aún más los niveles de acceso según sea necesario.

## <a name="next-steps"></a>Pasos siguientes
- [Más información](site-recovery-role-based-linked-access-control.md) sobre el control de acceso basado en roles para administrar implementaciones de Azure Site Recovery.
- [Más información](vmware-azure-architecture.md) sobre la arquitectura de replicación de VMware a Azure.
- [Revise el tutorial](vmware-azure-tutorial.md) de replicación de máquinas virtuales VMware en Azure.
