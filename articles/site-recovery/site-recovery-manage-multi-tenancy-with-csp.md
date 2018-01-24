---
title: "Administración de varios inquilinos en Azure Site Recovery con el programa del Proveedor de soluciones en la nube (CSP) | Microsoft Docs"
description: "Se describe cómo crear y administrar suscripciones de inquilinos mediante CSP e implementar Azure Site Recovery en una instalación multiinquilino"
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
ms.openlocfilehash: a49da33c8038ad467389c66e59727c7e195baf82
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>Administración de un entorno multiinquilino con el programa del Proveedor de soluciones en la nube (CSP)

El [programa CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) fomenta escenarios de colaboración para servicios en la nube de Microsoft, lo que incluye Office 365, Enterprise Mobility Suite y Microsoft Azure. Con CSP, los asociados tienen una relación completa con los clientes y se ha convertido en el principal punto de contacto de la relación. Los asociados pueden implementar suscripciones de Azure para los clientes y combinarlas con sus propias ofertas personalizadas de valor añadido.

Con Azure Site Recovery, los asociados pueden administrar la solución completa de recuperación ante desastres de los clientes directamente a través de CSP. O bien, pueden usar CSP para configurar entornos de Site Recovery y dejar que los clientes administren sus propias necesidades de recuperación ante desastres en modo de autoservicio. En ambos escenarios, los asociados son el enlace entre Site Recovery y sus clientes. Los asociados atienden la relación con los clientes y les facturan por el uso de Site Recovery.

En este artículo se describe cómo los asociados pueden crear y administrar suscripciones de inquilinos mediante CSP para una instalación multiinquilino de VMware.

## <a name="prerequisites"></a>requisitos previos

- [Preparar](tutorial-prepare-azure.md) recursos de Azure, que incluyan una suscripción de Azure, una red virtual de Azure y una cuenta de almacenamiento.
- [Preparar](tutorial-prepare-on-premises-vmware.md) máquinas virtuales y servidores VMware locales de VMware.
- Para cada inquilino, cree un servidor de administración independiente que se pueda comunicar con las máquinas virtuales de inquilino y el vCenter del asociado. Solo el asociado tiene derechos de acceso a este servidor. Para más información sobre los distintos entornos multiinquiino, consulte la guía de [VMware multiinquilino](site-recovery-multi-tenant-support-vmware-using-csp.md).

## <a name="create-a-tenant-account"></a>Creación de una cuenta de inquilino

1. Mediante el [Centro de partners de Microsoft](https://partnercenter.microsoft.com/), inicie sesión en su cuenta de CSP.

2. En el menú **Panel**, seleccione **Clientes**.

    ![El vínculo de los clientes al Centro de partners de Microsoft](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. En la página que se abre, haga clic en el botón **Agregar cliente**.

    ![El botón Agregar cliente](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. En la página **Nuevo cliente**, rellene los detalles de información de la cuenta del inquilino y haga clic en **Siguiente: Suscripciones**.

    ![La página Información de la cuenta](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. En la página de selección de suscripciones, active la casilla **Microsoft Azure**. Puede agregar otras suscripciones ahora o en cualquier otro momento.

    ![La casilla de la suscripción de Microsoft Azure](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. En la página **Revisar**, confirme los detalles del inquilino y, a continuación, haga clic en **Enviar**.

    ![La página Revisar](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    Después de haber creado la cuenta del inquilino, aparece una página de confirmación que muestra los detalles de la cuenta predeterminada y la contraseña de esa suscripción.

7. Guarde la información y cambie la contraseña más adelante si es necesario mediante la página de inicio de sesión de Azure Portal.  

    Puede compartir esta información con el inquilino tal cual, o puede crear y compartir una cuenta independiente si es necesario.

## <a name="access-the-tenant-account"></a>Acceso a la cuenta de inquilino

Puede acceder a la suscripción del inquilino mediante el panel del Centro de partners de Microsoft, como se describe en "Paso 1: Creación de una cuenta de inquilino".

1. Vaya a la página **Clientes** y, a continuación, haga clic en el nombre de la cuenta de inquilino.

2. En la página **Suscripciones** de la cuenta de inquilino, puede supervisar las suscripciones existentes de la cuenta y agregar más suscripciones, según sea necesario. Para administrar las operaciones de recuperación ante desastres del inquilino, seleccione **Todos los recursos (Azure Portal)**.

    ![El vínculo Todos los recursos](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    Al hacer clic en **Todos los recursos** se le concede acceso a las suscripciones de Azure del inquilino. Para comprobar el acceso, haga clic en el vínculo de Azure Active Directory en la parte superior derecha de Azure Portal.

    ![Vínculo de Azure Active Directory](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

Ahora puede realizar todas las operaciones de Site Recovery en el inquilino mediante Azure Portal y administrar las operaciones de recuperación ante desastres. Para acceder a la suscripción del inquilino mediante CSP para la recuperación ante desastres administrada, siga el proceso descrito anteriormente.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Implementación de recursos en la suscripción de inquilino
1. En Azure Portal, cree un grupo de recursos e implemente un almacén de Recovery Services por el proceso habitual.

2. Descargue la clave de registro del almacén.

3. Registre el CS en el inquilino con la clave de registro del almacén.

4. Escriba las credenciales de las dos cuentas de acceso: la cuenta de acceso de vCenter y la cuenta de acceso de máquina virtual.

    ![Cuentas de servidor de Configuration Manager](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Registro de la infraestructura de Site Recovery en el almacén de Recovery Services
1. En Azure Portal, en el almacén que creó anteriormente, registre el servidor de vCenter en el CS que registró en "Paso 3: Implementación de recursos en la suscripción de inquilino". Para ello, use la cuenta de acceso de vCenter.
2. Finalice el proceso de preparación de la infraestructura de Site Recovery del modo habitual.
3. Las máquinas virtuales ahora están listas para replicarse. Compruebe que solo se muestran las máquinas virtuales del inquilino en la hoja **Seleccionar máquinas virtuales** en la opción **Replicar**.

    ![Lista de máquinas virtuales de inquilino en la hoja Seleccionar máquinas virtuales](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>Asignación del acceso de inquilino a la suscripción

Para la recuperación ante desastres en modo de autoservicio, proporcione al inquilino los detalles de la cuenta, como se mencionó en el paso 6 de la sección "Paso 1: Creación de una cuenta de inquilino". Realice esta acción después de que el asociado configure la infraestructura de recuperación ante desastres. Si el tipo de recuperación ante desastres es administrado o autoservicio, los asociados deben acceder a las suscripciones del inquilino mediante el portal de CSP. Ellos configuran el almacén propiedad del asociado y registran la infraestructura en las suscripciones de inquilino.

Los asociados también pueden agregar un nuevo usuario a la suscripción de inquilino mediante el portal de CSP, de la manera siguiente:

1. Vaya a la página de suscripción de CSP del inquilino y seleccione la opción **Users and licenses** (Usuarios y licencias).

    ![Página de suscripción de CSP del inquilino](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    Ahora puede crear un nuevo usuario; para ello, escriba los datos pertinentes y seleccione los permisos, o bien cargue la lista de usuarios en un archivo CSV.

2. Después de crear un nuevo usuario, vuelva a Azure Portal y, luego, en la hoja **Suscripción**, seleccione la suscripción pertinente.

3. En la hoja que se abre, seleccione **Access Control (IAM)** y haga clic en **Agregar** para agregar un usuario con el nivel de acceso pertinente.      
    Los usuarios que se crearon mediante el portal de CSP se muestran automáticamente en la hoja que se abre al hacer clic en un nivel de acceso.

    ![Adición de un usuario](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    Para la mayoría de las operaciones de administración, el rol de *Colaborador* es suficiente. Un usuario con este nivel de acceso puede hacerlo todo en una suscripción, excepto cambiar los niveles de acceso (para lo cual se necesita el nivel de acceso de *Propietario*).

  Azure Site Recovery también tiene tres [roles de usuario predefinidos](site-recovery-role-based-linked-access-control.md) que se pueden usar para restringir aún más los niveles de acceso según sea necesario.

## <a name="next-steps"></a>pasos siguientes
  [Más información](site-recovery-role-based-linked-access-control.md) sobre el control de acceso basado en rol para administrar implementaciones de Azure Site Recovery.

  [Administración de entornos de VMware multiinquilino](site-recovery-multi-tenant-support-vmware-using-csp.md)
