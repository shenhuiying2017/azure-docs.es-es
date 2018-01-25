---
title: "Azure Disk Encryption para máquinas virtuales IaaS con Linux y Windows| Microsoft Docs"
description: "En este artículo se ofrece información general de Microsoft Azure Disk Encryption para máquinas virtuales IaaS con Windows y Linux."
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: devtiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: d6a19334b369c54ff6bad3404b4cf2ffe3b47c70
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows
Microsoft Azure está muy comprometido a garantizar la privacidad y soberanía de los datos, y permite controlar los datos hospedados en Azure datos mediante varias tecnologías avanzadas que cifran, controlan y administran las claves de cifrado, y controlan y auditan el acceso de los datos. Esto proporciona a los clientes de Azure la flexibilidad necesaria para elegir la solución que mejor cubra sus necesidades empresariales. En este artículo, le presentaremos a una nueva solución de tecnología, "Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows", que le ayudara a proteger sus datos para que cumplan los compromisos de seguridad y compatibilidad de su organización. Ofrece información detalladas sobre cómo usar las características de cifrado de disco de Azure, incluidos los escenarios admitidos y las experiencias de los usuarios.

> [!NOTE]
> Algunas de las recomendaciones pueden provocar un aumento del uso de datos, de la red o de recursos de proceso, lo que incrementará los costes de las licencias o suscripciones.

## <a name="overview"></a>Información general
Azure Disk Encryption es una nueva funcionalidad que permite cifrar los discos de las máquinas virtuales IaaS con Windows y Linux. Azure Disk Encryption aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de Windows, estándar en el sector, y la característica [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para ofrecer cifrado de volumen para los discos de datos y del sistema operativo. La solución se integra con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ayudarle a controlar y administrar los secretos y las claves de cifrado de discos en su suscripción de Key Vault. La solución también garantiza que todos los datos de los discos de máquinas virtuales se cifran en reposo en Azure Storage.

Azure Disk Encryption para máquinas virtuales IaaS de Windows y Linux ahora tiene **disponibilidad general** en todas las regiones públicas de Azure y regiones de AzureGov para VM estándares y VM con Premium Storage.

### <a name="encryption-scenarios"></a>Escenarios de cifrado
La solución Azure Disk Encryption admite los tres escenarios de cliente siguientes:

* Habilitación del cifrado en las nuevas máquinas virtuales IaaS creadas a partir de un VHD precifrado y las claves de cifrado
* Habilitación del cifrado en las nuevas máquinas virtuales IaaS creadas a partir de imágenes compatibles de la Galería de Azure
* Habilitación del cifrado en las máquinas virtuales IaaS existentes que se ejecutan en Azure
* Deshabilitación del cifrado en máquinas virtuales IaaS de Windows
* Deshabilitación del cifrado en unidades de datos para máquinas virtuales IaaS con Linux
* Habilitación del cifrado en máquinas virtuales de discos administrados
* Actualización de la configuración del cifrado de una máquina virtual cifrada con y sin Premium Storage existente
* Copia de seguridad y restauración de máquinas virtuales cifradas

La solución admite los siguientes escenarios para las máquinas virtuales IaaS cuando se habilitan en Microsoft Azure:

* Integración con Azure Key Vault
* Máquinas virtuales de nivel estándar: [máquinas virtuales IaaS de las series A, D, DS, G, GS, F, etc.](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Habilitación del cifrado en las máquinas virtuales IaaS Windows y Linux y las máquinas virtuales de disco administradas a partir de imágenes compatibles de la Galería de Azure
* Deshabilitación del cifrado en sistemas operativos y unidades de datos para máquinas virtuales IaaS de Windows y máquinas virtuales de discos administrados
* Deshabilitación del cifrado en unidades de datos para máquinas virtuales IaaS de Linux y máquinas virtuales de discos administrados
* Habilitación del cifrado en máquinas virtuales IaaS que ejecutan el sistema operativo cliente de Windows
* Habilitación del cifrado en volúmenes con rutas de montaje
* Habilitación del cifrado en máquinas virtuales de Linux configurado con seccionamiento de disco (RAID) mediante el uso de mdadm
* Habilitación del cifrado en máquinas virtuales de Linux mediante el uso de LVM para discos de datos
* Habilitación del cifrado en Linux LVM 7.3 para discos de sistema operativo y datos 
* Habilitación del cifrado en máquinas virtuales Windows configurado con espacios de almacenamiento
* Actualización de la configuración del cifrado de una máquina virtual cifrada con y sin Premium Storage existente
* Copia de seguridad y restauración de máquinas virtuales cifradas, para escenarios de KEK y no KEK (KEK: Key Encryption Key)
* Se admiten todas las regiones públicas de Azure y las regiones de AzureGov

La solución no admite los siguientes escenarios, características y tecnologías:

* Máquinas virtuales IaaS de nivel básico
* Deshabilitación del cifrado en una unidad del sistema operativo para máquinas virtuales IaaS Linux
* Deshabilitación del cifrado en una unidad de datos si la unidad del sistema operativo está cifrada para las máquinas virtuales Iaas Linux
* Máquinas virtuales IaaS creadas con el método clásico de generación de máquinas virtuales
* NO se admite la habilitación del cifrado en imágenes personalizadas de cliente de máquinas virtuales IaaS Windows y Linux.
* Integración con el Servicio de administración de claves local
* Azure Files (recurso compartido de archivos de Azure), Network File System (NFS), volúmenes dinámicos y máquinas virtuales Windows configuradas con sistemas RAID basados en software

### <a name="encryption-features"></a>Características de cifrado
Al habilitar e implementar Azure Disk Encryption para las máquinas virtuales IaaS con Azure, se habilitan las funcionalidades siguientes, dependiendo de la configuración proporcionada:

* Cifrado del volumen de sistema operativo para proteger el volumen de arranque en reposo en el almacenamiento
* Cifrado de volúmenes de datos para proteger los volúmenes de datos en reposo en el almacenamiento
* Deshabilitación del cifrado en unidades de datos y del sistema operativo para máquinas virtuales IaaS con Windows
* Deshabilitación del cifrado en las unidades de datos de las máquinas virtuales IaaS Linux (solo si la unidad del sistema operativo NO ESTÁ cifrada)
* Protección de las claves y secretos de cifrado en la suscripción de Key Vault
* Notificación del estado de cifrado de la máquina virtual IaaS cifrada
* Eliminación de las opciones de configuración de cifrado de disco de la máquina virtual IaaS
* Copia de seguridad y restauración de máquinas virtuales cifradas mediante el servicio Azure Backup

La solución Azure Disk Encryption para máquinas virtuales IaaS para Windows y Linux incluye lo siguiente:

* La extensión de cifrado del disco para Windows.
* La extensión de cifrado del disco para Linux.
* Los cmdlets de PowerShell de cifrado del disco.
* Los cmdlets de la interfaz de la línea de comandos (CLI) de Azure de cifrado del disco.
* Las plantillas de Azure Resource Manager de cifrado del disco.

La solución Azure Disk Encryption es compatible con las máquinas virtuales IaaS que ejecutan los sistemas operativos Windows o Linux. Para obtener más información acerca de los sistemas operativos compatibles, consulte la sección "Requisitos previos".

> [!NOTE]
> No hay cargo adicional por el cifrado de discos de máquinas virtuales con Azure Disk Encryption.

### <a name="value-proposition"></a>Propuesta de valor
Cuando se aplica la solución de administración de Azure Disk Encryption, puede atender las siguientes necesidades empresariales:

* Las máquinas virtuales IaaS se protegen en reposo, porque puede utilizar la tecnología de cifrado estándar del sector para cumplir los requisitos de seguridad y cumplimiento de la organización.
* Las máquinas virtuales IaaS arrancan bajo directivas y claves controladas por el cliente, y puede auditar su uso en su almacén de claves.

### <a name="encryption-workflow"></a>Flujo de trabajo de cifrado
Para habilitar el cifrado de disco para máquinas virtuales con Windows y Linux, haga lo siguiente:

1. Elija un escenario de cifrado entre los escenarios de cifrado anteriores.
2. Opte por habilitar el cifrado de disco mediante la plantilla de Resource Manager de Azure Disk Encryption, los cmdlets PowerShell o el comando de la CLI, y especifique la configuración del cifrado.

   * Para el escenario de VHD cifrado por el cliente, cargue el VHD cifrado a su cuenta de almacenamiento y el material de la clave de cifrado en su almacén de claves. A continuación, proporcione la configuración de cifrado para habilitar el cifrado en una nueva máquina virtual IaaS.
   * Para las nuevas máquinas virtuales creadas a partir de Marketplace y las máquinas virtuales existentes que ya se ejecutan en Azure, proporcione la configuración para habilitar el cifrado en la máquina virtual IaaS.

3. Conceda acceso a la plataforma Azure para leer el material de la clave de cifrado (sistemas de claves de cifrado de BitLocker para Windows y frase de contraseña para Linux) de su almacén de claves para habilitar el cifrado en la máquina virtual IaaS.

4. Proporcione la identidad de aplicación de Azure Active Directory (Azure AD) para escribir el material de clave de cifrado en su almacén de claves. De este modo, se habilita el cifrado en la máquina virtual IaaS para los escenarios mencionados en el paso 2.

5. Azure actualiza el modelo de servicio de la máquina virtual con la configuración del cifrado y del almacén de claves y configura la máquina virtual cifrada.

 ![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Flujo de trabajo de descifrado
Para deshabilitar el cifrado de disco para las máquinas virtuales IaaS, complete los siguientes pasos de alto nivel:

1. Elija deshabilitar el cifrado (descifrado) en una máquina virtual IaaS en ejecución en Azure a través de la plantilla de Resource Manager de Azure Disk Encryption o los cmdlets de PowerShell, y especifique la configuración de descifrado.

 Este paso deshabilita el cifrado del volumen del sistema operativo o de datos (o ambos) en la máquina virtual IaaS con Windows en ejecución. Sin embargo, como se mencionó en la sección anterior, no se admite deshabilitar el cifrado del disco del sistema operativo. El paso de descifrado se permite solo para unidades de datos en máquinas virtuales Linux siempre que el disco del sistema operativo no esté cifrado.
2. Azure actualiza el modelo de servicio de la máquina virtual y la máquina virtual IaaS se marca como descifrada. El contenido de la máquina virtual ya no se cifra en reposo.

> [!NOTE]
> La operación de deshabilitación del cifrado no elimina el almacén de claves y el material de clave de cifrado (claves de cifrado de BitLocker para sistemas Windows y frase de contraseña para Linux).
 > No se admite la deshabilitación del cifrado de disco del sistema operativo para Linux. El paso de descifrado se permite solo para las unidades de datos en máquinas virtuales con Linux.
La deshabilitación del cifrado del disco de datos para Linux no se admite si la unidad del sistema operativo está cifrada.

## <a name="prerequisites"></a>requisitos previos
Antes de habilitar Azure Disk Encryption en máquinas virtuales IaaS de Azure para los escenarios admitidos que se describen en la sección "Información general", vea los siguientes requisitos previos:

* Para crear recursos en Azure en las regiones admitidas, debe tener una suscripción válida de Azure activa.
* Azure Disk Encryption es compatible con las siguientes versiones de Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 y Windows Server 2016.
* Azure Disk Encryption es compatible con las siguientes versiones de cliente Windows: cliente de Windows 8 y cliente de Windows 10.

> [!NOTE]
> En el caso de Windows Server 2008 R2, debe tener .NET Framework 4.5 instalado para poder habilitar el cifrado en Azure. Se puede instalar desde Windows Update mediante la instalación de la actualización opcional Microsoft .NET Framework 4.5.2 para sistemas basados en x64 con Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Azure Disk Encryption se admite en las siguientes distribuciones y versiones del servidor Linux basadas en la Galería de Azure:

| Distribución de Linux | Versión | Tipo de volumen compatible con el cifrado|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Sistema operativo y disco de datos |
| Ubuntu | 14.04.5-DAILY-LTS | Sistema operativo y disco de datos |
| Ubuntu | 12.10 | Disco de datos |
| Ubuntu | 12.04 | Disco de datos |
| RHEL | 7.4 | Sistema operativo y disco de datos |
| RHEL | 7.3 | Sistema operativo y disco de datos |
| RHEL | LVM 7.3 | Sistema operativo y disco de datos |
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
| SLES | 12-SP1 (Premium) | Disco de datos |
| SLES | HPC 12 | Disco de datos |
| SLES | 11-SP4 (Premium) | Disco de datos |
| SLES | 11 SP4 | Disco de datos |

* Azure Disk Encryption requiere que el almacén de claves y las máquinas virtuales residan en la misma región y suscripción de Azure.

> [!NOTE]
> Si se configuran los recursos en regiones distintas, se producirá un error al habilitar la característica Azure Disk Encryption.

* Para instalar y configurar el almacén de claves para Azure Disk Encryption, vea la sección **Instalación y configuración del almacén de claves para Azure Disk Encryption** en la sección *Requisitos previos* de este artículo.
* Para instalar y configurar la aplicación de Azure AD en Azure Active Directory para usar Azure Disk Encryption, consulte la sección **Instalación de la aplicación de Azure AD en Azure Active Directory** en la sección *Requisitos previos* de este artículo.
* Para instalar y configurar la directiva de acceso al almacén de claves de la aplicación de Azure AD, consulte la sección **Establecimiento de la directiva de acceso al almacén de claves para la aplicación de Azure AD** en la sección *Requisitos previos* de este artículo.
* Para preparar un VHD con Windows precifrado, consulte la sección **Preparación de un VHD con Windows precifrado** en el *Apéndice*.
* Para preparar un VHD con Linux precifrado, consulte la sección **Preparación de un VHD con Linux precifrado** en el *Apéndice*.
* La plataforma Azure necesita acceso a las claves de cifrado o los secretos del almacén de claves para ponerlos a disposición de la máquina virtual cuando arranque y descifre el volumen del sistema operativo de la máquina virtual. Para conceder permisos a la plataforma Azure, configure la propiedad **EnabledForDiskEncryption** en el almacén de claves. Para obtener más información, vea **Instalación y configuración del almacén de claves para Azure Disk Encryption** en el Apéndice.
* El secreto del almacén de claves y las direcciones URL de KEK deben tener versiones. Azure exige esta restricción del control de versiones. Para ver direcciones URL de KEK y de secretos válidas, vea los ejemplos siguientes:

  * Ejemplo de dirección URL de secreto válida: *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Ejemplo de dirección URL de KEK válida: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption no admite que los números de puerto se especifiquen como parte de los secretos del almacén de claves y las direcciones URL de KEK. Para ver ejemplos de direcciones URL de almacén de claves admitidas y no admitidas, vea lo siguiente:

  * Dirección URL de almacén de claves no aceptable: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Dirección URL de almacén de claves aceptable: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Para habilitar la característica Azure Disk Encryption, las máquinas virtuales IaaS deben cumplir los siguientes requisitos de configuración de puntos de conexión de red:
  * Para que un token se conecte al almacén de claves, la máquina virtual IaaS debe poder conectarse a un punto de conexión de Azure Active Directory, \[login.microsoftonline.com\].
  * Para escribir las claves de cifrado en el almacén de claves, la máquina virtual IaaS debe poder conectarse al punto de conexión del almacén de claves.
  * La máquina virtual IaaS debe poder conectarse al punto de conexión de Azure Storage que hospeda el repositorio de extensiones de Azure y la cuenta de Azure Storage que hospeda los archivos VHD.

  > [!NOTE]
  > Si su directiva de seguridad limita el acceso desde máquinas virtuales de Azure a Internet, puede resolver el URI anterior y configurar una regla concreta para permitir la conectividad de salida para las direcciones IP.
  >
  >Configurar y obtener acceso a Azure Key Vault a través de un firewall (https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall)

* Utilice la versión más reciente del SDK de Azure PowerShell para configurar Azure Disk Encryption. Descargue la versión más reciente de [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

 > [!NOTE]
  > En el [SDK de Azure PowerShell (versión 1.1.0)](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016) no se admite Azure Disk Encryption. Si recibe un error relacionado con el uso de Azure PowerShell 1.1.0, vea [Azure Disk Encryption Error Related to Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx) (Error de Azure Disk Encryption relacionado con Azure PowerShell 1.1.0).

* Para ejecutar cualquiera de los comandos de la CLI de Azure y asociarlo a una suscripción de Azure, primero debe instalar la CLI de Azure:
  * Para instalar la CLI de Azure y asociarla a una suscripción de Azure, consulte [Instalación de la CLI de Azure](../cli-install-nodejs.md).
  * Para utilizar la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager, vea [Comandos de la CLI de Azure en el modo de Resource Manager](../virtual-machines/azure-cli-arm-commands.md).

* Cuando se cifra un disco administrado, es requisito previo obligatorio tomar una instantánea o hacer una copia de seguridad del disco fuera de Azure Disk Encryption antes de habilitar el cifrado.  Sin una copia de seguridad, cualquier error inesperado durante el cifrado puede provocar que el disco y la máquina virtual queden inaccesibles sin ninguna opción de recuperación.  Set-AzureRmVMDiskEncryptionExtension no realiza actualmente la copia de seguridad de los discos administrados y se generará un error si se utiliza en un disco administrado, a menos que se haya especificado el parámetro -skipVmBackup.  No es seguro usar este parámetro a menos que ya se haya hecho una copia de seguridad fuera de Azure Disk Encryption.   Cuando se especifique el parámetro -skipVmBackup, el cmdlet no realizará una copia de seguridad del disco administrado antes del cifrado.  Por este motivo, se considera un requisito previo obligatorio asegurarse de que hay disponible una copia de seguridad de la máquina virtual del disco administrado antes de habilitar Azure Disk Encryption por si más adelante fuera necesaria la recuperación.  
> [!NOTE]
 > El parámetro -skipVmBackup nunca debería utilizarse a menos que ya se haya realizado una copia de seguridad o una instantánea fuera de Azure Disk Encryption. 

* La solución Azure Disk Encryption usa el protector de claves externas de BitLocker para máquinas virtuales IaaS con Windows. Para las máquinas virtuales unidas en un dominio, NO cree ninguna directiva de grupo que exija protectores de TPM. Para obtener información acerca de la directiva de grupo para "Permitir BitLocker sin un TPM compatible", consulte la [Referencia de la directiva de grupo de BitLocker](https://technet.microsoft.com/library/ee706521).
* La directiva de BitLocker en las máquinas virtuales unidas a un dominio con una directiva de grupo personalizada debe incluir la siguiente configuración: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Azure Disk Encryption producirá un error cuando la configuración de directiva de grupo personalizada para Bitlocker no sea compatible. En los equipos que no tengan la configuración de directiva correcta, al aplicar la nueva directiva, se obligará a que la nueva directiva se actualice (gpupdate.exe /force) y, a continuación, podría ser necesario reiniciar.  
* Para crear una aplicación de Azure AD, crear un nuevo almacén de claves o configurar el ya existente y habilitar el cifrado, consulte [Azure Disk Encryption prerequisite PowerShell script](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1) (Script de PowerShell de requisito previo de Azure Disk Encryption).
* Para configurar los requisitos previos del cifrado del disco mediante la CLI de Azure, consulte [este script de Bash](https://github.com/ejarvi/ade-cli-getting-started).
* Para usar el servicio Azure Backup para hacer copias de seguridad y restaurar las máquinas virtuales cifradas, cuando esté habilitado el cifrado con Azure Disk Encryption, cifre las máquinas virtuales mediante la configuración de claves de Azure Disk Encryption. El servicio Backup es compatible con las máquinas virtuales cifradas mediante el uso de configuraciones de KEK o no KEK. Vea [Procedimiento de realización de copias de seguridad y restauración de máquinas virtuales cifradas con Azure Backup Encryption](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

* Cuando se cifra un volumen de sistema operativo Linux, tenga en cuenta que actualmente se requiere reiniciar la máquina virtual al final del proceso. Esto puede hacerse mediante el portal, Powershell o CLI.   Para realizar un seguimiento del progreso del cifrado, sondee periódicamente el mensaje de estado que devuelve Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Cuando se complete el cifrado, el mensaje de estado devuelto por este comando lo indicará.  Por ejemplo, "ProgressMessage: disco del sistema operativo cifrado correctamente, reinicie la máquina virtual". En este momento, la máquina virtual se puede reiniciar y usar.  

* Azure Disk Encryption para Linux requiere que los discos de datos tengan un sistema de archivos montado en Linux antes del cifrado

* Los discos de datos montados de forma recursiva no son compatibles con Azure Disk Encryption para Linux. Por ejemplo, si el sistema de destino ha montado un disco en/foo/barra y, a continuación, otro en /foo/barra/baz, el cifrado de /foo/barra/baz se realizará correctamente, pero se producirá un error de cifrado de /foo/barra. 

* Azure Disk Encryption solo se admite en imágenes admitidas de la Galería de Azure que cumplen los requisitos previos mencionados anteriormente. Las imágenes personalizadas de cliente no se admiten debido a los comportamientos de los procesos y los esquemas de las particiones personalizadas que puedan existir en estas imágenes. Además, incluso pueden no ser compatibles las máquinas virtuales basadas en imágenes de la galería que inicialmente cumplieran los requisitos previos, pero fueran modificadas después de su creación.  Por ello, el procedimiento sugerido para cifrar una VM de Linux es partir de una imagen limpia de la galería, cifrar la máquina virtual y, a continuación, agregarle los datos o el software personalizado según sea necesario.  

* Azure Disk Encryption y volumen de datos local: volumen BEK para Windows y /mnt/azure_bek_disk para máquinas virtuales IaaS de Linux para proteger de forma segura la clave de cifrado. No elimine ni modifique ningún contenido de este disco. No desmonte el disco ya que la presencia de la clave de cifrado es necesaria para las operaciones de cifrado en la máquina virtual IaaS. El archivo LÉAME incluido en el volumen contiene detalles adicionales.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Instalación de la aplicación de Azure AD en Azure Active Directory
Si es preciso habilitar el cifrado en una máquina virtual en ejecución en Azure, Azure Disk Encryption genera y escribe las claves de cifrado en su almacén de claves. La administración de claves de cifrado en el almacén de claves necesita la autenticación de Azure AD.

Para ello, cree una aplicación de Azure AD. Encontrará pasos detallados para registrar una aplicación en la sección "Get an Identity for the Application" (Obtener una identidad para la aplicación) de la entrada de blog [Azure Key Vault - Step by Step](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) (Azure Key Vault - Paso a paso). Esta entrada también contiene varios ejemplos útiles sobre la instalación y la configuración del almacén de claves. Para realizar la autenticación, se pueden usar la autenticación basada en secreto de cliente o la autenticación de Azure AD basada en certificado del cliente.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Autenticación basada en secreto de cliente para Azure AD
Las secciones siguientes pueden ayudarle a configurar una autenticación basada en secreto de cliente para Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Creación de una aplicación de Azure AD mediante Azure PowerShell
Utilice el siguiente cmdlet de PowerShell para crear una aplicación de Azure AD:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId es el identificador de cliente de Azure AD y $aadClientSecret es el secreto de cliente que se debe usar posteriormente para habilitar Azure Disk Encryption. Proteja el secreto del cliente de Azure AD apropiadamente.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Configuración del identificador y el secreto de cliente de Azure AD desde Azure Portal
También puede configurar el identificador y el secreto de cliente de Azure AD mediante Azure Portal. Para llevar a cabo esta tarea, haga lo siguiente:

1. Haga clic en la pestaña **Active Directory**.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. Haga clic en **Agregar aplicación** y escriba el nombre de la aplicación.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Haga clic en el botón de flecha y, a continuación, configure las propiedades de la aplicación.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Haga clic en la marca de verificación de la esquina inferior izquierda para finalizar. Aparece la página de configuración de la aplicación y el identificador de cliente de Azure AD se muestra en la parte inferior de la página.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. Guarde el secreto de cliente de Azure AD haciendo clic en el botón **Guardar**. Observe el secreto de cliente de Azure AD en el cuadro de texto de las claves. Protéjalo adecuadamente.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


##### <a name="use-an-existing-application"></a>Uso de una aplicación existente
Para ejecutar los siguientes comandos, obtenga y use el [módulo de PowerShell de Azure AD](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Los siguientes comandos deben ejecutarse desde una ventana nueva de PowerShell. No use Azure PowerShell o la ventana de Azure Resource Manager para ejecutar los comandos. Formulamos esta recomendación porque estos cmdlets están en el módulo MSOnline o en Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Autenticación basada en certificado para Azure AD
> [!NOTE]
> Actualmente no se admite la autenticación basada en certificado de Azure AD en máquinas virtuales con Linux.

Las secciones siguientes muestran cómo configurar una autenticación basada en certificado para Azure AD.

##### <a name="create-an-azure-ad-application"></a>Creación de una aplicación de Azure AD
Para crear una aplicación de Azure AD, ejecute los siguientes cmdlets de PowerShell:

> [!NOTE]
> Reemplace la cadena `yourpassword` siguiente por la contraseña segura y proteja la contraseña.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Una vez finalizado este paso, cargue un archivo PFX en el almacén de claves y habilite la directiva de acceso necesaria para implementar dicho certificado en una máquina virtual.

##### <a name="use-an-existing-azure-ad-application"></a>Uso de una aplicación de Azure AD existente
Si va a configurar una autenticación basada en certificado para una aplicación existente, use los cmdlets de PowerShell que se muestran aquí. Asegúrese de ejecutarlos desde una ventana nueva de PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Una vez finalizado este paso, cargue un archivo PFX en el almacén de claves y habilite la directiva de acceso necesaria para implementar dicho certificado en una máquina virtual.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Carga de un archivo PFX en el almacén de claves
Para obtener una explicación detallada de este proceso, consulte [el blog oficial del equipo de Azure Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). No obstante, para esta tarea todo lo que necesita son los siguientes cmdlets de PowerShell. Asegúrese de que los ejecuta desde la consola de Azure PowerShell.

> [!NOTE]
> Reemplace la cadena `yourpassword` siguiente por la contraseña segura y proteja la contraseña.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Implementación de un certificado del almacén de claves en una máquina virtual existente
Cuando termine de cargar el archivo PFX, implemente un certificado del almacén de claves en una máquina virtual existente con lo siguiente:
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Establecimiento de la directiva de acceso del almacén de claves para la aplicación de Azure AD
La aplicación de Azure AD necesita derechos de acceso a las claves o secretos del almacén. Use el cmdlet [`Set-AzureKeyVaultAccessPolicy`](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) para conceder permisos a la aplicación, con el identificador de cliente (que se generó cuando se registró la aplicación) como valor del parámetro _–ServicePrincipalName_. Para obtener más información, consulte la entrada de blog [Azure Key Vault - Step by Step](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) (Azure Key Vault - Paso a paso). Aquí se muestra un ejemplo de cómo realizar esta tarea mediante PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption requiere que configure las siguientes directivas de acceso a la aplicación de cliente de Azure AD: los permisos _WrapKey_ y _Set_.

## <a name="terminology"></a>Terminología
Para comprender algunos de los términos comunes que usa esta tecnología, utilice la siguiente tabla de terminología:

| Terminología | Definición |
| --- | --- |
| Azure AD | Azure AD es [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Una cuenta de Azure AD es un requisito previo para autenticar, almacenar y recuperar secretos del almacén de claves. |
| Azure Key Vault | Key Vault es un servicio de administración de claves criptográficas basado en módulos de seguridad de hardware validados por el Estándar federal de procesamiento de información (FIPS), que ayuda a proteger las claves criptográficas y los secretos confidenciales. Para obtener más información, consulte la documentación de [Key Vault](https://azure.microsoft.com/services/key-vault/). |
| ARM | Administrador de recursos de Azure |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) es una tecnología de cifrado de volúmenes de Windows reconocida por el sector que se usa para habilitar el cifrado de disco en máquinas virtuales IaaS con Windows. |
| BEK | Las claves de cifrado de BitLocker se usan para cifrar el volumen de arranque del sistema operativo y los volúmenes de datos. Las claves de BitLocker están protegidas en un almacén de claves como secretos. |
| CLI | Vea [Instalación de la CLI de Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) es el subsistema de cifrado transparente de disco basado en Linux que se usa para habilitar el cifrado de disco en las máquinas virtuales IaaS con Linux. |
| KEK | La clave de cifrado de claves (KEK) es la clave asimétrica (RSA 2048) que puede usar para proteger o encapsular el secreto. Se puede proporcionar una clave protegida mediante módulos de seguridad de hardware (HSM) o una clave protegida mediante software. Para obtener más información, consulte la documentación de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| cmdlets de PS | Consulte [Cmdlets de Azure PowerShell](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Instalación y configuración del almacén de claves para Azure Disk Encryption
Azure Disk Encryption ayuda a proteger las claves de cifrado de disco y los secretos en su almacén de claves. Para configurar el almacén de claves para Azure Disk Encryption, complete los pasos en cada una de las siguientes secciones.

#### <a name="create-a-key-vault"></a>Creación de un Almacén de claves
Para crear un almacén de claves, use una de las siguientes opciones:

* [Plantilla de Resource Manager llamada "101-Key-Vault-Create"](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Cmdlets de almacén de claves de Azure PowerShell](/powershell/module/azurerm.keyvault/#key_vault)
* Administrador de recursos de Azure
* Cómo [Proteger un almacén de claves](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Si ya ha configurado un almacén de claves para su suscripción, vaya a la sección siguiente.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Configuración de una clave de cifrado de claves (KEK) (opcional)
Si desea usar una KEK para una brindar una capa adicional de seguridad para las claves de cifrado de BitLocker, agregue una KEK a su almacén de claves. Use el cmdlet [`Add-AzureKeyVaultKey`](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) para crear una clave de cifrado de claves en el almacén de claves. También puede importar una KEK en el HSM de administración de claves local. Para obtener más información, consulte la [documentación de Key Vault](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Para agregar la KEK, vaya a Azure Resource Manager o use la interfaz del almacén de claves.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Configuración de los permisos del almacén de claves
La plataforma Azure necesita acceso a las claves de cifrado o secretos del almacén de claves para ponerlos a disposición de la máquina virtual para el proceso de arranque y descifrado de los volúmenes. Para conceder permisos a la plataforma Azure, establezca la propiedad **EnabledForDiskEncryption** en el almacén de claves mediante el cmdlet de PowerShell del almacén de claves:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

También puede establecer la propiedad **EnabledForDiskEncryption** por medio del [Explorador de recursos de Azure](https://resources.azure.com).

Como se indicó anteriormente, debe establecer la propiedad **EnabledForDiskEncryption** en el almacén de claves. De lo contrario, se producirá un error en la implementación.

Puede configurar las directivas de acceso para la aplicación de Azure AD desde la interfaz de almacén de claves, como se muestra aquí:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

En la pestaña **Directivas de acceso avanzado**, asegúrese de que el almacén de claves está habilitado para Azure Disk Encryption:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Escenarios de implementación del cifrado de disco y experiencias de usuario
Puede habilitar muchos escenarios de cifrado de disco, y los pasos pueden variar en función del escenario. En las secciones siguientes se tratan los escenarios con mayor detalle.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Habilitado del cifrado en nuevas máquinas virtuales IaaS creadas desde Marketplace
Puede habilitar el cifrado de disco en nuevas máquinas virtuales IaaS con Windows desde Marketplace en Azure mediante el uso de la [plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. En la plantilla de inicio rápido de Azure, haga clic en **Implementar en Azure**, especifique la configuración de cifrado en la hoja **Parámetros** y, a continuación, haga clic en **Aceptar**.

2. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato y haga clic en **Crear** para habilitar el cifrado en una máquina virtual IaaS nueva.

> [!NOTE]
> Esta plantilla crea una nueva máquina virtual con Windows cifrada que usa una imagen de la galería de Windows Server 2012.

Puede habilitar el cifrado de disco en una nueva máquina virtual IaaS con RedHat Linux 7.2 con una matriz RAID-0 de 200 GB mediante esta [plantilla de Resource Manager](https://aka.ms/fde-rhel). Después de implementar la plantilla, compruebe el estado de cifrado de la máquina virtual mediante el cmdlet `Get-AzureRmVmDiskEncryptionStatus`, tal como se describe en [Cifrado de la unidad del sistema operativo en una máquina virtual con Linux en ejecución](#encrypting-os-drive-on-a-running-linux-vm). Cuando la máquina devuelva un estado _VMRestartPending_, reinicie la máquina virtual.

En la tabla siguiente figuran los parámetros de la plantilla de Resource Manager para nuevas máquinas virtuales en el escenario de Marketplace con el identificador de cliente de Azure AD:

| . | DESCRIPCIÓN |
| --- | --- |
| adminUserName | Nombre de usuario administrador para la máquina virtual. |
| adminPassword | Contraseña de usuario administrador para la máquina virtual. |
| newStorageAccountName | Nombre de la cuenta de almacenamiento que almacena el sistema operativo y los VHD de datos. |
| vmSize | Tamaño de la máquina virtual. Actualmente, solo se admiten las series A, D y G estándar. |
| virtualNetworkName | Nombre de la red virtual a la que debería pertenecer la NIC de la máquina virtual. |
| subnetName | Nombre de la subred en la red virtual a la que debería pertenecer la NIC de la máquina virtual. |
| AADClientID | Identificador de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves. |
| AADClientSecret | Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves. |
| keyVaultURL | Dirección URL del almacén de claves en el que se que debe cargar la clave de BitLocker. Se puede obtener mediante el cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | Dirección URL de la clave de cifrado de claves que se utiliza para cifrar la clave generada por BitLocker (opcional). |
| keyVaultResourceGroup | Grupo de recursos del almacén de claves. |
| vmName | Nombre de la máquina virtual en que se va a realizar la operación de cifrado. |

> [!NOTE]
> _KeyEncryptionKeyURL_ es un parámetro opcional. Puede aportar su propia KEK para proteger aún más la clave de cifrado de datos (frase de contraseña secreta) en el almacén de claves.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Habilitación del cifrado en nuevas máquinas virtuales IaaS creadas a partir del VHD cifrado del cliente y las claves de cifrado
En este escenario, se puede habilitar el cifrado mediante la plantilla de Resource Manager, cmdlets de PowerShell o comandos de la CLI. Las siguientes secciones explican con más detalle la plantilla de Resource Manager y los comandos de la CLI.

Siga las instrucciones de una de estas secciones para preparar las imágenes previamente cifradas que se pueden utilizar en Azure. Una vez creada la imagen, puede seguir los pasos de la sección siguiente para crear una VM cifrada de Azure.

* [Preparación de un VHD con Windows precifrado](#preparing-a-pre-encrypted-windows-vhd)
* [Preparación de un VHD con Linux precifrado](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Uso de la plantilla de Resource Manager
Puede habilitar el cifrado de disco en el VHD cifrado mediante el uso de la [plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. En la plantilla de inicio rápido de Azure, haga clic en **Implementar en Azure**, especifique la configuración de cifrado en la hoja **Parámetros** y, a continuación, haga clic en **Aceptar**.

2. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato y haga clic en **Crear** para habilitar el cifrado en una máquina virtual IaaS nueva.

En la tabla siguiente figuran los parámetros de la plantilla de Resource Manager para el VHD cifrado:

| . | DESCRIPCIÓN |
| --- | --- |
| newStorageAccountName | Nombre de la cuenta de almacenamiento que almacena el VHD del sistema operativo cifrado. Esta cuenta de almacenamiento se debe haber creado en el mismo grupo de recursos y en la misma ubicación que la máquina virtual. |
| osVhdUri | Identificador URI del VHD de sistema operativo de la cuenta de almacenamiento. |
| osType | Tipo de producto de sistema operativo (Windows o Linux). |
| virtualNetworkName | Nombre de la red virtual a la que debería pertenecer la NIC de la máquina virtual. El nombre se debe haber creado en el mismo grupo de recursos y en la misma ubicación que la máquina virtual. |
| subnetName | El nombre de la subred en la red virtual a la que debería pertenecer la NIC de la máquina virtual. |
| vmSize | Tamaño de la máquina virtual. Actualmente, solo se admiten las series A, D y G estándar. |
| keyVaultResourceID | El valor ResourceID que identifica el recurso de almacén de claves en Azure Resource Manager. Se puede obtener mediante el cmdlet de PowerShell `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | La dirección URL de la clave de cifrado del disco que se puede configurar en el almacén de claves. |
| keyVaultKekUrl | La dirección URL de la clave de cifrado de claves para cifrar la clave de cifrado de disco generada. |
| vmName | Nombre de la máquina virtual IaaS. |

#### <a name="using-powershell-cmdlets"></a>Uso de cmdlets de PowerShell
Puede habilitar el cifrado de disco en el VHD cifrado mediante el cmdlet de PowerShell [`Set-AzureRmVMOSDisk`](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Uso de comandos de la CLI
Para habilitar el cifrado de disco para este escenario mediante comandos de la CLI, haga lo siguiente:

1. Configure las directivas de acceso en el almacén de claves:

   * Configure el marcador **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Configure los permisos para la aplicación de Azure AD para escribir los secretos en el almacén de claves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Para habilitar el cifrado en una máquina virtual existente o en ejecución, escriba:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Obtenga el estado de cifrado:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Para habilitar el cifrado en una nueva máquina virtual desde el VHD cifrado, utilice los siguientes parámetros con el comando `azure vm create`:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Habilitación del cifrado en una máquina virtual IaaS con Windows existente o en ejecución en Azure
En este escenario, se puede habilitar el cifrado mediante la plantilla de Resource Manager, cmdlets de PowerShell o comandos de la CLI. En las siguientes secciones se explica con más detalle cómo habilitarlo mediante el uso de la plantilla de Resource Manager y los comandos de la CLI.

#### <a name="using-the-resource-manager-template"></a>Uso de la plantilla de Resource Manager
Puede habilitar el cifrado de disco en máquinas virtuales IaaS con Windows existentes o en ejecución en Azure mediante la [plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. En la plantilla de inicio rápido de Azure, haga clic en **Implementar en Azure**, especifique la configuración de cifrado en la hoja **Parámetros** y, a continuación, haga clic en **Aceptar**.

2. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato y haga clic en **Crear** para habilitar el cifrado en la máquina virtual IaaS existente o en ejecución.

En la tabla siguiente figuran los parámetros de la plantilla de Resource Manager para máquinas virtuales existentes o en ejecución que usan un identificador de cliente de Azure AD:

| . | DESCRIPCIÓN |
| --- | --- |
| AADClientID | Identificador de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves. |
| AADClientSecret | Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves. |
| keyVaultName | Nombre del almacén de claves en el que se que debe cargar la clave de BitLocker. Se puede obtener mediante el cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Dirección URL de la clave de cifrado de claves que se utiliza para cifrar la clave generada por BitLocker. Este parámetro es opcional si selecciona **nokek** en la lista desplegable de UseExistingKek. Si selecciona **kek** en la lista desplegable de UseExistingKek, debe proporcionar el valor de _keyEncryptionKeyURL_. |
| volumeType | Tipo de volumen en que se realiza la operación de cifrado. Los valores válidos son _SO_, _Datos_ y _Todo_. |
| sequenceVersion | Versión de la secuencia de la operación de BitLocker. Incremente el número de versión cada vez que se realice una operación de cifrado de disco en la misma máquina virtual. |
| vmName | Nombre de la máquina virtual en que se va a realizar la operación de cifrado. |

> [!NOTE]
> _KeyEncryptionKeyURL_ es un parámetro opcional. Puede aportar su propia KEK para proteger aún más la clave de cifrado de datos (secreto de cifrado de BitLocker) en el almacén de claves.

#### <a name="using-powershell-cmdlets"></a>Uso de cmdlets de PowerShell
Para obtener información acerca de cómo habilitar el cifrado con Azure Disk Encryption mediante el uso de cmdlets de PowerShell, vea las entradas de blog [Explore Azure Disk Encryption with Azure PowerShell - Part 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) (Exploración de Azure Disk Encryption con Azure PowerShell - Parte 1) y [Explore Azure Disk Encryption with Azure PowerShell - Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) (Exploración de Azure Disk Encryption con Azure PowerShell - Parte 2).

#### <a name="using-cli-commands"></a>Uso de comandos de la CLI
Para habilitar el cifrado en una máquina virtual IaaS con Windows existente o en ejecución en Azure mediante comandos de la CLI, haga lo siguiente:

1. Para establecer las directivas de acceso en el almacén de claves:
   * Configure el marcador **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Configure los permisos para la aplicación de Azure AD para escribir los secretos en el almacén de claves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Para habilitar el cifrado en una máquina virtual existente o en ejecución:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Para obtener el estado de cifrado:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Para habilitar el cifrado en una nueva máquina virtual desde el VHD cifrado, utilice los siguientes parámetros con el comando `azure vm create`:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Habilitación del cifrado en una máquina virtual IaaS con Linux existente o en ejecución en Azure
Puede habilitar el cifrado de disco en una máquina virtual IaaS con Linux existente o en ejecución en Azure mediante la [plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Haga clic en **Implementar en Azure** en la plantilla de inicio rápido de Azure, especifique la configuración de cifrado en la hoja **Parámetros** y, a continuación, haga clic en **Aceptar**.

2. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato y haga clic en **Crear** para habilitar el cifrado en la máquina virtual IaaS existente o en ejecución.

En la tabla siguiente figuran los parámetros de la plantilla de Resource Manager para máquinas virtuales existentes o en ejecución que usan un identificador de cliente de Azure AD:

| . | DESCRIPCIÓN |
| --- | --- |
| AADClientID | Identificador de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves. |
| AADClientSecret | Secreto de cliente de la aplicación de Azure AD que tiene permisos para escribir secretos en el almacén de claves. |
| keyVaultName | Nombre del almacén de claves en el que se que debe cargar la clave de BitLocker. Se puede obtener mediante el cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Dirección URL de la clave de cifrado de claves que se utiliza para cifrar la clave generada por BitLocker. Este parámetro es opcional si selecciona **nokek** en la lista desplegable de UseExistingKek. Si selecciona **kek** en la lista desplegable de UseExistingKek, debe proporcionar el valor de _keyEncryptionKeyURL_. |
| volumeType | Tipo de volumen en que se realiza la operación de cifrado. Los valores admitidos válidos son _OS_ o _All_ (consulte las distribuciones de Linux admitidas y sus versiones para los discos de sistema operativo y datos en la sección anterior Requisitos previos). |
| sequenceVersion | Versión de la secuencia de la operación de BitLocker. Incremente el número de versión cada vez que se realice una operación de cifrado de disco en la misma máquina virtual. |
| vmName | Nombre de la máquina virtual en que se va a realizar la operación de cifrado. |
| passPhrase | Escriba una frase de contraseña segura como clave de cifrado de datos. |

> [!NOTE]
> _KeyEncryptionKeyURL_ es un parámetro opcional. Puede aportar su propia KEK para proteger aún más la clave de cifrado de datos (frase de contraseña secreta) en el almacén de claves.

#### <a name="cli-commands"></a>Comandos de la CLI
Puede habilitar el cifrado de disco en el VHD cifrado mediante la instalación y el uso del [comando de la CLI](../cli-install-nodejs.md). Para habilitar el cifrado en una máquina virtual IaaS con Linux existente o en ejecución en Azure mediante comandos de la CLI, haga lo siguiente:

1. Configuración de directivas de acceso en el almacén de claves:

 * Configure el marcador **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Configure los permisos para la aplicación de Azure AD para escribir los secretos en el almacén de claves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Para habilitar el cifrado en una máquina virtual existente o en ejecución:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Obtenga el estado de cifrado:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Para habilitar el cifrado en una nueva máquina virtual desde el VHD cifrado, utilice los siguientes parámetros con el comando `azure vm create`:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Obtención del estado de cifrado de una máquina virtual IaaS cifrada
Puede obtener el estado del cifrado mediante Azure Resource Manager, los [cmdlets de PowerShell](/powershell/azure/overview) o los comandos de la CLI. En las siguientes secciones se explica cómo usar los comandos de la CLI y de Azure Portal para obtener el estado del cifrado.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Obtención del estado del cifrado de una máquina virtual con Windows cifrada mediante Azure Resource Manager
Se puede obtener el estado del cifrado de la máquina virtual IaaS desde Azure Resource Manager haciendo lo siguiente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y, a continuación, haga clic en **Virtual Machines** en el panel izquierdo para ver una vista resumida de las máquinas virtuales en su suscripción. Para filtrar la vista de máquinas virtuales, seleccione el nombre de la suscripción en la lista desplegable **Suscripción**.

2. En la parte superior de la página **Virtual Machines**, haga clic en **Columnas**.

3. En la hoja de **elección de columna**, seleccione **Cifrado del disco** y, a continuación, haga clic en **Actualizar**. Debería ver la columna de cifrado de disco, que muestra el estado del cifrado _Habilitado_ o _No habilitado_ de cada máquina virtual, como se muestra en la ilustración siguiente:

 ![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Obtención del estado de cifrado de una máquina virtual IaaS (Windows o Linux) cifrada mediante el cmdlet de PowerShell de cifrado del disco
Puede obtener el estado de cifrado de la máquina virtual IaaS desde el cmdlet de PowerShell de cifrado del disco `Get-AzureRmVMDiskEncryptionStatus`. Para obtener la configuración de cifrado para la máquina virtual, escriba lo siguiente:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Puede inspeccionar la salida de _Get-AzureRmVMDiskEncryptionStatus_ para las direcciones URL de clave de cifrado.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Los valores de configuración de OSVolumeEncrypted y DataVolumesEncrypted se establecen en _Encrypted_, lo que muestra que ambos volúmenes se cifran con Azure Disk Encryption. Para obtener información acerca de cómo habilitar el cifrado con Azure Disk Encryption mediante el uso de cmdlets de PowerShell, vea las entradas de blog [Explore Azure Disk Encryption with Azure PowerShell - Part 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) (Exploración de Azure Disk Encryption con Azure PowerShell - Parte 1) y [Explore Azure Disk Encryption with Azure PowerShell - Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) (Exploración de Azure Disk Encryption con Azure PowerShell - Parte 2).

> [!NOTE]
> En máquinas virtuales de Linux, el cmdlet `Get-AzureRmVMDiskEncryptionStatus` tarda tres o cuatro minutos en informar sobre el estado de cifrado.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Obtención del estado del cifrado de la máquina virtual IaaS desde el comando de la CLI de cifrado de disco
Puede obtener el estado de cifrado de la VM IaaS mediante el comando de la CLI de cifrado del disco `azure vm show-disk-encryption-status`. Para obtener la configuración del cifrado para su máquina virtual, especifique la sesión de la CLI de Azure:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Deshabilitación del cifrado en máquinas virtuales IaaS con Windows en ejecución
En una máquina virtual IaaS con Windows o Linux en ejecución, se puede deshabilitar el cifrado con la plantilla de Resource Manager o con los cmdlets de PowerShell de Azure Disk Encryption y especificar la configuración de descifrado.

##### <a name="windows-vm"></a>Máquina virtual de Windows
El paso de deshabilitación del cifrado deshabilita el cifrado del volumen del sistema operativo o de datos (o ambos) en la máquina virtual IaaS con Windows en ejecución. No es posible deshabilitar el volumen del sistema operativo y dejar el volumen de datos cifrado. Cuando se realiza el paso de deshabilitación del cifrado, el modelo de implementación clásica de Azure actualiza el modelo de servicio de la máquina virtual y la máquina virtual IaaS con Windows se marca como descifrada. El contenido de la máquina virtual ya no se cifra en reposo. El descifrado no elimina el almacén de claves ni el material de clave de cifrado (claves de cifrado de BitLocker para Windows y frase de contraseña para Linux).

##### <a name="linux-vm"></a>Máquina virtual de Linux
El paso de deshabilitación del cifrado deshabilita el cifrado del volumen de datos en la máquina virtual IaaS con Linux en ejecución. Este paso solo funciona si el disco del sistema operativo no está cifrado.

> [!NOTE]
> No se permite deshabilitar el cifrado en el disco del sistema operativo en máquinas virtuales Linux.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Deshabilitado del cifrado en una máquina virtual IaaS existente o en ejecución
Puede deshabilitar el cifrado de disco en máquinas virtuales IaaS con Windows en ejecución mediante el uso de la [plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. En la plantilla de inicio rápido de Azure, haga clic en **Implementar en Azure**, especifique la configuración de descifrado en la hoja **Parámetros** y, a continuación, haga clic en **Aceptar**.

2. Seleccione la suscripción, el grupo de recursos, la ubicación del grupo de recursos, los términos legales y el contrato y haga clic en **Crear** para habilitar el cifrado en una máquina virtual IaaS nueva.

Para máquinas virtuales con Linux, puede deshabilitar el cifrado mediante el uso de la plantilla para [deshabilitar el cifrado en una máquina virtual con Linux en ejecución](https://aka.ms/decrypt-linuxvm).

En la tabla siguiente se enumeran los parámetros de la plantilla de Resource Manager para deshabilitar el cifrado en una máquina virtual IaaS en ejecución:

| . | DESCRIPCIÓN |
| --- | --- |
| vmName | Nombre de la máquina virtual en que se va a realizar la operación de cifrado.
| volumeType | Tipo de volumen en que se realiza la operación de descifrado. Los valores válidos son _SO_, _Datos_ y _Todo_. No es posible deshabilitar el cifrado en el volumen de arranque o del SO de una máquina virtual IaaS con Windows en ejecución sin deshabilitar el cifrado del volumen _Datos_. Tenga en cuenta también que no se permite deshabilitar el cifrado en el disco del sistema operativo en máquinas virtuales con Linux. |
| sequenceVersion | Versión de la secuencia de la operación de BitLocker. Incremente el número de versión cada vez que se realice una operación de descifrado de disco en la misma máquina virtual. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Deshabilitado del cifrado en una máquina virtual IaaS existente o en ejecución
Para deshabilitar el cifrado en una VM IaaS existente o en ejecución mediante el cmdlet de PowerShell, consulte [`Disable-AzureRmVMDiskEncryption`](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Este cmdlet admite máquinas virtuales Linux y Windows. Para deshabilitar el cifrado, instala una extensión en la máquina virtual. Si no se especifica el parámetro _Name_, se crea una extensión con el nombre predeterminado _AzureDiskEncryption para máquinas virtuales con Windows_.

En las máquinas virtuales con Linux, se utiliza la extensión AzureDiskEncryptionForLinux.

> [!NOTE]
> Este cmdlet reinicia la máquina virtual.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Habilitar el cifrado en máquinas virtuales IaaS precifradas con el disco administrado de Azure
Use la plantilla de ARM del disco administrado de Azure para crear una máquina virtual cifrada desde un VHD cifrado previamente con la plantilla de ARM ubicado en   
[Crear un nuevo disco administrado cifrado desde un VHD cifrado previamente/blob de almacenamiento] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Habilitar el cifrado en máquinas virtuales IaaS nuevas de Linux con el disco administrado de Azure
Use la plantilla de ARM del disco administrado de Azure para crear una máquina virtual IaaS cifrada de Linux nueva con la plantilla de ARM ubicada en   
[Implementación de RHEL 7.2 con cifrado de disco completo] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Habilitar el cifrado en máquinas virtuales IaaS nuevas de Windows con el disco administrado de Azure
 Use la plantilla de ARM del disco administrado de Azure para crear una máquina virtual IaaS cifrada de Linux nueva con la plantilla de ARM ubicada en   
 [Crear una máquina virtual IaaS de disco administrado cifrado de Windows nueva a partir de una imagen de la galería] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Es obligatorio crear una instantánea o una copia de seguridad de una instancia de la máquina virtual basada en un disco administrado fuera de Azure Disk Encryption y antes de habilitar esta característica.  Puede tomar una instantánea del disco administrado desde el portal o se puede usar Azure Backup.  Las copias de seguridad garantizan que es posible disponer de una opción de recuperación en el caso de que se produzca un error inesperado durante el cifrado.  Una vez que se realiza una copia de seguridad, el cmdlet Set-AzureRmVMDiskEncryptionExtension puede utilizarse para cifrar los discos administrados especificando el parámetro -skipVmBackup.  Este comando producirá un error en las máquinas virtuales basadas en un disco administrado hasta que se realice una copia de seguridad y se especifique este parámetro.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Actualización de la configuración del cifrado de una máquina virtual cifrada no premium existente
  Utilice las interfaces existentes permitidas de Azure Disk Encryption para ejecutar máquinas virtuales [cmdlets de PS, plantillas de CLI o ARM] para actualizar la configuración del cifrado, como ID de cliente AAD/secreto de cliente, clave de cifrado de clave [KEK], clave de cifrado de BitLocker para VM de Windows o la frase de contraseña para VM de Linux, etc. La configuración de cifrado de la actualización se admite en las máquinas virtuales con y sin Premium Storage.

## <a name="appendix"></a>Anexo
### <a name="connect-to-your-subscription"></a>su suscripción
Antes de continuar, revise la sección *Requisitos previos* de este artículo. Después de asegurarse de que se cumplen todos los requisitos previos, conéctese a su suscripción haciendo lo siguiente:

1. Inicie una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:

    `Login-AzureRmAccount`

2. Si tiene varias suscripciones y desea especificar que se use una en concreto, escriba lo siguiente para ver las suscripciones de su cuenta:

    `Get-AzureRmSubscription`

3. Para especificar la suscripción que desea usar, escriba:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Para comprobar que la suscripción configurada es correcta, escriba:

    `Get-AzureRmSubscription`

5. Para confirmar que están instalados los cmdlets de Azure Disk Encryption, escriba:

    `Get-command *diskencryption*`

6. El siguiente resultado confirma la instalación de PowerShell de Azure Disk Encryption:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Preparación de un VHD con Windows precifrado
Las secciones siguientes son necesarias para preparar un VHD con Windows precifrado para implementarlo como VHD cifrado en IaaS de Azure. Utilice la información para preparar y arrancar una máquina virtual (VHD) con Windows nueva en Azure Site Recovery o Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Actualización de una directiva de grupo para permitir un módulo no TPM para la protección del sistema operativo
Configure la opción de la directiva de grupos de BitLocker denominada **Cifrado de unidad BitLocker**, que se encuentra en **Directiva de equipo local** > **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows**. Cambie esta opción a: **Unidades del sistema operativo** > **Requerir autenticación adicional al iniciar** > **Permitir BitLocker sin un TPM compatible**, como se muestra en la ilustración siguiente:

![Microsoft Antimalware en Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Instalación de componentes de características de BitLocker
Para Windows Server 2012 y versiones posteriores, utilice el siguiente comando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Para Windows Server 2008 R2, utilice el siguiente comando:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparación del volumen del sistema operativo para BitLocker mediante `bdehdcfg`
Ejecute el comando siguiente para comprimir la partición del sistema operativo y preparar la máquina para BitLocker:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Protección del volumen del sistema operativo mediante BitLocker
Use el comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) para habilitar el cifrado en el volumen de arranque mediante un protector de clave externo. También puede colocar la clave externa (archivo .bek) en el disco externo o el volumen. El cifrado se habilita en el volumen de sistema o de arranque la próxima vez que se reinicie el equipo.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Prepare la máquina virtual con un VHD de datos o recursos separado para obtener la clave externa mediante BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Cifrado de la unidad del sistema operativo en una máquina virtual con Linux en ejecución
El cifrado de la unidad del sistema operativo en una máquina virtual con Linux en ejecución se admite en las distribuciones siguientes:

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>Requisitos previos para el cifrado de disco del sistema operativo

* La máquina virtual debe crearse a partir de la imagen de Marketplace en Azure Resource Manager.
* La máquina virtual de Azure con al menos 4 GB de RAM (el tamaño recomendado es 7 GB).
* (Para RHEL y CentOS) Deshabilite SELinux. Para deshabilitar SELinux, consulte "4.4.2. Disabling SELinux" (Deshabilitar SELinux) en el documento [SELinux User's and Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) (Guía del administrador y del usuario de SELinux) en la máquina virtual.
* Después de deshabilitar SELinux, reinicie la máquina virtual al menos una vez.

##### <a name="steps"></a>Pasos
1. Cree una máquina virtual mediante una de las distribuciones especificadas anteriormente.

 Para CentOS 7.2, se admite el cifrado de disco del sistema operativo a través de una imagen especial. Para usar esta imagen, especifique "7.2n" como SKU al crear la máquina virtual:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Configure la máquina virtual según sus necesidades. Si va a cifrar todas las unidades (sistema operativo y datos), las unidades de datos tienen que especificarse y montarse desde /etc/fstab.

 > [!NOTE]
 > Use UUID=... para especificar unidades de datos en /etc/fstab en lugar de especificar el nombre del dispositivo de bloque (por ejemplo, /dev/sdb1). Durante el cifrado, se cambia el orden de las unidades en la máquina virtual. Si la máquina virtual depende de un orden específico de dispositivos de bloque, se producirá un error de montaje después del cifrado.

3. Cierre las sesiones de SSH.

4. Para cifrar el sistema operativo, especifique volumeType como **All** o **OS** al [habilitar el cifrado](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Todos los procesos de espacio de usuario que no se ejecutan como servicios `systemd` deben terminarse con `SIGKILL`. Reinicie la máquina virtual. Al habilitar el cifrado del disco de sistema operativo en una máquina virtual en ejecución, tenga en cuenta el tiempo de inactividad de la máquina virtual.

5. Supervise periódicamente el progreso del cifrado con las instrucciones de la [sección siguiente](#monitoring-os-encryption-progress).

6. Cuando Get-AzureRmVmDiskEncryptionStatus muestra "VMRestartPending", reinicie la máquina virtual iniciando sesión en ella o a través del portal, PowerShell o la CLI.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Antes de reiniciar, se recomienda que guarde los [diagnósticos de arranque](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) de la máquina virtual.

#### <a name="monitoring-os-encryption-progress"></a>Supervisión del progreso de cifrado del sistema operativo
Hay tres maneras de supervisar el progreso de cifrado del sistema operativo:

* Use el cmdlet `Get-AzureRmVmDiskEncryptionStatus` e inspeccione el campo ProgressMessage:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Una vez que la máquina virtual llega al mensaje de inicio del cifrado de disco del sistema operativo, el proceso tarda aproximadamente entre 40 y 50 minutos en una máquina virtual de almacenamiento Premium.

 Debido al [problema 388](https://github.com/Azure/WALinuxAgent/issues/388) de WALinuxAgent, `OsVolumeEncrypted` y `DataVolumesEncrypted` se muestran como `Unknown` en algunas distribuciones. En WALinuxAgent versión 2.1.5 y versiones posteriores, este problema se ha corregido automáticamente. En caso de que se vea `Unknown` en la salida, puede comprobar el estado de cifrado del disco mediante el Explorador de recursos de Azure.

 Vaya al [Explorador de recursos de Azure](https://resources.azure.com/) y expanda esta jerarquía en el panel de selección a la izquierda:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 En el objeto InstanceView, desplácese hacia abajo para ver el estado de cifrado de las unidades de disco.

 ![Vista de instancia de máquina virtual](./media/azure-security-disk-encryption/vm-instanceview.png)

* Fíjese en los [diagnósticos de arranque](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Los mensajes de la extensión ADE deben llevar el prefijo `[AzureDiskEncryption]`.

* Inicie sesión en la máquina virtual a través de SSH y obtenga el registro de la extensión de:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Se recomienda que no inicie sesión en la máquina virtual mientras el cifrado del sistema operativo esté en curso. Copie los registros solo cuando los otros dos métodos no den resultado.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Preparación de un VHD con Linux precifrado
##### <a name="ubuntu-16"></a>Ubuntu 16
Configure el cifrado durante la instalación de la distribución haciendo lo siguiente:

1. Seleccione **Configure encrypted volumes** (Configurar volúmenes cifrados) al realizar la partición de los discos.

 ![Instalación de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Cree una unidad de arranque independiente, que no debe estar cifrada. Cifre la unidad raíz.

 ![Instalación de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Proporcione una frase de contraseña. Se trata de la frase de contraseña que se carga en el almacén de claves.

 ![Instalación de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Finalice la partición.

 ![Instalación de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Cuando arranque la máquina virtual y se le pida una frase de contraseña, utilice la frase de contraseña que especificó en el paso 3.

 ![Instalación de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Prepare la máquina virtual para su carga en Azure con [estas instrucciones](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). No ejecute todavía el último paso (desaprovisionamiento de la máquina virtual).

Configure el cifrado para que funcione con Azure haciendo lo siguiente:

1. Cree un archivo en /usr/local/sbin/azure_crypt_key.sh con el contenido del script siguiente. Preste atención a KeyFileName, porque es el nombre que Azure ha puesto al archivo de frase de contraseña.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Cambie la configuración de cifrado en */etc/crypttab*. Debería ser parecido a este:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Si está editando *azure_crypt_key.sh* en Windows y lo ha copiado en Linux, ejecute `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Agregue permisos de ejecución al script:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Edite */etc/initramfs-tools/modules* anexando líneas:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Ejecute `update-initramfs -u -k all` para actualizar initramfs a fin de que `keyscript` surta efecto.

7. Ahora puede desaprovisionar la máquina virtual.

 ![Instalación de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continúe con el paso siguiente y [cargue el VHD](#upload-encrypted-vhd-to-an-azure-storage-account) en Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Para configurar el cifrado durante la instalación de distribución, haga lo siguiente:
1. Al crear particiones de los discos, seleccione **Encrypt Volume Group** (Cifrar el grupo de volúmenes) y, a continuación, escriba una contraseña. Esta es la contraseña que se cargará en el almacén de claves.

 ![Instalación de openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Arranque la máquina virtual con la contraseña.

 ![Instalación de openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Prepare la máquina virtual para cargarla en Azure con las instrucciones de [Preparación de una máquina virtual SLES u openSUSE para Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). No ejecute todavía el último paso (desaprovisionamiento de la máquina virtual).

Para configurar el cifrado de manera que funcione con Azure, haga lo siguiente:
1. Edite /etc/dracut.conf y agregue la línea siguiente:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Agregue comentarios a estas líneas al final del archivo /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Agregue la siguiente línea al principio del archivo /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
 ```
    DRACUT_SYSTEMD=0
 ```
Y cambie todas las apariciones de:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
a:
```
    if [ 1 ]; then
```
4. Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh y anéxelo a "# Open LUKS device":

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Ejecute `/usr/sbin/dracut -f -v` para actualizar el initrd.

6. Ahora puede desaprovisionar la máquina virtual y [cargar el VHD](#upload-encrypted-vhd-to-an-azure-storage-account) en Azure.

##### <a name="centos-7"></a>CentOS 7
Para configurar el cifrado durante la instalación de distribución, haga lo siguiente:
1. Seleccione **Encrypt my data** (Cifrar mis datos) al crear particiones en discos.

 ![Instalación de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Asegúrese de que **Encrypt** (Cifrar) está seleccionado para la partición raíz.

 ![Instalación de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Proporcione una frase de contraseña. Se trata de la frase de contraseña que va a cargar en el almacén de claves.

 ![Instalación de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Cuando arranque la máquina virtual y se le pida una frase de contraseña, utilice la frase de contraseña que especificó en el paso 3.

 ![Instalación de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Prepare la máquina virtual para cargarla en Azure con las instrucciones de "CentOS 7.0+" en [Preparación de una máquina virtual basada en CentOS para Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). No ejecute todavía el último paso (desaprovisionamiento de la máquina virtual).

6. Ahora puede desaprovisionar la máquina virtual y [cargar el VHD](#upload-encrypted-vhd-to-an-azure-storage-account) en Azure.

Para configurar el cifrado de manera que funcione con Azure, haga lo siguiente:

1. Edite /etc/dracut.conf y agregue la línea siguiente:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Agregue comentarios a estas líneas al final del archivo /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Agregue la siguiente línea al principio del archivo /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
```
    DRACUT_SYSTEMD=0
```
Y cambie todas las apariciones de:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```
    if [ 1 ]; then
```
4. Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh y anéxelo después de "# Open LUKS device":
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Ejecute "/usr/sbin/dracut -f -v" para actualizar initrd.

![Instalación de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Carga de un VHD cifrado a una cuenta de almacenamiento de Azure
Cuando el cifrado de BitLocker o el cifrado DM-Crypt están habilitados, el VHD cifrado local cifrado tiene que cargarse en la cuenta de almacenamiento.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Carga del secreto del cifrado de disco para la máquina virtual precifrada en el almacén de claves
El secreto de cifrado del disco que obtuvo con anterioridad se debe cargar como un secreto en el almacén de claves. El almacén de claves debe tener habilitados el cifrado de discos y los permisos para el cliente de Azure AD.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Secreto del cifrado de disco no cifrado con una KEK
Para configurar el secreto en el almacén de claves, use [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Si tiene una máquina virtual con Windows, el archivo bek se codifica como cadena base64 y, a continuación, se carga en el almacén de claves mediante el cmdlet `Set-AzureKeyVaultSecret`. En Linux, la frase de contraseña se codifica como cadena base64 y, a continuación, se carga en el almacén de claves. Además, asegúrese de que se establecen las siguientes etiquetas al crear el secreto en el almacén de claves.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Use `$secretUrl` en el paso siguiente para [conectar el disco del sistema operativo sin usar KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Secreto del cifrado de disco cifrado con una KEK
Antes de cargar el secreto en el almacén de claves, puede cifrarlo si lo desea mediante una clave de cifrado de claves. Utilice la [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de encapsulamiento para cifrar por primera vez el secreto mediante la clave de cifrado de claves. El resultado de esta operación de encapsulamiento es una cadena codificada en URL como base64 que luego se carga como secreto con el cmdlet [`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret).

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Use `$KeyEncryptionKey` y `$secretUrl` en el paso siguiente para [conectar el disco del sistema operativo usando KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especificación de una dirección URL de secreto al adjuntar un disco del sistema operativo
#### <a name="without-using-a-kek"></a>Sin utilizar una KEK
Mientras esté adjuntando el disco del sistema operativo, tiene que pasar `$secretUrl`. La dirección URL se generó en la sección "Secreto de cifrado de disco no cifrado con una KEK".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Uso de una KEK
Cuando conecte un disco del sistema operativo, pase `$KeyEncryptionKey` y `$secretUrl`. La dirección URL se generó en la sección "Secreto de cifrado de disco no cifrado con una KEK".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Descargar esta guía
Puede descargar esta guía de la [Galería de TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

## <a name="for-more-information"></a>Para obtener más información
[Explore Azure Disk Encryption with Azure PowerShell - Part 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0) (Exploración de Azure Disk Encryption con Azure PowerShell - Parte 1)  
[Exploración de Azure Disk Encryption con Azure PowerShell, parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
