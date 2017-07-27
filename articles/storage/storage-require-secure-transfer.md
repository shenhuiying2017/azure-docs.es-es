---
title: Requerir transferencia segura en Azure Storage | Microsoft Docs
description: "Obtenga información acerca de la característica \"Requerir transferencia segura\" de Azure Storage y cómo habilitarla."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 516618653064fd4e334197bba767a013a805260a
ms.contentlocale: es-es
ms.lasthandoff: 06/21/2017

---
# <a name="require-secure-transfer"></a>Requerir transferencia segura

La opción "Se requiere transferencia segura" mejora la seguridad de su cuenta de almacenamiento, ya que solo permite enviar solicitudes a la cuenta de almacenamiento desde conexiones seguras. Por ejemplo, al llamar a las API de REST para acceder a una cuenta de almacenamiento, es preciso conectarse mediante HTTPS. Si "Se requiere transferencia segura" está habilitada, se rechazan todas las solicitudes en que se use HTTP.

Si se usa el servicio Azure Files, se produce un error en todas las conexiones sin cifrado cuando la opción "Se requiere transferencia segura" está habilitada. Aquí se incluyen los escenarios que usan SMB 2.1 y SMB 3.0 sin cifrado, y algunas versiones del cliente SMB de Linux. 

De forma predeterminada, la opción "Se requiere transferencia segura" está deshabilitada.

> [!NOTE]
> Dado que Azure Storage no admite HTTPS para los nombres de dominio personalizados, esta opción no se aplica cuando se utiliza un nombre de dominio personalizado.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Habilitación de "Se requiere transferencia segura" en Azure Portal

La opción de "Se requiere transferencia segura" se puede habilitar cuando se crea una cuenta de almacenamiento en [Azure Portal](https://portal.azure.com)y para las cuentas de almacenamiento existentes.

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>Solicitud de transferencia segura al crear una cuenta de almacenamiento

1. Abra la hoja **Crear cuenta de almacenamiento** en Azure Portal.
1. En **Se requiere transferencia segura**, seleccione **Habilitado**.

  ![captura de pantalla](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Solicitud de transferencia segura en una cuenta de almacenamiento existente

1. Seleccione una cuenta de almacenamiento existente en Azure Portal.
1. Seleccione **Configuración** en **CONFIGURACIÓN** en la hoja del menú de la cuenta de almacenamiento.
1. En **Se requiere transferencia segura**, seleccione **Habilitado**.

  ![captura de pantalla](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="next-steps"></a>Pasos siguientes
Azure Storage proporciona un completo conjunto de funcionalidades de seguridad que, conjuntamente, permiten a los desarrolladores compilar aplicaciones seguras. Para obtener más detalles, visite la [Guía de seguridad para almacenamiento](storage-security-guide.md).

