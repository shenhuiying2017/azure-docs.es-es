---
title: "Protección de aplicaciones PaaS con Azure Storage | Microsoft Docs"
description: " Obtenga información sobre los procedimientos recomendados de seguridad de Azure Storage para proteger aplicaciones web y móviles PaaS. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: TomShinder
ms.openlocfilehash: 62c7b6706268e3c6e329f90651125fe299f61d67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Protección de aplicaciones web y móviles PaaS con Azure Storage
En este artículo se explican una serie de procedimientos recomendados de seguridad de Azure Storage para proteger aplicaciones web y móviles PaaS. Estos procedimientos recomendados proceden de nuestra experiencia con Azure y las experiencias de clientes como usted.

La [Guía de seguridad de Azure Storage](../storage/common/storage-security-guide.md) es una excelente fuente para obtener información detallada sobre Azure Storage y la seguridad.  Este artículo aborda de forma resumida algunos de los conceptos de la Guía de seguridad. Además, se ofrecen vínculos a dicha guía y otras fuentes de información.

## <a name="azure-storage"></a>Azure Storage
Azure permite implementar y usar el almacenamiento de formas inauditas con un entorno local. Gracias a Azure Storage, se pueden alcanzar altos niveles de escalabilidad y disponibilidad con relativamente poco esfuerzo. Azure Storage no solo constituye la base de las máquinas virtuales de Azure con Windows y Linux, sino que también puede admitir aplicaciones distribuidas de gran tamaño.

Azure Storage proporciona los cuatro servicios siguientes: Blob Storage, Table Storage, Queue Storage y File Storage. Para obtener más información, consulte [Introducción a Microsoft Azure Storage](../storage/storage-introduction.md).

## <a name="best-practices"></a>Prácticas recomendadas
En este artículo se explican los siguientes procedimientos recomendados:

- Protección de acceso:
   - Firmas de acceso compartido (SAS)
   - Disco administrado
   - Control de acceso basado en rol (RBAC)

- Cifrado de almacenamiento:
   - Cifrado del lado cliente para datos de gran valor
   - Azure Disk Encryption para máquinas virtuales (VM)
   - Cifrado del servicio de almacenamiento

## <a name="access-protection"></a>Protección de acceso
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Uso de la firma de acceso compartido en lugar de una clave de cuenta de almacenamiento

En una solución de IaaS, normalmente máquinas virtuales Linux o Windows en ejecución, los archivos están protegidos contra la divulgación y las amenazas de alteración a través de mecanismos de control de acceso. En Windows se usaría las [listas de control de acceso (ACL)](../virtual-network/virtual-networks-acl.md) y, en Linux, probablemente [chmod](https://en.wikipedia.org/wiki/Chmod). Básicamente, esto es exactamente lo que haría actualmente si estuviese protegiendo archivos de un servidor de su propio centro de datos.

Pero con PaaS es diferente. Una de las formas más habituales de almacenar archivos en Microsoft Azure es utilizando [Azure Blob Storage](../storage/storage-dotnet-how-to-use-blobs.md). Una de las diferencias entre Blob Storage y otro sistema de almacenamiento de archivos es la E/S de archivos y los métodos de protección que se proporcionan con dicha E/S de archivos.

El control de acceso es fundamental. Para ayudarlo a controlar el acceso a Azure Storage, el sistema generará dos claves de cuenta de almacenamiento de 512 bits (SAK) al [crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md). El nivel de redundancia clave permite evitar la interrupción del servicio durante la rotación de clave rutinaria.

Las claves de acceso de almacenamiento son secretos de prioridad alta y solo deben estar accesibles a los responsables del control de acceso de almacenamiento. Si los usuarios equivocados consiguen acceder a estas claves, podrían controlar por completo el almacenamiento y reemplazar, eliminar o agregar archivos en él. Por ejemplo, software malintencionado y otro tipo de contenido que puede poner en peligro a sus clientes u organización.

Seguirá necesitando una forma de proporcionar acceso a los objetos del almacenamiento. Para proporcionar un acceso más detallado, puede aprovechar las ventajas de la [firma de acceso compartido](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). La SAS permite compartir objetos específicos en el almacenamiento durante intervalo de tiempo predefinido y con permisos concretos. Una firma de acceso compartido permite definir lo siguiente:

- El intervalo durante el que la SAS es válida, incluida la hora de inicio y la hora de caducidad.
- Los permisos concedidos por la SAS. Por ejemplo, una SAS de un blob podría conceder a un usuario permisos de lectura y escritura para el blob, pero no permisos de eliminación.
- Una dirección IP opcional o un intervalo de direcciones IP de las que Azure Storage aceptará la SAS. Por ejemplo, podría especificar un intervalo de direcciones IP que pertenezcan a su organización. Esto proporciona otra medida de seguridad para su SAS.
- El protocolo a través del cual Azure Storage aceptará la SAS. Puede usar este parámetro opcional para restringir el acceso a los clientes mediante HTTPS.

SAS permite compartir contenido de la forma que se quiera sin tener que proporcionar las claves de cuenta de almacenamiento. Utilizar siempre SAS en la aplicación es una forma segura de compartir los recursos de almacenamiento sin poner en peligro las claves de cuenta de almacenamiento.

Para obtener más información, consulte [Uso de firmas de acceso compartido](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). Para obtener más información sobre los posibles riesgos y las recomendaciones para mitigarlos, consulte [Procedimientos recomendados al usar SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-managed-disks-for-vms"></a>Uso de discos administrados para máquinas virtuales

Cuando elige [Azure Managed Disks](../storage/storage-managed-disks-overview.md), Azure administra las cuentas de almacenamiento que utiliza para los discos de VM. Todo lo que necesita es elegir el tipo de disco (Premium o Estándar) y el tamaño; Azure Storage se encargará del resto. No tiene que preocuparse por los límites de escalabilidad que podría haber en el caso de utilizar varias cuentas de almacenamiento.

Para obtener más información, consulte [Frequently Asked Questions about managed and unmanaged premium disks](../storage/storage-faq-for-disks.md) (Preguntas más frecuentes sobre los discos premium administrados y no administrados).

### <a name="use-role-based-access-control"></a>Uso del control de acceso basado en rol

Antes, explicamos cómo utilizar la firma de acceso compartido (SAS) para conceder acceso limitado a los objetos de la cuenta de almacenamiento a otros clientes sin poner en riesgo la clave de cuenta de almacenamiento. En ocasiones, los riesgos asociados a una operación determinada en la cuenta de almacenamiento superan a las ventajas del uso de la SAS. A veces, es más sencillo administrar el acceso de otras formas.

Otra forma administrar el acceso es usando la funcionalidad [Control de acceso basado en rol de Azure](../active-directory/role-based-access-control-what-is.md) (RBAC). Gracias a RBAC, podrá centrarse en proporcionar a los empleados los permisos exactos que necesiten, según la necesidad de información y los principios de seguridad de privilegios mínimos. Un número elevado de permisos puede provocar que la cuenta esté expuesta a los atacantes. Si se conceden muy pocos, los empleados no podrán realizar su trabajo de manera eficaz. RBAC ayuda a abordar este problema, ya que es posible realizar una administración avanzada del acceso para Azure. Esto es fundamental para las organizaciones que deseen aplicar directivas de seguridad para el acceso a los datos.

Puede aprovechar los roles de RBAC integrados en Azure para asignar privilegios a los usuarios. Plantéese usar Colaborador de la cuenta de almacenamiento para los operadores en la nube que necesiten administrar cuentas de almacenamiento, y el rol Colaborador de la cuenta de almacenamiento clásica para administrar cuentas de almacenamiento clásicas. En lo que respecta a los operadores en la nube que necesitan administrar máquinas virtuales, pero no la red virtual ni la cuenta de almacenamiento a la que están conectadas, plantéese agregarlos al rol Colaborador de la máquina virtual.

Es posible que las organizaciones que no apliquen el control de acceso a los datos mediante funcionalidades como RBAC estén concediendo más privilegios de los necesarios a sus usuarios. Esto puede poner en peligro los datos si algunos usuarios acceden a datos que no les conciernen.

Para obtener más información sobre RBAC, consulte los siguientes artículos:

- [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md)
- [Roles integrados para el control de acceso basado en rol de Azure](../active-directory/role-based-access-built-in-roles.md)
- [Guía de seguridad de Azure Storage](../storage/common/storage-security-guide.md) (para obtener información detallada sobre cómo proteger una cuenta de almacenamiento con RBAC)

## <a name="storage-encryption"></a>Cifrado de almacenamiento
### <a name="use-client-side-encryption-for-high-value-data"></a>Uso del cifrado del lado cliente para datos de gran valor

El cifrado del lado cliente permite cifrar datos en tránsito mediante programación antes de cargarse en Azure Storage y descifrarlos de la misma manera cuando se recuperan del almacenamiento.  De este modo, se cifran tanto los datos en tránsito como los que están en reposo.  El cifrado del lado cliente el método más seguro para cifrar los datos, pero hay que realizar cambios mediante programación en la aplicación e implementar procesos de administración de claves.

El cifrado del lado cliente también permite controlar de forma exclusiva las claves de cifrado.  Puede generar y administrar sus propias claves de cifrado.  El cifrado del lado cliente utiliza una técnica de sobres en la que la biblioteca de cliente de Azure Storage genera una clave de cifrado de contenido (CEK) que, posteriormente, se encapsula (se cifra) con la clave de cifrado de claves (KEK). La KEK se identifica mediante un identificador de clave y puede ser un par de clave asimétrico o una clave simétrica que puede administrarse de forma local o guardarse en [Azure Key Vault](../key-vault/key-vault-whatis.md).

El cifrado del lado cliente se integra en las bibliotecas de cliente de almacenamiento. de Java y .NET.  Consulte [Cifrado del lado de cliente y Azure Key Vault para Microsoft Azure Storage](../storage/storage-client-side-encryption.md) para obtener información sobre cómo cifrar los datos en las aplicaciones cliente, y generar y administrar claves de cifrado.

### <a name="azure-disk-encryption-for-vms"></a>Azure Disk Encryption para máquinas virtuales
Azure Disk Encryption es una funcionalidad que permite cifrar los discos de las máquinas virtuales IaaS con Windows y Linux. Cifrado de discos de Azure aprovecha la característica BitLocker de Windows, estándar en el sector, y la característica DM-Crypt de Linux para ofrecer cifrado de volumen para los discos de datos y del sistema operativo. La solución se integra con Azure Key Vault para ayudarlo a controlar y administrar los secretos y las claves de cifrado de discos en la suscripción de Key Vault. La solución también garantiza que todos los datos de los discos de máquinas virtuales se cifran en reposo en Azure Storage.

Consulte [Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows](azure-security-disk-encryption.md).

### <a name="storage-service-encryption"></a>Cifrado del servicio de almacenamiento
Cuando el [cifrado del servicio Storage](../storage/storage-service-encryption.md) está habilitado en File Storage, los datos se cifran automáticamente con el cifrado AES-256. Microsoft controla todo el proceso de cifrado, descifrado y administración de claves. Esta característica está disponible en los tipos de redundancia LRS y GRS.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se presenta una serie de procedimientos recomendados de seguridad de Azure Storage para proteger aplicaciones web y móviles PaaS. Para obtener más información sobre cómo proteger las implementaciones de PaaS, vea:

- [Protección de implementaciones de PaaS](security-paas-deployments.md)
- [Protección de aplicaciones web y móviles PaaS con Azure App Services](security-paas-applications-using-app-services.md)
- [Protección de bases de datos PaaS en Azure](security-paas-applications-using-sql.md)
