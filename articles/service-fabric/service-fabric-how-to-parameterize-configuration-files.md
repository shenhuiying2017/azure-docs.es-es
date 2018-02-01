---
title: "Cómo parametrizar los archivos de configuración en Azure Service Fabric | Microsoft Docs"
description: "Se muestra cómo parametrizar los archivos de configuración en Service Fabric"
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 1e7d59ecb231440711b8ed3dc0b27a2b105890c4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Cómo parametrizar los archivos de configuración en Service Fabric

En este artículo se muestra cómo parametrizar un archivo de configuración de Service Fabric.

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedimiento para parametrizar los archivos de configuración

En este ejemplo, se reemplaza un valor de configuración mediante parámetros en la implementación de la aplicación.

1. Abra el archivo Config\Settings.xml.
1. Establezca un parámetro de configuración agregando el siguiente código XML:

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. Guarde y cierre el archivo.
1. Abra el archivo `ApplicationManifest.xml` .
1. Agregue un elemento `ConfigOverride` que haga referencia al paquete de configuración, la sección y el parámetro.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. Todavía en el archivo ApplicationManifest.xml, a continuación, especifique el parámetro en el elemento `Parameters`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. Y defina un `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> Cuando se agrega un elemento ConfigOverride, Service Fabric siempre elige los parámetros de la aplicación o el valor predeterminado especificado en el manifiesto de la aplicación.
>
>

Al publicar la aplicación desde Visual Studio con el perfil de publicación Cloud.xml, el servicio está configurado para utilizar el puerto 80. Si implementa la aplicación sin especificar el parámetro MyWebAPI_PortNumber, el servicio utiliza el puerto 8080.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre algunos de los conceptos principales que se describen en este artículo, consulte los artículos de [Administración de aplicaciones para varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).

Para obtener más información sobre otras funcionalidades de administración de aplicaciones disponibles en Visual Studio, vea [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).