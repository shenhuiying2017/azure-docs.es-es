---
title: "Administración de una cuenta de Azure Automation | Microsoft Docs"
description: "En este artículo se describe cómo administrar la configuración de la cuenta de Automation: renovación, eliminación o configuración incorrecta de certificados."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: 56216f16ba3730d1488e45c0e7a81e87dbad6410
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="manage-azure-automation-account"></a>Administración de la cuenta de Azure Automation
En algún momento antes de que expire su cuenta de Automation, debe renovar el certificado. Si cree que se ha puesto en peligro la cuenta de ejecución, puede eliminarla y volver a crearla. En esta sección se describe cómo realizar estas operaciones.

## <a name="self-signed-certificate-renewal"></a>Renovación de certificado autofirmado
El certificado autofirmado que creó para la cuenta de ejecución expira un año a partir de la fecha de creación. Se puede renovar en cualquier momento antes de que expire. Cuando se renueva, el certificado válido actual se conserva para tener la seguridad de que los runbooks que están en cola o que se están ejecutando activamente y que se autentican con la cuenta de ejecución, no resultan afectados negativamente. El certificado es válido hasta la fecha de expiración.

> [!NOTE]
> Si ha configurado la cuenta de ejecución de Automation para usar un certificado emitido por una entidad de certificación de empresa y usa esta opción, ese certificado se reemplaza por otro autofirmado.

Para renovar el certificado, realice estos pasos:

1. Abra la cuenta de Automation en Azure Portal.

2. En la **Cuenta de Automation**, 
3. en el panel **Propiedades de la cuenta**, en **Configuración de la cuenta**, seleccione **Cuentas de ejecución**.

    ![Panel de propiedades de la cuenta de Automation](media/automation-manage-account/automation-account-properties-pane.png)
3. En la página de propiedades **Cuentas de ejecución**, seleccione la cuenta de ejecución o la cuenta de ejecución clásica para la que quiere renovar el certificado.

4. En el panel **Propiedades** de la cuenta seleccionada, haga clic en **Renovar certificado**.

    ![Renovación del certificado para una cuenta de ejecución](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. Mientras se está renovando el certificado, puede seguir el progreso desde el menú, en **Notificaciones**.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminación de una cuenta de ejecución o de ejecución clásica
En esta sección se describe cómo eliminar y volver a crear una cuenta de ejecución o de ejecución clásica. Al realizar esta acción, la cuenta de Automation se conserva. Después de eliminar una cuenta de ejecución o de ejecución clásica, puede volver a crearla en el portal de Azure.

1. Abra la cuenta de Automation en Azure Portal.

2. En la página **Cuenta de Automation**, seleccione **Cuentas de ejecución**.

3. En la página de propiedades **Cuentas de ejecución**, seleccione la cuenta de ejecución o la cuenta de ejecución clásica que quiere eliminar. A continuación, en el panel **Propiedades** de la cuenta seleccionada, haga clic en **Eliminar**.

 ![Eliminación de una cuenta de ejecución](media/automation-manage-account/automation-account-delete-runas.png)

4. Mientras se está eliminando la cuenta, puede seguir el progreso desde el menú, en **Notificaciones**.

5. Después de eliminar la cuenta, puede volver a crearla en la página de propiedades **Cuentas de ejecución** seleccionando la opción de creación **Cuenta de ejecución de Azure**.

 ![Nueva creación de la cuenta de ejecución de Automation](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>Error de configuración
Puede que alguno de los elementos de configuración necesarios para que la cuenta de ejecución o la cuenta de ejecución clásica funcionen correctamente se haya eliminado o no se haya creado correctamente durante la configuración inicial. Estos elementos son:

* Recurso de certificado
* Recurso de conexión
* La cuenta de ejecución se ha quitado del rol de colaborador
* Entidad de servicio o aplicación en Azure AD

En los casos anteriores y en otros casos de errores de configuración, la cuenta de Automation detecta los cambios y muestra el estado *Incompleto* en el panel de propiedades **Cuentas de ejecución** de la cuenta.

![Estado de configuración incompleta de la cuenta de ejecución](media/automation-manage-account/automation-account-runas-incomplete-config.png)

Al seleccionar la cuenta de ejecución, el panel **Propiedades** muestra el mensaje de error siguiente:

![Mensaje de advertencia de configuración incompleta de la cuenta de ejecución](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png).

Rápidamente puede resolver estos problemas de la cuenta de ejecución con solo eliminarla cuenta y volver a crearla.

## <a name="next-steps"></a>pasos siguientes
* Para más información acerca de las entidades de servicio, consulte [Objetos Application y objetos ServicePrincipal](../active-directory/active-directory-application-objects.md).
* Para más información acerca del control de acceso basado en rol de Azure Automation, consulte [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).
* Para más información acerca de los certificados y de los servicios de Azure, consulte [Introducción a los certificados para Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).