---
title: Aplicación del cifrado de discos en el Centro de seguridad de Azure | Microsoft Docs
description: En este documento, se muestra cómo implementar la recomendación **Aplicar el cifrado de discos** del Centro de seguridad de Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# Aplicación del cifrado de discos en el Centro de seguridad de Azure
El Centro de seguridad de Azure recomendará que aplique el cifrado de discos si tiene discos de máquina virtual de Windows o Linux que no estén cifrados con el Cifrado de discos de Azure. El Cifrado de discos permite cifrar los discos de máquina virtual IaaS de Windows y Linux. Se recomienda cifrar tanto los volúmenes de datos como los del sistema operativo en la máquina virtual.

El Cifrado de discos aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de Windows estándar en el sector y la característica [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para proporcionar cifrado de datos y del sistema operativo a fin de proteger sus datos y asumir las responsabilidades de seguridad y cumplimiento de la organización. El Cifrado de discos se integra con el [Almacén de claves de Azure](https://azure.microsoft.com/documentation/services/key-vault/) para ayudarle a controlar y administrar las claves y los secretos de cifrado de discos en su suscripción del Almacén de claves, al mismo tiempo que garantiza que todos los datos de los discos de las máquinas virtuales se cifren en reposo en el [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> El Cifrado de discos de Azure es compatible con los siguientes sistemas operativos de Windows Server: Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. El Cifrado de discos es compatible con los siguientes sistemas operativos de Linux Server: Ubuntu, CentOS, SUSE y SUSE Linux Enterprise Server (SLES).
> 
> 

## Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Aplicar el cifrado de discos**.
2. En la hoja **Aplicar el cifrado de discos**, verá una lista de las máquinas virtuales para las que se recomienda el Cifrado de discos.
3. Siga las instrucciones para aplicar el cifrado a estas máquinas virtuales.

![][1]

Para cifrar las máquinas virtuales de Azure que Security Center ha identificado que necesitan cifrado, recomendamos que siga los siguientes pasos:

* Instale y configure Azure PowerShell. Esto permitirá ejecutar los comandos de PowerShell para configurar los requisitos previos necesarios para cifrar las máquinas virtuales de Azure.
* Obtenga y ejecute el script de Azure PowerShell de requisitos previos de Cifrado de discos de Azure.
* Cifre las máquinas virtuales.

La sección [Cifrado de una máquina virtual de Azure](security-center-disk-encryption.md) le guiará a través de estos pasos. En este tema se asume que utiliza Windows 10 como máquina cliente desde la que configurará el cifrado de disco.

Existen varios enfoques que pueden usarse para configurar los requisitos previos y el cifrado para máquinas virtuales de Azure. Si conoce bien Azure PowerShell o CLI de Azure, puede preferir el uso de métodos alternativos. Para más información sobre estos otros enfoques, consulte [Cifrado de discos de Azure](../security/azure-security-disk-encryption.md).

## Consulte también
En este documento, mostramos cómo implementar la recomendación "Aplicar el cifrado de discos" del Centro de seguridad. Para más información acerca del cifrado de discos, consulte lo siguiente:

* [Encryption and key management with Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (Cifrado y administración de claves con el Almacén de claves de Azure) (vídeo, 36 m y 39 s): aprenda a usar la administración del cifrado de discos para máquinas virtuales IaaS y el Almacén de claves de Azure para ayudar a proteger sus datos.
* [Cifrado de una máquina virtual de Azure](security-center-disk-encryption.md) (documento), aprenda a cifrar las máquinas virtuales de Azure.
* [Cifrado de discos de Azure](../security/azure-security-disk-encryption.md) (documento): aprenda a habilitar el cifrado de discos para máquinas virtuales de Windows y Linux.

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): aprenda a configurar directivas de seguridad.
* [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): aprenda a supervisar el estado de los recursos de Azure.
* [Administración de alertas de seguridad y respuesta a estas en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): aprenda a administrar alertas de seguridad y responder a ellas.
* [Administración de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md): sepa cómo las recomendaciones lo ayudan a proteger sus recursos de Azure.
* [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png

<!---HONumber=AcomDC_0803_2016-->