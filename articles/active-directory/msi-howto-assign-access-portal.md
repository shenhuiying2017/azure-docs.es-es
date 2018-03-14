---
title: "Asignación de un acceso de MSI a un recurso de Azure mediante Azure Portal"
description: Instrucciones paso a paso para asignar a una identidad de servicio administrada (MSI) de un recurso acceso a otro recurso, mediante Azure Portal.
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 6522a5ef7f1e1af89ee70ee74041e1e69614ebca
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2018
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Asignación de acceso de Managed Service Identity a un recurso mediante Azure Portal

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Una vez que haya configurado un recurso de Azure con Managed Service Identity (MSI), puede dar acceso de MSI a otro recurso, al igual que cualquier entidad de seguridad. En este artículo se muestra cómo otorgar acceso a la MSI de un conjunto de escalado de máquinas virtuales o una máquina virtual de Azure para una cuenta de Azure Storage mediante Azure Portal.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Uso de RBAC para asignar el acceso de MSI a otro recurso

Después de habilitar MSI en un recurso de Azure, como [Azure VM](msi-qs-configure-portal-windows-vm.md) o [Azure VMSS](msi-qs-configure-portal-windows-vmss.md):

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure en la que ha configurado la entidad de servicio administrada.

2. Vaya al recurso para el que desea modificar el control de acceso. En este ejemplo, vamos a dar a una máquina virtual de Azure y a un conjunto de escalado de máquinas virtuales de Azure acceso a una cuenta de almacenamiento, de modo que navegamos a la cuenta de almacenamiento.

3. Para una máquina virtual de Azure, seleccione la página **Control de acceso (IAM)** del recurso y seleccione **+ Agregar**. A continuación, especifique el **Rol**, **asigne acceso a la máquina virtual** y especifique la correspondiente **Suscripción** y **Grupo de recursos** donde reside el recurso. En el área de criterios de búsqueda, debería ver el recurso. Seleccione el recurso y seleccione **Guardar**. 

   ![Captura de pantalla Control de acceso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Para un conjunto de escalado de máquinas virtuales de Azure, seleccione la página **Control de acceso (IAM)** del recurso y seleccione **+ Agregar**. A continuación, especifique el **Rol** y **Asignar acceso a**. En el área de criterios de búsqueda, busque el conjunto de escalado de máquinas virtuales. Seleccione el recurso y seleccione **Guardar**.
   
   ![Captura de pantalla Control de acceso (IAM)](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Vuelve a la página principal **Control de acceso (IAM)**, donde se ve una nueva entrada para la MSI del recurso.

    Máquina virtual de Azure:

   ![Captura de pantalla Control de acceso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Conjunto de escalado de máquinas virtuales de Azure:

    ![Captura de pantalla Control de acceso (IAM)](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>solución de problemas

Si la identidad de servicio administrada para el recurso no aparece en la lista de identidades disponibles, compruebe que la identidad se haya habilitado correctamente. En nuestro caso, podemos volver a la máquina virtual de Azure y comprobar lo siguiente:

- Mirar la página **Configuración** y asegurarse de que el valor de **MSI habilitado** es **Sí**.
- Mire la página **Extensiones** y asegúrese de que la extensión MSI se implementó correctamente (la página **Extensiones** no está disponible para un conjunto de escalado de máquinas virtuales de Azure).

Si alguna no es correcta, puede que tenga que volver a implementar MSI en el recurso de nuevo o solucionar el error de implementación.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).
- Para habilitar MSI en una máquina virtual de Azure, consulte [Configuración de Managed Service Identity (MSI) en una máquina virtual de Azure con Azure Portal](msi-qs-configure-portal-windows-vm.md).
- Para habilitar MSI en un conjunto de escalado de máquinas virtuales de Azure, consulte [Configure an Azure Virtual Machine Scale Set Managed Service Identity (MSI) using the Azure portal](msi-qs-configure-portal-windows-vmss.md) (Configuración de Managed Service Identity [MSI] de conjunto de escalado de máquinas virtuales de Azure con Azure Portal).


