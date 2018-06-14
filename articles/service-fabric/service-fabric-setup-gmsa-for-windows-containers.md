---
title: Configuración de gMSA para servicios de contenedor de Azure Service Fabric | Microsoft Docs
description: Aprenda ahora a configurar gMSA para un contenedor que se ejecuta en Azure Service Fabric.
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
ms.date: 03/23/2018
ms.author: subramar
ms.openlocfilehash: e4cd0b42e21609f88edc28c8fd7b5c433d56b3c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209100"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configuración de gMSA para contenedores de Windows que se ejecutan en Service Fabric

Para configurar la gMSA (cuentas de servicio administradas de grupo), se coloca un archivo de especificación de credenciales (`credspec`) en todos los nodos del clúster. El archivo se puede copiar en todos los nodos mediante una extensión de VM.  El archivo `credspec` debe contener la información de cuenta de gMSA. Para obtener más información sobre el archivo `credspec`, vea [Service Accounts](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts) (Cuentas de servicio). La especificación de credenciales y la etiqueta `Hostname` se especifican en el manifiesto de aplicación. La etiqueta `Hostname` debe coincidir con el nombre de cuenta de gMSA bajo la que se ejecuta el contenedor.  La etiqueta `Hostname` permite al contenedor autenticarse en otros servicios en el dominio mediante la autenticación Kerberos.  En el fragmento de código siguiente se muestra un ejemplo para especificar `Hostname` y `credspec` en el manifiesto de aplicación:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
A continuación, lea los siguientes artículos:

* [Implementación de un contenedor de Windows en Service fabric en Windows Server 2016](service-fabric-get-started-containers.md)
* [Implementación de un contenedor de Docker en Service Fabric en Linux](service-fabric-get-started-containers-linux.md)
