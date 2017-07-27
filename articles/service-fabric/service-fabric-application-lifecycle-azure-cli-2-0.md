---
title: "Administración de aplicaciones de Service Fabric mediante la CLI de Azure 2.0"
description: "Describe el proceso de implementación y eliminación de aplicaciones de un clúster de Service Fabric mediante la CLI de Azure 2.0"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>Administración de una aplicación de Service Fabric mediante la CLI de Azure 2.0

Siga esta documentación para crear y eliminar aplicaciones que se ejecutan en un clúster de Service Fabric.

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de instalar la CLI de Azure 2.0 y seleccionar el clúster de Service Fabric. Puede encontrar más información en la [documentación de introducción a la CLI de Azure 2.0](service-fabric-azure-cli-2-0.md).

También debe tener un paquete de aplicación de Service Fabric listo para la implementación. Puede obtener más información acerca de cómo crear y empaquetar una aplicación en la [documentación del modelo de aplicación](service-fabric-application-model.md).

## <a name="overview"></a>Información general

La implementación de una nueva aplicación consta de cuatro pasos:

1. Carga de un paquete de aplicación en el almacén de imágenes de Service Fabric
1. Aprovisionamiento de un tipo de aplicación
1. Especificación y creación de una aplicación
1. Especificación y creación de servicios

La eliminación de una aplicación existente precisa tres pasos:

1. Eliminar aplicación
1. Anulación del aprovisionamiento del tipo de aplicación asociado
1. Eliminación del contenido del almacén de imágenes

## <a name="deploy-a-new-application"></a>Implementación de una nueva aplicación

Para implementar una nueva aplicación, siga estos pasos:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carga de un nuevo paquete de aplicación en el almacén de imágenes

Antes de crear una aplicación, el paquete de aplicación debe cargarse en el almacén de imágenes de Service Fabric.
Suponga que el paquete de aplicación se encuentra en el directorio `app_package_dir`. Use los comandos siguientes para cargar el directorio:

```azurecli
az sf application upload --path ~/app_package_dir
```

Para paquetes de aplicación de gran tamaño, puede especificar la opción `--show-progress` para mostrar el progreso de la carga.

### <a name="provision-application-type"></a>Aprovisionamiento del tipo de aplicación

Una vez finalizada la carga, la aplicación tiene que aprovisionarse. Utilice el siguiente comando para aprovisionar la aplicación

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

El `application-type-build-path` es el mismo que el nombre del directorio que contiene el paquete de aplicación cargado previamente

### <a name="create-application-from-application-type"></a>Creación de una aplicación desde el tipo de aplicación

Después de que se aprovisione la aplicación, puede poner un nombre a la aplicación y crearla mediante el siguiente comando:

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

Aquí `app-name` es el nombre que le gustaría poner a la instancia de la aplicación. Los demás parámetros pueden encontrarse en el manifiesto de aplicación aprovisionado anteriormente.

El nombre de la aplicación debe comenzar con el prefijo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Creación de servicios para la nueva aplicación

Una vez creada una aplicación, puede crear servicios desde la aplicación. En este ejemplo, creamos un nuevo servicio sin estado desde nuestra aplicación. Los servicios que puede crear a partir de una aplicación se definen en un manifiesto de servicio dentro del paquete de aplicación aprovisionado anteriormente.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-creation-and-health"></a>Comprobación de la creación y el estado de la aplicación

Para comprobar que una aplicación y el servicio se han implementado correctamente, puede comprobar que la aplicación y el servicio se muestren utilizando los siguientes comandos:

```azurecli
az sf application list
az sf service list --application-list TestApp
```

Para comprobar que el servicio es correcto, use comandos similares para recuperar el estado del servicio y de la aplicación

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

Las aplicaciones y los servicios correctos deben tener un valor `HealthState` de `Ok`.

## <a name="remove-an-existing-application"></a>Eliminación de una aplicación existente

Para quitar una aplicación, siga estos pasos:

### <a name="delete-the-application"></a>Eliminación de la aplicación

Eliminación de la aplicación ejecutando el siguiente comando

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Anulación del aprovisionamiento del tipo de aplicación

Una vez eliminada la aplicación, se puede anular el aprovisionamiento del tipo de aplicación si ya no es necesario. Utilice el siguiente comando para anular el aprovisionamiento del tipo de aplicación

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

En este caso, el nombre de tipo y la versión de tipo deben coincidir con el nombre y la versión del manifiesto de aplicación aprovisionado anteriormente

### <a name="delete-application-package"></a>Eliminación del paquete de aplicación

Después de que se haya anulado el aprovisionamiento del tipo de aplicación, el paquete de aplicación puede eliminarse desde el almacén de imágenes si ya no es necesario. La eliminación de paquetes de aplicación le ayuda a recuperar espacio en disco. Utilice el siguiente comando para eliminar el paquete de aplicación del almacén de imágenes:

```azurecli
az sf application package-delete --content-path app_package_dir
```

En este caso, el `content-path` debe ser el mismo nombre que el directorio que se cargó inicialmente al crear la aplicación

## <a name="related-articles"></a>Artículos relacionados

* [Getting started with Service Fabric and Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (Introducción a Service Fabric y la CLI de Azure 2.0)
* [Getting started with Service Fabric XPlat CLI](service-fabric-azure-cli.md) (Introducción a la CLI de XPlat de Service Fabric)

