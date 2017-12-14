---
title: "Asignación de un acceso de MSI a un recurso de Azure mediante Azure Portal"
description: Instrucciones paso a paso para asignar a una identidad de servicio administrada (MSI) de un recurso acceso a otro recurso, mediante Azure Portal.
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 88abc2a9836633e5d88a91e59f7078a388b26068
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Asignación de acceso de Managed Service Identity a un recurso mediante Azure Portal

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Una vez que haya configurado un recurso de Azure con Managed Service Identity (MSI), puede dar acceso de MSI a otro recurso, al igual que cualquier entidad de seguridad. En este artículo se muestra cómo otorgar acceso a la MSI de una máquina virtual de Azure para una cuenta de Azure Storage mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Uso de RBAC para asignar el acceso de MSI a otro recurso

Después de habilitar MSI en un recurso de Azure, [como una máquina virtual de Azure](msi-qs-configure-portal-windows-vm.md):

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure en la que ha configurado la entidad de servicio administrada.

2. Vaya al recurso para el que desea modificar el control de acceso. En este ejemplo, vamos a dar a una máquina virtual de Azure acceso a una cuenta de almacenamiento, de modo que navegamos a la cuenta de almacenamiento.

3. Seleccione la página **Control de acceso (IAM)** del recurso y seleccione **+ Agregar**. A continuación, especifique el **Rol**, **asigne acceso a la máquina virtual** y especifique la correspondiente **Suscripción** y **Grupo de recursos** donde reside el recurso. En el área de criterios de búsqueda, debería ver el recurso. Seleccione el recurso y seleccione **Guardar**. 

   ![Captura de pantalla Control de acceso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Vuelve a la página principal **Control de acceso (IAM)**, donde se ve una nueva entrada para la MSI del recurso. En este ejemplo, la máquina virtual "SimpleWinVM" del grupo de recursos Demo tiene el acceso **Colaborador** para la cuenta de almacenamiento.

   ![Captura de pantalla Control de acceso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Solución de problemas

Si la identidad de servicio administrada para el recurso no aparece en la lista de identidades disponibles, compruebe que la identidad se haya habilitado correctamente. En nuestro caso, podemos volver a la máquina virtual de Azure y comprobar lo siguiente:

- Mirar la página **Configuración** y asegurarse de que el valor de **MSI habilitado** es **Sí**.
- Mirar la página **Extensiones** y asegurarse de que la extensión MSI se ha implementado correctamente.

Si alguna no es correcta, puede que tenga que volver a implementar MSI en el recurso de nuevo o solucionar el error de implementación.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).
- Para habilitar MSI en una máquina virtual de Azure, consulte [Configuración de Managed Service Identity (MSI) en una máquina virtual de Azure con Azure Portal](msi-qs-configure-portal-windows-vm.md).


