<properties
   pageTitle="Guía del desarrollador del Almacén de claves | Microsoft Azure"
   description="Los desarrolladores pueden usar el Almacén de clave de Azure para administrar las claves criptográficas en el entorno de Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2015"
   ms.author="mbaldwin" />

# Guía del desarrollador del Almacén de claves de Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Los desarrolladores pueden usar el Almacén de clave de Azure para administrar las claves criptográficas en el entorno de Microsoft Azure. Almacén de claves admite varios tipos de claves y algoritmos y puede usarse con módulos de seguridad de hardware (HSM) para las claves de cliente de alto valor. Además, puede usar el almacén de claves para almacenar de forma segura los secretos que son objetos de octeto de tamaño limitado sin ninguna semántica específica. Un almacén de claves puede contener una combinación de claves y secretos. El control de acceso para los tipos de objetos se administra de forma independiente.

Los usuarios, siempre que obtengan una autorización correcta, pueden hacer lo siguiente:

- Administrar claves criptográficas mediante [Crear](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Actualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Eliminar](https://msdn.microsoft.com/library/azure/dn903611.aspx) y otras operaciones

- Administrar la información confidencial mediante [Obtener](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Actualizar] (https://msdn.microsoft.com/library/azure/dn986818.aspx, [Eliminar](https://msdn.microsoft.com/library/azure/dn903613.aspx) y otras operaciones)

- Usar claves de cifrado con operaciones de [Inicio de sesión](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Comprobar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) y [Cifrar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Descifrar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Las operaciones en los almacenes de claves se autentican y autorizan mediante Azure Active Directory.

## Programación para el almacén de claves

El sistema de administración del almacén de claves para los programadores está compuesto por varias interfaces, con REST como base.

### REST

La API de REST es la base de toda la interacción mediante programación con el almacén de claves.

Almacén de claves tiene su propio extremo REST que se describe en la [Referencia de la API de REST del almacén de claves](https://msdn.microsoft.com/library/azure/dn903609.aspx)

### .NET

La API de .NET es un conjunto de contenedores que permite la implementación a través del modelo de programación de C# sin necesidad de interactuar directamente con el extremo REST. Aquí se puede encontrar la [referencia de la API de cliente de .NET del almacén de claves de Azure](https://msdn.microsoft.com/library/azure/dn903301.aspx).

### Node.js

La API de Node.js es un conjunto de contenedores que permite la implementación a través del modelo de programación de JavaScript sin necesidad de interactuar directamente con el extremo REST. Aquí puede encontrar el [SDK de Azure para Node.js - Administración del almacén de claves](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/).

## Administración del almacén de claves con PowerShell y CLI

Las claves y secretos del almacén de claves de Azure también puede administrarse mediante PowerShell y CLI, tal como se describe en los siguientes artículos:

- [Crear y administrar almacenes claves con PowerShell](key-vault-get-started.md)
- [Crear y administrar almacenes claves con CLI](key-vault-manage-with-cli.md)
- [Generación y transferencia de claves protegidas con HSM para el Almacén de claves de Azure](key-vault-hsm-protected-keys.md)
- [Acerca de las claves y secretos](https://msdn.microsoft.com/library/azure/dn903623.aspx)

## Consulte también

- [Ejemplos de código de almacén de claves de Azure](http://www.microsoft.com/download/details.aspx?id=45343)

<!---HONumber=Sept15_HO4-->