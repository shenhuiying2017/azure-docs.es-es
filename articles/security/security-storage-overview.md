---
title: Características de seguridad que se pueden usar con Azure Storage | Microsoft Docs
description: Este artículo ofrece una visión general de las principales características de seguridad de Azure que se pueden usar con Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 9558f1ec0d8ccd83da764a0967fa83d93e1e6a02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365393"
---
# <a name="azure-storage-security-overview"></a>Información general sobre seguridad de Azure Storage
Azure Storage es la solución de almacenamiento en la nube para las aplicaciones modernas que dependen de la durabilidad, la disponibilidad y la escalabilidad para satisfacer las necesidades de sus clientes. Azure Storage proporciona un conjunto completo de funcionalidades de seguridad. Puede:

* Proteger la cuenta de almacenamiento mediante el control de acceso basado en roles (RBAC) y Azure Active Directory.
* Proteger los datos en tránsito entre una aplicación y Azure mediante cifrados de cliente, HTTPS o SMB 3.0.
* Establecer una configuración para que los datos se cifren automáticamente cuando se escriben en Azure Storage mediante Storage Service Encryption.
* Establecer el cifrado de los discos de datos y del sistema operativo que usan las máquinas virtuales mediante Azure Disk Encryption.
* Conceder acceso delegado a los objetos de datos de Azure Storage mediante las firmas de acceso compartido (SAS).
* Usar análisis para seguir el método de autenticación que alguien esté usando cuando obtienen acceso a Storage.

Para obtener más información sobre la seguridad en Azure Storage, consulte la [guía de seguridad de Azure Storage](../storage/common/storage-security-guide.md). En esta guía se proporciona información detallada de las características de seguridad de Azure Storage. Estas características incluyen las claves de cuenta de almacenamiento, el cifrado de datos en tránsito y en reposo y el análisis de almacenamiento.

Igualmente, en este artículo se ofrece información general de las características de seguridad de Azure que se pueden usar con Azure Storage. Además, se incluyen vínculos a artículos que ofrecen detalles de cada una de estas características para que pueda tener más información al respecto.

## <a name="role-based-access-control"></a>Control de acceso basado en roles
Puede proteger su cuenta de almacenamiento mediante el control de acceso basado en roles (RBAC). En organizaciones que quieren aplicar directivas de seguridad para el acceso a los datos, es imperativo restringir el acceso en función de los principios de seguridad [necesidad de saber](https://en.wikipedia.org/wiki/Need_to_know) y [mínimo privilegio](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Estos derechos de acceso se conceden al asignar el rol RBAC adecuado a grupos y aplicaciones de un determinado ámbito. Puede aprovechar los [roles integrados de RBAC](../role-based-access-control/built-in-roles.md), tales como el de colaborador de la cuenta de almacenamiento, para asignar privilegios a los usuarios.

Más información:

* [Control de acceso basado en roles de Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Acceso delegado a objetos de almacenamiento
Una firma de acceso compartido ofrece acceso delegado a recursos en la cuenta de almacenamiento. Esto significa que puede conceder a un cliente permisos limitados para objetos en su cuenta de almacenamiento durante un período específico y con un conjunto determinado de permisos sin tener que compartir las claves de acceso a las cuentas. 

La SAS es un URI que incluye en sus parámetros de consulta toda la información necesaria para el acceso autenticado a un recurso de almacenamiento. Para obtener acceso a los recursos de almacenamiento con SAS, el cliente solo tiene que proporcionar SAS al método o constructor adecuados.

Más información:

* [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Crear y usar una SAS con Blob Storage](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Cifrado en tránsito
El cifrado en tránsito es un mecanismo para proteger datos cuando se transmiten a través de redes. Gracias a Azure Storage, puede proteger los datos mediante:

* [El cifrado de nivel de transporte](../storage/common/storage-security-guide.md#encryption-in-transit), como HTTPS, para transferir datos a o desde Azure Storage.
* [El cifrado en el cable](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como el cifrado SMB 3.0, para recursos compartidos de Azure File.
* [Cifrado de cliente](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para cifrar los datos antes de transferirlos a Storage y descifrarlos una vez transferidos desde este servicio.

Más información acerca del cifrado de cliente:

* [Cifrado del lado cliente para Microsoft Azure Storage (vista previa)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloud security controls series: Encrypting Data in Transit (Serie de controles de seguridad en la nube: cifrado de datos en tránsito)](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Cifrado en reposo
Para muchas organizaciones, el [cifrado de los datos en reposo](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) es un paso obligatorio en lo que respecta a la privacidad de los datos, el cumplimiento y la soberanía de los datos. Hay tres características de Azure que proporcionan el cifrado de datos que están en reposo:

* [Storage Service Encryption](../storage/common/storage-security-guide.md#encryption-at-rest), que le permite solicitar que el servicio de almacenamiento cifre automáticamente los datos al escribirlos en Azure Storage.
* [Cifrado de cliente](../storage/common/storage-security-guide.md#client-side-encryption), que también proporciona la característica de cifrado en reposo.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines), que le permite cifrar los discos de datos y del sistema operativo que usa una máquina virtual de IaaS.

Más información acerca del cifrado de servicio Storage

* [Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) está disponible para [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Para obtener más información sobre otros tipos de almacenamiento de Azure, consulte [Azure Files](https://azure.microsoft.com/services/storage/files/), [Disco (Premium Storage)](https://azure.microsoft.com/services/storage/premium-storage/), [Table Storage](https://azure.microsoft.com/services/storage/tables/) y [Queue Storage](https://azure.microsoft.com/services/storage/queues/).
* [Cifrado del servicio Azure Storage para datos en reposo (versión preliminar)](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption para máquinas virtuales le ayuda a abordar la seguridad de la organización y los requisitos de cumplimiento. Se encarga de cifrar los discos de las máquinas virtuales (incluidos los discos de arranque y de datos) mediante el uso de claves y directivas que puede controlar en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Cifrado de discos para máquinas virtuales funciona en sistemas operativos Linux y Windows. También utiliza Key Vault para ayudarle a proteger, administrar y auditar el uso de las claves de cifrado del disco. Todos los datos de los discos de máquinas virtuales se cifran en reposo mediante la tecnología de cifrado estándar del sector en las cuentas de Azure Storage. La solución Cifrado de discos para Windows se basa en la tecnología de [Cifrado de unidad BitLocker de Microsoft](https://technet.microsoft.com/library/cc732774.aspx), y la solución de Linux se basa en [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Más información:

* [Azure Disk Encryption for Windows and Linux Azure Virtual Machines (Azure Disk Encryption para máquinas virtuales IaaS de Linux y Windows)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Disk Encryption usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para ayudarle a controlar y administrar las claves y los secretos de cifrado de discos en la suscripción de Key Vault. Gracias a ello, se garantiza que todos los datos de los discos de máquinas virtuales se cifren en reposo en Azure Storage. Debe usar Key Vault para auditar el uso de la directiva y las claves.

Más información:

* [¿Qué es Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Introducción a Azure Key Vault](../key-vault/key-vault-get-started.md)
