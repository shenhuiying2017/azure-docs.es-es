---
title: Cifrado del servicio Azure Storage para datos en reposo | Microsoft Docs
description: Use la característica Cifrado del servicio Azure Storage para cifrar Azure Blob Storage en el servicio cuando almacene los datos y descifrarlos al recuperarlos.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/14/2018
ms.author: lakasa
ms.openlocfilehash: d9df2218acc218a796e502fa4e3b94573af86ca8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Cifrado del servicio Azure Storage para datos en reposo (versión preliminar)

El Cifrado del servicio Azure Storage para datos en reposo le ayuda a proteger sus datos con el fin de cumplir con los compromisos de cumplimiento y seguridad de su organización. Con esta característica, Azure Storage cifra automáticamente los datos antes de almacenarlos y los descifra antes de recuperarlos. El control del cifrado, el cifrado en reposo, el descifrado y la administración de claves en Cifrado del servicio Storage es transparente para los usuarios. Todos los datos escritos en Azure Storage se cifran mediante [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uno de los cifrados de bloques más fuertes disponibles.

El Cifrado del servicio Storage está habilitado para todas las cuentas de almacenamiento nuevas y existentes y no se puede deshabilitar. Dado que los datos están protegidos de forma predeterminada, no es necesario modificar el código o las aplicaciones para aprovechar el Cifrado del servicio Storage.

La característica cifra los datos automáticamente en:

- Ambos niveles de rendimiento (ediciones Estándar y Premium).
- Ambos modelos de implementación (Azure Resource Manager y clásica).
- Todos los servicios de Azure Storage (Blob Storage, Queue Storage, Table Storage y Azure Files). 

El Cifrado del servicio Storage no afecta al rendimiento de Azure Storage.

Puede usar las claves de cifrado administradas por Microsoft con el Cifrado del servicio Storage, o puede usar sus propias claves de cifrado. Para más información sobre el uso de sus propias claves, consulte [Cifrado del servicio Storage mediante claves administradas por el cliente en Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Visualización de la configuración de cifrado en Azure Portal

Para ver la configuración de Cifrado del servicio Storage, inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione una cuenta de almacenamiento. En el panel **Configuración**, seleccione la opción **Cifrado**.

![Captura de pantalla del portal con la configuración de Cifrado](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Preguntas frecuentes sobre el Cifrado del servicio Storage

**P: Tengo una cuenta de almacenamiento clásica. ¿Puedo habilitar en ella el Cifrado del servicio Storage?**

R: El Cifrado del servicio Storage está habilitado de forma predeterminada para todas las cuentas de almacenamiento (clásicas y de Resource Manager).

**P: ¿Cómo puedo cifrar datos en mi cuenta de almacenamiento clásico?**

R: Gracias a que el cifrado está habilitado de forma predeterminada, Azure Storage cifra automáticamente los nuevos datos. 

**P: Tengo una cuenta de almacenamiento de Resource Manager. ¿Puedo habilitar en ella el Cifrado del servicio Storage?**

R: El Cifrado del servicio Storage está habilitado de forma predeterminada en todas las cuentas de almacenamiento existentes de Resource Manager. Esto se aplica a Blob Storage, Table Storage, Queue Storage y Azure Files. 

**P: ¿Cómo cifro los datos de una cuenta de almacenamiento de Resource Manager?**

R: El Cifrado del servicio Storage está habilitado de forma predeterminada para todas las cuentas de almacenamiento (clásicas y de Resource Manager). Sin embargo, no se cifran los datos existentes. Para cifrar los datos existentes, puede copiarlos con otro nombre o en otro contenedor y, luego, quitar las versiones sin cifrar. 

**P: ¿Puedo crear cuentas de almacenamiento con Cifrado del servicio Storage habilitado mediante Azure PowerShell y la CLI de Azure?**

R: El Cifrado del servicio Storage se habilita de forma predeterminada en el momento de crear cualquier cuenta de almacenamiento (clásica o de Resource Manager). Puede comprobar las propiedades de la cuenta con Azure PowerShell y la CLI de Azure.

**P: ¿Cuánto aumenta el costo de Azure Storage si se habilita el Cifrado del servicio Storage?**

R: No hay costo adicional.

**P: ¿Puedo usar mis propias claves de cifrado?**

R: Sí, puede usar sus propias claves de cifrado. Para más información, consulte [Cifrado del servicio Storage mediante claves administradas por el cliente en Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**P: ¿Puedo revocar el acceso a las claves de cifrado?**

R: Sí, si [utiliza sus propias claves de cifrado](storage-service-encryption-customer-managed-keys.md) en Azure Key Vault.

**P: ¿El Cifrado del servicio Storage se habilita de forma predeterminada al crear una cuenta de almacenamiento?**

R: Sí, el Cifrado del servicio Storage (mediante claves administradas de Microsoft) está habilitado de forma predeterminada para todas las cuentas de almacenamiento, tanto clásicas como de Azure Resource Manager. También está habilitado para todos los servicios: Blob Storage, Table Storage, Queue Storage y Azure Files.

**P: ¿Qué diferencia tiene con Azure Disk Encryption?**

R: Azure Disk Encryption se usa para cifrar discos de datos y del sistema operativo en máquinas virtuales de IaaS. Para más información, consulte la [Guía de seguridad de Azure Storage](../storage-security-guide.md).

**P: ¿Qué ocurre si habilito Azure Disk Encryption en mis discos de datos?**

R: Funcionará sin problemas. Ambos métodos cifrarán los datos.

**P: Mi cuenta de almacenamiento está configurada para la replicación con redundancia geográfica. Con el Cifrado del servicio Storage, ¿también se cifra mi copia redundante?**

R: Sí, se cifran todas las copias de la cuenta de almacenamiento. Se admiten todas las opciones de redundancia: almacenamiento con redundancia local, almacenamiento con redundancia geográfica y almacenamiento con redundancia geográfica con acceso de lectura.

**P: ¿Puedo deshabilitar el cifrado en mi cuenta de almacenamiento?**

R: El cifrado está habilitado de forma predeterminada y no hay ninguna forma de deshabilitarlo en la cuenta de almacenamiento. 

**P: ¿El Cifrado del servicio Storage se permite únicamente en determinadas regiones?**

R: El Cifrado del servicio Storage está disponible en todas las regiones para todos los servicios. 

**P: ¿Con quién me puedo comunicar si tengo problemas o quiero proporcionar algunos comentarios?**

R: Si tiene algún problema o quiere realizar algún comentario en relación con el Cifrado del servicio Storage, envíe un correo electrónico a [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes
Azure Storage proporciona un completo conjunto de funcionalidades de seguridad que, juntas, ayudan a los desarrolladores a crear aplicaciones seguras. Para más información, consulte la [Guía de seguridad de Azure Storage](../storage-security-guide.md).
