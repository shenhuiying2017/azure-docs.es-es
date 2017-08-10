---
title: Preguntas frecuentes sobre Azure Disk Encryption | Microsoft Docs
description: "En este artículo se ofrecen respuestas a las preguntas más frecuentes sobre Microsoft Azure Disk Encryption para máquinas virtuales IaaS con Windows y Linux."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 2a81a88750ae40cd75e5d394f97c8310416546e4
ms.contentlocale: es-es
ms.lasthandoff: 07/28/2017

---
# <a name="azure-disk-encryption-frequently-asked-questions-faq"></a>Preguntas frecuentes sobre Azure Disk Encryption (P+F)

En este artículo se responde a las preguntas más frecuentes sobre Azure Disk Encryption para las máquinas virtuales IaaS con Windows o Linux; para más información acerca de este servicio, lea [Azure Disk Encryption para máquinas virtuales IaaS con Windows o Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Preguntas generales
P: ¿En qué región tiene disponibilidad general Azure Disk Encryption?
R: Azure Disk Encryption para máquinas virtuales IaaS Windows y Linux tiene disponibilidad general en todas las regiones públicas de Azure.

P: ¿Qué experiencias del usuario están disponibles con Azure Disk Encryption?
R: La disponibilidad general de Azure Disk Encryption admite plantillas de Azure Resource Manager, Azure PowerShell y CLI de Azure. Esto ofrece mucha flexibilidad, ya que dispone de tres opciones distintas para habilitar el cifrado de disco para las máquinas virtuales IaaS. Hay más detalles sobre la experiencia del usuario e instrucciones paso a paso en las experiencias y los escenarios de implementación de Azure Disk Encryption.

P: ¿Cuánto cuesta Azure Disk Encryption?
R: El cifrado de discos de máquinas virtuales con Azure Disk Encryption no supone un cargo adicional.

P: ¿Qué niveles de máquina virtual se pueden usar con Azure Disk Encryption?
R: Azure Disk Encryption solo está disponible en máquinas virtuales de nivel estándar, como son [A, D, DS, G, GS, F](https://azure.microsoft.com/pricing/details/virtual-machines/) y máquinas virtuales IaaS de series sucesivas, incluidas las que tienen almacenamiento premium. No está disponible en las máquinas virtuales de nivel básico.

P: ¿Qué distribuciones de Linux son compatibles con Azure Disk Encryption?
R: Azure Disk Encryption se admite en las siguientes distribuciones y versiones de servidores Linux:
| Distribución de Linux | Versión | Tipo de volumen compatible con el cifrado|
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

P: ¿Cómo puedo empezar a usar Azure Disk Encryption?
R: Los clientes pueden empezar leyendo las notas del producto Azure Disk Encryption que se encuentran [aquí](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

P: ¿Puedo cifrar los volúmenes de datos y arranque con Azure Disk Encryption?
R: Sí, puede cifrar los volúmenes de datos y de arranque para las máquinas virtuales IaaS de Windows y Linux. En las máquinas virtuales de Windows, no se pueden cifrar los datos sin cifrar primero el volumen del sistema operativo. En las de Linux, se puede cifrar el volumen de datos sin cifrar primero el volumen del sistema operativo. Una vez que haya cifrado el volumen del sistema operativo para Linux, no se puede deshabilitar el cifrado en un volumen del sistema operativo para las máquinas virtuales IaaS de Linux.

P: ¿Permite Azure Disk Encryption habilitar la funcionalidad "traiga su propia clave" (BYOK)?
R: Sí, puede proporcionar sus propias claves de cifrado de claves. Dichas claves están protegidas en Azure Key Vault, que es el almacén de claves de Azure Disk Encryption. Para más detalles sobre los escenarios de compatibilidad con claves de cifrado de claves, vea los escenarios de implementación y las experiencias de Azure Disk Encryption.

P: ¿Puedo usar una clave de cifrado de claves creada en Azure?
R: Sí, puede usar Azure Key Vault para generar la clave de cifrado de claves para usar en Azure Disk Encryption. Dichas claves están protegidas en Azure Key Vault, que es el almacén de claves de Azure Disk Encryption. Para más detalles sobre los escenarios de compatibilidad con claves de cifrado de claves, vea los escenarios de implementación y las experiencias de Azure Disk Encryption.

P: ¿Puedo usar el servicio de administración de claves local o HSM para proteger las claves de cifrado?
R: No puede utilizar el servicio de administración de claves local ni HSM para proteger las claves de cifrado con Azure Disk Encryption. Para proteger las claves de cifrado, solo se puede utilizar el servicio Azure Key Vault. Para más detalles sobre los escenarios de compatibilidad con claves de cifrado de claves, vea los escenarios de implementación y las experiencias de Azure Disk Encryption.

P: ¿Cuáles son los requisitos previos para configurar Azure Disk Encryption?
R: El script de PowerShell constituye un requisito previo para Azure Disk Encryption que permite crear la aplicación AAD, crear un nuevo almacén de claves o configurar el ya existente para el acceso al cifrado de disco y habilitar el cifrado y proteger los secretos y la clave.  Para más detalles sobre los escenarios de compatibilidad con claves de cifrado de claves, vea las experiencias, los escenarios de implementación y los requisitos previos de Azure Disk Encryption.

P: ¿Dónde puedo obtener más información sobre cómo usar PowerShell para configurar Azure Disk Encryption?
R: Tenemos algunos artículos excelentes sobre cómo realizar tareas básicas de Azure Disk Encryption, así como escenarios más avanzados. Para las tareas básicas, consulte [Exploración de Azure Disk Encryption con Azure PowerShell, parte 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Para escenarios más avanzados, consulte [Azure Disk Encryption con Azure PowerShell, parte 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

P: ¿Qué versión de Azure PowerShell es compatible con Azure Disk Encryption?
R: Utilice la versión más reciente del SDK de Azure PowerShell para configurar Azure Disk Encryption. Descargue la versión más reciente de [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). La versión 1.1.0 del SDK de Azure no admite Azure Disk Encryption.

> [!NOTE]
> La extensión de la versión preliminar de Azure Disk Encryption para Linux está en desuso. Para más detalles, consulte la documentación [aquí](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

P: ¿Puedo aplicar Azure Disk Encryption en mi imagen personalizada de Linux?
R: No puede aplicar Azure Disk Encryption a una imagen personalizada de Linux. Solo se admiten las imágenes de Linux de la galería para las distribuciones admitidas mencionadas anteriormente. Actualmente no se admiten imágenes personalizadas de Linux.

P: ¿Puedo aplicar actualizaciones a una máquina virtual Red Hat de Linux utilizando la actualización de Yum?
R: Sí, puede realizar la actualización y aplicar una revisión a una máquina virtual Red Hat de Linux siguiendo los consejos que se documentan [aquí](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

P: ¿Dónde puedo formular preguntas o enviar comentarios? R: Puede realizar preguntas o publicar comentarios en el foro sobre Azure Disk Encryption, [aquí](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption).

## <a name="see-also"></a>Otras referencias
En este documento, aprendió más acerca de las preguntas más frecuentes sobre Azure Disk Encryption; para obtener más información sobre este servicio y su funcionalidad, lea:

- [Aplicación de cifrado de discos en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Cifrado de una máquina virtual de Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Cifrado en reposo de datos de Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

