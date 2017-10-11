---
title: Azure Storage Service Encryption mediante claves administradas por el cliente en Azure Key Vault | Microsoft Docs
description: "Use la característica Azure Storage Service Encryption para cifrar Azure Blob Storage en el servicio cuando almacene los datos y descifrarlo cuando recupere dichos datos mediante claves administradas por el cliente."
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Storage Service Encryption mediante claves administradas por el cliente en Azure Key Vault

Microsoft Azure está comprometido a ayudarle a asegurar y proteger sus datos con el fin de satisfacer los compromisos de cumplimiento y seguridad de su organización.  Una manera de poder proteger los datos en reposo es usar Storage Service Encryption (SSE), que automáticamente los cifra al escribirlos en el almacenamiento y los descifra al recuperarlos. El cifrado y descifrado son completamente transparentes y automáticos y usan [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uno de los cifrados de bloques más seguros disponibles.

Puede usar claves de cifrado administradas por Microsoft con SSE o puede utilizar sus propias claves de cifrado. En este artículo se trata sobre lo último mencionado. Para más información sobre el uso de claves administradas por Microsoft, o sobre SSE en general, consulte [Storage Service Encryption para datos en reposo](../storage-service-encryption.md).

Para proporcionar la capacidad de utilizar sus propias claves de cifrado, SSE para el almacenamiento de blobs se integra con Azure Key Vault (AKV). Puede crear sus propias claves de cifrado y almacenarlas en AKV; también puede usar las API de AKV para generar las claves de cifrado. AKV no solo le permite administrar y controlar sus claves, sino que también le permite auditar el uso de las mismas. 

¿Por qué se desearía crear sus propias claves? Proporciona mayor flexibilidad, lo que le permite crear, girar, deshabilitar y definir controles de acceso. También permite auditar las claves de cifrado utilizadas para proteger los datos.

## <a name="sse-with-customer-managed-keys-preview"></a>SSE con la versión preliminar de las claves administradas de por el cliente

Esta funcionalidad actualmente está en su versión preliminar. Para usar esta característica, necesita crear una nueva cuenta de almacenamiento. Puede crear un almacén de claves y una clave nuevos, o bien puede usar un almacén de claves y una clave existentes. La cuenta de almacenamiento y el almacén de claves deben estar en la misma región, pero pueden estar en distintas suscripciones.

Para participar en la versión preliminar, póngase en contacto con [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com). Se proporcionará un vínculo especial para participar en la versión preliminar.

Para más información, consulte la sección de [preguntas más frecuentes](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest).

> [!IMPORTANT]
> Debe suscribirse a la versión preliminar antes de seguir los pasos descritos en este artículo. Sin acceso de versión preliminar, no podrá habilitar esta característica en el portal.

## <a name="getting-started"></a>Introducción
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Paso 1: [Crear una cuenta de almacenamiento nueva](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>Paso 2: Habilitar el cifrado
Puede habilitar SSE para la cuenta de almacenamiento mediante [Azure Portal](https://portal.azure.com). En la hoja Configuración correspondiente a la cuenta de almacenamiento, busque la sección Blob Service como se muestra en la ilustración siguiente y haga clic en Cifrado.

![Captura de pantalla del portal que muestra la opción de cifrado](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*Habilitar SSE para Blob service*

Si desea habilitar o deshabilitar mediante programación el cifrado del servicio de almacenamiento en una cuenta de almacenamiento, puede usar la [API de REST del proveedor de recursos de Azure Storage](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN), la [Biblioteca del cliente proveedor de recursos de almacenamiento para .NET](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN), [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0) o la [CLI de Azure](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli).

En esta pantalla, si no ve la casilla "Use su propia clave", significa que no tiene aprobación para la versión preliminar. Envíe un correo electrónico a [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) y solicite la aprobación.

![Captura de pantalla del portal que muestra la versión preliminar del cifrado](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

De forma predeterminada, SSE usa claves administradas de Microsoft. Para usar sus propias claves, active la casilla. A continuación, puede especificar el URI de la clave o seleccionar un Key Vault mediante el selector.

## <a name="step-3-select-your-key"></a>Paso 3: Seleccionar su clave

![Captura de pantalla del portal que muestra la opción de cifrado con su propia clave](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![Captura de pantalla del portal que muestra el cifrado con la opción de especificación del UIR de la clave](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Si la cuenta de almacenamiento no tiene acceso a Key Vault, puede ejecutar el siguiente comando con Azure Powershell para conceder acceso a las cuentas de almacenamiento para el almacén de claves necesario.

![Captura de pantalla de portal que muestra el acceso denegado para el almacén de claves](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

También puede conceder acceso a través de Azure Portal yendo a Azure Key Vault en Azure Portal y concediendo acceso a la cuenta de almacenamiento.

## <a name="step-4-copy-data-to-storage-account"></a>Paso 4: Copiar datos en una cuenta de almacenamiento
Si desea transferir datos a la nueva cuenta de almacenamiento de manera que se cifren, consulte el apartado [Paso 3 de la sección Introducción de Storage Service Encryption para datos en reposo](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account).

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>Paso 5: Consultar el estado de los datos cifrados
Para consultar el estado de los datos cifrados, consulte el apartado [Paso 4 de la sección Introducción de Storage Service Encryption para datos en reposo](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data).

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Preguntas más frecuentes acerca de Cifrado del servicio de Almacenamiento para datos en reposo
**P: Uso Premium Storage. ¿Puedo usar SSE con claves administradas por el cliente?**

R: Sí, SSE con claves administradas por Microsoft y claves administradas el cliente es compatible tanto con almacenamiento estándar como con almacenamiento premium. 

**P: ¿Puedo crear cuentas de almacenamiento con SSE con claves administradas por el cliente habilitado a través de Azure PowerShell y la CLI de Azure?**

R: Sí.

**P: ¿Cuánto aumenta el costo de Azure Storage si se habilita SSE con claves administradas por el cliente?**

R: Hay un costo asociado para usar Azure Key Vault. Para más información, visite [Key Vault Precios](https://azure.microsoft.com/en-us/pricing/details/key-vault/). No hay ningún coste adicional por usar SSE.

**P: ¿Puedo revocar el acceso a las claves de cifrado?**

R: Sí, puede revocar el acceso en cualquier momento. Hay varias maneras para revocar el acceso a las claves. Para más información, consulte [PowerShell de Azure Key Vault](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) y la [CLI de Azure Key Vault](https://docs.microsoft.com/en-us/cli/azure/keyvault). La revocación del acceso bloqueará eficazmente el acceso a todos los blobs de la cuenta de almacenamiento, ya que Azure Storage no podrá acceder a la clave de cifrado de la cuenta.

**P: ¿puedo crear una cuenta de almacenamiento y un clave en una región diferente?**

R: No, la cuenta de almacenamiento y el almacén de claves o la clave deben estar en la misma región. 

**P: ¿Puedo habilitar SSE con claves administradas por el cliente durante la creación de la cuenta de almacenamiento?**

R.: No. Cuando habilite SSE al crear la cuenta de almacenamiento, solo se pueden usar claves administradas por Microsoft. Si desea usar claves administradas por el cliente, debe actualizar las propiedades de la cuenta de almacenamiento. Puede usar REST o una de las bibliotecas de cliente de almacenamiento para actualizar mediante programación la cuenta de almacenamiento, o actualice las propiedades de la cuenta de almacenamiento mediante Azure Portal después de crear la cuenta.

**P: ¿Puedo deshabilitar el cifrado mientras utilizo SSE con claves administradas por el cliente?**

R: No, no puede deshabilitar el cifrado mientras utiliza SSE con claves administradas por el cliente. Para deshabilitar el cifrado, debe pasar a utilizar claves administradas por Microsoft. Para ello, pueden usar Azure Portal o PowerShell.

**P: ¿SSE está habilitado de manera predeterminada cuando creo una cuenta de almacenamiento?**

R: SSE no viene habilitado de manera predeterminada; puede usar el Portal de Azure para habilitarlo. También puede habilitar de manera programática esta característica a través de la API de REST del proveedor de recursos de almacenamiento. 

**P: No puedo habilitar el cifrado en mi cuenta de almacenamiento.**

R: ¿Es una cuenta de almacenamiento de Resource Manager? Las cuentas de almacenamiento clásico no son compatibles. SSE con claves administradas por el cliente también se puede habilitar únicamente en las cuentas de almacenamiento de Resource Manager recién creadas.

**P: ¿Solo se permite en determinadas regiones a SSE con claves administradas por el cliente?**

R: SSE está disponible solamente en determinadas regiones para Blob Storage para esta versión preliminar. Envíe un correo electrónico a [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para comprobar la disponibilidad y los detalles de la versión preliminar. 

**P: ¿Cómo me puedo comunicar con alguna persona si tengo problemas o si deseo enviar comentarios?**

R: Envíe un mensaje de correo electrónico a [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) si tiene algún problema relacionado con el cifrado del servicio Storage. 

## <a name="next-steps"></a>Pasos siguientes

*   Para más información sobre el conjunto completo de funcionalidades de seguridad que ayudan a los desarrolladores a crear aplicaciones seguras, consulte la [Guía de seguridad de Azure Storage](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide).
*   Para información general sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)
*   Para una introducción sobre Azure Key Vault, consulte [Introducción al Almacén de claves de Azure](../../key-vault/key-vault-get-started.md).
