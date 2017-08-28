---
title: Seguridad del contenedor de Azure Service Fabric | Microsoft Docs
description: "Obtenga información sobre la protección de servicios de contenedor."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: a262730aec6ce5a1c6f3b7d2d41056a6e6edfbe0
ms.contentlocale: es-es
ms.lasthandoff: 08/16/2017

---

# <a name="container-security"></a>Seguridad del contenedor

Service Fabric proporciona un mecanismo para los servicios dentro de un contenedor para acceder a un certificado que está instalado en los nodos de un clúster de Windows o Linux (versión 5.7 o superior). Además, Service Fabric también admite gMSA (cuentas de servicio administradas de grupo) para los contenedores de Windows. 

## <a name="certificate-management-for-containers"></a>Administración de certificados para contenedores

Puede proteger los servicios de contenedor especificando un certificado. Este certificado debe instalarse en los nodos del clúster. La información del certificado se proporciona en el manifiesto de aplicación en la etiqueta `ContainerHostPolicies` como se muestra en el siguiente fragmento de código:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Al iniciar la aplicación, el entorno en tiempo de ejecución lee los certificados y genera un archivo PFX y una contraseña para cada certificado. Se puede acceder a este archivo PFX y a la contraseña dentro del contenedor mediante las siguientes variables de entorno: 

* **Certificate_[CodePackageName]_[CertName]_PFX**
* **Certificate_[CodePackageName]_[CertName]_Password**

El proceso o el servicio de contenedor es el responsable de importar el archivo PFX en el contenedor. Para importar el certificado, puede usar scripts de `setupentrypoint.sh` o código personalizado ejecutado dentro del proceso de contenedor. A continuación se muestra código de ejemplo en C# para importar el archivo PFX:

```c#
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificate_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificate_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
Este certificado PFX puede usarse para autenticar la aplicación o el servicio o proteger la comunicación con otros servicios.


## <a name="set-up-gmsa-for-windows-containers"></a>Configuración de gMSA para contenedores de Windows

Para configurar la gMSA (cuentas de servicio administradas de grupo), se coloca un archivo de especificación de credenciales (`credspec`) en todos los nodos del clúster. El archivo se puede copiar en todos los nodos mediante una extensión de VM.  El archivo `credspec` debe contener la información de cuenta de gMSA. Para obtener más información sobre el archivo `credspec`, vea [Service Accounts](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts) (Cuentas de servicio). La especificación de credenciales y la etiqueta `Hostname` se especifican en el manifiesto de aplicación. La etiqueta `Hostname` debe coincidir con el nombre de cuenta de gMSA bajo la que se ejecuta el contenedor.  La etiqueta `Hostname` permite al contenedor autenticarse en otros servicios en el dominio mediante la autenticación Kerberos.  En el fragmento de código siguiente se muestra un ejemplo para especificar `Hostname` y `credspec` en el manifiesto de aplicación:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>Pasos siguientes

* [Implementación de un contenedor de Windows en Service fabric en Windows Server 2016](service-fabric-get-started-containers.md)
* [Implementación de un contenedor de Docker en Service Fabric en Linux](service-fabric-get-started-containers-linux.md)

