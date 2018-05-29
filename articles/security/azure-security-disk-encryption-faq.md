---
title: Preguntas frecuentes sobre Azure Disk Encryption | Microsoft Docs
description: En este artículo se ofrecen respuestas a las preguntas más frecuentes sobre Microsoft Azure Disk Encryption para máquinas virtuales IaaS con Windows y Linux.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: barclayn
ms.openlocfilehash: 47ccf91a64653c928cc4da01bc98535c97440d37
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187698"
---
# <a name="azure-disk-encryption-faq"></a>Preguntas más frecuentes de Azure Disk Encryption

En este artículo se ofrecen respuestas a las preguntas más frecuentes (P+F) sobre Azure Disk Encryption para máquinas virtuales IaaS con Windows y Linux. Para más información sobre este servicio, consulte [Azure Disk Encryption para máquinas virtuales IaaS de Windows y Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>¿Dónde está Azure Disk Encryption en la disponibilidad general (GA)?

Azure Disk Encryption para máquinas virtuales IaaS Windows y Linux tiene disponibilidad general en todas las regiones públicas de Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>¿Qué experiencias del usuario están disponibles con Azure Disk Encryption?

La disponibilidad general de Azure Disk Encryption admite plantillas de Azure Resource Manager, Azure PowerShell y CLI de Azure. Esto ofrece mucha flexibilidad. Dispone de tres opciones distintas para habilitar el cifrado de disco para las máquinas virtuales IaaS. Para más información sobre la experiencia del usuario e instrucciones paso a paso disponibles en Azure Disk Encryption, consulte [Escenarios de implementación del cifrado de disco y experiencias de usuario de Azure Disk Encryption](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="how-much-does-azure-disk-encryption-cost"></a>¿Cuánto cuesta Azure Disk Encryption?

No hay ningún cargo por cifrar discos de máquina virtual con Azure Disk Encryption, pero hay cargos asociados al uso de Azure Key Vault. Para más información sobre los costos de Azure Key Vault, consulte la página [precios de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>¿Qué niveles de máquina virtual admite Azure Disk Encryption?

Azure Disk Encryption está disponible en las máquinas virtuales de nivel estándar, incluidas las de IaaS de las series [A, D, DS, G, GS y F](https://azure.microsoft.com/pricing/details/virtual-machines/). También está disponible para las máquinas virtuales con almacenamiento premium. No está disponible en las máquinas virtuales de nivel básico.

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>¿Qué distribuciones de Linux son compatibles con Azure Disk Encryption?

Azure Disk Encryption se admite en las siguientes distribuciones y versiones del servidor de Linux:

| Distribución de Linux | Versión | Tipo de volumen admitido para el cifrado|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Sistema operativo y disco de datos |
| Ubuntu | 14.04.5-DAILY-LTS | Sistema operativo y disco de datos |
| RHEL | 7.4 | Disco de datos* |
| RHEL | 7.3 | Disco de datos* |
| RHEL | 7,2 | Disco de datos* |
| RHEL | 6,8 | Disco de datos* |
| RHEL | 6.7 | Disco de datos* |
| CentOS | 7.3 | Sistema operativo y disco de datos |
| CentOS | 7.2n | Sistema operativo y disco de datos |
| CentOS | 6,8 | Sistema operativo y disco de datos |
| CentOS | 7.1 | Disco de datos |
| CentOS | 7.0 | Disco de datos |
| CentOS | 6.7 | Disco de datos |
| CentOS | 6.6 | Disco de datos |
| CentOS | 6.5 | Disco de datos |
| openSUSE | 13.2 | Disco de datos |
| SLES | 12 SP1 | Disco de datos |
| SLES | Priority:12-SP1 | Disco de datos |
| SLES | HPC 12 | Disco de datos |
| SLES | Priority:11-SP4 | Disco de datos |
| SLES | 11 SP4 | Disco de datos |

*__ADE es admite RHEL en los discos de datos. La implementación actual de ADE funciona con discos del sistema operativo, pero no se admite por ahora de manera conjunta. Tanto Microsoft como Red Hat están trabajando en una solución conjunta compatible. Mientras tanto, puede consultar las notas del producto de ADE sobre el cifrado de discos del sistema operativo Linux [aquí](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>¿Cómo puedo empezar a usar Azure Disk Encryption?

Para comenzar, lea las notas del producto [Azure Disk Encryption para máquinas virtuales IaaS de Windows y Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>¿Puedo cifrar los volúmenes de datos y arranque con Azure Disk Encryption?

Sí, puede cifrar los volúmenes de datos y de arranque para las máquinas virtuales IaaS de Windows y Linux. En las máquinas virtuales de Windows, no se pueden cifrar los datos sin cifrar primero el volumen del sistema operativo. En las de Linux, se puede cifrar el volumen de datos sin tener que cifrar primero el volumen del sistema operativo. Una vez que haya cifrado el volumen del sistema operativo para Linux, no se puede deshabilitar el cifrado en un volumen del sistema operativo para las máquinas virtuales IaaS de Linux.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>¿Permite Azure Disk Encryption habilitar la funcionalidad "traiga su propia clave" (BYOK)?

Sí, puede proporcionar sus propias claves de cifrado de claves. Dichas claves están protegidas en Azure Key Vault, que es el almacén de claves de Azure Disk Encryption. Para más información sobre los escenarios de compatibilidad con claves de cifrado de claves, consulte [Escenarios de implementación del cifrado de disco y experiencias de usuario de Azure Disk Encryption](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>¿Puedo usar una clave de cifrado de claves creada en Azure?

Sí, puede usar Azure Key Vault para generar la clave de cifrado de claves para usar en Azure Disk Encryption. Dichas claves están protegidas en Azure Key Vault, que es el almacén de claves de Azure Disk Encryption. Para obtener más información sobre los escenarios de compatibilidad con claves de cifrado de claves, consulte [Escenarios de implementación del cifrado de disco y experiencias de usuario de Azure Disk Encryption](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>¿Puedo usar el servicio de administración de claves local o HSM para proteger las claves de cifrado?

No puede utilizar el servicio de administración de claves local ni HSM para proteger las claves de cifrado con Azure Disk Encryption. Para proteger las claves de cifrado, solo se puede utilizar el servicio Azure Key Vault. Para obtener más información sobre los escenarios de compatibilidad con claves de cifrado de claves, consulte [Escenarios de implementación del cifrado de disco y experiencias de usuario de Azure Disk Encryption](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>¿Cuáles son los requisitos previos para configurar Azure Disk Encryption?

Hay un script de PowerShell como requisito previo. Con este script, puede crear una aplicación de Azure Active Directory, crear un nuevo almacén de claves o configurar el existente para el acceso al cifrado de disco y habilitar el cifrado y proteger los secretos y la clave. Para obtener más información sobre los escenarios de compatibilidad con claves de cifrado de claves, consulte los [requisitos previos y los escenarios de implementación del cifrado de disco y experiencias de usuario de Azure Disk Encryption](azure-security-disk-encryption.md#prerequisites).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>¿Dónde puedo obtener más información sobre cómo usar PowerShell para configurar Azure Disk Encryption?

Tenemos algunos artículos excelentes sobre cómo realizar tareas básicas de Azure Disk Encryption, así como escenarios más avanzados. Para las tareas básicas, consulte [Exploración de Azure Disk Encryption con Azure PowerShell, parte 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Para escenarios más avanzados, consulte [Azure Disk Encryption con Azure PowerShell, parte 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>¿Qué versión de Azure PowerShell es compatible con Azure Disk Encryption?

Utilice la versión más reciente del SDK de Azure PowerShell para configurar Azure Disk Encryption. Descargue la versión más reciente de [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). La versión 1.1.0 del SDK de Azure *no* admite Azure Disk Encryption.

> [!NOTE]
> La extensión de la versión preliminar de Azure Disk Encryption para Linux está en desuso. Para obtener más información, consulte [Desuso de la extensión de vista previa de Azure Disk Encryption para máquinas virtuales de IaaS de Linux](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>¿Puedo aplicar Azure Disk Encryption en mi imagen personalizada de Linux?

No puede aplicar Azure Disk Encryption a una imagen personalizada de Linux. Solo se admiten las imágenes de Linux de la galería para las distribuciones admitidas mencionadas anteriormente. Actualmente no se admiten imágenes personalizadas de Linux.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>¿Puedo aplicar actualizaciones a una máquina virtual Red Hat de Linux utilizando la actualización de Yum?

Sí, puede realizar una actualización o revisión de una máquina virtual de Red Hat de Linux. Para más información, consulte [Aplicación de actualizaciones a una máquina virtual de Red Hat IaaS de Azure cifrada mediante la actualización de yum](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>¿Cuál es el flujo de trabajo de Azure Disk Encryption recomendado para Linux?

Para obtener los mejores resultados en Linux, se recomienda el siguiente flujo de trabajo:
* Comenzar en la imagen de la galería en existencias sin modificar correspondiente a la distribución y versión deseadas del sistema operativo.
* Realizar una copia de seguridad de las unidades montadas que se van a cifrar.  Esto permite la recuperación en caso de error, por ejemplo, si la máquina virtual se reinicia antes de que finalice el cifrado.
* Cifrar (puede tardar varias horas o incluso días según las características de la máquina virtual y el tamaño de los discos de datos adjuntos).
* Personalizar y agregar software a la imagen según sea necesario.

Si este flujo de trabajo no es posible, el uso del [Cifrado del servicio de almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) en el nivel de la cuenta de almacenamiento de la plataforma puede ser una alternativa al cifrado del disco completo mediante dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>¿Qué es el disco "volumen Bek" o "/mnt/azure_bek_disk"?

"Volumen Bek" para Windows o "/mnt/azure_bek_disk" para Linux es un volumen de datos locales que almacena de forma segura las claves de cifrado para máquinas virtuales cifradas de IaaS de Azure.
> [!NOTE]
> No elimine ni modifique ningún contenido de este disco. No desmonte el disco ya que la presencia de la clave de cifrado es necesaria para las operaciones de cifrado en la máquina virtual IaaS.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>¿Dónde puedo formular preguntas o enviar comentarios?

Puede realizar preguntas o publicar comentarios en el [Foro de Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Pasos siguientes
En este documento, aprendió más acerca de las preguntas más frecuentes sobre Azure Disk Encryption. Para más información acerca de este servicio y su funcionalidad, consulte los artículos siguientes:

- [Aplicación de cifrado de discos en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Cifrado de una máquina virtual de Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Cifrado de datos en reposo de Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
