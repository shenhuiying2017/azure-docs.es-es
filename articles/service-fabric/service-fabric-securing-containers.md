---
title: Importación de certificados a un contenedor que se ejecuta en Azure Service Fabric | Microsoft Docs
description: Aprenda ahora a importar los archivos de certificados a un servicio de contenedor de Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f234a6f6ca56d1833aac53f490feb5f667a6bf1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208223"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importación de un archivo de certificados a un contenedor que se ejecuta en Service Fabric

Puede proteger los servicios de contenedor especificando un certificado. Service Fabric proporciona un mecanismo para los servicios dentro de un contenedor para acceder a un certificado que está instalado en los nodos de un clúster de Windows o Linux (versión 5.7 o superior). Este certificado debe instalarse en LocalMachine en todos los nodos del clúster. La información del certificado se proporciona en el manifiesto de aplicación en la etiqueta `ContainerHostPolicies` como se muestra en el siguiente fragmento de código:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Para clústeres Windows, al iniciar la aplicación, el entorno en tiempo de ejecución lee los certificados y genera un archivo PFX y una contraseña para cada certificado. Se puede acceder a este archivo PFX y a la contraseña dentro del contenedor mediante las siguientes variables de entorno: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Para clústeres Linux, los certificados (PEM) se copian del almacén que especifica X509StoreName al contenedor. Las variables de entorno correspondientes en Linux son:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Como alternativa, si ya tiene los certificados con el formato necesario y quiere poder acceder desde dentro del contenedor, puede crear un paquete de datos dentro del paquete de aplicación y especificar lo siguiente en el manifiesto de aplicación:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

El proceso o el servicio de contenedor es el responsable de importar archivos de certificado en el contenedor. Para importar el certificado, puede usar scripts de `setupentrypoint.sh` o ejecutar código personalizado dentro del proceso de contenedor. A continuación se muestra código de ejemplo en C# para importar el archivo PFX:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Este certificado PFX puede usarse para autenticar la aplicación o el servicio, o para proteger la comunicación con otros servicios. De forma predeterminada, los archivos se agregan a listas de control de acceso solo en SYSTEM. Puede agregarlos a listas de control de acceso en otras cuentas, según lo requiera el servicio.

A continuación, lea los siguientes artículos:

* [Implementación de un contenedor de Windows en Service fabric en Windows Server 2016](service-fabric-get-started-containers.md)
* [Implementación de un contenedor de Docker en Service Fabric en Linux](service-fabric-get-started-containers-linux.md)
