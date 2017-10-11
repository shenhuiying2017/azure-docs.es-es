---
title: "Aplicación del cifrado de discos en Azure Security Center | Microsoft Docs"
description: "En este documento, se muestra cómo implementar la recomendación **Aplicar el cifrado de discos** del Centro de seguridad de Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 67cff664f3723b2194ecd1519729cca17069d07f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicación del cifrado de discos en el Centro de seguridad de Azure
Azure Security Center recomienda que aplique el cifrado de discos si tiene discos de máquina virtual de Windows o Linux que no estén cifrados con Azure Disk Encryption. El Cifrado de discos permite cifrar los discos de máquina virtual IaaS de Windows y Linux.  Se recomienda cifrar tanto los volúmenes de datos como los del sistema operativo en la máquina virtual.

Disk Encryption usa la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) estándar del sector de Windows y la característica [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux. Estas características proporcionan cifrado de datos y del sistema operativo para proteger sus datos y asumir las responsabilidades de cumplimiento y seguridad de la organización. La solución se integra con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ayudarle a controlar y administrar las claves y los secretos de cifrado de discos en su suscripción de Key Vault, al mismo tiempo que garantiza que todos los datos de los discos de las máquinas virtuales se cifran en reposo en [Azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> El Cifrado de discos de Azure es compatible con los siguientes sistemas operativos de Windows Server: Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. El cifrado de discos es compatible con los siguientes sistemas operativos de Linux Server: Ubuntu, CentOS, SUSE y SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Aplicar cifrado de discos**.
2. En la hoja **Aplicar cifrado de discos**, verá una lista de las máquinas virtuales para las que se recomienda el cifrado de discos.
3. Siga las instrucciones para aplicar el cifrado a estas máquinas virtuales.

![][1]

Para cifrar las máquinas virtuales de Azure que Security Center ha identificado que necesitan cifrado, recomendamos que siga los siguientes pasos:

* Instale y configure Azure PowerShell. Esto permite ejecutar los comandos de PowerShell para configurar los requisitos previos necesarios para cifrar Azure Virtual Machines.
* Obtenga y ejecute el script de Azure PowerShell de requisitos previos de Cifrado de discos de Azure.
* Cifre las máquinas virtuales.

[Encrypt an Azure Virtual Machine](security-center-disk-encryption.md) (Cifrado de una máquina virtual de Azure) le guiará a través de estos pasos.  En este tema se asume que utiliza Windows 10 como máquina cliente desde la que configura el cifrado de disco.

Existen varios enfoques que se pueden usar para Azure Virtual Machines. Si conoce bien Azure PowerShell o CLI de Azure, puede preferir el uso de métodos alternativos. Para más información sobre estos otros enfoques, consulte [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Consulte también
En este documento, mostramos cómo implementar la recomendación "Aplicar el cifrado de discos" del Centro de seguridad. Para más información acerca del cifrado de discos, consulte lo siguiente:

* [Encryption and key management with Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (Cifrado y administración de claves con el Almacén de claves de Azure) (vídeo, 36 m y 39 s): aprenda a usar la administración del cifrado de discos para máquinas virtuales IaaS y el Almacén de claves de Azure para ayudar a proteger sus datos.
* [Cifrado de una máquina virtual de Azure](security-center-disk-encryption.md) (documento), aprenda a cifrar las máquinas virtuales de Azure.
* [Cifrado de discos de Azure](../security/azure-security-disk-encryption.md) (documento): aprenda a habilitar el cifrado de discos para máquinas virtuales de Windows y Linux.

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad.
* [Supervisión del estado de seguridad en el Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : aprenda a administrar y responder a alertas de seguridad.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
