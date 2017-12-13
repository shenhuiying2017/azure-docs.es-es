---
title: "Solución de problemas de reciclaje de un servicio web clásico Azure Machine Learning | Microsoft Docs"
description: Identifique y corrija los problemas comunes que se encuentran al volver a entrenar el modelo para un servicio web Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 011/01/2017
ms.author: garye
ms.openlocfilehash: 1e5327ad135d9bc8881354679dc3f1b8a472cad3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Solución de problemas del reentrenamiento de un servicio web clásico de Azure Machine Learning
## <a name="retraining-overview"></a>Información general sobre reentrenamiento
Cuando se implementa un experimento predictivo, tal como servicio web de puntuación, es un modelo estático. Cuando hay nuevos datos disponibles o cuando el consumidor de la API tiene sus propios datos, el modelo debe volver a entrenarse. 

Para ver un tutorial completo del proceso de reentrenamiento de un servicio web clásico, consulte [Volver a entrenar modelos de Machine Learning mediante programación](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Proceso de reentrenamiento
Cuando necesite volver a entrenar el servicio web, debe agregar algunos elementos adicionales:

* Un servicio web implementado desde el experimento de entrenamiento. El experimento debe tener un módulo de **salida de servicio web** conectado a la salida del módulo **Entrenar modelo**.  
  
    ![Asocie la salida del servicio web al modelo entrenado.][image1]
* Un nuevo punto de conexión agregado a su servicio web de puntuación.  El punto de conexión se puede agregar mediante programación usando el código de ejemplo al que se hace referencia en el tema Volver a entrenar modelos de Machine Learning mediante programación, o mediante el portal Servicios web de Azure Machine Learning.

Después, para entrenar el modelo puede usar el código C# de ejemplo de la página de Ayuda de la API del servicio web de aprendizaje. Cuando haya evaluado los resultados y esté satisfecho con ellos, actualice el servicio web de puntuación con el modelo entrenado para que use el nuevo punto de conexión agregado.

Con todos los elementos en su lugar, estos son los pasos principales que debe seguir para reentrenar el modelo:

1. Llame al servicio web de entrenamiento: la llamada se realiza al servicio de ejecución por lotes (BES, Batch Execution Service), no al servicio de solicitud-respuesta (RRS, Request-Response Service). Puede usar el código C# de ejemplo en la página de ayuda de la API para realizar la llamada. 
2. Busque los valores de *BaseLocation*, *RelativeLocation* y *SasBlobToken*: estos valores se devuelven en el resultado de la llamada al servicio web de entrenamiento. 
   ![muestra de la salida de la ejemplo de reentrenamiento y los valores de BaseLocation, RelativeLocation y SasBlobToken.][image6]
3. Actualice el punto de conexión agregado en el servicio web de puntuación con el nuevo modelo entrenado: use el código de ejemplo proporcionado en el tema Volver a entrenar modelos de Machine Learning mediante programación para actualizar el nuevo punto de conexión que agregó al modelo de puntuación con el modelo recién entrenado en el servicio web de aprendizaje.

## <a name="common-obstacles"></a>Obstáculos más comunes
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Compruebe si el valor de PATCH URL es el correcto
El valor de PATCH URL que use deberá ser el único asociado con el nuevo punto de conexión de puntuación que agregó al servicio web de puntuación. Hay varias maneras de obtener el valor de PATCH URL:

**Opción 1: De forma programada**

Para obtener valor de PATCH URL correcto:

1. Ejecute el código de ejemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2. En la salida de AddEndpoint, busque el valor *HelpLocation* y copie la dirección URL.
   
   ![HelpLocation en la salida del ejemplo de addEndpoint.][image2]
3. Pegue la dirección URL en un explorador para ir a una página que proporciona vínculos de ayuda para el servicio web.
4. Haga clic en el vínculo **Actualizar recurso** para abrir la página de ayuda sobre aplicación de revisiones.

**Opción 2: use el portal Servicios web de Azure Machine Learning**

1. Inicie sesión en el portal [Servicios web Azure Machine Learning](https://services.azureml.net/).
2. Haga clic en **Web Services** (Servicios web) o **Classic Web Services** (Servicios web clásicos) en la parte superior.
4. Haga clic en el servicio web de puntuación con el que trabaja (si no ha modificado el nombre predeterminado del servicio web, terminará en "[Scoring Exp.]").
5. Haga clic en **+ NUEVO**.
6. Una vez agregado el punto de conexión, haga clic en su nombre.
7. En la dirección URL de **Revisión**, haga clic en **API Help** (Ayuda de API) para abrir la página de ayuda de la aplicación de revisiones.

> [!NOTE]
> Si ha agregado el punto de conexión al servicio web de entrenamiento, en lugar de al predictivo, verá el siguiente mensaje de error al hacer clic en el vínculo **Update Resource** (Actualizar recurso): "Esta característica no se admite en este contexto ni está disponible en él. Este servicio web no tiene ningún recurso actualizable. Sentimos las molestias. Estamos trabajando en mejorar este flujo de trabajo."
> 
> 

La página de ayuda sobre PATCH contiene el valor de PATCH URL que debe usar y proporciona un código de ejemplo que puede usar para realizar la llamada.

![PATCH URL.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Compruebe que está actualizando el punto de conexión de puntuación correcto.
* No aplique revisiones al servicio web de aprendizaje: la operación de revisión debe realizarse en el servicio web de puntuación.
* No aplique revisiones al punto de conexión predeterminado en el servicio web: la operación de revisión debe realizarse en el nuevo punto de conexión del servicio web de puntuación que agregó.

Para comprobar en qué servicio web está el punto de conexión, visite el portal de servicios web. 

> [!NOTE]
> Asegúrese de agregar el punto de conexión al servicio web predictivo y no al de entrenamiento. Si ha implementado correctamente un servicio web predictivo y otro de entrenamiento, debería ver dos servicios web independientes. El servicio web predictivo debe terminar con "[predictive exp.]".
> 
> 

1. Inicie sesión en el portal [Servicios web Azure Machine Learning](https://services.azureml.net/).
2. Haga clic en **servicios web** o **Classic Web Services** (Servicios web clásicos).
3. Seleccione su servicio web predictivo.
4. Compruebe que el nuevo punto de conexión se agregó al servicio web.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Comprobación de que el área de trabajo está en la misma región que el servicio web
1. Inicie sesión en [Machine Learning Studio](https://studio.azureml.net/).
2. En la parte superior, haga clic en la lista desplegable de las áreas de trabajo.

   ![Interfaz de usuario de la región de Aprendizaje automático.][image4]

3. Compruebe la región en que está el área de trabajo.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
