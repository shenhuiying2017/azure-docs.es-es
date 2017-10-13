---
title: "Repetición del entrenamiento de los modelos de Machine Learning mediante programación | Microsoft Docs"
description: "Obtenga información acerca de cómo volver a entrenar un modelo y actualizar el servicio web mediante programación para utilizar el modelo recién entrenado en el Aprendizaje automático de Azure."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl;garye;v-donglo
ms.openlocfilehash: c56ce659766536772d203d0366ef6b53e544a82b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-machine-learning-models-programmatically"></a>Volver a entrenar modelos de aprendizaje automático mediante programación
En este tutorial, aprenderá a entrenar mediante programación un servicio web Azure Machine Learning mediante C# y el servicio de ejecución por lotes de Machine Learning.

Cuando haya reentrenado el modelo, los tutoriales siguientes le mostrarán cómo actualizarlo en el servicio web predictivo:

* Si implementó un servicio web clásico en el portal Servicios web Machine Learning, consulte [Reentrenamiento de un servicio web clásico](retrain-a-classic-web-service.md). 
* Si implementó un servicio web nuevo, consulte [Reentrenamiento de un servicio web nuevo mediante los cmdlets de administración de Machine Learning](retrain-new-web-service-using-powershell.md).

Para obtener información general sobre el proceso de reentrenamiento, consulte [Retrain a Machine Learning Model](retrain-machine-learning-model.md) (Reentrenamiento de un modelo de Machine Learning).

Si desea comenzar por el servicio web existente basado en el nuevo Azure Resource Manager, consulte [Retrain an existing Predictive Web service](retrain-existing-resource-manager-based-web-service.md) (Reentrenamiento de un servicio web predictivo existente).

## <a name="create-a-training-experiment"></a>Crear un experimento de entrenamiento
En este ejemplo, usará "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" (Ejemplo 5: Entrenar, probar, evaluar para clasificación binaria: Conjunto de datos para adultos) de entre los ejemplos de Aprendizaje automático de Microsoft Azure. 

Para crear el experimento:

1. Inicie sesión en Estudio de aprendizaje automático de Microsoft Azure. 
2. En la esquina inferior derecha del panel, haga clic en **Nuevo**.
3. En los ejemplos de Microsoft, seleccione el ejemplo 5.
4. Para cambiar el nombre del experimento, en la parte superior del lienzo del experimento, seleccione el nombre del experimento, en este caso "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset".
5. Escriba Census Model (Modelo de censo).
6. En la parte inferior del lienzo del experimento, haga clic en **Ejecutar**.
7. Haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Retraining Web Service** (Servicio web de reentrenamiento). 

A continuación, se muestra el experimento inicial.
   
   ![Experimento inicial.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Creación de un experimento predictivo y publicación como servicio web
A continuación creará un experimento predicativo.

1. En la parte inferior del lienzo del experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Predictive web Service** (Servicio web predictivo). Esto permite guardar el modelo como un modelo entrenado y agrega los módulos de entrada y salida de servicio web. 
2. Haga clic en **Ejecutar**. 
3. Cuando el experimento haya terminado de ejecutarse, haga clic en **Deploy Web Service [Classic]** (Implementar servicio web [clásico]) o en **Deploy Web Service [New]** (Implementar servicio web [nuevo]).

> [!NOTE] 
> Para implementar un nuevo servicio web, debe tener permisos suficientes en la suscripción en la que lo implementa. Para obtener más información, consulte [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Implementación del experimento de entrenamiento como un servicio web de entrenamiento
Para reciclar el modelo entrenado, debe implementar el experimento de entrenamiento que creó como un servicio web de reciclaje. Este servicio necesita un módulo de *salida del servicio web* conectado al módulo *[Entrenar modelo][train-model]* para poder generar nuevos modelos entrenados.

1. Para volver al experimento de entrenamiento, haga clic en el icono Experimentos en el panel izquierdo, y luego haga clic en el experimento denominado Census Model (Modelo de censo).  
2. En el cuadro de búsqueda Search Experiment Items (Buscar elementos de experimentos), escriba servicio web. 
3. Arrastre un módulo de *entrada de servicio web* al lienzo de experimentos y conecte la salida al módulo *Clean Missing Data* (Limpiar datos que faltan).  Esto garantiza que los datos de reentrenamiento se procesen del mismo modo que los datos de entrenamiento originales.
4. Arrastre dos módulos de *salida de servicio web* al lienzo de experimentos. Conecte la salida del módulo *Entrenar modelo* a uno y la salida del módulo *Evaluate Model* (Evaluar modelo), al otro. La salida del servicio web para **Entrenar modelo** nos ofrecerá el nuevo modelo entrenado. La salida conectada a **Evaluate Model** (Evaluar modelo) devuelve los resultados de ese módulo, que son los resultados de rendimiento.
5. Haga clic en **Ejecutar**. 

Después, debe implementar el experimento de entrenamiento como un servicio web que genera un modelo entrenado y resultados de evaluación del modelo. Para lograr esto, el siguiente conjunto de acciones depende de si está trabajando con un servicio web clásico o un servicio web nuevo.  

**Servicio web clásico**

En la parte inferior del lienzo del experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Deploy Web Service [Classic]** (Implementar servicio web [clásico]). Aparecerá el **panel** del servicio web con la clave de API y la página de Ayuda de API para la ejecución por lotes. Solo se puede usar el método de ejecución por lotes para crear modelos entrenados.

**Servicio web nuevo**

En la parte inferior del lienzo del experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Deploy Web Service [New]** (Implementar servicio web [nuevo]). El portal de servicios web de Azure Machine Learning se abre en la página Deploy Web service (Implementar servicio web). Escriba un nombre para el servicio web y elija un plan de pago y después haga clic en **Implementar**. Solo se puede usar el método de ejecución de lotes para crear modelos entrenados.

En cualquier caso, una vez que el experimento ha finalizado su ejecución, el flujo de trabajo resultante debe ser el siguiente:

![Flujo de trabajo resultante después de la ejecución.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Reentrenamiento del modelo con nuevos datos mediante BES
En este ejemplo, se utiliza C# para crear la aplicación de reentrenamiento. También puede utilizar el código de ejemplo de Python o R para realizar esta tarea.

Para llamar a las API de reentrenamiento:

1. Cree una nueva aplicación de consola de C# en Visual Studio: **Nuevo** > **Proyecto** > **Visual C#** > **Escritorio clásico de Windows** > **Aplicación de consola (.NET Framework)**.
2. Inicie sesión en el portal de servicio web Machine Learning.
3. Si está trabajando con un servicio web clásico, haga clic en **Classic Web Services**(Servicios web clásicos).
   1. Haga clic en el servicio web con el que está trabajando.
   2. Haga clic en el punto de conexión predeterminado.
   3. Haga clic en **Consume**(Consumo).
   4. En la parte inferior de la página **Consume** (Consumo), en la sección **Código de ejemplo**, haga clic en **Batch**.
   5. Vaya al paso 5 de este procedimiento.
4. Si está trabajando con un servicio web nuevo, haga clic en **Servicios web**.
   1. Haga clic en el servicio web con el que está trabajando.
   2. Haga clic en **Consume**(Consumo).
   3. En la parte inferior de la página Consume (Consumo), en la sección **Código de ejemplo**, haga clic en **Batch**.
5. Copie el código C# de ejemplo para la ejecución por lotes y péguelo en el archivo Program.cs, asegurándose de que el espacio de nombres permanece intacto.

Agregue el paquete NuGet Microsoft.AspNet.WebApi.Client tal como se especifica en los comentarios. Para agregar la referencia a Microsoft.WindowsAzure.Storage.dll, primero debe instalar la biblioteca de cliente para servicios de Microsoft Azure Storage. Para obtener más información, vea [Servicios de almacenamiento de Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Actualización de la declaración de apikey
Localice la declaración de **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

En la sección **Basic consumption info** (Información básica sobre consumo) de la página **Consume** (Consumo), localice la clave principal y cópiela en la declaración de **apikey**.

### <a name="update-the-azure-storage-information"></a>Actualización de la información de Almacenamiento de Azure
El código de ejemplo de BES carga un archivo desde una unidad local (por ejemplo "C:\temp\CensusIpnput.csv") en el Almacenamiento de Azure, lo procesa y escribe los resultados de nuevo en el Almacenamiento de Azure.  

Para ello, debe recuperar el nombre, la clave y la información de contenedor de la cuenta de almacenamiento desde el Portal de Azure clásico y actualizar los valores correspondientes del código. 

1. Inicie sesión en el Portal de Azure clásico.
2. En la columna de navegación izquierda, haga clic en **Almacenamiento**.
3. En la lista de cuentas de almacenamiento, seleccione una para almacenar el modelo reciclado.
4. En la parte inferior de la página, haga clic en **Administrar claves de acceso**.
5. Copie y guarde la **clave de acceso principal** y cierre el cuadro de diálogo. 
6. En la parte superior de la página, haga clic en **Contenedores**.
7. Seleccione un contenedor existente o cree uno nuevo y guarde el nombre.

Busque las declaraciones *StorageAccountName*, *StorageAccountKey* y *StorageContainerName* y actualice los valores guardados en Azure Portal.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

También deberá asegurarse de que el archivo de entrada está disponible en la ubicación que especifique en el código. 

### <a name="specify-the-output-location"></a>Especifique la ubicación de salida.
Al especificar la ubicación de salida en la carga útil de solicitud, la extensión del archivo especificado en *RelativeLocation* debe especificarse como ilearner. 

Consulte el ejemplo siguiente:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> Los nombres de las ubicaciones de salida pueden ser diferentes de aquellos que aparecen en este tutorial que se basa en el orden en que se agregaron los módulos de salida del servicio web. Puesto que ha configurado este experimento de entrenamiento con dos salidas, los resultados incluyen información de ubicación de almacenamiento para ambas.  
> 
> 

![Salida de reentrenamiento][6]

Diagrama 4: Salida de entrenamiento.

## <a name="evaluate-the-retraining-results"></a>Evaluación de los resultados de reentrenamiento
Al ejecutar la aplicación, la salida incluye la dirección URL y el token SAS necesarios para tener acceso a los resultados de evaluación.

Podrá ver los resultados de rendimiento del modelo reentrenado combinando *BaseLocation*, *RelativeLocation* y *SasBlobToken* de los resultados de salida de *output2* (como se muestra en la imagen de la salida de reentrenamiento anterior) y copiando y pegando la dirección URL completa en la barra de direcciones del explorador.  

Revise los resultados para determinar si el modelo recientemente entrenado funciona lo suficientemente bien como para reemplazar el existente.

Copie *BaseLocation*, *RelativeLocation* y *SasBlobToken* de los resultados de salida, ya que se usarán durante el proceso de reentrenamiento.

## <a name="next-steps"></a>Pasos siguientes
Si implementa el servicio web predictivo haciendo clic en **Deploy Web Service [Classic]** (Implementar servicio web [clásico]), consulte [Reentrenamiento de un servicio web clásico](retrain-a-classic-web-service.md).

Si implementó el servicio web predictivo haciendo clic en **Deploy Web Service [Classic]** (Implementar servicio web [clásico]), consulte [Reentrenamiento de un servicio web nuevo mediante los cmdlets de administración de Machine Learning](retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
