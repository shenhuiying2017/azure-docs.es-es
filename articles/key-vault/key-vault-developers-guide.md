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
   ms.date="03/07/2016"
   ms.author="bruceper" />

# Guía del desarrollador del Almacén de claves de Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Con el Almacén de claves, podrá obtener acceso seguro a información confidencial sobre el acceso desde sus aplicaciones, por ejemplo:

- Las claves y los secretos estarán protegidos sin tener que escribir el código manualmente, y podrá usarlos fácilmente en sus aplicaciones.
- Sus clientes podrán poseer y administrar sus propias claves para que pueda centrarse en proporcionar las características de software fundamentales. De este modo, las aplicaciones no serán responsables de las claves y secretos del inquilino de sus clientes.
- Su aplicación puede usar claves para firma y cifrado y la administración de las claves sigue siendo externa a la aplicación para que la solución sea adecuada para una aplicación distribuida geográficamente.

Para más información sobre el Almacén de claves de Azure, vea [¿Qué es el Almacén de claves de Azure?](key-vault-whatis.md)

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

Los artículos y escenarios siguientes proporcionan orientación específica sobre las tareas:

- [Generación y transferencia de claves protegidas con HSM para el Almacén de claves de Azure](key-vault-hsm-protected-keys.md)
- [Paso de valores seguros durante la implementación](../resource-manager-keyvault-parameter.md).
- Para obtener instrucciones específicas sobre las tareas de integración y el uso de Almacén de claves de Azure, vea las [plantillas de ARM de ejemplo de Ryan Jones para Almacén de claves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)

## Bibliotecas compatibles

- [La biblioteca principal de Almacén de claves de Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) proporciona las interfaces `IKey` e `IKeyResolver` para localizar las claves en los identificadores y para realizar operaciones con las claves.

- [Las extensiones del Almacén de claves de Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) proporcionan capacidades ampliadas para el Almacén de claves de Azure.

<!---HONumber=AcomDC_0309_2016-->