---
title: "Cómo generar y transferir claves protegidas con HSM para Azure Key Vault | Microsoft Docs"
description: "Use este artículo para ayudarle a planear, generar y, a continuación, transfiera sus propias claves protegidas con HSM para utilizar con el Almacén de clave de Azure. También se denomina BYOK o \"traiga su propia clave\"."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: ambapat
ms.openlocfilehash: 5dbee1221f64045c64fecb344de1e03b2183dfb1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Generación y transferencia de claves protegidas con HSM para el Almacén de claves de Azure
## <a name="introduction"></a>Introducción
Para obtener una mayor seguridad, cuando utilice el Almacén de claves de Azure, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Con frecuencia este escenario se conoce también como *Aportar tu propia clave*, o BYOK. Los HSM tienen la validación FIPS 140-2 de nivel 2. El Almacén de claves de Azure usa la familia Thales nShield de HSM para proteger sus claves.

La información de este tema le ayudará a planear, generar y transferir sus propias claves protegidas con HSM para utilizarlas en Azure Key Vault.

Esta funcionalidad no está disponible para Azure China.

> [!NOTE]
> Para obtener más información sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de claves de Azure?](key-vault-whatis.md)  
>
> Para ver tutorial introductorio, que incluye la creación de un almacén de claves para claves protegidas con HSM, consulte [Introducción al Almacén de claves de Azure](key-vault-get-started.md).
>
>

Obtenga más información acerca de cómo generar y transferir una clave protegida con HSM a través de Internet:

* Genere la clave desde una estación de trabajo sin conexión, lo que reduce la superficie de ataque.
* La clave se cifra con una clave de intercambio de claves (KEK), que permanece cifrada hasta que se transfiere a los HSM del Almacén de claves de Azure. Solo la versión cifrada de la clave deja la estación de trabajo original.
* El conjunto de herramientas establece las propiedades en su clave de inquilino que enlaza la clave con el espacio de seguridad del Almacén de claves de Azure. Por consiguiente, después de que los HSM del Almacén de claves de Azure reciban y descifren la clave, dichos HSM son los únicos los que puedan usarla. La clave no se puede exportar. Este enlace lo exigen los HSM de Thales.
* La clave de intercambio de claves (KEK) que se utiliza para cifrar la clave se genera dentro de los HSM del Almacén de clave de Azure y no es exportable. Los HSM exigen que no pueda haber una versión sin cifrar de la KEK fuera de los HSM. Además, el conjunto de herramientas incluye la atestación desde Thales de que la KEK no es exportable y se generó dentro de un HSM genuino que fabricó Thales.
* El conjunto de herramientas incluye la atestación desde Thales de que el espacio de seguridad del Almacén de claves de Azure también se generó en un HSM genuino que fabricó Thales. Esta certificación demuestra que Microsoft usa hardware original.
* Microsoft usa KEK independientes y separa los espacios de seguridad de las distintas regiones geográficas. Esta separación garantiza que la clave puede utilizarse únicamente en centros de datos de la región en la que se ha cifrado. Por ejemplo, una clave de un cliente europeo no se puede utilizar en centros de datos de América del Norte o Asia.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Más información acerca de los HSM de Thales y los servicios de Microsoft
Thales e-Security es uno de los principales proveedores mundiales de soluciones de cifrado de datos y ciberseguridad para servicios financieros, tecnología punta, industria manufacturera, gobierno y sectores tecnológicos. Las soluciones de Thales cuentan con una trayectoria de 40 años en la protección de información corporativa y gubernamental, y las usan cuatro de las cinco mayores compañías de los sectores energético y aeroespacial. También las utilizan 22 países de la OTAN y protegen más del 80 por ciento de las transacciones de pago mundiales.

Microsoft ha colaborado con Thales para mejorar la tecnología de vanguardia de los HSM. Estas mejoras te permiten obtener los beneficios típicos de los servicios hospedados sin tener que renunciar al control de las claves. En concreto, estas mejoras permiten que Microsoft administre los HSM para que no lo tengan que hacer sus usuarios. Al ser un servicio en la nube, el Almacén de claves de Azure se escala verticalmente en muy poco tiempo para cubrir los picos de uso de cualquier organización. Al mismo tiempo, la clave está protegida dentro de los HSM de Microsoft y se conserva el control sobre su ciclo de vida, ya que es el usuario el que genera la clave y la transfiere a los HSM de Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementación del método Aportar tu propia clave (BYOK) en el Almacén de claves de Azure
Utilice la siguiente información y procedimientos si va a generar su propia clave protegida con HSM y, a continuación, va a transferirla al Almacén de claves de Azure, el escenario de Aportar tu propia clave (BYOK).

## <a name="prerequisites-for-byok"></a>Requisitos previos de BYOK
En la tabla siguiente puede ver una lista de los requisitos previos del método Aportar tu propia clave (BYOK) para el Almacén de claves de Azure.

| Requisito | Más información |
| --- | --- |
| Una suscripción a Azure |Para crear un Almacén de claves de Azure, se necesita una suscripción a Azure: [Regístrese para obtener la versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/) |
| Nivel de servicio Premium de Azure Key Vault, que admita claves protegidas con HSM |Para obtener más información sobre los niveles de servicio y las capacidades del Almacén de claves de Azure, consulte el sitio web [Precios de Almacén de claves](https://azure.microsoft.com/pricing/details/key-vault/) . |
| HSM de Thales, tarjetas inteligentes y software compatible |Debe tener acceso al módulo de seguridad de hardware de Thales y al conocimiento operacional básico de los HSM de Thales. Para ver la lista de modelos compatibles o comprar un HSM, consulte [Módulo de seguridad de hardware de Thales](https://www.thales-esecurity.com/msrms/buy) . |
| El siguiente hardware y software:<ol><li>Una estación de trabajo x64 sin conexión con un sistema operativo Windows 7 y software Thales nShield versión 11.50 o superior.<br/><br/>Si esta estación de trabajo ejecuta Windows 7, debe [instalar Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Una estación de trabajo conectada a Internet y con un sistema operativo Windows 7 como mínimo y [Azure PowerShell](/powershell/azure/overview)  **con al menos la versión 1.1.0**  instalada.</li><li>Una unidad USB u otro dispositivo de almacenamiento portátil con al menos 16 MB de espacio libre.</li></ol> |Por seguridad, se recomienda que la primera estación de trabajo no esté conectada a una red. Sin embargo, esta recomendación no es de obligado cumplimiento.<br/><br/>Tenga en cuenta que en las instrucciones siguientes, esta estación de trabajo se conoce como la desconectada.</p></blockquote><br/>Además, si la clave de inquilino es para una red de producción, se recomienda usar una segunda estación de trabajo independiente para descargar el conjunto de herramientas y cargar la clave de inquilino. Sin embargo, para la prueba puede usar la misma estación de trabajo que la primera.<br/><br/>Tenga en cuenta que en las instrucciones siguientes, la segunda estación de trabajo se conoce como la que está conectada a Internet.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generación y transferencia de una clave a un HSM del Almacén de claves de Azure
Los cinco pasos siguientes sirven para generar y transferir la clave a un HSM de Azure Key Vault:

* [Paso 1: preparación de la estación de trabajo conectada a Internet](#step-1-prepare-your-internet-connected-workstation)
* [Paso 2: preparación de la estación de trabajo desconectada](#step-2-prepare-your-disconnected-workstation)
* [Paso 3: generación de la clave](#step-3-generate-your-key)
* [Paso 4: preparación de la clave para la transferencia](#step-4-prepare-your-key-for-transfer)
* [Paso 5: transferencia de la clave al Almacén de claves de Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Paso 1: preparación de la estación de trabajo conectada a Internet
En este primer paso, realice los siguientes procedimientos en la estación de trabajo conectada a Internet.

### <a name="step-11-install-azure-powershell"></a>Paso 1.1: instalación de Azure PowerShell
Desde la estación de trabajo conectada a Internet, descargue e instale el módulo de Azure PowerShell que incluye los cmdlets para administrar el Almacén de claves de Azure. Esto requiere una versión mínima de 0.8.13.

Para ver las instrucciones de instalación, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Paso 1.2: especificación del identificador de la suscripción a Azure
Inicie una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:

        Add-AzureAccount
En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. A continuación, utilice el comando [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) :

        Get-AzureSubscription
En la salida, busque el identificador de la suscripción que va a utilizar para el Almacén de claves de Azure. Lo necesitará más adelante.

No cierre la ventana de Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Paso 1.3: descarga del conjunto de herramientas BYOK para el Almacén de claves de Azure
Vaya al Centro de descarga de Microsoft y [descargue el conjunto de herramientas de BYOK para el Almacén de claves de Azure](http://www.microsoft.com/download/details.aspx?id=45345) de su región geográfica o instancia de Azure. Utilice la siguiente información para identificar el nombre del paquete para descargar y su hash de paquete SHA-256 correspondiente:

- - -
**Estados Unidos:**

KeyVault-BYOK-Tools-UnitedStates.zip

760EE9BD6445C87CFF0E8B032577118704B3BEAA045AA55977C10EF68BC67E2B

- - -
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

7A64B94225F59B847C5C27C2200BAD7D16C901E1687767EDBBB8B09BB285011D

- - -
**Asia:**

KeyVault-BYOK-Tools-AsiaPacific.zip

813DC94B23079CF7A5CEA71D5B444E86B292F463C53EE47AED25D4F7CD58E7D8

- - -
**América Latina:**

KeyVault-BYOK-Tools-LatinAmerica.zip

3F29069E3500F95C0E156F4B8914E1DC60C20FB64B464306A299EA5145D755C0

- - -
**Japón:**

KeyVault-BYOK-Tools-Japan.zip

453FFEA2F8F410720B68B8BAC4CF79135A7F37F4E491FF840BE9E69E88A98C90

- - -
**Corea:**

KeyVault-BYOK-Tools-Korea.zip

C17B7E93224DA80F5668E09CF7DAE2F92527E8226179995BBE2E43DA4323595A

- - -
**Australia:**

KeyVault-BYOK-Tools-Australia.zip

4AD893396E86F2D2A71682876A6A8EA59E3C7895BEAD2F7E7C8516682582C34B

- - -
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

3AAE1A96B9D15B899B8126CFC0380719EB54FDF2EA94489B43FAD21ECC745F64

- - -
**DoD del Gobierno de Estados Unidos:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A61E78297B0732DF2682FDE63D7B572CE4D23B0BC27CC48AFF620BD060BB9E9D

- - -
**Canadá:**

KeyVault-BYOK-Tools-Canada.zip

30B87A0BA8208F6B7241C30C794FED1C370D7445ACA179685816E4E156CD2AF7

- - -
**Alemania:**

KeyVault-BYOK-Tools-Germany.zip

5E3E4AA54715E4F93C3C145035B18275B7C6815A06D7ABB212E7FADBF2929261

- - -
**India:**

KeyVault-BYOK-Tools-India.zip

136733A6C6A71D75571BB80819B3D55A9B83CCAD5C996C686BC5682A3F369BF7

- - -
**Reino Unido:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

ED331A6F1D34A402317D3F27D5396046AF0E5C2D44B5D10CCCE293472942D268

- - -

Para validar la integridad del conjunto de herramientas BYOK que descargó, use el cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) en la sesión de Azure PowerShell.

    Get-FileHash KeyVault-BYOK-Tools-*.zip

El conjunto de herramientas incluye:

* Un paquete de clave de intercambio de claves (KEK) cuyo nombre comienza por **BYOK-KEK-pkg-.**
* Un paquete de espacio de seguridad cuyo nombre comienza por **BYOK-SecurityWorld-pkg-.**
* Un script Python denominado **verifykeypackage.py.**
* Un archivo ejecutable de línea de comandos denominado **KeyTransferRemote.exe** y asociado a las DLL.
* Un paquete redistribuible de Visual C++ denominado **vcredist_x64.exe**.

Copie el paquete en una unidad USB u otro dispositivo de almacenamiento portátil.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Paso 2: preparación de la estación de trabajo desconectada
En este segundo paso, realice los siguientes procedimientos en la estación de trabajo que no está conectada a una red (Internet o la red interna).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Paso 2.1: preparación de la estación de trabajo desconectada con HSM de Thales
Instale el software nCipher (Thales) en un equipo de Windows y, a continuación, adjunte un HSM de Thales a dicho equipo.

Asegúrese de que las herramientas de Thales estén en su ruta de acceso (**%nfast_home%\bin**). Por ejemplo, escriba lo siguiente:

        set PATH=%PATH%;"%nfast_home%\bin"

Para obtener más información, consulte el Manual del usuario que se incluye en el HSM de Thales.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Paso 2.2: instalación del conjunto de herramientas BYOK en la estación de trabajo desconectada
Copie el paquete del conjunto de herramientas BYOK de la unidad USB, u otro dispositivo de almacenamiento portátil, y, a continuación, haga lo siguiente:

1. Extraiga los archivos del paquete descargado en cualquier carpeta.
2. En esa carpeta, ejecute vcredist_x64.exe.
3. Siga las instrucciones para instalar los componentes de tiempo de ejecución de Visual C++ para Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Paso 3: generación de la clave
En el tercer paso, realice los siguientes procedimientos en la estación de trabajo desconectada. Para completar este paso, HSM debe estar en modo de inicialización. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>Paso 3.1: cambio del modo HSM a "I"
Si está usando Thales nShield Edge, para cambiar el modo: 1. Use el botón de modo para resaltar el modo requerido. 2. Después de un momento, mantenga presionado el botón Borrar durante un par de segundos. Si el modo cambia, el LED del nuevo modo deja de parpadear y permanece encendido. El LED del estado puede parpadear irregularmente durante algunos segundos y, luego, parpadea regularmente cuando el dispositivo está listo. En caso contrario, el dispositivo permanece en el modo actual, con el modo correspondiente de LED encendido.

### <a name="step-32-create-a-security-world"></a>Paso 3.2: creación de un espacio de seguridad
Inicie un símbolo del sistema y ejecute el programa new-world de Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Este programa crea un archivo de **espacio de seguridad** en %NFAST_KMDATA%\local\world, que corresponde a la carpeta C:\ProgramData\nCipher\Key Management Data\local. Puede usar valores diferentes para el quórum, pero en nuestro ejemplo, se le pedirá que escriba tres tarjetas en blanco y PIN para cada una de ellos. A continuación, dos tarjetas cualesquiera le proporcionarán acceso total al espacio de seguridad. Estas tarjetas pasan a ser el **Conjunto de tarjetas de administrador** del nuevo espacio de seguridad.

A continuación, haga lo siguiente:

* Realice una copia de seguridad del archivo del espacio. Proteja dicho archivo, las tarjetas de administrador y sus PIN, y asegúrese de que nadie tiene acceso a más de una tarjeta.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Paso 3.3: cambio del modo HSM a "O"
Si está usando Thales nShield Edge, para cambiar el modo: 1. Use el botón de modo para resaltar el modo requerido. 2. Después de un momento, mantenga presionado el botón Borrar durante un par de segundos. Si el modo cambia, el LED del nuevo modo deja de parpadear y permanece encendido. El LED del estado puede parpadear irregularmente durante algunos segundos y, luego, parpadea regularmente cuando el dispositivo está listo. En caso contrario, el dispositivo permanece en el modo actual, con el modo correspondiente de LED encendido.


### <a name="step-34-validate-the-downloaded-package"></a>Paso 3.4: validación del paquete descargado
Este paso es opcional, pero se recomienda realizarlo para poder validar estos elementos:

* La clave de intercambio de claves que se incluye en el conjunto de herramientas se ha generado desde un HSM de Thales original.
* El hash del espacio de seguridad que se incluye en el conjunto de herramientas se ha generado en un HSM de Thales original.
* La clave de intercambio de claves no es exportable.

> [!NOTE]
> Para validar el paquete descargado, el HSM debe estar conectado, encendido y debe tener un espacio de seguridad (como el que acaba de crear).
>
>

Para validar el paquete descargado:

1. Ejecute el script verifykeypackage.py, para lo que, en función de su región geográfica o instancia de Azure, debe escribir lo siguiente:

   * Norteamérica:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Europa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Asia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * América Latina:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Japón:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Para Corea:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Para Australia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Para [Azure Government](https://azure.microsoft.com/features/gov/), que usa la instancia del gobierno de Estados Unidos de Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Para DoD del Gobierno de Estados Unidos:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Para Canadá:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Para Alemania:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Para India:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
     > [!TIP]
     > El software de Thales incluye Python en %NFAST_HOME%\python\bin
     >
     >
2. Confirme que ve lo siguiente, que indica que el resultado de la validación ha sido satisfactorio: **Result: SUCCESS**

Este script valida la cadena del firmante hasta la clave raíz de Thales. El hash de esta clave raíz está insertado en el script y su valor debe ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Este valor también se puede confirmar por separado en el [sitio web de Thales](http://www.thalesesec.com/).

Ya está listo para crear una nueva clave.

### <a name="step-35-create-a-new-key"></a>Paso 3.5: creación de una nueva clave
Genere una clave con el programa **generatekey** de Thales.

Ejecute el comando siguiente para generar la clave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Cuando ejecute este comando, siga estas instrucciones:

* El parámetro *protect* debe establecerse en el valor **module**, como se muestra. Esto crea una clave protegida por el módulo. El conjunto de herramientas BYOK no es compatible con claves protegidas con OCS.
* Reemplace el valor de *contosokey* en **ident** y **plainname** por cualquier valor de cadena. Para minimizar gastos administrativos y reducir el riesgo de errores, se recomienda utilizar el mismo valor en ambos. El valor **ident** debe contener solo números, guiones y minúsculas.
* En este ejemplo, pubexp se deja en blanco (valor predeterminado), pero puede especificar valores concretos. Para obtener más información, consulte la documentación de Thales.

Este comando crea un archivo de clave con tokens en la carpeta %NFAST_KMDATA%\local cuyo nombre comienza por **key_simple_**, seguido del valor **ident** que se especificó en el comando. Por ejemplo: **key_simple_contosokey**. Este archivo contiene una clave cifrada.

Realice una copia del archivo tokenizado en una ubicación segura.

> [!IMPORTANT]
> Cuando posteriormente transfiera la clave al Almacén de claves de Azure, Microsoft no podrá exportar esta clave, por lo que resulta extremadamente importante que realice una copia de seguridad de la misma y del espacio de seguridad. Póngase en contacto con Thales si necesita guía y procedimientos recomendados para realizar copias de seguridad de una clave.
>
>

Ya está listo para transferir la clave al Almacén de claves de Azure.

## <a name="step-4-prepare-your-key-for-transfer"></a>Paso 4: preparación de la clave para la transferencia
En este paso, realice los siguientes procedimientos en la estación de trabajo desconectada.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Paso 4.1: creación de una copia de la clave con menos permisos

Abra un nuevo símbolo del sistema y cambie el directorio actual a la ubicación en la que descomprimió el archivo zip de BYOK. Para reducir los permisos en su clave, desde un símbolo del sistema, ejecute uno de los siguientes comandos, en función de su región geográfica o instancia de Azure:

* Norteamérica:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Asia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* América Latina:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Japón:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Para Corea:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Para Australia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Para [Azure Government](https://azure.microsoft.com/features/gov/), que usa la instancia del gobierno de Estados Unidos de Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Para DoD del Gobierno de Estados Unidos:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Para Canadá:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Para Alemania:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Para India:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1

Cuando ejecute este comando, reemplace *contosokey* por el valor que especificó en el **Paso 3.5: creación de una nueva clave** del paso [Generación de la clave](#step-3-generate-your-key).

Se le pedirá que conecte sus tarjetas de administrador del espacio de seguridad.

Cuando se complete el comando, verá **Result: SUCCESS** y la copia de la clave con menos permisos estará en el archivo denominado key_xferacId_<contosokey>.

Puede inspeccionar ACLS mediante los comandos siguientes utilizando las herramientas de Thales:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Cuando ejecute estos comandos, reemplace contosokey por el mismo valor que especificó en el **Paso 3.5: creación de una nueva clave** del paso [Generación de la clave](#step-3-generate-your-key) .

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Paso 4.2: cifrado de la clave mediante la clave de intercambio de claves de Microsoft
Ejecute uno de los comandos siguientes, dependiendo de su región geográfica o la instancia de Azure:

* Norteamérica:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Asia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* América Latina:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Japón:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para Corea:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para Australia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para [Azure Government](https://azure.microsoft.com/features/gov/), que usa la instancia del gobierno de Estados Unidos de Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para DoD del Gobierno de Estados Unidos:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para Canadá:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para Alemania:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para India:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Cuando ejecute este comando, siga estas instrucciones:

* Reemplace *contosokey* por el identificador que usó para generar la clave en el **Paso 3.5: creación de una nueva clave** del paso [Generación de la clave](#step-3-generate-your-key) .
* Reemplace *SubscriptionID* por el identificador de la suscripción de Azure con Key Vault. Este valor lo recuperó anteriormente, en el **paso 1.2: especificación del identificador de la suscripción de Azure** del paso de [preparación de la estación de trabajo conectada a Internet](#step-1-prepare-your-internet-connected-workstation).
* Reemplace *ContosoFirstHSMKey* por una etiqueta que se usará para el nombre del archivo de salida.

Cuando la operación se complete correctamente, se mostrará **Result: SUCCESS** y habrá un nuevo archivo en la carpeta actual que tendrá el siguiente nombre: KeyTransferPackage-*ContosoFirstHSMkey*.byok.

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Paso 4.3: copia del paquete de transferencia de claves a la estación de trabajo conectada a Internet
Utilice una unidad USB u otro dispositivo de almacenamiento portátil para copiar el archivo de salida del paso anterior (KeyTransferPackage-ContosoFirstHSMkey.byok) a la estación de trabajo conectada a Internet.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Paso 5: transferencia de la clave al Almacén de claves de Azure
En este paso final, en la estación de trabajo conectada a Internet, use el cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) para cargar el paquete de transferencia de claves que copió de la estación de trabajo desconectada en el HSM de Azure Key Vault:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Si la carga se realiza correctamente, verá que se muestran las propiedades de la clave que acaba de agregar.

## <a name="next-steps"></a>Pasos siguientes
Ahora puede usar esta clave protegida con HSM en el almacén de claves. Para obtener más información, consulte la sección **Si desea usar un módulo de seguridad de hardware (HSM)** del tutorial [Introducción al Almacén de claves de Azure](key-vault-get-started.md) .
