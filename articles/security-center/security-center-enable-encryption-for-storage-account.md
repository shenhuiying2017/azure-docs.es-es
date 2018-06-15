---
title: Habilitación del cifrado para la cuenta de almacenamiento en Azure Security Center | Microsoft Docs
description: Este documento muestra cómo implementar las recomendaciones de Azure Security Center de **Habilitar el cifrado para la cuenta de Azure Storage**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
ms.openlocfilehash: 82bb201c0b518d0b45e06a1eb25d54f60cb3e028
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30235026"
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Habilitación del cifrado para la cuenta de Azure Storage en Azure Security Center
Azure Security Center puede recomendar que habilite el cifrado del servicio de Azure Storage para datos en reposo.

El Cifrado de servicio de almacenamiento (SSE) funciona mediante el cifrado de los datos cuando se escriben en Azure Storage y el descifrado de los datos antes de la recuperación.  SSE actualmente solo está disponible para Azure Blob service y puede usarse para blobs en bloques, blobs de página y blobs en anexos.  Para obtener más información, consulte [Cifrado del servicio Azure Storage para datos en reposo (versión preliminar)](../storage/common/storage-service-encryption.md).


> [!Note]
> Después de habilitar el cifrado, se cifran únicamente los datos nuevos. Los blobs existentes en la cuenta de almacenamiento permanecen sin cifrar. Para cifrar los blobs existentes, consulte [Preguntas más frecuentes acerca de cifrado del servicio Storage para datos en reposo](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption).
>
>

El Cifrado del servicio de almacenamiento solo se admite en las cuentas de almacenamiento de Resource Manager. Las cuentas de almacenamiento clásico no se admiten en este momento. Para entender los modelos de implementación clásico y de Resource Manager, consulte [Modelos de implementación de Azure](../azure-classic-rm.md).

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  Este documento no es una guía paso a paso.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Enable encryption for Azure Storage Account** (Habilitar el cifrado para la cuenta de Azure Storage).
   ![Habilitar el cifrado para la cuenta de almacenamiento][1]
2. Se abre la hoja **Habilitar el cifrado de almacenamiento**. En esta hoja se enumera las cuentas de Azure Storage donde el cifrado de almacenamiento está deshabilitado. En este ejemplo, vamos a seleccionar **storageacct1**.
   ![Habilitar el cifrado de almacenamiento][2]
3. Se abre la hoja **Cifrado** para **storageacct1**. Seleccione **Habilitado**.
   ![Hoja de cifrado][3]
4. Seleccione **Guardar**.

Ahora ha habilitado el cifrado de almacenamiento para **storageacct1**.


## <a name="see-also"></a>Otras referencias
En este documento se muestra cómo implementar la recomendación de Azure Security Center "Habilitar el cifrado para la cuenta de Azure Storage". Para obtener más información acerca del cifrado de servicio de Azure Storage, vea lo siguiente:

* [Cifrado del servicio Azure Storage para datos en reposo (versión preliminar)](../storage/common/storage-service-encryption.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
