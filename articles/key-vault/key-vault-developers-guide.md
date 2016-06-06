<properties
   pageTitle="Guía del desarrollador del Almacén de claves | Microsoft Azure"
   description="Los desarrolladores pueden usar el Almacén de clave de Azure para administrar las claves criptográficas en el entorno de Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/25/2016"
   ms.author="bruceper" />

# Guía del desarrollador del Almacén de claves de Azure
Con el Almacén de claves, podrá obtener acceso seguro a información confidencial sobre el acceso desde sus aplicaciones, por ejemplo:

- Las claves y los secretos estarán protegidos sin tener que escribir el código manualmente, y podrá usarlos fácilmente en sus aplicaciones.
- Sus clientes podrán poseer y administrar sus propias claves para que pueda centrarse en proporcionar las características de software fundamentales. De este modo, las aplicaciones no serán responsables de las claves y secretos del inquilino de sus clientes.
- Su aplicación puede usar claves para firma y cifrado y la administración de las claves sigue siendo externa a la aplicación para que la solución sea adecuada para una aplicación distribuida geográficamente.

Para obtener más información sobre Almacén de claves de Azure, consulte [¿Qué es Almacén de claves?](key-vault-whatis.md).

## Vídeos
Este vídeo muestra cómo crear su propio almacén de claves y cómo usarlo desde la aplicación de ejemplo 'Hello Key Vault'.

Vínculos a los recursos mencionados en el vídeo:
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Código de ejemplo de Almacén de claves de Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Para obtener más información, puede seguir el [blog de Almacén de claves](http://aka.ms/kvblog) y participar en el [foro sobre Almacén de claves](http://aka.ms/kvforum).



> [AZURE.VIDEO azure-key-vault-developer-quick-start]



## Creación y administración de almacenes de claves

Antes de trabajar con Almacén de claves de Azure en el código, puede crear y administrar almacenes mediante REST, plantillas de Resource Manager, PowerShell o CLI, tal y como se describe en los siguientes artículos:

- [Crear y administrar almacenes de claves con CLI](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Crear y administrar almacenes claves con PowerShell](key-vault-get-started.md)
- [Crear y administrar almacenes claves con CLI](key-vault-manage-with-cli.md)
- [Creación de un almacén de claves e incorporación de un secreto mediante una plantilla de ARM](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Las operaciones en los almacenes de claves se autentican mediante AAD y se autorizan mediante la propia directiva de acceso de Almacén de claves, definida para cada almacén.

## Codificación con Almacén de claves

El sistema de administración de Almacén de claves para los programadores está compuesto por varias interfaces, con REST como base. [Referencia de la API de REST de Almacén de claves](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Con la debida autorización, puede hacer lo siguiente:

- Administrar claves criptográficas mediante [Crear](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Actualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Eliminar](https://msdn.microsoft.com/library/azure/dn903611.aspx) y otras operaciones

- Administrar secretos mediante [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) y otras operaciones

- Usar claves de cifrado con operaciones de [Inicio de sesión](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Comprobar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) y [Cifrar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Descifrar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Los SDK siguientes están disponibles para trabajar con Almacén de claves:

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentación del SDK de .NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Documentación del SDK de Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Paquete del SDK de .NET](https://azure.microsoft.com/documentation/api/)|[Paquete del SDK de Node.js](https://www.npmjs.com/package/azure-keyvault)|


Para obtener ejemplos completos de cómo usar Almacén de claves con las aplicaciones, vea:

- Aplicación .NET de ejemplo *HelloKeyVault* y un ejemplo de servicio web de Azure. [Ejemplos de código de almacén de claves de Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Tutorial de ayuda para aprender a usar Almacén de claves de Azure desde una aplicación web en Azure. [Uso del Almacén de claves de Azure desde una aplicación web](key-vault-use-from-web-application.md)

## Procedimientos

Los artículos y escenarios siguientes proporcionan instrucciones específicas de tarea sobre cómo trabajar con Almacén de claves de Azure:

- [Generación y transferencia de claves protegidas con HSM para Almacén de claves de Azure](key-vault-hsm-protected-keys.md). Esto le ayudará a planear, generar y transferir sus propias claves protegidas con HSM para usarlas con Almacén de claves de Azure.
- [Paso de valores seguros (como contraseñas) durante la implementación](../resource-manager-keyvault-parameter.md). Si necesita pasar un valor seguro (como una contraseña) como un parámetro durante la implementación, puede almacenar ese valor como un secreto en un Almacén de claves de Azure y hacer referencia al valor en otras plantillas de Resource Manager.
- [Uso de Almacén de claves para la administración extensible de claves con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). El Conector de SQL Server para Almacén de claves de Azure permite que SQL Server y SQL en una VM use el servicio de Almacén de claves de Azure como un proveedor de administración extensible de claves (EKM) con el fin de proteger sus claves de inscripción para el vínculo de aplicaciones; Cifrado de datos transparente, Cifrado de copia de seguridad y Cifrado de nivel de columna.
- [Implementación de certificados en máquinas virtuales desde el almacén de claves](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/): una aplicación de nube que se ejecute en una máquina virtual de Azure necesita un certificado. Sepa cómo añadirlo a la máquina virtual hoy mismo.
- [Integración del almacén de claves con Cifrado de discos de Azure para máquinas virtuales de IaaS de Windows y Linux](../azure-security-disk-encryption.md): la solución de cifrado de discos se integra con el Almacén de claves de Azure para contribuir al control y la administración de los secretos y las claves de cifrado de discos de su suscripción del almacén de claves, al mismo tiempo que garantiza que todos los datos de los discos de las máquinas virtuales se cifran en reposo en el almacenamiento de Azure.

Para obtener más instrucciones específicas sobre las tareas de integración y el uso de almacenes de claves con Azure, consulte las [plantillas de ARM de ejemplo de Ryan Jones para Almacén de claves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## Bibliotecas compatibles

- [La biblioteca principal del Almacén de claves de Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) proporciona las interfaces `IKey` e `IKeyResolver` para localizar las claves de los identificadores y realizar operaciones con ellas.

- [Las extensiones del Almacén de claves de Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) proporcionan capacidades ampliadas para el Almacén de claves de Azure.

## Otros recursos de Almacén de claves
- [Blog de Almacén de claves](http://aka.ms/kvblog)
- [Foro sobre Almacén de claves](http://aka.ms/kvforum)

<!---HONumber=AcomDC_0525_2016-->