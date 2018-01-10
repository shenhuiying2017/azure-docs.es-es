---
title: "Introducción conceptual a las características de la versión preliminar de Azure Machine Learning | Microsoft Docs"
description: "Una introducción conceptual a las características de la versión preliminar de Azure Machine Learning, como suscripciones, cuentas, áreas de trabajo o proyectos, entre otras."
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: ea9da6f23fd08c09f9e805519487648480816f35
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning: Conceptos

En este artículo se definen y describen los conceptos que necesita saber para usar Azure Machine Learning. 

![Jerarquía de conceptos](media/overview-general-concepts/hierarchy.png)

- **Suscripción:** una suscripción de Azure permite el acceso a los recursos de Azure. Como Azure Machine Learning está totalmente integrado con el proceso, almacenamiento y muchos otros recursos y servicios de Azure, Workbench requiere que cada usuario tenga acceso a una suscripción válida de Azure. Los usuarios también deben tener permisos suficientes en esa suscripción para crear recursos.


- **Cuenta de experimentación:** esta cuenta es un recurso de Azure que requiere Azure Machine Learning, además de un vehículo de facturación. Contiene las áreas de trabajo, que a su vez contienen proyectos. Puede agregar varios usuarios, se conocen como _puestos_, a una cuenta de experimentación. Debe tener acceso a una cuenta de experimentación con el fin de usar Azure Machine Learning Workbench para ejecutar experimentos. 


- **Cuenta de Administración de modelos:** esta cuenta también es un recurso de Azure que requiere Azure Machine Learning para administrar modelos. Puede usarlo para registrar modelos y manifiestos, crear servicios web en contenedores e implementarlos localmente o en la nube. También es el otro vehículo de facturación de Azure Machine Learning.


- **Área de trabajo:** es el componente principal de compartir y colaborar en Azure Machine Learning. Los proyectos se agrupan dentro de un área de trabajo. Un área de trabajo se puede compartir después con varios usuarios que se hayan agregado a la cuenta de experimentación.


- **Proyecto:** en Azure Machine Learning, un proyecto es el contenedor lógico para todo el trabajo realizado para resolver un problema. Se asigna a una sola carpeta de archivos en el disco local y puede agregar archivos o subcarpetas a él. Si lo desea, el proyecto puede asociarse con un repositorio de Git para el control de código fuente y la colaboración.  

- **Experimento:** en Azure Machine Learning, un experimento es uno o más archivos de código fuente que se pueden ejecutar desde un único punto de entrada. Puede contener tareas como la ingesta de datos, ingeniería de características, aprendizaje de modelos o evaluación de modelos. Actualmente, Azure Machine Learning es compatible solo con experimentos de Python o PySpark.


- **Modelo:** en Azure Machine Learning, los modelos hacen referencia al producto de un experimento de aprendizaje automático. Son recetas que, cuando se aplican correctamente a los datos, generan los valores previstos. Los modelos pueden implementarse en entornos de prueba o de producción y usarse para puntuar nuevos datos. Una vez en producción, los modelos pueden supervisarse en busca de un desfase en el rendimiento y los datos, y volver a entrenarlos según sea necesario. 

- **Destino de proceso:** es el recurso de proceso que se configura para ejecutar sus experimentos. Puede ser el equipo local (Windows o macOS), el contenedor de Docker que se ejecuta en el equipo local o en una máquina virtual Linux en Azure, o un clúster de HDInsight Spark.


- **Ejecución:** el servicio Experimentación define una ejecución como la duración de una ejecución del experimento en un destino de proceso. Azure Machine Learning captura automáticamente la información de cada ejecución y presenta todo el historial de un experimento determinado en el formulario del historial de ejecución.

- **Entorno:** en Azure Machine Learning, un entorno denota un determinado recurso de proceso que se usa para implementar y administrar los modelos. Puede ser el equipo local, una máquina virtual Linux en Azure o un clúster de Kubernetes que se ejecuta en Azure Container Service, según el contexto y la configuración. El modelo está hospedado en un contenedor de Docker que se ejecuta en estos entornos y se expone como un punto de conexión de la API de REST.


- **Modelo administrado**: Administración de modelos permite implementar modelos en forma de servicios web, administrar varias versiones de los modelos y supervisar su rendimiento y sus métricas. Los modelos administrados están registrado con una cuenta de Administración de modelos de Azure Machine Learning.

- **Manifiestos:** cuando el sistema Administración de modelos implementa un modelo en producción, incluye un manifiesto que puede estar formada por el modelo, las dependencias, el script de puntuación, los datos de ejemplo y el esquema. El manifiesto es la receta utilizada para crear una imagen de contenedor de Docker. Con Administración de modelos puede generar automáticamente los manifiestos, crear versiones diferentes y administrar estos manifiestos. 


- **Imágenes:** puede usar manifiestos para generar (y volver a generar) imágenes de Docker. Las imágenes de Docker en contenedores crean flexibilidad para ejecutarlos en la nube, en máquinas locales o en dispositivos de IoT. Las imágenes son autónomas e incluyen todas las dependencias necesarias para puntuar nuevos datos con modelos. 

- **Servicios:** Administración de modelos le permite implementar modelos como servicios web. La lógica de servicio web y las dependencias se encapsulan en una imagen. Cada servicio web es un conjunto de contenedores basados en la imagen, preparado para atender las solicitudes a una dirección URL determinada. Un servicio web se cuenta como una única implementación.
