---
title: Personalización de la imagen de contenedor usada para implementar modelos de Azure ML | Microsoft Docs
description: En este artículo se describe cómo personalizar una imagen de contenedor para los modelos de Azure Machine Learning.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 8ff2bca405bbd8faeaa4527f493804950fced6ce
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Personalización de la imagen de contenedor usada para los modelos de Azure ML

En este artículo se describe cómo personalizar una imagen de contenedor para los modelos de Azure Machine Learning.  Azure ML Workbench usa contenedores para la implementación de modelos de aprendizaje automático. Los modelos se implementan junto con sus dependencias y Azure ML genera una imagen a partir del modelo, las dependencias y los archivos asociados.

## <a name="how-to-customize-the-docker-image"></a>Personalización de la imagen de Docker
Personalice la imagen de Docker que Azure ML implementa mediante:

1. Un archivo `dependencies.yml`: para administrar las dependencias que se pueden instalar desde [PyPi]( https://pypi.python.org/pypi), puede usar el archivo `conda_dependencies.yml` del proyecto Workbench, o crear el suyo propio. Este es el enfoque recomendado para la instalación de las dependencias de Python en las que se puede instalar PIP.

   Ejemplo del comando de la CLI:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Ejemplo del archivo conda_dependencies: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Un archivo de pasos de Docker: con esta opción, personaliza la imagen implementada mediante la instalación de dependencias que no se pueden instalar desde PyPi. 

   El archivo debe incluir pasos de instalación de Docker, como un archivo DockerFile. En el archivo se permiten los siguientes comandos: 

    RUN, ENV, ARG, LABEL y EXPOSE

   Ejemplo del comando de la CLI:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Los comandos Image, Manifest y Service aceptan la marca de archivo de Docker.

   Ejemplo del archivo de pasos de Docker:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> La imagen base de los contenedores de Azure ML es Ubuntu y no se puede cambiar. Si especifica otra imagen base, se omitirá.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha personalizado la imagen de contenedor, puede implementarla en un clúster para su uso a gran escala.  Para más información sobre cómo configurar un clúster para la implementación del servicio web, consulte [Model Management configuration](deployment-setup-configuration.md) (Configuración de Administración de modelos). 
