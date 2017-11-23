---
title: "Administración de aplicaciones de Azure Service Fabric mediante la CLI de Azure Service Fabric"
description: "Aprenda a implementar y quitar aplicaciones de un clúster de Azure Service Fabric mediante la CLI de Azure Service Fabric."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 08/22/2017
ms.author: edwardsa
ms.openlocfilehash: 6c8501e8c863e1240a277bf8064118a2b113a81b
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli"></a>Administración de aplicaciones de Azure Service Fabric mediante la CLI de Azure Service Fabric

Aprenda cómo crear y eliminar aplicaciones que se ejecutan en un clúster de Azure Service Fabric.

## <a name="prerequisites"></a>Requisitos previos

* Instale la CLI de Service Fabric. A continuación, seleccione su clúster de Service Fabric. Para más información, consulte [Introducción a la CLI de Service Fabric](service-fabric-cli.md).

* Tenga un paquete de aplicación de Service Fabric listo para la implementación. Para más información sobre cómo crear y empaquetar una aplicación, lea la información sobre el [modelo de aplicación de Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Información general

Para implementar una nueva aplicación, complete estos pasos:

1. Cargue un paquete de aplicación en el almacén de imágenes de Service Fabric.
2. Aprovisione un tipo de aplicación.
3. Elimine el contenido del almacén de imágenes.
4. Especifique y cree una aplicación.
5. Especifique y cree servicios.

Para quitar una aplicación existente, complete estos pasos:

1. Elimine la aplicación.
2. Anule el aprovisionamiento del tipo de aplicación asociado.

## <a name="deploy-a-new-application"></a>Implementación de una nueva aplicación

Para implementar una nueva aplicación, realice las tareas siguientes:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carga de un nuevo paquete de aplicación en el almacén de imágenes

Antes de crear una aplicación, cargue el paquete de aplicación en el almacén de imágenes de Service Fabric.

Por ejemplo, si el paquete de aplicación está en el directorio `app_package_dir`, use los siguientes comandos para cargar el directorio:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Para paquetes de aplicación de gran tamaño, puede especificar la opción `--show-progress` para mostrar el progreso de la carga.

### <a name="provision-the-application-type"></a>Aprovisionamiento del tipo de aplicación

Cuando la carga haya finalizado, aprovisione la aplicación. Utilice el siguiente comando para aprovisionar la aplicación:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

El valor de `application-type-build-path` es el nombre del directorio donde cargó el paquete de aplicación.

### <a name="delete-the-application-package"></a>Eliminación del paquete de aplicación

Se recomienda que quite el paquete de aplicación después de que la aplicación se haya registrado correctamente.  Al eliminar los paquetes de aplicación del almacén de imágenes, se liberan recursos del sistema.  Mantener los paquetes de aplicación sin usar consume almacenamiento en disco y conduce a problemas de rendimiento de la aplicación. 

Para eliminar el paquete de aplicación del almacén de imágenes, use el comando siguiente:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` debe ser el nombre del directorio que cargó cuando creó la aplicación.

### <a name="create-an-application-from-an-application-type"></a>Creación de una aplicación desde un tipo de aplicación

Después de que se aprovisione la aplicación, utilice el comando siguiente para asignar un nombre a la aplicación y crearla:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` es el nombre que desea utilizar para la instancia de la aplicación. Puede obtener parámetros adicionales del manifiesto de aplicación aprovisionado anteriormente.

El nombre de la aplicación debe comenzar con el prefijo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Creación de servicios para la nueva aplicación

Una vez creada una aplicación, puede crear servicios desde ella. En el siguiente ejemplo, creamos un nuevo servicio sin estado desde nuestra aplicación. Los servicios que puede crear a partir de una aplicación se definen en un manifiesto de servicio en del paquete de aplicación aprovisionado anteriormente.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Comprobación de implementación y el estado de la aplicación

Para comprobar que todo es correcto, use los siguientes comandos de mantenimiento:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Para comprobar que el servicio es correcto, use comandos similares para recuperar tanto el estado del servicio como el de la aplicación:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Las aplicaciones y los servicios correctos tienen un valor `HealthState` de `Ok`.

## <a name="remove-an-existing-application"></a>Eliminación de una aplicación existente

Para quitar una aplicación, realice las tareas siguientes:

### <a name="delete-the-application"></a>Eliminación de la aplicación

Para eliminar la aplicación, use el siguiente comando:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Anulación del aprovisionamiento del tipo de aplicación

Después de eliminar la aplicación, puede deshacer el aprovisionamiento del tipo de aplicación si ya no lo necesita. Para anular el aprovisionamiento del tipo de aplicación, use el siguiente comando:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

El nombre de tipo y la versión de tipo deben coincidir con el nombre y la versión del manifiesto de aplicación aprovisionado anteriormente.

## <a name="upgrade-application"></a>Actualización de una aplicación

Después de crear la aplicación, puede repetir el mismo conjunto de pasos para aprovisionar una segunda versión de la aplicación. Luego, con una actualización de la aplicación de Service Fabric puede pasar a la ejecución de la segunda versión de la aplicación. Para más información, consulte la documentación de [Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md).

Para llevar a cabo una actualización, primero aprovisione la siguiente versión de la aplicación mediante los mismos comandos que antes:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Se recomienda luego realizar una actualización automática supervisada e iniciar la actualización ejecutando el comando siguiente:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Las actualizaciones reemplazan los parámetros existentes por el conjunto especificado. Los parámetros de la aplicación se deben pasar como argumentos al comando de actualización, si es necesario. Los parámetros de la aplicación deben codificarse como objetos JSON.

Para recuperar un parámetro anteriormente especificado, puede usar el comando `sfctl application info`.

Cuando una actualización de la aplicación está en curso, el estado se puede recuperar mediante el comando `sfctl application upgrade-status`.

Finalmente, si una actualización está en curso y es necesario cancelarla, puede usar `sfctl application upgrade-rollback` para revertirla.

## <a name="next-steps"></a>Pasos siguientes

* [Conceptos básicos de la CLI de Service Fabric](service-fabric-cli.md)
* [Introducción a Service Fabric con Linux](service-fabric-get-started-linux.md)
* [Inicio de una la actualización de una aplicación de Service Fabric](service-fabric-application-upgrade.md)
