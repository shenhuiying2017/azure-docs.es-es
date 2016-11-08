---
title: Cifrado del servicio Azure Storage para datos en reposo| Microsoft Docs
description: Use la característica Cifrado del servicio Almacenamiento de Azure para cifrar el Almacenamiento de blobs de Azure en el servicio cuando almacene los datos y descífrelos cuando los recupere.
services: storage
documentationcenter: .net
author: robinsh
manager: carmonm
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: robinsh

---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Cifrado del servicio Almacenamiento de Azure para datos en reposo (versión preliminar)
Cifrado del servicio Almacenamiento de Azure (SSE) para datos en reposo le ayuda a asegurar y proteger sus datos con el fin de cumplir con los compromisos de cumplimiento y seguridad de su organización. Con esta característica, Almacenamiento de Azure cifra automáticamente sus datos antes de continuar al almacenamiento y los descifra después de la recuperación. La administración de claves, el cifrado y el descifrado son completamente transparentes para los usuarios.

En las siguientes secciones, se brindan instrucciones detalladas sobre cómo usar las características de Cifrado del servicio de almacenamiento, además de las experiencias de usuario y los escenarios compatibles.

## <a name="overview"></a>Información general
Almacenamiento de Azure pone a su disposición diferentes funciones de seguridad que, al usarlas en conjunto, permiten a los desarrolladores compilar aplicaciones seguras. Los datos se pueden proteger en tránsito entre una aplicación y Azure usando [cifrado de cliente](storage-client-side-encryption.md), HTTPs o SMB 3.0. El cifrado del servicio de almacenamiento proporciona cifrado en reposo, una administración de claves, cifrado y descifrado de manera completamente transparente. A todos los datos se les aplica el [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uno de los cifrados de bloques más seguros disponibles.

SSE funciona mediante el cifrado de los datos cuando se escribe en Azure Storage, y puede utilizarse para blobs en bloques, blobs en páginas y blobs en anexos. Funciona para lo siguiente:

* Cuentas de almacenamiento de fin general y cuentas de Blob Storage
* Standard Storage y Premium Storage 
* Todos los niveles de redundancia (LRS, ZRS, GRS y RA-GRS)
* Cuentas de almacenamiento de Azure Resource Manager (pero no el clásico) 
* Todas las regiones

Para habilitar o deshabilitar el cifrado del servicio Storage para una cuenta de almacenamiento, inicie sesión en [Azure Portal](https://azure.portal.com) y seleccione una cuenta de almacenamiento. En la hoja Configuración, busque la sección Blob service como se muestra en esta captura de pantalla y haga clic en Cifrado.

![Captura de pantalla del Portal que muestra la opción de la característica Cifrado](./media/storage-service-encryption/image1.png)

Una vez que haga clic en la configuración de cifrado, puede habilitar o deshabilitar Cifrado del servicio de Almacenamiento.

![Captura de pantalla del Portal que muestra las propiedades de la característica Cifrado](./media/storage-service-encryption/image2.png)

## <a name="encryption-scenarios"></a>Escenarios de cifrado
Cifrado del servicio de Almacenamiento se puede habilitar en el nivel de la cuenta de almacenamiento. Es compatible con los siguientes escenarios de cliente:

* El cifrado de blobs en bloques, blobs en anexos y blobs en páginas.
* El cifrado de plantillas y VHD archivados migrados desde instalaciones locales a Azure.
* El cifrado de discos de datos y OS subyacentes para las VM de IaaS creadas con los VHD.

SEE tiene las siguientes limitaciones:

* No se admite el cifrado de cuentas de almacenamiento clásico.
* No se admite el cifrado de cuentas de almacenamiento clásico migradas a cuentas de almacenamiento de Resource Manager.
* Datos existentes: SSE solo cifra los datos recientemente creados una vez que se habilita el cifrado. Por ejemplo, si crea una cuenta de almacenamiento de Resource Manager nueva pero no activa el cifrado, luego carga los blobs o los VHD archivados a esa cuenta de almacenamiento y, después, activa SSE, no se cifrarán esos blobs, a menos que se reescriban o se copien.
* Compatibilidad con Marketplace: habilite el cifrado de las máquinas virtuales creadas desde Marketplace mediante el [Portal de Azure](https://portal.azure.com), PowerShell y la CLI de Azure. La imagen base de VHD seguirá sin cifrar; sin embargo, se cifrarán todas las escrituras que se realicen una vez que se ponga en marcha la VM.
* No se cifrarán los datos de tabla, colas y archivos.

## <a name="getting-started"></a>Introducción
### <a name="step-1:-[create-a-new-storage-account](storage-create-storage-account.md)."></a>Paso 1: [Crear una cuenta de almacenamiento nueva](storage-create-storage-account.md).
### <a name="step-2:-enable-encryption."></a>Paso 2: Habilitar el cifrado.
Puede habilitar el cifrado mediante el [Portal de Azure](https://portal.azure.com).

> [!NOTE]
> Si desea habilitar o deshabilitar mediante programación el cifrado del servicio de almacenamiento en una cuenta de almacenamiento, puede usar la [API de REST del proveedor de recursos de Azure Storage](https://msdn.microsoft.com/library/azure/mt163683.aspx), la [Biblioteca del cliente proveedor de recursos de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](../powershell-install-configure.md) o la [CLI de Azure](storage-azure-cli.md).
> 
> 

### <a name="step-3:-copy-data-to-storage-account"></a>Paso 3: Copiar datos en una cuenta de almacenamiento
Si habilita SSE en una cuenta de almacenamiento y, a continuación, escribe blobs en esa cuenta de almacenamiento, se cifrarán los blobs. No se cifrarán los blobs que ya se encuentren en esa cuenta de almacenamiento hasta que se vuelvan a escribir. Puede copiar los datos de una cuenta de almacenamiento a una con SSE cifrado, o incluso habilitar SSE y copiar los blobs de un contenedor a otro para asegurarse de que se cifran los datos anteriores. Puede utilizar cualquiera de las siguientes herramientas para lograr esto.

#### <a name="using-azcopy"></a>Uso de AzCopy
AzCopy es una utilidad de línea de comandos diseñada para copiar datos a y desde los servicios de Almacenamiento de blobs, Archivos y Almacenamiento de tablas de Microsoft Azure, mediante sencillos comandos con un rendimiento óptimo. Puede utilizarla para copiar los blobs de una cuenta de almacenamiento a otra con SSE habilitado. 

Para más información, visite [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).

#### <a name="using-the-storage-client-libraries"></a>Uso de las bibliotecas de cliente de almacenamiento
Puede copiar datos de blobs en Almacenamiento de blobs y desde él, o entre cuentas de almacenamiento, través de nuestro completo conjunto de bibliotecas de cliente de almacenamiento, que incluyen .NET, C++, Java, Android, Node.js, PHP, Python y Ruby.

Para obtener más información, visite la página [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Uso de un explorador de almacenamiento
Puede usar un explorador de almacenamiento para crear cuentas de almacenamiento, cargar y descargar datos, ver el contenido de los blobs y navegar entre los directorios. Puede usar una de estas para cargar blobs a su cuenta de almacenamiento con cifrado habilitado. Con algunos exploradores de almacenamiento, también puede copiar datos provenientes del almacenamiento de blobs existente a un contenedor diferente en la cuenta de almacenamiento o en una nueva cuenta de almacenamiento con SSE habilitado.

Si desea obtener más información, visite [Exploradores de almacenamiento de Azure](storage-explorers.md).

### <a name="step-4:-query-the-status-of-the-encrypted-data"></a>Paso 4: Consultar el estado de los datos cifrados
Se ha implementado una versión actualizada de las bibliotecas de cliente de almacenamiento que le permite consultar el estado de un objeto para determinar si está cifrado o no. Los ejemplos se agregarán a este documento en un futuro próximo.

Mientras tanto, puede llamar a [Obtener propiedades de la cuenta](https://msdn.microsoft.com/library/azure/mt163553.aspx) para comprobar que la cuenta de almacenamiento tenga habilitado el cifrado o consultar las propiedades de la cuenta de almacenamiento en el Portal de Azure.

## <a name="encryption-and-decryption-workflow"></a>Flujo de trabajo del cifrado y el descifrado
A continuación, podrá ver una descripción breve del flujo de trabajo de cifrado y descifrado:

* El cliente habilita el cifrado en la cuenta de almacenamiento.
* Cuando el cliente escriba datos nuevos (PUT Blob, PUT Block, PUT Page, etc.) en Blob Storage, cada escritura se cifra mediante un [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uno de los cifrados de bloques más sólidos disponible.
* Cuando el cliente necesita tener acceso a los datos (GET, blob, etc.), estos se descifran automáticamente antes de que se devuelvan al usuario.
* Si el cifrado está deshabilitado, las escrituras nuevas se dejan de cifrar y los datos cifrados existentes siguen cifrados hasta que el usuario los reescriba. Mientras el cifrado esté habilitado, se cifrarán las escrituras en el Almacenamiento de blobs. El estado de los datos no cambia cuando el usuario habilita y deshabilita el cifrado de la cuenta de almacenamiento.
* Microsoft almacena, cifra y administra todas las claves de cifrado.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Preguntas más frecuentes acerca de Cifrado del servicio de Almacenamiento para datos en reposo
**P: Ya tengo una cuenta de almacenamiento clásico. ¿Puedo habilitar SSE en ella?**

R: No. SSE solo es compatible con las cuentas de almacenamiento de Resource Manager.

**P: ¿Cómo puedo cifrar datos en mi cuenta de almacenamiento clásico?**

R: Puede crear una cuenta de almacenamiento de Resource Manager nueva y usar [AzCopy](storage-use-azcopy.md) para copiar los datos desde la cuenta de almacenamiento clásico existente a la cuenta de almacenamiento de Resource Manager recién creada. 

Otra opción es migrar su cuenta de almacenamiento clásico a una cuenta de almacenamiento de Resource Manager. Para más información sobre el proceso de migración, consulte [Platform Supported Migration of IaaS Resources from Classic to Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/)(Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Resource Manager).

**P: Ya tengo una cuenta de almacenamiento de Resource Manager. ¿Puedo habilitar SSE en ella?**

R: Sí, pero solo se cifrarán los blobs recién escritos. No vuelve atrás y cifra datos que ya estaban presentes. 

**P: Me gustaría cifrar los datos actuales de una cuenta de almacenamiento de Resource Manager existente.**

R: Puede habilitar SSE en cualquier momento en una cuenta de almacenamiento de Resource Manager. Sin embargo, no se cifrarán los blobs que ya estaban presentes. Para cifrar los blobs, puede copiarlos en otro nombre o en otro contenedor y, a continuación, quitar las versiones sin cifrar.

**P: Uso el Almacenamiento premium. ¿Puedo usar SSE?**

R: Sí, SSE es compatible tanto con el almacenamiento estándar como con el Almacenamiento premium.

**P: Si creo una cuenta de almacenamiento nueva, habilito SSE y, luego, creo una VM nueva con esa cuenta de almacenamiento, ¿la VM queda cifrada?**

R: Sí. Se cifrarán todos los discos creados que usen la cuenta de almacenamiento nueva, siempre y cuando se creen después de habilitar SSE. Si la VM se creó con Azure Marketplace, la imagen base de VHD seguirá sin cifrar; sin embargo, se cifrarán todas las escrituras que se realicen una vez que se ponga en marcha la VM.

**P: ¿Puedo crear cuentas de almacenamiento con SSE habilitado a través de Azure PowerShell y la CLI de Azure?**

R: Sí.

**P: ¿El costo de Almacenamiento de Azure sube si se habilita SSE?**

R: No hay costo adicional.

**P: ¿Quién administra las claves de cifrado?**

R: Las administra Microsoft.

**P: ¿Puedo usar mis propias claves de cifrado?**

R: Estamos trabajando para brindar funcionalidades que permitan a los clientes usar sus propias claves de cifrado.

**P: ¿Puedo revocar el acceso a las claves de cifrado?**

R: No por el momento, porque Microsoft administra completamente las claves.

**P: ¿SSE está habilitado de manera predeterminada cuando creo una cuenta de almacenamiento?**

R: SSE no viene habilitado de manera predeterminada; puede usar el Portal de Azure para habilitarlo. También puede habilitar de manera programática esta característica a través de la API de REST del proveedor de recursos de almacenamiento.

**P: ¿Qué diferencia tiene con Cifrado de discos de Azure?**

R: Esta característica se usa para cifrar los datos en el Almacenamiento de blobs de Azure. Cifrado de discos de Azure se usa para cifrar discos de datos y SO en VM de IaaS. Para obtener más detalles, visite nuestra [Guía de seguridad para almacenamiento](storage-security-guide.md).

**P: ¿Qué ocurre si habilito SSE y, luego, habilito Cifrado de discos de Azure en los discos?**

R: Funcionará sin problemas. Ambos métodos cifrarán los datos.

**P: Mi cuenta de almacenamiento está configurada para la replicación con redundancia geográfica. Si habilito SSE, ¿también se cifrará la copia redundante?**

R: Sí, se cifran todas las copias de la cuenta de almacenamiento, además de todas las opciones de redundancia. Se admite el almacenamiento con redundancia local (LRS), almacenamiento con redundancia de zona (ZRS), almacenamiento con redundancia geográfica (GRS) y almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).

**P: No puedo habilitar el cifrado en mi cuenta de almacenamiento.**

R: ¿Es una cuenta de almacenamiento de Resource Manager? Las cuentas de almacenamiento clásico no son compatibles. 

**P: ¿SSE solo se permite en regiones específicas?**

R: SSE está disponible en todas las regiones. 

**P: ¿Cómo me puedo comunicar con alguna persona si tengo problemas o si deseo enviar comentarios?**

R: Envíe un correo electrónico a [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) si tiene algún problema relacionado con el cifrado del servicio de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes
Almacenamiento de Azure pone a su disposición diferentes funciones de seguridad que, al usarlas en conjunto, permiten a los desarrolladores compilar aplicaciones seguras. Para obtener más detalles, visite la [Guía de seguridad para almacenamiento](storage-security-guide.md).

<!--HONumber=Oct16_HO2-->


