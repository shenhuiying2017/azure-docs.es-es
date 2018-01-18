---
title: "Configuración de Postman para llamadas API de REST de Azure Media Services"
description: "Aprenda cómo configurar Postman para llamadas API de REST de Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: juliako
ms.openlocfilehash: a24b73a93bddbeb5b56ddfdf604fa99faccea442
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configuración de Postman para llamadas API de REST de Azure Media Services

En este tutorial se muestra cómo configurar **Postman** para que se pueda usar para llamar a las API de REST de Azure Media Services (AMS). El tutorial muestra cómo importar archivos de entorno y colección a **Postman**. La colección contiene definiciones agrupadas de solicitudes HTTP que llaman a API de REST de Azure Media Services. El archivo de entorno contiene variables que la colección usa.

Este entorno y esta colección se utilizan en los artículos que muestran cómo realizar diversas tareas con las API de REST de Azure Media Services.

## <a name="prerequisites"></a>Requisitos previos

- Instale el cliente de REST de [Postman](https://www.getpostman.com/) para ejecutar las API de REST mostradas en algunos de los tutoriales de REST de AMS. 

    Usamos **Postman** pero cualquier herramienta de REST sería adecuada. Otras alternativas son: **Visual Studio Code** con el complemento de REST o **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Configuración del entorno 

1. Cree un archivo .json que contenga las variables de entorno utilizadas en los tutoriales de AMS. Asigne el nombre **AzureMediaServices.postman_environment.json** al archivo. Abra el archivo y pegue el código que define el entorno de Postman desde [esta lista de códigos](postman-environment.md). 
2. Abra **Postman**.
3. A la derecha de la pantalla, seleccione la opción **Manage Environments** (Administrar entornos).

    ![Cargar un archivo](./media/media-services-rest-upload-files/postman-create-env.png)
4. En el cuadro de diálogo **Manage Environments** (Administrar entornos), haga clic en **Import** (Importar).
5. Busque y seleccione el archivo **AzureMediaServices.postman_environment.json**.
6. Se agregará el entorno **AzureMedia**.
7. Cierre el cuadro de diálogo.
8. Seleccione el entorno **AzureMedia**.

    ![Cargar un archivo](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configuración de la colección

1. Cree un archivo .json que contenga la colección de **Postman** con todas las operaciones que son necesarios para cargar un archivo a Media Services. Asigne el nombre **AzureMediaServicesOperations.postman_collection.json** al archivo. Abra el archivo y pegue el código que define la colección de **Postman** desde [esta lista de códigos](postman-collection.md).
2. Haga clic en **Import** (Importar) para importar el archivo de la colección.
3. Elija el archivo **AzureMediaServicesOperations.postman_collection.json**.

    ![Cargar un archivo](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>pasos siguientes

Consulte el artículo sobre [carga de activos](media-services-rest-upload-files.md).  
