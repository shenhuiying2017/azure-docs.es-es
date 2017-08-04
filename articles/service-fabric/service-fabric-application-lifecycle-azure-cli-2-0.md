---
title: "Administración de aplicaciones de Azure Service Fabric mediante la CLI de Azure 2.0"
description: "Aprenda cómo implementar y quitar aplicaciones de un clúster de Azure Service Fabric mediante la CLI de Azure 2.0."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5728339236e3819b301e428f9d7a8add08f02b3e
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-cli-20"></a>Administración de una aplicación de Azure Service Fabric mediante la CLI de Azure 2.0

Aprenda cómo crear y eliminar aplicaciones que se ejecutan en un clúster de Azure Service Fabric.

## <a name="prerequisites"></a>Requisitos previos

* Instale la CLI de Azure 2.0. A continuación, seleccione su clúster de Service Fabric. Para más información, consulte [Service Fabric y CLI de Azure 2.0](service-fabric-azure-cli-2-0.md).

* Tenga un paquete de aplicación de Service Fabric listo para la implementación. Para más información sobre cómo crear y empaquetar una aplicación, lea la información sobre el [modelo de aplicación de Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Información general

Para implementar una nueva aplicación, complete estos pasos:

1. Cargue un paquete de aplicación en el almacén de imágenes de Service Fabric.
2. Aprovisione un tipo de aplicación.
3. Especifique y cree una aplicación.
4. Especifique y cree servicios.

Para quitar una aplicación existente, complete estos pasos:

1. Elimine la aplicación.
2. Anule el aprovisionamiento del tipo de aplicación asociado.
3. Elimine el contenido del almacén de imágenes.

## <a name="deploy-a-new-application"></a>Implementación de una nueva aplicación

Para implementar una nueva aplicación, complete las tareas siguientes.

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carga de un nuevo paquete de aplicación en el almacén de imágenes

Antes de crear una aplicación, cargue el paquete de aplicación en el almacén de imágenes de Service Fabric. 

Por ejemplo, si el paquete de aplicación está en el directorio `app_package_dir`, use los siguientes comandos para cargar el directorio:

```azurecli
az sf application upload --path ~/app_package_dir
```

Para paquetes de aplicación de gran tamaño, puede especificar la opción `--show-progress` para mostrar el progreso de la carga.

### <a name="provision-the-application-type"></a>Aprovisionamiento del tipo de aplicación

Cuando la carga haya finalizado, aprovisione la aplicación. Utilice el siguiente comando para aprovisionar la aplicación:

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

El valor de `application-type-build-path` es el nombre del directorio donde cargó el paquete de aplicación.

### <a name="create-an-application-from-an-application-type"></a>Creación de una aplicación desde un tipo de aplicación

Después de que se aprovisione la aplicación, utilice el comando siguiente para asignar un nombre a la aplicación y crearla:

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` es el nombre que desea utilizar para la instancia de la aplicación. Puede obtener parámetros adicionales del manifiesto de aplicación aprovisionado anteriormente.

El nombre de la aplicación debe comenzar con el prefijo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Creación de servicios para la nueva aplicación

Una vez creada una aplicación, puede crear servicios desde ella. En el siguiente ejemplo, creamos un nuevo servicio sin estado desde nuestra aplicación. Los servicios que puede crear a partir de una aplicación se definen en un manifiesto de servicio en del paquete de aplicación aprovisionado anteriormente.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Comprobación de implementación y el estado de la aplicación

Para comprobar que una aplicación y un servicio se implementaron correctamente, compruebe que la aplicación y el servicio se muestren:

```azurecli
az sf application list
az sf service list --application-list TestApp
```

Para comprobar que el servicio es correcto, use comandos similares para recuperar tanto el estado del servicio como el de la aplicación:

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

Las aplicaciones y los servicios correctos tienen un valor `HealthState` de `Ok`.

## <a name="remove-an-existing-application"></a>Eliminación de una aplicación existente

Para quitar una nueva aplicación, complete las tareas siguientes.

### <a name="delete-the-application"></a>Eliminación de la aplicación

Para eliminar la aplicación, use el siguiente comando:

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Anulación del aprovisionamiento del tipo de aplicación

Después de eliminar la aplicación, puede deshacer el aprovisionamiento del tipo de aplicación si ya no lo necesita. Para anular el aprovisionamiento del tipo de aplicación, use el siguiente comando:

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

El nombre de tipo y la versión de tipo deben coincidir con el nombre y la versión del manifiesto de aplicación aprovisionado anteriormente.

### <a name="delete-the-application-package"></a>Eliminación del paquete de aplicación

Después de que se haya anulado el aprovisionamiento del tipo de aplicación, puede eliminar el paquete de aplicación del almacén de imágenes si ya no lo necesita. La eliminación de paquetes de aplicación le ayuda a recuperar espacio en disco. 

Para eliminar el paquete de aplicación del almacén de imágenes, use el comando siguiente:

```azurecli
az sf application package-delete --content-path app_package_dir
```

`content-path` debe ser el nombre del directorio que cargó cuando creó la aplicación.

## <a name="related-articles"></a>Artículos relacionados

* [Service Fabric y CLI de Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Uso de la CLI de XPlat para interactuar con un clúster de Service Fabric](service-fabric-azure-cli.md)

