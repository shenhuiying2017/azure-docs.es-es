---
title: Guía del desarrollador del Almacén de claves | Microsoft Docs
description: 'Los desarrolladores pueden usar el Almacén de clave de Azure para administrar las claves criptográficas en el entorno de Microsoft Azure. '
services: key-vault
documentationcenter: ''
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper

ms.service: key-vault
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2016
ms.author: bruceper

---
# <a name="azure-key-vault-developer's-guide"></a>Guía del desarrollador del Almacén de claves de Azure
Con el Almacén de claves, podrá obtener acceso seguro a información confidencial sobre el acceso desde sus aplicaciones, por ejemplo:

* Las claves y los secretos estarán protegidos sin tener que escribir el código manualmente, y podrá usarlos fácilmente en sus aplicaciones.
* Sus clientes podrán poseer y administrar sus propias claves para que pueda centrarse en proporcionar las características de software fundamentales. De este modo, las aplicaciones no serán responsables de las claves y secretos del inquilino de sus clientes.
* Su aplicación puede usar claves para firma y cifrado y la administración de las claves sigue siendo externa a la aplicación para que la solución sea adecuada para una aplicación distribuida geográficamente.
* Con la versión de septiembre de 2016 de Key Vault, las aplicaciones pueden ahora hacer uso de los certificados de Key Vault. Para más información, consulte el artículo **About keys, secrets, and certificates** (sobre claves, secretos y certificados) en la [referencia REST](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Para obtener más información sobre Almacén de claves de Azure, consulte [¿Qué es Almacén de claves?](key-vault-whatis.md).

## <a name="videos"></a>Vídeos
Este vídeo muestra cómo crear su propio almacén de claves y cómo usarlo desde la aplicación de ejemplo 'Hello Key Vault'.

[!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Key-Vault-Developer-Quick-Start/player]


Vínculos a los recursos mencionados en el vídeo:

* [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
* [Código de ejemplo de Almacén de claves de Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Para más información, puede seguir el [blog de Key Vault](http://aka.ms/kvblog) y participar en el [foro sobre Key Vault](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Creación y administración de almacenes de claves
Antes de trabajar con Almacén de claves de Azure en el código, puede crear y administrar almacenes mediante REST, plantillas de Resource Manager, PowerShell o CLI, tal y como se describe en los siguientes artículos:

* [Crear y administrar almacenes de claves con CLI](https://msdn.microsoft.com/library/azure/mt620024.aspx)
* [Crear y administrar almacenes claves con PowerShell](key-vault-get-started.md)
* [Crear y administrar almacenes claves con CLI](key-vault-manage-with-cli.md)
* [Creación de un almacén de claves e incorporación de un secreto mediante una plantilla de Azure Resource Manager](../resource-manager-template-keyvault.md)

> [!NOTE]
> Las operaciones en los almacenes de claves se autentican mediante AAD y se autorizan mediante la propia directiva de acceso de Almacén de claves, definida para cada almacén.
> 
> 

## <a name="coding-with-key-vault"></a>Codificación con Almacén de claves
El sistema de administración de Key Vault para los programadores está compuesto por varias interfaces, con REST como base. [Key Vault REST API Reference](https://msdn.microsoft.com/library/azure/dn903609.aspx) (Referencia de la API de REST de Key Vault).

Con la debida autorización, puede hacer lo siguiente:

* Administrar claves criptográficas mediante [Create](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Import](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Update](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903611.aspx) y otras operaciones
* Administrar secretos mediante [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) y otras operaciones
* Usar claves de cifrado con operaciones de [Sign](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) y [Encrypt](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrypt](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Los SDK siguientes están disponibles para trabajar con Almacén de claves:

| [![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx) | [![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest) |
|:---:|:---:|
| [Documentación del SDK de .NET](https://msdn.microsoft.com/library/mt765854.aspx) |[Documentación del SDK de Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest) |
| [Paquete del SDK de .NET en Nuget](http://www.nuget.org/packages/Microsoft.Azure.KeyVault) |[Paquete del SDK de Node.js](https://www.npmjs.com/package/azure-keyvault) |

Para más información sobre la versión 2.x de SDK de .NET, consulte las [notas de la versión](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Ejemplo de código
Para obtener ejemplos completos de cómo usar Key Vault con las aplicaciones, vea:

* Aplicación .NET de ejemplo *HelloKeyVault* y un ejemplo de servicio web de Azure. [Ejemplos de código de almacén de claves de Azure](http://www.microsoft.com/download/details.aspx?id=45343)
* Tutorial de ayuda para aprender a usar Almacén de claves de Azure desde una aplicación web en Azure. [Uso del Almacén de claves de Azure desde una aplicación web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Procedimientos
Los artículos y escenarios siguientes proporcionan instrucciones específicas de tarea sobre cómo trabajar con Almacén de claves de Azure:

* [Cambio del identificador de inquilino de Key Vault después de mover la suscripción](key-vault-subscription-move-fix.md). Al mover su suscripción de Azure del inquilino A al inquilino B, los almacenes de claves existentes se vuelven inaccesibles para los principales (usuarios y aplicaciones) en el inquilino B. Para corregir este problema, utilice esta guía.
* [Acceso a Key Vault detrás de un firewall](key-vault-access-behind-firewall.md). Para acceder a un almacén de claves, es preciso que la aplicación cliente de Key Vault pueda acceder a varios puntos de conexión para diversas funcionalidades.
* [Generación y transferencia de claves protegidas con HSM para Azure Key Vault](key-vault-hsm-protected-keys.md). Esto le ayudará a planear, generar y transferir sus propias claves protegidas con HSM para usarlas con Azure Key Vault.
* [Paso de valores seguros (como contraseñas) durante la implementación](../resource-manager-keyvault-parameter.md). Si necesita pasar un valor seguro (como una contraseña) como un parámetro durante la implementación, puede almacenar ese valor como un secreto en Azure Key Vault y hacer referencia al valor en otras plantillas de Resource Manager.
* [Uso de Key Vault para la administración extensible de claves con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). El Conector de SQL Server para Azure Key Vault permite que SQL Server y SQL en una VM use el servicio Azure Key Vault como un proveedor de administración extensible de claves (EKM) con el fin de proteger sus claves de cifrado para el vínculo de aplicaciones; cifrado de datos transparente, cifrado de copia de seguridad y cifrado de nivel de columna.
* [Implementación de certificados en máquinas virtuales desde el almacén de claves](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : una aplicación de nube que se ejecute en una máquina virtual de Azure necesita un certificado. Sepa cómo añadirlo a la máquina virtual hoy mismo.
* [How to setup Key Vault with end to end key rotation and auditing](key-vault-key-rotation-log-monitoring.md) (Configuración del Almacén de claves con rotación y auditoría de claves de un extremo a otro): este es un tutorial sobre la rotación y auditoría de claves con el Almacén de claves de Azure.

Para obtener más instrucciones específicas sobre las tareas de integración y el uso de los almacenes de claves con Azure, consulte los [ejemplos de plantillas de Azure Resource Manager de Ryan Jones para Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Integración con el Almacén de claves
En estos artículos se describen otros escenarios y servicios que pueden integrarse con el Almacén de claves.

* [Cifrado de disco Azure](../security/azure-security-disk-encryption.md) aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) estándar del sector de Windows y la característica [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para ofrecer cifrado de volumen para el sistema operativo y los discos de datos. La solución se integra con el Almacén de claves de Azure para ayudarle a controlar y administrar las claves y secretos de cifrado de disco en su suscripción del almacén de claves, al mismo tiempo que garantiza que todos los datos de los discos de las máquinas virtuales se cifran en reposo en el almacenamiento de Azure.

## <a name="supporting-libraries"></a>Bibliotecas compatibles
* [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) (biblioteca principal Microsoft Azure Key Vault) proporciona las interfaces `IKey` e `IKeyResolver` para localizar las claves de los identificadores y realizar operaciones con ellas.
* [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) (extensiones de Microsoft Azure Key Vault) proporcionan capacidades ampliadas para Azure Key Vault.

## <a name="other-key-vault-resources"></a>Otros recursos de Almacén de claves
* [Blog de Almacén de claves](http://aka.ms/kvblog)
* [Foro sobre Almacén de claves](http://aka.ms/kvforum)

<!--HONumber=Oct16_HO2-->


