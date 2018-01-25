---
title: Preguntas frecuentes sobre Azure Disk Encryption | Microsoft Docs
description: "En este artículo se ofrecen respuestas a las preguntas más frecuentes sobre Microsoft Azure Disk Encryption para máquinas virtuales IaaS con Windows y Linux."
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
ms.date: 08/11/2017
ms.author: devtiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: 1144533cd64e80ed2b15c70732c94e332ea06c3d
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-disk-encryption-faq"></a>Preguntas más frecuentes de Azure Disk Encryption

En este artículo se ofrecen respuestas a las preguntas más frecuentes (P+F) sobre Azure Disk Encryption para máquinas virtuales IaaS con Windows y Linux. Para más información sobre este servicio, consulte [Azure Disk Encryption para máquinas virtuales IaaS de Windows y Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Preguntas generales
**P:**  ¿Dónde está Azure Disk Encryption en la disponibilidad general (GA)?

**R:** Azure Disk Encryption para máquinas virtuales IaaS Windows y Linux tiene disponibilidad general en todas las regiones públicas de Azure.

**P:** ¿Qué experiencias del usuario están disponibles con Azure Disk Encryption?

**R:** La disponibilidad general de Azure Disk Encryption admite plantillas de Azure Resource Manager, Azure PowerShell y CLI de Azure. Esto ofrece mucha flexibilidad. Dispone de tres opciones distintas para habilitar el cifrado de disco para las máquinas virtuales IaaS. Para más información sobre la experiencia del usuario e instrucciones paso a paso disponibles en Azure Disk Encryption, consulte las experiencias y los escenarios de implementación con esta funcionalidad.

**P:** ¿Cuánto cuesta Azure Disk Encryption?

**R:** El cifrado de discos de máquinas virtuales con Azure Disk Encryption no supone un cargo adicional.

**P:** ¿Qué niveles de máquina virtual admite Azure Disk Encryption?

**R:** Azure Disk Encryption está disponible en las máquinas virtuales de nivel estándar incluidas las de IaaS de las series [A, D, DS, G, GS y F](https://azure.microsoft.com/pricing/details/virtual-machines/). También está disponible para las máquinas virtuales con almacenamiento premium. No está disponible en las máquinas virtuales de nivel básico.

**P:** ¿Qué distribuciones de Linux son compatibles con Azure Disk Encryption?

**R:** Azure Disk Encryption se admite en las siguientes distribuciones y versiones de servidores Linux:

| Distribución de Linux | Versión | Tipo de volumen admitido para el cifrado|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Sistema operativo y disco de datos |
| Ubuntu | 14.04.5-DAILY-LTS | Sistema operativo y disco de datos |
| RHEL | 7.3 | Sistema operativo y disco de datos |
| RHEL | 7,2 | Sistema operativo y disco de datos |
| RHEL | 6,8 | Sistema operativo y disco de datos |
| RHEL | 6.7 | Disco de datos |
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

**P:** ¿Cómo puedo empezar a usar Azure Disk Encryption?

**R:** Para comenzar, lea las notas del producto [Azure Disk Encryption para máquinas virtuales IaaS de Windows y Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

**P:** ¿Puedo cifrar los volúmenes de datos y arranque con Azure Disk Encryption?

**R:** Sí, puede cifrar los volúmenes de datos y de arranque para las máquinas virtuales IaaS de Windows y Linux. En las máquinas virtuales de Windows, no se pueden cifrar los datos sin cifrar primero el volumen del sistema operativo. En las de Linux, se puede cifrar el volumen de datos sin tener que cifrar primero el volumen del sistema operativo. Una vez que haya cifrado el volumen del sistema operativo para Linux, no se puede deshabilitar el cifrado en un volumen del sistema operativo para las máquinas virtuales IaaS de Linux.

**P:** ¿Permite Azure Disk Encryption habilitar la funcionalidad "traiga su propia clave" (BYOK)?

**R:** Sí, puede proporcionar sus propias claves de cifrado de claves. Dichas claves están protegidas en Azure Key Vault, que es el almacén de claves de Azure Disk Encryption. Para más información sobre los escenarios de compatibilidad con claves de cifrado de claves, consulte los escenarios de implementación y las experiencias de Azure Disk Encryption.

**P:** ¿Puedo usar una clave de cifrado de claves creada en Azure?

**R:** Sí, puede usar Azure Key Vault para generar la clave de cifrado de claves para usar en Azure Disk Encryption. Dichas claves están protegidas en Azure Key Vault, que es el almacén de claves de Azure Disk Encryption. Para más información sobre los escenarios de compatibilidad con claves de cifrado de claves, consulte los escenarios de implementación y las experiencias de Azure Disk Encryption.

**P:** ¿Puedo usar el servicio de administración de claves local o HSM para proteger las claves de cifrado?

**R:** No puede utilizar el servicio de administración de claves local ni HSM para proteger las claves de cifrado con Azure Disk Encryption. Para proteger las claves de cifrado, solo se puede utilizar el servicio Azure Key Vault. Para más información sobre los escenarios de compatibilidad con claves de cifrado de claves, consulte los escenarios de implementación y las experiencias de Azure Disk Encryption.

**P:** ¿Cuáles son los requisitos previos para configurar Azure Disk Encryption?

**R:** hay un script de PowerShell de requisitos previos. Con este script, puede crear una aplicación de Azure Active Directory, crear un nuevo almacén de claves o configurar el existente para el acceso al cifrado de disco y habilitar el cifrado y proteger los secretos y la clave. Para más información sobre los escenarios de compatibilidad con claves de cifrado de claves, vea las experiencias, los escenarios de implementación y los requisitos previos de Azure Disk Encryption.

**P:** ¿Dónde puedo obtener más información sobre cómo usar PowerShell para configurar Azure Disk Encryption?

**R:** Tenemos algunos artículos excelentes sobre cómo realizar tareas básicas de Azure Disk Encryption, así como escenarios más avanzados. Para las tareas básicas, consulte [Exploración de Azure Disk Encryption con Azure PowerShell, parte 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Para escenarios más avanzados, consulte [Azure Disk Encryption con Azure PowerShell, parte 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

**P:** ¿Qué versión de Azure PowerShell es compatible con Azure Disk Encryption?

**R:** Utilice la versión más reciente del SDK de Azure PowerShell para configurar Azure Disk Encryption. Descargue la versión más reciente de [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). La versión 1.1.0 del SDK de Azure *no* admite Azure Disk Encryption.

> [!NOTE]
> La extensión de la versión preliminar de Azure Disk Encryption para Linux está en desuso. Para obtener más información, consulte [Desuso de la extensión de vista previa de Azure Disk Encryption para máquinas virtuales de IaaS de Linux](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

**P:** ¿Puedo aplicar Azure Disk Encryption en mi imagen personalizada de Linux?

**R:** No puede aplicar Azure Disk Encryption a una imagen personalizada de Linux. Solo se admiten las imágenes de Linux de la galería para las distribuciones admitidas mencionadas anteriormente. Actualmente no se admiten imágenes personalizadas de Linux.

**P:** ¿Puedo aplicar actualizaciones a una máquina virtual Red Hat de Linux utilizando la actualización de Yum?

**R:** Sí, puede realizar una actualización o revisión de una máquina virtual de Red Hat de Linux. Para más información, consulte [Aplicación de actualizaciones a una máquina virtual de Red Hat IaaS de Azure cifrada mediante la actualización de yum](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

**P:** ¿Cuál es el flujo de trabajo de Azure Disk Encryption recomendado para Linux?

**R:** Para obtener los mejores resultados de Linux, se recomienda el siguiente flujo de trabajo:
* Comenzar en la imagen de la galería en existencias sin modificar correspondiente a la distribución y versión deseadas del sistema operativo.
* Realizar una copia de seguridad de las unidades montadas que se van a cifrar.  Esto permite la recuperación en caso de error, por ejemplo, si la máquina virtual se reinicia antes de que finalice el cifrado.
* Cifrar (puede tardar varias horas o incluso días según las características de la máquina virtual y el tamaño de los discos de datos adjuntos).
* Personalizar y agregar software a la imagen según sea necesario.

Si este flujo de trabajo no es posible, el uso del [Cifrado del servicio de almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) en el nivel de la cuenta de almacenamiento de la plataforma puede ser una alternativa al cifrado del disco completo mediante dm-crypt.

**P:**  ¿Qué es el disco "Bek volumen" o "/mnt/azure_bek_disk"?

**R:** "Bek volumen" para Windows o "/mnt/azure_bek_disk" para Linux es un volumen de datos locales que almacena de forma segura las claves de cifrado para máquinas virtuales cifradas de IaaS de Azure.
> [!NOTE]
> No elimine ni modifique ningún contenido de este disco. No desmonte el disco ya que la presencia de la clave de cifrado es necesaria para las operaciones de cifrado en la máquina virtual IaaS.

**P:** ¿Dónde puedo formular preguntas o enviar comentarios?

**R:** Puede realizar preguntas o publicar comentarios en el [Foro de Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>pasos siguientes
En este documento, aprendió más acerca de las preguntas más frecuentes sobre Azure Disk Encryption. Para más información acerca de este servicio y su funcionalidad, consulte los artículos siguientes:

- [Aplicación de cifrado de discos en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Cifrado de una máquina virtual de Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Cifrado de datos en reposo de Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
