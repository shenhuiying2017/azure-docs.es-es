<properties
    pageTitle="Complemento de Excel para los servicios web Machine Learning | Microsoft Azure"
    description="Uso de los servicios web Azure Machine Learning directamente en Excel sin escribir código."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />


# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Complemento de Excel para servicios web Azure Machine Learning

Excel facilita la llamada directa a servicios web sin necesidad de escribir ningún código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Pasos para usar un servicio web existente en el libro

1. Abra el [archivo de Excel de ejemplo](http://aka.ms/amlexcel-sample-2)que contiene el complemento de Excel y los datos acerca de los pasajeros del Titanic.
2. Elija el servicio web haciendo clic en él. En este ejemplo, es "Predictor de supervivientes del Titanic (complemento de Excel de ejemplo) [puntuación]".

    ![Seleccionar un servicio web][01]

3. Esto le llevará a la sección **Predicción** .  Este libro ya contiene datos de ejemplo, pero para un libro en blanco también puede seleccionar una celda en Excel y hacer clic en **Usar datos de ejemplo**.
4. Seleccione los datos con encabezados y haga clic en el icono del intervalo de datos de entrada.  Asegúrese de que está activada la casilla "Mis datos tienen encabezados".
5. En **Resultado**, escriba el número de la celda donde desea que se muestre el resultado, por ejemplo, "H1" aquí.
6. Haga clic en **Predicción**.

    ![Sección Predicción][02]

## <a name="steps-to-add-a-new-web-service"></a>Pasos para agregar un nuevo servicio web

Implemente un servicio web o use uno existente. Para obtener más información acerca de cómo implementar un servicio web, consulte [Paso 5 del tutorial: Implementación del servicio web Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).

Obtenga la clave de API del servicio web. Dónde hacerlo depende de si publicó un servicio web Machine Learning clásico o en uno nuevo.

**Uso de un servicio web clásico** 

1. En Machine Learning Studio, haga clic en la sección **SERVICIOS WEB** en el panel izquierdo y luego seleccione el servicio web.

    ![Seleccionar un servicio web de Studio][04]

2. Copie la clave de API del servicio web.

    ![Clave de API de Studio][05]

3. En la pestaña **PANEL** del servicio web, haga clic en el vínculo **SOLICITUD-RESPUESTA**.
4. Busque la sección **URI de solicitud** .  Copie y guarde la URL.

>[AZURE.NOTE] Ahora se puede iniciar sesión en el portal [Servicios web Azure Machine Learning](https://services.azureml.net) para obtener la clave de API de un servicio web Machine Learning clásico.

**Uso de un servicio web nuevo**

1. En el portal [Servicios web Azure Machine Learning](https://services.azureml.net), haga clic en **Servicios web** y seleccione su servicio web. 
2. Haga clic en **Consume**(Consumo).
3. Busque la sección **Basic consumption info** (Información básica de consumo). Copie y guarde la **clave principal** y la URL de **solicitud-respuesta**.


## <a name="steps-to-add-a-new-web-service"></a>Pasos para agregar un nuevo servicio web

1. Implemente un servicio web o use uno existente. Para obtener más información acerca de cómo implementar un servicio web, consulte [Paso 5 del tutorial: Implementación del servicio web Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).
2. Haga clic en **Consume**(Consumo).
3. Busque la sección **Basic consumption info** (Información básica de consumo). Copie y guarde la **clave principal** y la URL de **solicitud-respuesta**.
2. En Excel, vaya a la sección **Servicios web** (si se encuentra en la sección **Predicción**, haga clic en la flecha Atrás para ir a la lista de servicios web).

    ![Ir a la selección de servicios web][03]
    
3. Haga clic en **Agregar servicio web**.
4. Pegue la URL en el cuadro de texto de complemento de Excel denominado " **URL**".
5. Pegue la clave principal o de API en el cuadro de texto denominado **Clave de API**.
6. Haga clic en **Agregar**.

    ![Dirección URL y clave de API y de un servicio web clásico.][06]

10. Para usar el servicio web, siga las instrucciones anteriores, "Pasos para usar un servicio web existente".

## <a name="sharing-your-workbook"></a>Compartir el libro

Si guarda el libro, también se guardarán la clave principal o de API de los servicios web que haya agregado. Esto significa que solo debe compartir el libro con personas de confianza.

Plantee cualquier pregunta que le surja en la sección de comentarios a continuación o en nuestro [foro](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png



<!--HONumber=Oct16_HO2-->


