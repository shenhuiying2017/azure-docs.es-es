---
title: "Versión preliminar de Azure Service Fabric Docker Compose | Microsoft Docs"
description: "Azure Service Fabric acepta el formato de Docker Compose para facilitar la orquestación de los contenedores existentes mediante Service Fabric. Esta compatibilidad se encuentra actualmente en versión preliminar."
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
ms.date: 05/01/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 800f5bacd5197f64968fb1c169ef58330ee75e0d
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017

---


# <a name="compose-application-support-in-service-fabric-preview"></a>Compatibilidad con la aplicación Compose en Service Fabric (versión preliminar)

Docker usa el archivo [docker compose.yml](https://docs.docker.com/compose) para definir aplicaciones de varios contenedores. Para que sea más fácil para los clientes familiarizados con Docker orquestar las aplicaciones de contenedores existentes en Service Fabric, se ha incluido compatibilidad de vista previa para Docker Compose de forma nativa en la plataforma. Service Fabric puede aceptar la versión 3(+) de los archivos `docker-compose.yml`. Dado que esta compatibilidad está en una versión preliminar, se admite solo un subconjunto de directivas de Compose. Por ejemplo, no se admiten las actualizaciones de aplicaciones. Sin embargo, siempre puede quitar e implementar aplicaciones en lugar de actualizarlas.  

Para usar esta versión preliminar, debe instalar el SDK de la versión preliminar (versión 255.255.x.x) a través del portal. 

> [!NOTE]
> Esta característica está en versión preliminar y no tiene soporte.

## <a name="using-a-docker-composeyml-file-with-service-fabric-preview"></a>Uso de un archivo docker-compose.yml con Service Fabric (versión preliminar)

Cree una aplicación de Service Fabric a partir de un archivo docker-compose.yml ejecutando el siguiente comando de PS:

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

RepositoryUserName y RepositoryPassword hacen referencia al nombre de usuario y contraseña del registro de contenedor.

Si usa la CLI de Azure 2.0, ejecute el comando siguiente:

```bash
az sf compose create --application-id fabric:/TestContainerApp --file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```
Estos comandos crean una aplicación de Service Fabric (denominada `fabric:/TestContainerApp` en el ejemplo anterior) que se puede supervisar mediante Service Fabric Explorer(). El `ApplicationName` especificado se utiliza para realizar consultas de estado mediante PS, CLI de Azure 2.0 o SFX.

Para eliminar la aplicación mediante PS, utilice el siguiente comando:

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

Para eliminar la aplicación mediante la CLI de Azure 2.0, utilice el siguiente comando:

```bash
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

Para obtener el estado de la aplicación de Compose, use el siguiente comando en PS:

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

Para la CLI de Azure 2.0, use el siguiente comando:

```bash
az sf compose status --application-id TestContainerApp [ --timeout ]
```



## <a name="supported-compose-directives"></a>Directivas de Compose admitidas

Se admite un subconjunto de las opciones de configuración del formato Compose V3 en esta versión preliminar. Se admiten los siguientes primitivos:

* Services->Deploy->replicas    
* Services->Deploy->Placement->Constraints    
* Services->Deploy->Resources->Limits 
*         -cpu-shares    
*         -memory
*         -memory-swap
* Services->Commands    
* Services->Environment
* Services->Ports    
* Services->image
* Services->Isolation(Only for windows)
* Services->logging->driver    
* Services->logging->driver->options
* Volume & Deploy->Volume    

El clúster se debe configurar para aplicar límites de recursos como se describe en [Service Fabric Resource Governance](service-fabric-resource-governance.md) (Regulación de recursos en Service Fabric).
El resto de directivas de Docker Compose no están admitidas para esta versión preliminar. 

## <a name="servicednsname-computation"></a>Cálculo de ServiceDnsName

Si el nombre del servicio especificado en el archivo de Compose es un nombre de dominio completo (es decir, contiene un punto, "."), el nombre DNS registrado por Service Fabric será `<ServiceName>` incluido el punto. En caso contrario, cada segmento de la ruta de ApplicationName se convierte en una etiqueta de dominio del nombre DNS del servicio, siendo el primer segmento de la ruta el que se convierte en la etiqueta de dominio de nivel superior. Por tanto, si el nombre especificado de la aplicación es `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` sería el nombre DNS registrado.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Diferencias entre Compose (definición de instancia) y el modelo de aplicación de Service Fabric (definición de tipo)

El archivo docker-compose.yml describe un conjunto implementable de contenedores que incluye sus propiedades y configuraciones (por ejemplo, las variables de entorno y los puertos). Los parámetros de implementación como, por ejemplo, las restricciones de ubicación, los límites de recursos y los nombres DNS se especifican también en el archivo docker-compose.yml.

El [modelo de aplicación de Service Fabric](service-fabric-application-model.md) usa tipos de servicio y tipos de aplicación, en los que puede tener muchas instancias de aplicaciones del mismo tipo (por ejemplo, una instancia de aplicación por cliente). Este modelo basado en tipos admite varias versiones del mismo tipo de aplicación registrado con el tiempo de ejecución. Por ejemplo, un cliente A puede tener una aplicación con una instancia creada con el tipo 1.0 de AppTypeA y un cliente B puede tener otra aplicación con una instancia creada con el mismo tipo y versión. Los tipos de la aplicación se definen en los manifiestos de esta, y el nombre y los parámetros de implementación se especifican en el momento de la creación de la aplicación.

Aunque este modelo ofrece flexibilidad, tenemos intención de admitir un modelo más sencillo de implementación basado en instancias donde los tipos son implícitos en el archivo de manifiesto. En este modelo, cada aplicación obtiene su propio manifiesto independiente. Esta idea se trasladará a una versión preliminar agregando compatibilidad para el archivo docker-compose.yml, que es un formato de implementación basado en instancias.


## <a name="next-steps"></a>Pasos siguientes

* Descripción del [modelo de aplicación de Service Fabric](service-fabric-application-model.md).

