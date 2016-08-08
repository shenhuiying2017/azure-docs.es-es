<properties
	pageTitle="Cifrado del servicio Almacenamiento de Azure para datos en reposo (versión preliminar)| Microsoft Azure"
	description="Use la característica Cifrado del servicio Almacenamiento de Azure para cifrar el Almacenamiento de blobs de Azure en el servicio cuando almacene los datos y descífrelos cuando los recupere."
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="robinsh"/>

# Cifrado del servicio Almacenamiento de Azure para datos en reposo (versión preliminar)

Cifrado del servicio Almacenamiento de Azure (SSE) para datos en reposo le ayuda a asegurar y proteger sus datos con el fin de cumplir con los compromisos de cumplimiento y seguridad de su organización. Con esta característica, Almacenamiento de Azure cifra automáticamente sus datos antes de continuar al almacenamiento y los descifra después de la recuperación. La administración de claves, cifrado y descifrado es completamente transparente para los usuarios.

En las siguientes secciones, se brindan instrucciones detalladas sobre cómo usar las características de Cifrado del servicio de almacenamiento, además de las experiencias de usuario y los escenarios compatibles.

## Información general


Almacenamiento de Azure pone a su disposición diferentes funciones de seguridad que, al usarlas en conjunto, permiten a los desarrolladores compilar aplicaciones seguras. Los datos se pueden proteger en tránsito entre una aplicación y Azure usando [cifrado de cliente](storage-client-side-encryption.md), HTTPs o SMB 3.0. Cifrado del servicio de Almacenamiento es una característica nueva de Almacenamiento de Azure que cifrará los datos cuando se escriben en el Almacenamiento de Azure que admite blobs en bloques, blobs en páginas y blobs en anexos. Esta característica se puede habilitar para las cuentas de almacenamiento nuevas con el modelo de implementación de Azure Resource Manager y se encuentra disponible para todos los niveles de redundancia (LRS, ZRS, GRS, RA-GRS). Cifrado del servicio de Almacenamiento está disponible para el Almacenamiento premium y estándar, lo que permite controlar la administración de claves, cifrado y descifrado de manera completamente transparente. A todos los datos se les aplica el [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uno de los cifrados de bloques más seguros disponibles. La sección Viersión preliminar que aparece a continuación muestra detalles sobre cómo incorporar el programa de versión preliminar para Cifrado del servicio de Almacenamiento.

En esta captura de pantalla, se muestra dónde encontrar la configuración de Cifrado del servicio de Almacenamiento a través del [Portal de Azure](https://azure.portal.com). En esta pantalla, deberá hacer clic en Cifrado para continuar.

![Captura de pantalla del Portal que muestra la opción de la característica Cifrado](./media/storage-service-encryption/image1.png)

Una vez que haga clic en la configuración de cifrado, puede habilitar o deshabilitar Cifrado del servicio de Almacenamiento.

![Captura de pantalla del Portal que muestra las propiedades de la característica Cifrado](./media/storage-service-encryption/image2.png)

##Disponibilidad

Para el almacenamiento estándar, esta característica está disponible actualmente en el sudeste de Australia, centro de Canadá, este de Canadá, centro de EE. UU., Asia Oriental, este de EE. UU. 2, centro-norte de EE. UU., Sudeste Asiático, Europa Occidental y oeste de EE. UU.

Para el almacenamiento Premium, esta característica está disponible actualmente en el sudeste de Australia, centro de Canadá, este de Canadá, centro de EE. UU., Asia Oriental, este de EE. UU. 2, centro-norte de EE. UU., Sudeste Asiático, Japón Oriental y oeste de EE. UU.

Actualizaremos este documento a medida que esta característica se implemente en otras regiones.

##Escenarios de cifrado

Cifrado del servicio de Almacenamiento se puede habilitar en el nivel de la cuenta de almacenamiento. Es compatible con los siguientes escenarios de cliente:

-   El cifrado de blobs en bloques, blobs en anexos y blobs en páginas.

-   El cifrado de plantillas y VHD archivados migrados desde instalaciones locales a Azure.

-   El cifrado de discos de datos y OS subyacentes para las VM de IaaS creadas con los VHD.

La versión preliminar pública tiene las siguientes limitaciones:

-   No se admite el cifrado de cuentas de almacenamiento clásico.

-   No se admite el cifrado de cuentas de almacenamiento clásico migradas a cuentas de almacenamiento de Resource Manager.

-   Datos existentes: SSE solo cifra los datos recientemente creados una vez que se habilita el cifrado. Por ejemplo, si crea una cuenta de almacenamiento de Resource Manager nueva pero no activa el cifrado, luego carga los blobs o los VHD archivados a esa cuenta de almacenamiento y, después, activa SSE, no se cifrarán esos blobs, a menos que se reescriban o se copien.

-   Compatibilidad con Marketplace: habilite el cifrado de las VM creadas desde Marketplace mediante el (Portal de Azure)[https://portal.azure.com], PowerShell y la CLI de Azure. La imagen base de VHD seguirá sin cifrar; sin embargo, se cifrarán todas las escrituras que se realicen una vez que se ponga en marcha la VM.

-   No se cifrarán los datos de tabla, colas y archivos.

##Vista previa

Esta característica solo es compatible con las cuentas de almacenamiento de Resource Manager recién creadas; no se admiten las cuentas de almacenamiento clásico. Debe registrar su suscripción con los cmdlets de PowerShell si desea usar esta característica. Una vez que se apruebe la suscripción, podrá habilitar SSE para su cuenta de almacenamiento en la suscripción aprobada. Al igual que lo que ocurre con la mayoría de las versiones preliminares, no se debe usar para las cargas de trabajo de producción hasta que la característica tenga una disponibilidad general. Puede unirse a nuestro grupo de Versión preliminar de Cifrado del servicio de Almacenamiento en Yammer para proporcionar cualquier comentario que tenga con respecto a su experiencia.

### Registro para la versión preliminar

-   [Instale los cmdlets de Azure PowerShell](../powershell-install-configure.md).

-   En Windows 10, abra PowerShell como administrador.

-   Regístrese con el espacio de nombres Proveedor de recursos de almacenamiento. Esto solo se necesita para una suscripción que todavía no se registra con SRP.

    `PS E:> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage" `

-   Si desea registrarse para obtener la característica, puede usar el cmdlet Register-AzureRmProviderFeature de PowerShell.

    `Register-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

-   Si desea consultar el estado del registro y ver si se aprobó la suscripción, puede usar el cmdlet Get-AzureRmProviderFeature de PowerShell.

    `Get-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

Cuando el estado del registro es "Registrado", significa que se aprobó la suscripción. Visite también nuestro grupo de Versión preliminar de Cifrado del servicio Almacenamiento de Azure en Yammer.

##Introducción

###Paso 1: [suscribirse a la versión preliminar](#registering-for-preview).

###Paso 2: [crear una cuenta de almacenamiento nueva](storage-create-storage-account.md).

###Paso 3: Habilitar el cifrado.

Puede habilitar el cifrado a través del [Portal de Azure](https://portal.azure.com).

> [AZURE.NOTE] Si desea habilitar o deshabilitar mediante programación el cifrado del servicio de almacenamiento en una cuenta de almacenamiento, puede usar la [API de REST del proveedor de recursos de Almacenamiento de Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx). Pronto agregaremos esta funcionalidad a la [Biblioteca de cliente de proveedor de recursos de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), Azure PowerShell y la CLI de Azure.

###Paso 4: Copiar datos en una cuenta de almacenamiento.

#### Uso de AzCopy

AzCopy es una utilidad de línea de comandos diseñada para copiar datos a y desde los servicios de Almacenamiento de blobs, Archivos y Almacenamiento de tablas de Microsoft Azure, mediante sencillos comandos con un rendimiento óptimo. Puede usar esta utilidad para copiar datos provenientes de una cuenta de Almacenamiento de blobs existente a la cuenta de almacenamiento nueva con la funcionalidad de cifrado habilitada.

Para obtener más información, visite [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).

#### Uso de las bibliotecas de cliente de almacenamiento

Puede cargar y descargar datos hacia y desde el Almacenamiento de blobs, a través de nuestro completo conjunto de bibliotecas de cliente de almacenamiento, que incluyen .NET, C++, Java, Android, Node.js, PHP, Python y Ruby.

Para obtener más información, visite la página [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md).

#### Uso de un explorador de almacenamiento

Un explorador de almacenamiento se puede usar para crear cuentas de almacenamiento, cargar y descargar datos, ver el contenido de los blobs y navegar entre los directorios. Muchos admiten cuentas de almacenamiento clásico o de Resource Manager.

Puede usar una de estas para cargar blobs a su cuenta de almacenamiento con cifrado habilitado. Con algunos exploradores de almacenamiento, también puede copiar datos provenientes de su cuenta de almacenamiento existente a la nueva cuenta de almacenamiento que tiene habilitado SSE.

Si desea obtener más información, visite [Exploradores de almacenamiento de Azure](storage-explorers.md).

###Paso 5: Consultar el estado de los datos cifrados.

Una vez que SSE esté ampliamente disponible, se implementará una versión actualizada de las bibliotecas de cliente de almacenamiento que le permitirá consultar el estado de un objeto para determinar si está cifrado o no.

Mientras tanto, puede llamar a [Obtener propiedades de la cuenta](https://msdn.microsoft.com/library/azure/mt163553.aspx) para comprobar que la cuenta de almacenamiento tiene habilitado el cifrado o consultar las propiedades de la cuenta de almacenamiento en el Portal de Azure.

##Flujo de trabajo del cifrado y el descifrado

A continuación, podrá ver una descripción breve del flujo de trabajo de cifrado y descifrado:

-   El cliente habilita el cifrado en la cuenta de almacenamiento.

-   Cuando el cliente escriba datos nuevos (PUT Blob, PUT Block, PUT Page, etc.) en el Almacenamiento de blobs, cada escritura se cifra mediante un [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uno de los cifrados de bloques más sólidos disponible.

-   Cuando el cliente necesita tener acceso a los datos (GET, blob, etc.), estos se descifran automáticamente antes de que se devuelvan al usuario.

-   Si el cifrado está deshabilitado, las escrituras nuevas se dejan de cifrar y los datos cifrados existentes siguen cifrados hasta que el usuario los reescriba. Mientras el cifrado esté habilitado, se cifrarán las escrituras en el Almacenamiento de blobs. El estado de los datos no cambia cuando el usuario habilita y deshabilita el cifrado de la cuenta de almacenamiento.

-   Microsoft almacena, cifra y administra todas las claves de cifrado.

##Preguntas más frecuentes acerca de Cifrado del servicio de Almacenamiento para datos en reposo

**P: Ya tengo una cuenta de almacenamiento clásico. ¿Puedo habilitar SSE en ella?**

R: No. SSE solo es compatible con las cuentas de almacenamiento de Resource Manager creadas recientemente en la versión preliminar.

**P: ¿Cómo puedo cifrar datos en mi cuenta de almacenamiento clásico?**

R: Puede crear una cuenta de almacenamiento de Resource Manager nueva y usar [AzCopy](storage-use-azcopy.md) para copiar los datos desde la cuenta de almacenamiento clásico existente a la cuenta de almacenamiento de Resource Manager recién creada.

**P: Ya tengo una cuenta de almacenamiento de Resource Manager. ¿Puedo habilitar SSE en ella?**

R: Durante la versión preliminar de SSE, debe crear una cuenta para poder tener acceso a la nueva característica de SSE.

**P: Me gustaría cifrar los datos actuales de una cuenta de almacenamiento de Resource Manager existente.**

R: Si la cuenta de almacenamiento de Resource Manager existente se creó antes de este anuncio sobre la versión preliminar, puede crear una cuenta de almacenamiento de Resource Manager nueva y habilitar el cifrado. Luego, puede copiar los datos de la cuenta de almacenamiento anterior, los que se cifrarán de manera automática. Sin embargo, si la cuenta de almacenamiento de Resource Manager se creó después del anuncio sobre la versión preliminar y decidió habilitar el cifrado más adelante, puede usar el Portal de Azure para habilitar el cifrado de esta cuenta de almacenamiento y reescribir los datos no cifrados nuevamente en la cuenta de almacenamiento.

**P: Uso el Almacenamiento premium. ¿Puedo usar SSE?**

R: Sí, SSE es compatible tanto con el almacenamiento estándar como con el Almacenamiento premium.

**P: Si creo una cuenta de almacenamiento nueva, habilito SSE y, luego, creo una VM nueva con esa cuenta de almacenamiento, ¿la VM queda cifrada?**

R: Sí. Se cifrarán todos los discos creados que usen la cuenta de almacenamiento nueva, siempre y cuando se creen después de habilitar SSE. Si la VM se creó con Azure Marketplace, la imagen base de VHD seguirá sin cifrar; sin embargo, se cifrarán todas las escrituras que se realicen una vez que se ponga en marcha la VM.

**P: ¿Puedo crear cuentas de almacenamiento con SSE habilitado a través de Azure PowerShell y la CLI de Azure?**

R: Lanzaremos esta funcionalidad en la próxima versión de Azure PowerShell y la CLI de Azure, que actualmente está programada para fines de abril.

**P: ¿El costo de Almacenamiento de Azure sube si se habilita SSE?**

R: No hay costo adicional.

**P: ¿Cómo me puedo suscribir a la versión preliminar?**

R: Puede usar PowerShell para registrarse y obtener acceso a la versión preliminar. Una vez que se apruebe la suscripción para usar la característica, podrá usar PowerShell para habilitar Cifrado en reposo.

**P: ¿Cuál es el nombre de la característica que debo registrar cuando me suscribo a la versión preliminar con PowerShell?**

R: EncryptionAtRest.

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

R: ¿Cuándo creó la cuenta de almacenamiento? Para poder usar SSE durante la versión preliminar, deberá registrar su suscripción y, además, crear una cuenta de almacenamiento nueva; no puede habilitar SSE en las cuentas de almacenamiento que se crearon antes de la versión preliminar.

**P: ¿La versión preliminar de SSE solo se permite en algunas regiones específicas?**

R.: La versión preliminar de SSE está disponible en Asia Oriental y Europa Occidental en el caso del almacenamiento estándar, y en Japón Oriental en el caso del Almacenamiento premium. Actualizaremos este documento a medida que lo implementemos en más regiones durante los próximos meses.

**P: ¿Cómo me puedo comunicar con alguna persona si tengo problemas o si deseo enviar comentarios?**

R: Envíe un correo electrónico a [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) si tiene algún problema relacionado con el cifrado del servicio de almacenamiento.

##Pasos siguientes

Almacenamiento de Azure pone a su disposición diferentes funciones de seguridad que, al usarlas en conjunto, permiten a los desarrolladores compilar aplicaciones seguras. Para obtener más detalles, visite la [Guía de seguridad para almacenamiento](storage-security-guide.md).

<!---HONumber=AcomDC_0727_2016-->