---
title: "Especificación de variables de entorno para servicios de Azure Service Fabric | Microsoft Docs"
description: "Se muestra cómo usar variables de entorno en aplicaciones de Service Fabric."
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: d487eeadde9f9a45549763863f8fe5b06b2945a4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Especificación de variables de entorno para servicios de Service Fabric

En este artículo se muestra cómo especificar variables de entorno para un servicio o un contenedor de Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedimiento para especificar variables de entorno para servicios

En este ejemplo, puede establecer una variable de entorno para un contenedor. En el artículo se da por supuesto que ya tiene un manifiesto de servicio y aplicación.

1. Abra el archivo ServiceManifest.xml.
1. En el elemento `CodePackage`, agregue un nuevo elemento `EnvironmentVariables` y un elemento `EnvironmentVariable` para cada variable de entorno.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DeafultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Estas variables de entorno se pueden invalidar en el manifiesto de aplicación.

1. Para invalidar las variables de entorno en el manifiesto de aplicación, use el elemento `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>pasos siguientes
Para más información sobre algunos de los conceptos principales que se describen en este artículo, consulte los artículos de [Administración de aplicaciones para varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).

Para obtener más información sobre otras funcionalidades de administración de aplicaciones disponibles en Visual Studio, vea [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).