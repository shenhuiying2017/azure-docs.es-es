---
title: "Asignación de un acceso de MSI a un recurso de Azure mediante Azure Portal"
description: Instrucciones paso a paso para asignar una identidad de servicio administrada (MSI) en un recurso y acceso a otro recurso, mediante Azure Portal.
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9dd02c8d7580cd9233e192f807686b7857ccf696
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---

# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-the-azure-portal"></a>Asignación de un acceso de identidad de servicio administrada (MSI) a un recurso mediante Azure Portal

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Una vez haya configurado un recurso de Azure con una identidad de servicio administrada, puede dar el acceso de MSI a otro recurso, al igual que cualquier entidad de seguridad. En este ejemplo se muestra cómo otorgar acceso de MSI a una máquina virtual de Azure para una cuenta de almacenamiento de Azure, mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Uso de RBAC para asignar el acceso de MSI a otro recurso

Después de habilitar MSI en un recurso de Azure, [como una máquina virtual de Azure](msi-qs-configure-portal-windows-vm.md):

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure en la que ha configurado la entidad de servicio administrada.

2. Vaya al recurso para el que desea modificar el control de acceso. En este ejemplo, vamos a dar a una máquina virtual de Azure acceso a una cuenta de almacenamiento, de modo que navegamos a la cuenta de almacenamiento.

3. Haga clic en la página "Control de acceso (IAM)" del recurso y haga clic en "+ Agregar". A continuación, especifique el rol, asigne acceso a una "máquina virtual" y especifique la suscripción y el grupo de recursos correspondientes donde reside el recurso. En el área de criterios de búsqueda, debería ver que se muestra el recurso. Seleccione el recurso y haga clic en "Guardar": 

   ![Captura de pantalla Control de acceso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Vuelve a la página principal "Control de acceso (IAM)", donde se ve una nueva entrada para la MSI del recurso. En este ejemplo, a la máquina virtual "SimpleWinVM" del grupo de recursos de demostración se le ha otorgado el acceso "Colaborador" para la cuenta de almacenamiento:

   ![Captura de pantalla Control de acceso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Solución de problemas

Si la identidad de servicio administrada para el recurso no aparece en la lista de identidades disponibles, compruebe que la identidad se haya habilitado correctamente. En nuestro caso, podemos volver a la máquina virtual de Azure y:

- Buscar en la página "Configuración" y asegurarnos de que MSI habilitado = "Sí".
- Buscar en la página "Extensiones" y asegurarnos de que la extensión MSI se ha implementado correctamente.

Si alguna de las opciones no es correcta, puede que tenga que volver a implementar la identidad de servicio administrada en el recurso nuevo o solucionar el error de implementación.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).
- Para habilitar MSI en una máquina virtual de Azure, consulte [Configuración de Managed Service Identity (MSI) en una máquina virtual de Azure con Azure Portal](msi-qs-configure-portal-windows-vm.md).



