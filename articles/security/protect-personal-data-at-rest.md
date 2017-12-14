---
title: Datos personales de Azure Protect en reposo con cifrado | Microsoft Docs
description: "Este artículo forma parte de una serie que ayuda a usar Azure para proteger datos personales."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 31e41f9befd9319115e5d147b473756486100c6e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Tecnologías de cifrado de Azure: protección de datos personales en reposo con cifrado

Este artículo le ayudará a entender y usar las tecnologías de cifrado de Azure para proteger datos en reposo.

El cifrado de datos en reposo es un procedimiento recomendado fundamental para proteger datos confidenciales o personales y para cumplir los requisitos de cumplimiento normativo y privacidad de los datos.
El cifrado en reposo está diseñado para evitar que el atacante obtenga acceso a los datos sin cifrar asegurándose de que los datos se cifran en el disco.

## <a name="scenario"></a>Escenario 

Una gran empresa de cruceros, con sede en Estados Unidos, se encuentra en proceso de expansión de sus operaciones para ofrecer itinerarios en los mares Mediterráneo y Báltico, así como en las Islas Británicas. Para apoyar esos esfuerzos, ha adquirido varias líneas de cruceros más pequeñas establecidas en Italia, Alemania, Dinamarca y Reino Unido.

La empresa usa Microsoft Azure para almacenar datos corporativos en la nube. Estos pueden incluir información sobre clientes o empleados como, por ejemplo:

- direcciones
- números de teléfono
- números de identificación fiscal
- información de tarjeta de crédito

La empresa debe proteger la privacidad de los datos de los clientes y los empleados y, al mismo tiempo, hacer que aquellos departamentos que lo necesiten puedan tener acceso a esos datos (por ejemplo, los departamentos de nóminas y reservas)

La línea de cruceros también conserva una gran base de datos de miembros del programa de recompensa y lealtad que incluye información personal para hacer un seguimiento de las relaciones con clientes actuales y antiguos.

### <a name="problem-statement"></a>Declaración del problema

La empresa debe proteger la privacidad de los datos de los clientes y los empleados y, al mismo tiempo, hacer que aquellos departamentos que lo necesiten puedan acceder a esos datos (por ejemplo, los departamentos de nóminas y reservas). Estos datos personales se almacenan fuera del centro de datos controlado por la corporación y no está bajo el control físico de la compañía.

### <a name="company-goal"></a>Objetivo de la empresa

Como parte de una estrategia de seguridad de defensa en profundidad de varias capas, el objetivo de la empresa es garantizar que todos los orígenes de datos que contienen datos personales están cifrados, incluidos aquellos que se encuentran en el almacenamiento en la nube. Si personas no autorizadas logran acceder a los datos personales, estos deben resultar ilegibles. La aplicación del cifrado debería ser fácil y transparente tanto para los usuarios como para los administradores.

## <a name="solutions"></a>Soluciones

Los servicios de Azure proporcionan varias herramientas y tecnologías para ayudarle a proteger datos personales en reposo mediante el cifrado.

### <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) ofrece un almacenamiento seguro para las claves utilizadas para cifrar los datos en reposo de los servicios de Azure y es la solución de almacenamiento y administración de claves recomendada. La administración de claves de cifrado es esencial para proteger los datos almacenados.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>¿Cómo se puede usar Azure Key Vault para proteger las claves que cifran los datos personales?

Para usar Azure Key Vault, necesita una suscripción a una cuenta de Azure. También necesita tener Azure Powershell instalado. Los pasos incluyen el uso de cmdlets de PowerShell para hacer lo siguiente:

1. Conectarse a sus suscripciones

2. Creación de un Almacén de claves

3. Adición de una clave o un secreto al Almacén de claves

4. Registrar las aplicaciones que va a usar el almacén de claves con Azure Active Directory

5. Autorizar las aplicaciones que van a usar la clave o el secreto

Para crear un almacén de claves, use el cmdlet New-AzureRmKeyVault de PowerShell. Asignará un nombre de almacén, un nombre de grupo de recursos y una ubicación geográfica. Deberá usar el nombre de almacén al administrar claves mediante otros cmdlets. Las aplicaciones que usen el almacén a través de la API de REST utilizarán el URI de este.

Azure Key Vault puede proporcionar una clave protegida mediante software o importar una clave ya existente de un archivo .PFX. También puede almacenar secretos (contraseñas) en el almacén.

También puede generar una clave en el HSM local y transferirla al HSM del servicio Key Vault, sin que la clave salga del límite del HSM.

Para obtener instrucciones detalladas sobre cómo usar Azure Key Vault, siga los pasos descritos en [Introducción a Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)

Para obtener una lista de cmdlets de PowerShell que se usan con Azure Key Vault, consulte [AzureRM.KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

### <a name="azure-disk-encryption-for-windows"></a>Azure Disk Encryption para Windows

[Azure Disk Encryption para máquinas virtuales de IaaS de Windows y Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) protege los datos personales en reposo en las máquinas virtuales de Azure y se integra con Azure Key Vault. Azure Disk Encryption usa [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) en Windows y [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) en Linux para cifrar tanto el sistema operativo como los discos de datos. Azure Disk Encryption es compatible con Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016, y con clientes de Windows 8 y Windows 10.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>¿Cómo se puede usar Azure Disk Encryption para proteger los datos personales?

Para usar Azure Disk Encryption, necesita una suscripción a una cuenta de Azure. Para habilitar Azure Disk Encryption para máquinas virtuales con Windows y Linux, haga lo siguiente:

1. Use la plantilla de Resource Manager de Azure Disk Encryption, PowerShell o la interfaz de la línea de comandos (CLI) para habilitar el cifrado de disco y especificar la configuración del cifrado. 

2. Conceda acceso a la plataforma de Azure para leer el material de cifrado desde el almacén de claves.

3. Proporcione una identidad de aplicación de Azure Active Directory (AAD) para escribir el material de clave de cifrado en su almacén de claves.

Azure actualizará la máquina virtual y la configuración del almacén de claves y configurará la máquina virtual cifrada.

Al configurar el almacén de claves para que sea compatible con Azure Disk Encryption, puede agregar una clave de cifrado de claves (KEK) para una mayor seguridad y para que admita la copia de seguridad de máquinas virtuales cifradas.

![](media/protect-personal-data-at-rest/create-key.png)

En [Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) puede encontrar instrucciones detalladas sobre escenarios de implementación y experiencias de usuario específicas.

### <a name="azure-storage-service-encryption"></a>Cifrado del servicio Azure Storage

[Cifrado del servicio Azure Storage (SSE) para datos en reposo](https://docs.microsoft.com/azure/storage/storage-service-encryption) le ayuda a asegurar y proteger sus datos con el fin de cumplir con los compromisos de cumplimiento y seguridad de su organización. Azure Storage cifra automáticamente sus datos mediante cifrado AES de 256-bits antes de continuar al almacenamiento y los descifra antes de la recuperación. Este servicio está disponible para los archivos y blobs de Azure.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>¿Cómo se puede usar Cifrado del servicio Azure Storage para proteger los datos personales?

Para habilitar Cifrado del servicio Azure Storage, haga lo siguiente:

1. Inicie sesión en Azure Portal.

2. Seleccione una cuenta de almacenamiento.

3. En Configuración, en la sección Blob service, seleccione Cifrado.

4. En la sección Servicio Archivo, seleccione Cifrado.

Una vez que haga clic en la configuración de cifrado, puede habilitar o deshabilitar Cifrado del servicio Storage.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

Los datos nuevos se cifrarán. Los datos en los archivos ya existentes de esta cuenta de almacenamiento permanecerán sin cifrar.

Después de habilitar el cifrado, copie datos en la cuenta de almacenamiento mediante uno de los métodos siguientes:

1. Copie los blobs o archivos con la [utilidad de línea de comandos AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy).

2. [Monte un recurso compartido de archivos mediante SMB](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-windows) para que pueda usar una utilidad como Robocopy para copiar archivos.

3. Copie los datos de blobs o archivos en Blob Storage y desde este, o entre cuentas de almacenamiento mediante las [bibliotecas de cliente de Storage, como .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs).

4.  Use un [Explorador de Storage](https://docs.microsoft.com/azure/storage/storage-explorers) para cargar blobs a su cuenta de almacenamiento con cifrado habilitado.

### <a name="transparent-data-encryption"></a>Cifrado de datos transparente

Cifrado de datos transparente (TDE) es una característica de SQL Azure mediante la cual puede cifrar los datos en los niveles de la base de datos y el servidor. TDE ahora está habilitado de forma predeterminada en todas las bases de datos recién creadas. TDE realiza el cifrado y descifrado de E/S en tiempo real de los archivos de datos y de registro.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>¿Cómo se usa TDE para proteger los datos personales?

Puede configurar TDE a través de Azure Portal, mediante el uso de la API de REST o mediante PowerShell. Para habilitar TDE en una base de datos existente mediante Azure Portal, haga lo siguiente:

1. Visite Azure Portal en <https://portal.azure.com> e inicie sesión con su cuenta de administrador o colaborador de Azure.

2. En el encabezado de la izquierda, haga clic en EXAMINAR y, a continuación, haga clic en bases de datos SQL.

3. Con las bases de datos SQL seleccionadas en el panel izquierdo, haga clic en la base de datos de usuario.

4. En la hoja de la base de datos, haga clic en Toda la configuración.

5. En la hoja de configuración, haga clic en la parte de Cifrado de datos transparente para abrir la hoja correspondiente.

6. En la hoja de cifrado de datos, mueva el botón de cifrado de datos a On y, a continuación, haga clic en Guardar (en la parte superior de la página) para aplicar la configuración. El estado de cifrado será aproximadamente el mismo que el progreso del cifrado de datos transparente.

![Habilitar el cifrado de datos](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

Puede encontrar instrucciones sobre cómo habilitar TDE y obtener información acerca de cómo descifrar las bases de datos protegidas por TDE y mucho más en el artículo [Cifrado de datos transparente con Azure SQL Database.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)

## <a name="summary"></a>Resumen

La compañía puede lograr su objetivo de cifrar datos personales almacenados en la nube de Azure. Puede hacerlo mediante Azure Disk Encryption para proteger volúmenes completos. Esto incluye los archivos del sistema operativo y los archivos de datos que contienen información de identificación personal y otros datos confidenciales. Cifrado del servicio Azure Storage puede utilizarse para proteger los datos personales que se almacenan en archivos y blobs. Para los datos que se almacenan en instancias de Azure SQL Database, el cifrado de datos transparente ofrece protección frente a una exposición no autorizada de información personal.

Para proteger las claves que se usan para cifrar los datos de Azure, la empresa puede usar Azure Key Vault. Este agiliza el proceso de administración de claves y permite a la empresa mantener el control de claves que obtienen acceso a los datos personales y los cifran.

## <a name="next-steps"></a>Pasos siguientes

- [Guía de solución de problemas de Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-tsg)

- [Cifrado de una máquina virtual de Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Cifrado de datos en Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

- [Cifrado de base de datos en reposo en Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)
