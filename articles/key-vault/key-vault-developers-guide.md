---
title: "Guía del desarrollador de Azure Key Vault"
description: "Los desarrolladores pueden usar Azure Key Vault para administrar las claves criptográficas en el entorno de Microsoft Azure."
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: alleonar
ms.openlocfilehash: 68a08f1f6e8036b9ca966e8d9596c09f2b2ccf81
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-developers-guide"></a>Guía del desarrollador de Azure Key Vault

Key Vault le permite acceder de forma segura a información confidencial desde sus aplicaciones:

- Las claves y los secretos están protegidos sin tener que escribir el código manualmente, y puede usarlos fácilmente en sus aplicaciones.
- Sus clientes pueden poseer y administrar sus propias claves para que pueda centrarse en proporcionar las características de software fundamentales. De este modo, las aplicaciones no serán responsables de las claves y secretos del inquilino de sus clientes.
- La aplicación puede usar claves para firma y cifrado y aun así que la administración de claves sea externa a la aplicación, lo que le permite ser adecuada como aplicación distribuida geográficamente.
- A partir de la versión de septiembre de 2016 de Key Vault, las aplicaciones ahora pueden usar los [certificados](https://docs.microsoft.com/rest/api/keyvault/certificate-operations) de Key Vault. Para más información, consulte el artículo [About keys, secrets, and certificates](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates) (Claves, secretos y certificados).

Para obtener más información sobre Azure Key Vault, consulte [¿Qué es Key Vault?](key-vault-whatis.md)

## <a name="public-previews"></a>Versiones preliminares públicas

Periódicamente se publica una versión preliminar pública de una nueva característica de Key Vault. Pruébelas y díganos su opinión a través de la dirección de correo electrónico de comentarios azurekeyvault@microsoft.com.

### <a name="storage-account-keys---july-10-2017"></a>Claves de cuenta de almacenamiento: 10 de julio de 2017

>[!NOTE]
>En esta actualización de Azure Key Vault, la única característica en versión preliminar es **Claves de cuenta de almacenamiento**.

Esta versión preliminar incluye nuestra nueva característica Claves de cuenta de almacenamiento, disponible a través de estas interfaces; [.NET/C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) y [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Para más información sobre la nueva característica Claves de cuenta de almacenamiento, vea [Introducción a las claves de cuenta de almacenamiento de Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Vídeos

Este vídeo muestra cómo crear su propio almacén de claves y cómo usarlo desde la aplicación de ejemplo 'Hello Key Vault'.

- [Desarrollador de Key Vault: guía de inicio rápido](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Recursos mencionados en el vídeo anterior:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Código de ejemplo de Azure Key Vault](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Creación y administración de almacenes de claves

Azure Key Vault proporciona una manera de almacenar de forma segura las credenciales y otras claves y secretos, pero el código tiene que autenticarse en Key Vault para recuperarlos. Para solucionar este problema, Managed Service Identity (MSI) proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory (Azure AD). Puede usar esta identidad para autenticar cualquier servicio que admita la autenticación de Azure AD, incluido Key Vault, sin necesidad de tener credenciales en el código. 

Para más información sobre MSI, consulte [Managed Service Identity (MSI) para recursos de Azure](https://docs.microsoft.com/azure/active-directory/msi-overview).

Para más información sobre cómo trabajar con AAD, consulte [Integración de aplicaciones con Azure Active Directory](/active-directory/develop/active-directory-integrating-applications).

Antes de trabajar con claves, secretos o certificados en el almacén de claves, podrá crear y administrar su almacén de claves a través de la CLI, PowerShell, plantillas de Resource Manager o REST, tal y como se describe en los siguientes artículos:

- [Creación y administración de almacenes claves con la CLI](key-vault-manage-with-cli2.md)
- [Creación y administración de almacenes claves con PowerShell](key-vault-get-started.md)
- [Creación de un almacén de claves e incorporación de un secreto mediante una plantilla de Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Creación y administración de almacenes de claves con REST](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codificación con Key Vault

El sistema de administración de Key Vault para los programadores está compuesto por varias interfaces. Esta sección contiene vínculos a todos los lenguajes, así como algunos ejemplos de código. 

### <a name="supported-programming-and-scripting-languages"></a>Lenguajes de programación y de scripting admitidos

#### <a name="rest"></a>REST

Mediante la interfaz REST se puede acceder a todos los recursos de Key Vault: almacenes, claves, secretos, etc. 

[Referencia de la API de REST de Key Vault](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[Referencia de API de .NET para Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Para más información sobre la versión 2.x de SDK de .NET, consulte las [notas de la versión](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[SDK de Java para Key Vault](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

En Node.js, la API de administración y la API de objetos de Key Vault son independientes. El siguiente artículo de introducción proporciona acceso a ambos. 

[Módulos de Azure Key Vault para Node.js](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Bibliotecas de Azure Key Vault para Python](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>CLI de Azure 2

[CLI de Azure para Key Vault](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell para Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Guías de inicio rápido

- [Create Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Introducción a Key Vault en Node.js](https://azure.microsoft.com/en-us/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Ejemplos de código

Para obtener ejemplos completos de cómo usar Key Vault con las aplicaciones, vea:

- [Ejemplos de código de Azure Key Vault](http://www.microsoft.com/download/details.aspx?id=45343) - Aplicación .NET de ejemplo *HelloKeyVault* y un ejemplo de servicio web de Azure. 
- [Uso de Azure Key Vault desde una aplicación web](key-vault-use-from-web-application.md) - Tutorial de ayuda para aprender a usar Azure Key Vault desde una aplicación web en Azure. 

## <a name="how-tos"></a>Procedimientos

Los artículos y escenarios siguientes proporcionan instrucciones específicas de tarea sobre cómo trabajar con Azure Key Vault:

- [Cambio del identificador de inquilino de Key Vault después de mover la suscripción](key-vault-subscription-move-fix.md). Al mover su suscripción de Azure del inquilino A al inquilino B, los almacenes de claves existentes se vuelven inaccesibles para los principales (usuarios y aplicaciones) en el inquilino B. Para corregir este problema, utilice esta guía.
- [Acceso a Key Vault detrás de un firewall](key-vault-access-behind-firewall.md). Para acceder a un almacén de claves, es preciso que la aplicación cliente de Key Vault pueda acceder a varios puntos de conexión para diversas funcionalidades.
- [Generación y transferencia de claves protegidas con HSM para Azure Key Vault](key-vault-hsm-protected-keys.md). Esto le ayudará a planear, generar y transferir sus propias claves protegidas con HSM para usarlas con Azure Key Vault.
- [Paso de valores seguros (como contraseñas) durante la implementación](../azure-resource-manager/resource-manager-keyvault-parameter.md). Si necesita pasar un valor seguro (como una contraseña) como un parámetro durante la implementación, puede almacenar ese valor como un secreto en Azure Key Vault y hacer referencia al valor en otras plantillas de Resource Manager.
- [Uso de Key Vault para la administración extensible de claves con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). El Conector de SQL Server para Azure Key Vault permite que SQL Server y SQL en una VM use el servicio Azure Key Vault como un proveedor de administración extensible de claves (EKM) con el fin de proteger sus claves de cifrado para el vínculo de aplicaciones; cifrado de datos transparente, cifrado de copia de seguridad y cifrado de nivel de columna.
- [Implementación de certificados en máquinas virtuales desde Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/): una aplicación de nube que se ejecute en una máquina virtual de Azure necesita un certificado. Sepa cómo añadirlo a la máquina virtual hoy mismo.
- [Configuración de Azure Key Vault con la auditoría y la rotación de claves de un extremo a otro](key-vault-key-rotation-log-monitoring.md): este es un tutorial sobre la rotación y auditoría de claves con Azure Key Vault.
- [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Implementación de certificado Azure Web App a través de Key Vault) proporciona instrucciones paso a paso para implementar certificados almacenados en Key Vault como parte de la oferta de [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/).
- [Concesión de permisos para que muchas aplicaciones tengan acceso a almacén de claves](key-vault-group-permissions-for-apps.md) La directiva de control de acceso de Key Vault solo admite 16 entradas. Sin embargo, puede crear un grupo de seguridad de Azure Active Directory. Agregue todas las entidades de servicio asociadas a este grupo de seguridad y, luego, conceda acceso a este grupo de seguridad a Key Vault.
- Para obtener más instrucciones específicas sobre las tareas de integración y el uso de los almacenes de claves con Azure, consulte los [ejemplos de plantillas de Azure Resource Manager de Ryan Jones para Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Uso de la eliminación temporal de Key Vault con la CLI](key-vault-soft-delete-cli.md) le guiará por el uso y ciclo de vida de un almacén de claves y de varios objetos de almacén de claves con la eliminación temporal habilitada.
- [Uso de la eliminación temporal de Key Vault con PowerShell](key-vault-soft-delete-powershell.md) le guiará por el uso y ciclo de vida de un almacén de claves y de varios objetos de almacén de claves con la eliminación temporal habilitada.

## <a name="integrated-with-key-vault"></a>Integración con Key Vault

En estos artículos se describen otros escenarios y servicios que usan Key Vault o se integran en este servicio.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) estándar del sector de Windows y la característica [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para ofrecer cifrado de volumen para el sistema operativo y los discos de datos. La solución se integra con Azure Key Vaultpara ayudarle a controlar y administrar las claves y secretos de cifrado de disco en su suscripción del almacén de claves, al mismo tiempo que garantiza que todos los datos de los discos de las máquinas virtuales se cifran en reposo en Azure Storage.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) proporciona la opción para el cifrado de datos que se almacenan en la cuenta. Para la administración de claves, Data Lake Store ofrece dos modos de administrar las claves de cifrado maestras (MEK) que son necesarias para descifrar los datos que se almacenan en Data Lake Store. Se puede dejar que Data Lake Store administre las MEK en su lugar o decidir conservar la propiedad de estas mediante su cuenta de Azure Key Vault. Puede especificar el modo de administración de claves durante la creación de una cuenta de Data Lake Store. 
- [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) le permite administrar su propia clave de inquilino. Por ejemplo, en lugar de que Microsoft administre su clave de inquilino (la opción predeterminada), puede administrar su propia clave de inquilino para cumplir con las normas específicas que se aplican a su organización. La administración de su propia clave de inquilino también se conoce como "aportar su propia clave", o BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Conceptos y datos globales de Key Vault

- [Comportamiento de eliminación temporal de Key Vault](key-vault-ovw-soft-delete.md) describe una característica que permite la recuperación de objetos eliminados tanto si dicha eliminación ha sido accidental como intencionada.
- [Limitación del cliente de Key Vault](key-vault-ovw-throttling.md) proporciona orientación sobre los conceptos básicos de la limitación y ofrece un enfoque para la aplicación.
- [Introducción a las claves de cuenta de Key Vault](key-vault-ovw-storage-keys.md) describe la integración de Key Vault con las claves de cuenta de Azure Storage.
- [Espacios de seguridad de Key Vault](key-vault-ovw-security-worlds.md) describe las relaciones entre regiones y zonas de seguridad.

## <a name="social"></a>Redes sociales

- [Blog de Key Vault](http://aka.ms/kvblog)
- [Foro sobre Key Vault](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas compatibles

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) (biblioteca principal Microsoft Azure Key Vault) proporciona las interfaces **IKey** e **IKeyResolver** para localizar las claves de los identificadores y realizar operaciones con ellas.
- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) (extensiones de Microsoft Azure Key Vault) proporcionan capacidades ampliadas para Azure Key Vault.


