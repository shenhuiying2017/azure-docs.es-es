---
title: "Introducción conceptual a la Administración de modelos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se explican los conceptos de Administración de modelos para Azure Machine Learning."
services: machine-learning
author: nk773
ms.author: neerajkh, padou
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 0474e7ae77128a46fc6218de814913d07aa63d97
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2017
---
# <a name="azure-machine-learning-model-management"></a>Administración de modelos de Azure Machine Learning

Administración de modelos de Azure Machine Learning le permite administrar e implementar flujos de trabajo y modelos de aprendizaje automático. 

Administración de modelos proporciona funcionalidades para:
- Control de versiones de los modelos
- Seguimiento de modelos en producción
- Implementación de modelos en producción a través del entorno de proceso de Azure Machine Learning con [Azure Container Service](https://azure.microsoft.com/services/container-service/) y [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Creación de contenedores de Docker con los modelos y pruebas locales
- Reentrenamiento de modelos automatizado
- Captura de telemetría de modelos para detalles accionables 

Administración de modelos de Azure Machine Learning proporciona un registro de versiones del modelo. También proporciona flujos de trabajo automatizados para empaquetar e implementar contenedores de Machine Learning como las API de REST. Los modelos y sus dependencias en tiempo de ejecución se empaquetan en un contenedor de Docker basado en Linux con la API de predicción. 

Los entornos de proceso de Azure Machine Learning ayudan a configurar y administrar clústeres escalables para hospedar los modelos. El entorno de proceso se basa en instancias de Azure Container Service. Azure Container Service proporciona una exposición automática de las API de Machine Learning como puntos de conexión de la API de REST con las siguientes características:

- Autenticación
- Equilibrio de carga
- Escalabilidad horizontal automática
- Cifrado

Administración de modelos de Azure Machine Learning ofrece estas funcionalidades a través de CLI, API y Azure Portal. 

Administración de modelos de Azure Machine Learning usa la siguiente información:

 - Archivo de modelo o un directorio con los archivos de modelo
 - Archivo de Python creado por el usuario que implementa una función de puntuación de modelos
 - Archivo de dependencias Conda que enumera las dependencias en tiempo de ejecución
 - Opción de entorno en tiempo de ejecución y 
 - Archivo de esquema para parámetros de la API 

Esta información se utiliza al realizar las siguientes acciones:

- Registro de un modelo
- Creación de un manifiesto que se usa al crear un contenedor
- Compilación de una imagen de contenedor de Docker
- Implementación de un contenedor en Azure Container Service
 
En la siguiente ilustración se muestra una introducción de cómo se registran e implementan los modelos en el clúster. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Creación y administración de modelos 
Puede registrar modelos con Administración de modelos de Azure Machine Learning para realizar el seguimiento de las versiones de los modelos en producción. Para facilitar la reproducción y gobierno, el servicio captura todas las dependencias e información asociada. Para obtener información detallada sobre el rendimiento, puede capturar la telemetría de modelos con el SDK proporcionado. La telemetría de modelos se archiva en el almacenamiento proporcionado por el usuario. Se puede usar posteriormente la telemetría de modelos para analizar el rendimiento de modelos, realizar el reentrenamiento y obtener información para su empresa.

## <a name="create-and-manage-manifests"></a>Creación y administración de manifiestos 
Los modelos requieren artefactos adicionales para implementarse en producción. El sistema proporciona la funcionalidad de crear un manifiesto que abarque el modelo, las dependencias, el script de inferencia (también conocido como script de puntuación), datos de ejemplo, esquema, etc. Este manifiesto actúa como receta para crear una imagen de contenedor de Docker. Las empresas pueden generar automáticamente el manifiesto, crear versiones diferentes y administrar sus manifiestos. 

## <a name="create-and-manage-docker-container-images"></a>Creación y administración de imágenes del contenedor de Docker 
Puede usar el manifiesto en el paso anterior para crear imágenes de contenedor basadas en Docker en sus respectivos entornos. Las imágenes en contenedores, basadas en Docker, proporcionan a las empresas la flexibilidad necesaria para ejecutar estas imágenes en los siguientes entornos de proceso:

- [Azure Container Service basado en Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Servicios de contenedor locales
- Entornos de desarrollo
- Dispositivos IoT

Estas imágenes en contenedores basadas en Docker son autónomas y cuentan con todas las dependencias necesarias para generar predicciones. 

## <a name="deploy-docker-container-images"></a>Implementación de imágenes de contenedor de Docker 
Con Administración de modelos de Azure Machine Learning, puede implementar imágenes de contenedor basadas en Docker con un único comando en Azure Container Service administrado por un entorno de proceso de Machine Learning. Estas implementaciones se crean con un servidor front-end que proporciona las siguientes características:

- Predicciones de baja latencia a escala
- Equilibrio de carga
- Escalado automático de puntos de conexión de Machine Learning
- Autorización de la clave de API
- Documento de swagger de API

Puede controlar la escala de implementación y la telemetría a través de las opciones de configuración siguientes:

- Registro del sistema y telemetría de modelos para cada nivel de servicio web. Si está habilitado, todos los registros de stdout se transmiten a [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). La telemetría de modelos se archiva en el almacenamiento que haya proporcionado el usuario. 
- Límites de escala automática y simultaneidad. Esta configuración aumenta automáticamente el número de contenedores implementados en función de la carga en el clúster existente. También controlan el rendimiento y la coherencia de la latencia de predicción.

## <a name="consumption"></a>Consumo 
Administración de modelos de Azure Machine Learning crea la API de REST para el modelo implementado junto con el documento de swagger. Puede consumir modelos implementados mediante la llamada a las API de REST con la clave de API y entradas de modelo para obtener las predicciones como parte de las aplicaciones de línea de negocio. El código de ejemplo está disponible en GitHub para los lenguajes Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py) y C# para llamar a las API de REST. La CLI de Administración de modelos de Azure Machine Learning proporciona una manera sencilla de trabajar con estas API de REST. Puede consumir las API mediante un único comando de la CLI, aplicaciones habilitadas para swagger o con curl. 

## <a name="retraining"></a>Reentrenamiento 
Administración de modelos Azure Machine Learning proporciona las API que se pueden usar para reentrenar los modelos. También puede usar las API para actualizar implementaciones existentes con las versiones actualizadas del modelo. Como parte del flujo de trabajo de ciencia de datos, vuelva a crear el modelo en su entorno de experimentación. Después, registre el modelo con Administración de modelos y actualice las implementaciones existentes. Las actualizaciones se realizan con el comando UPDATE de la CLI. El comando UPDATE actualiza las implementaciones existentes sin cambiar la dirección URL de la API o la clave. Las aplicaciones que consumen el modelo seguirán funcionando sin ningún cambio en el código y comenzarán a obtener mejores predicciones con el nuevo modelo.

El flujo de trabajo completo que describe estos conceptos se captura en la ilustración siguiente:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes 
- ¿Qué tipos de datos se admiten? ¿Se pueden pasar matrices de NumPy directamente como entrada al servicio web?

   Si va a proporcionar el archivo de esquema que se creó mediante el SDK generate_schema, puede pasar NumPy o Pandas DF. También puede pasar cualquier entrada serializable de JSON. Puede pasar una imagen como cadena codificada binaria.

- ¿Admite el servicio web varias entradas o analiza diferentes entradas? 

   Sí, puede empaquetar varias entradas en una solicitud de JSON como un diccionario. Cada entrada se corresponde a una clave única del diccionario.

- ¿Se ha activado la llamada por una solicitud a la web de servicio, una llamada de bloqueo o una llamada asincrónica?

   Si el servicio se creó mediante la opción en tiempo real como parte de la CLI o API, en este caso es debido a una llamada sincrónica o de bloqueo. Se espera que sea muy rápida. Aunque puede llamarla en el cliente mediante la biblioteca HTTP asincrónica para evitar bloquear el subproceso de cliente.

- ¿Cuántas solicitudes puede controlar al mismo tiempo el servicio web?

   Depende de la escala del clúster y del servicio web. Puede escalar horizontalmente su servicio multiplicando las réplicas por 100 y, después, administrar muchas solicitudes simultáneamente. También puede configurar el número máximo de solicitudes simultáneas por réplica para aumentar el rendimiento de servicio.

- ¿Cuántas solicitudes puede poner en cola el servicio web?

   Es configurable. De forma predeterminada, se establece en ~10 por réplica, pero se puede aumentar o disminuir en función de los requisitos de la aplicación. Por lo general, si se aumenta el número de solicitudes en cola, se aumenta el rendimiento del servicio pero empeora las latencias en los percentiles más altos. Para mantener la coherencia de las latencias, establezca la puesta en cola en un valor bajo (1-5) y aumente el número de réplicas para controlar el rendimiento. También puede activar el escalado automático para que el número de réplicas se ajuste automáticamente en función de la carga. 

- ¿Se puede usar la misma máquina o clúster en varios puntos de conexión del servicio web?

   Totalmente. Puede multiplicar por 100 los servicios o puntos de conexión y ejecutarlos en el mismo clúster. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener una introducción a Administración de modelos, consulte [Configuring Model Management](deployment-setup-configuration.md) (Configuración de Administración de modelos).
