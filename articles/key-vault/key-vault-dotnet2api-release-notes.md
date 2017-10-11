---
title: "Notas de la versión de la API de .NET 2.x de Key Vault | Microsoft Docs"
description: "Los desarrolladores de .NET usarán esta API para programar para Azure Key Vault"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.openlocfilehash: c5b5fd7f16faf17d16ecc82269fb1264adf4dd06
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>.NET 2.0 de Azure Key Vault: notas de la versión y guía de migración
Tanto las notas como la guía siguientes son para desarrolladores que trabajan con la biblioteca de C# o .NET de Azure Key Vault. En la transición de la versión 1.0 a la versión 2.0, se han creado una serie de actualizaciones que requerirán un trabajo de migración en su código para que pueda disfrutar de las mejoras funcionales y la incorporación de características, como, por ejemplo, la compatibilidad con **certificados de Key Vault**.

## <a name="key-vault-certificates"></a>Certificados de Key Vault

La compatibilidad con certificados de Key Vault proporciona la administración de sus certificados x509 y los comportamientos siguientes:  

* Permite que el propietario de un certificado cree un certificado a través de un proceso de creación de Key Vault o a través de la importación de un certificado existente. Esto incluye tanto certificados autofirmados como certificados generados por una entidad de certificación.
* Permite que el propietario de un certificado de Key Vault implemente un almacenamiento seguro y la administración de certificados X509 sin interacción con material de clave privada.  
* Permite que el propietario de un certificado cree una directiva que indique a Key Vault cómo administrar el ciclo de vida de un certificado.  
* Permite que los propietarios de certificados proporcionen información de contacto para la notificación de eventos del ciclo de vida de caducidad y renovación de los certificados.  
* Admite la renovación automática con emisores seleccionados: proveedores de certificados X509 y entidades de certificación asociados con Key Vault.
  
  * NOTA: Los proveedores y entidades no asociados también pueden, pero en este caso no se admitirá la característica de renovación automática.

## <a name="net-support"></a>Compatibilidad con .NET

* **.NET 4.0** no es compatible con la versión 2.0 de .NET de Azure Key Vault ni con la biblioteca de C#
* **.NET Core** no es compatible con la versión 2.0 de .NET de Azure Key Vault ni con la biblioteca de C#

## <a name="namespaces"></a>Espacios de nombres

* El espacio de nombres para **modelos** se cambia de **Microsoft.Azure.KeyVault** a **Microsoft.Azure.KeyVault.Models**.
* El espacio de nombres **Microsoft.Azure.KeyVault.Internal** se quita.
* El espacio de nombres de las dependencias del SDK de Azure se cambia de **Hyak.Common** y **Hyak.Common.Internals** a **Microsoft.Rest** y **Microsoft.Rest.Serialization**

## <a name="type-changes"></a>Cambios de tipo

* *Secret* se cambia a *SecretBundle*
* *Dictionary* se cambia a *IDictionary*
* *List<T>, string []* se cambia a *IList<T>*
* *NextList* se cambia a  *NextPageLink*

## <a name="return-types"></a>Tipos de valor devuelto

* **KeyList** y **SecretList** devolverán *IPage<T>* en lugar de *ListKeysResponseMessage*
* El valor **BackupKeyAsync** generado devolverá *BackupKeyResult*, que contiene *Value* (blob de copia de seguridad). Antes se ajustó el método, devolviendo solo el valor.

## <a name="exceptions"></a>Excepciones

* *KeyVaultClientException* se cambia a *KeyVaultErrorException*
* El error de servicio se cambia de *exception.Error* a *exception.Body.Error.Message*.
* Información adicional quitada del mensaje de error para **[JsonExtensionData]**.

## <a name="constructors"></a>Constructores

* En lugar de aceptar *HttpClient* como argumento de constructor, el constructor solo acepta *HttpClientHandler* o *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Paquetes descargados

Al procesar un cliente una dependencia en Key Vault, se descargaron los siguientes

### <a name="previous-package-list"></a>Lista de paquetes anterior

* package id="Hyak.Common" version="1.0.2" targetFramework="net45"
* package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"
* package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"
* package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"
* package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"
* package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"

### <a name="current-package-list"></a>Lista de paquetes actual

* package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"

## <a name="class-changes"></a>Cambios de clase

* Se ha quitado la clase **UnixEpoch**
* El nombre de la clase **Base64UrlConverter** se ha cambiado a **Base64UrlJsonConverter**

## <a name="other-changes"></a>Otros cambios

* La compatibilidad con la configuración de la directiva de reintentos de operaciones de KV sobre errores transitorios se ha agregado a esta versión de la API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>NuGet Microsoft.Azure.Management.KeyVault

* Para las operaciones que devolvieron un *almacén*, el tipo de valor devuelto era una clase que contenía una propiedad Vault. Ahora el tipo de valor devuelto es *Vault*.
* Ahora *PermissionsToKeys* y *PermissionsToSecrets* son *Permissions.Keys* y *Permissions.Secrets*
* Algunos de los cambios de tipos de valor devuelto se aplican también al plano de control.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>NuGet Microsoft.Azure.KeyVault.Extensions

* El paquete se divide en **Microsoft.Azure.KeyVault.Extensions** y **Microsoft.Azure.KeyVault.Cryptography** para las operaciones de criptografía.

