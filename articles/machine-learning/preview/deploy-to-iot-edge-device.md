---
title: "Implementación de un modelo de Azure Machine Learning en un dispositivo de Azure IoT Edge | Microsoft Docs"
description: "En este documento se describe cómo se pueden implementar modelos de Azure Machine Learning en dispositivos de Azure IoT Edge."
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: f83e4b17a3209adeb655723362e6882e38bfaf49
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Implementación de un modelo de Azure Machine Learning en un dispositivo de Azure IoT Edge

Todos los modelos de Azure Machine Learning almacenados en contenedores como servicios web basados en Docker-también se pueden ejecutar en dispositivos de Azure IoT Edge. Se pueden encontrar scripts e instrucciones adicionales en el [kit de herramientas de AI para Azure IoT Edge](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Puesta en marcha del modelo
Ponga en marcha el modelo mediante las instrucciones que se describen en [Implementación de un servicio web de administración de modelos de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy) para crear una imagen de Docker con su modelo.

## <a name="deploy-to-azure-iot-edge"></a>Implementación en Azure IoT Edge
Azure IoT Edge traslada los análisis en la nube y la lógica de negocios personalizada a los dispositivos. Todos los modelos de Machine Learning se pueden ejecutar en dispositivos IoT Edge. La documentación para configurar un dispositivo IoT Edge y crear una implementación se puede encontrar en [aka.ms/azure-iot-edge-doc](https://aka.ms/azure-iot-edge-doc).

Algunos aspectos adicionales que se deben tener en cuenta son:

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>Agregar credenciales del Registro al entorno de tiempo de ejecución de Edge en el dispositivo de Edge
En la máquina donde se ejecuta IoT Edge, agregue las credenciales de su registro para que el entorno de tiempo de ejecución pueda tener acceso al contenedor y extraerlo.

En Windows, ejecute el siguiente comando:
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
En Linux, ejecute el siguiente comando:
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>Buscar la ubicación de la imagen de contenedor de Machine Learning
Necesita la ubicación de la imagen de contenedor de Machine Learning. Para buscar la ubicación de la imagen de contenedor:

1. Inicie sesión en [Azure Portal](http://portal.azure.com/).
2. En **Azure Container Registry**, seleccione el registro que desea inspeccionar.
3. En el registro, haga clic en **Repositorios** para ver una lista de todos los repositorios y sus imágenes.













