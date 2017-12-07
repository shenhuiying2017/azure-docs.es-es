---
title: "Usar Azure Event Grid para automatizar el cambio de tamaño de imágenes cargadas | Microsoft Docs"
description: "Azure Event Grid puede desencadenarse en cargas de blob de Azure Storage. Puede usarlo para enviar archivos de imagen cargados en Azure Storage a otros servicios, como Azure Functions, a fin de cambiar el tamaño y otras mejoras."
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 22eafca56eb5677c63a833d298799b725c50f768
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Automatizar el cambio de tamaño de imágenes cargadas mediante Event Grid

[Azure Event Grid](overview.md) es un servicio de eventos para la nube. Event Grid permite crear suscripciones a eventos producidos por servicios de Azure o recursos de terceros.  

Este tutorial es la segunda parte de una serie de tutoriales sobre almacenamiento. Amplía el [tutorial anterior sobre almacenamiento][previous-tutorial] para agregar la generación de vistas en miniatura automática sin servidor con Azure Event Grid y Azure Functions. Event Grid permite que [Azure Functions](..\azure-functions\functions-overview.md) responda a eventos de [Azure Blob Storage](..\storage\blobs\storage-blobs-introduction.md) y genere vistas en miniatura de imágenes cargadas. Se crea una suscripción de eventos en el evento de creación de Blob Storage. Cuando se agrega un blob a un contenedor de Blob Storage determinado, se llama a un punto de conexión de función. Los datos pasados al enlace de función desde Event Grid se usan para acceder al blob y generar la imagen en miniatura. 

Use la CLI de Azure y Azure Portal para agregar la funcionalidad de cambio de tamaño a una aplicación existente de carga de imágenes.

![Aplicación web publicada en el explorador Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una cuenta general de Azure Storage
> * Implementar código sin servidor con Azure Functions
> * Crear una suscripción de eventos de Blob Storage en Event Grid

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

+ Debe haber realizado el tutorial anterior de Blob Storage: [Upload image data in the cloud with Azure Storage (Cargar datos de imagen en la nube con Azure Storage)][previous-tutorial]. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0.14 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

Si no usa Cloud Shell, primero debe iniciar sesión con `az login`.

## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

Azure Functions necesita una cuenta de almacenamiento general. Cree una cuenta de almacenamiento general independiente en el grupo de recursos con el comando [az storage account create](/cli/azure/storage/account#create).

Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas. 

En el siguiente comando, sustituya su propio nombre único global por la cuenta de almacenamiento general donde vea el marcador de posición `<general_storage_account>`. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Creación de una aplicación de función  

Debe tener una Function App para hospedar la ejecución de la función. La Function App proporciona un entorno para la ejecución sin servidor de su código de función. Cree una Function App con el comando [az functionapp create](/cli/azure/functionapp#create). 

En el siguiente comando, sustituya su propio nombre único de Function App donde vea el marcador de posición `<function_app>`. El `<function_app>` se usa como el dominio DNS predeterminado para la Function App y, por ello, el nombre debe ser único en todas las aplicaciones de Azure. En este caso, `<general_storage_account>` es el nombre de la cuenta de almacenamiento general creada.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

Ahora debe configurar la Function App para conectarse a Blob Storage. 

## <a name="configure-the-function-app"></a>Configuración de la Function App

La función necesita la cadena de conexión para conectarse a la cuenta de Blob Storage. En este caso, `<blob_storage_account>` es el nombre de la cuenta de Blob Storage creada en el tutorial anterior. Obtenga la cadena de conexión con el comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). El nombre del contenedor de la imagen en miniatura también debe establecerse en `thumbs`. Agregue esta configuración de aplicación a la Function App con el comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

Ahora puede implementar un proyecto de código de función en esta Function App.

## <a name="deploy-the-function-code"></a>Implementación del código de función 

La función de C# que realiza el cambio de tamaño de la imagen está disponible en este [repositorio de GitHub de ejemplo](https://github.com/Azure-Samples/function-image-upload-resize). Implemente este proyecto de código de Functions en la Function App mediante el comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config). 

En el siguiente comando, `<function_app>` es la misma Function App creada en el script anterior.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

La función de cambio de tamaño de imagen se desencadena mediante una suscripción de eventos a un evento de blob creado. Los datos pasados al desencadenador incluyen la dirección URL del blob, que a su vez se pasa en el enlace de entrada para obtener la imagen cargada desde Blob Storage. La función genera una imagen en miniatura y escribe el flujo resultante en un contenedor independiente de Blob Storage. Para más información sobre esta función, vea el [archivo Léame en el repositorio de ejemplo](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md).
 
El código de proyecto de función se implementa directamente desde el repositorio público de ejemplo. Para más información sobre las opciones de implementación de Azure Functions, vea [Implementación continua para Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-your-event-subscription"></a>Creación de la suscripción de eventos

Una suscripción de eventos indica los eventos generados por el proveedor que se quieren enviar a un punto de conexión determinado. En este caso, el punto de conexión se expone mediante la función. Use los pasos siguientes para crear una suscripción de eventos a partir de la función en Azure Portal: 

1. En [Azure Portal](https://portal.azure.com), haga clic en la flecha de la parte inferior izquierda para expandir todos los servicios, escriba `functions` en el campo **Filtro** y luego elija **Function Apps**. 

    ![Function Apps en Azure Portal](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Expanda la Function App, elija la función **imageresizerfunc** y luego seleccione **Agregar suscripción a Event Grid**.

    ![Function Apps en Azure Portal](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Use la configuración de suscripción de eventos que se especifica en la tabla.

    ![Creación de suscripción de eventos a partir de la función en Azure Portal](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | imageresizersub | Nombre que identifica a la nueva suscripción de eventos. | 
    | **Tipo de tema** |  Cuentas de almacenamiento | Elija el proveedor de eventos de las cuentas de almacenamiento. | 
    | **Suscripción** | Su suscripción | De forma predeterminada, se debe seleccionar la suscripción actual.   |
    | **Grupos de recursos** | myResourceGroup | Seleccione **Usar existente** y elija el grupo de recursos que se ha venido usando en este tema.  |
    | **Instancia** |  `<blob_storage_account>` |  Elija la cuenta de Blob Storage que ha creado. |
    | **Tipos de evento** | Blob creado | Desactive todos los tipos que no sean **Blob creado**. Solo los tipos de evento de `Microsoft.Storage.BlobCreated` se pasan a la función.| 
    | **Punto de conexión de suscriptor** | generado automáticamente | Use la dirección URL del punto de conexión generado automáticamente. | 
    | **Filtro de prefijo** | /blobServices/default/containers/images/blobs/ | Filtra los eventos de almacenamiento a solo aquellos del contenedor **images**.| 

4. Haga clic en **Crear** para agregar la suscripción de eventos. Con esto se crea una suscripción de eventos que desencadena **imageresizerfunc** cuando se agrega un blob al contenedor **images**. Las imágenes cuyo tamaño ha cambiado se agregan al contenedor **thumbs**.

Ahora que se han configurado los servicios back-end, pruebe la funcionalidad de cambio de tamaño de imagen en la aplicación web de ejemplo. 

## <a name="test-the-sample-app"></a>Prueba de la aplicación de ejemplo

Para probar el cambio de tamaño de imagen en la aplicación web, vaya a la dirección URL de la aplicación publicada. La dirección URL predeterminada de la aplicación web es `https://<web_app>.azurewebsites.net`.

Haga clic en la región **Cargar fotos** para seleccionar y cargar un archivo. También puede arrastrar una foto a esta región. 

Observe que después de que la imagen cargada desaparezca, se muestra una copia de ella en el carrusel **Miniaturas generadas**. El tamaño de esta imagen ha sido cambiado por la función, la imagen se ha agregado al contenedor thumbs y ha sido descargada por el cliente web. 

![Aplicación web publicada en el explorador Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear una cuenta general de Azure Storage
> * Implementar código sin servidor con Azure Functions
> * Crear una suscripción de eventos de Blob Storage en Event Grid

Vaya a la tercera parte de la serie de tutoriales sobre almacenamiento para aprender a proteger el acceso a la cuenta de almacenamiento.

> [!div class="nextstepaction"]
> [Proteger el acceso a los datos de una aplicación en la nube](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


+ Para más información sobre Event Grid, vea [Una introducción a Event Grid](overview.md). 
+ Para probar otro tutorial sobre Azure Functions, vea [Creación de una función que se integre con Azure Logic Apps](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md