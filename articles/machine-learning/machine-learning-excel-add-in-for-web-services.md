<properties
	pageTitle="Complemento de Excel para los servicios web de Aprendizaje automático | Microsoft Azure"
	description="Cómo obtener acceso a servicios web de Aprendizaje automático de Azure directamente en Excel sin escribir ningún código."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
    tags=""/>

<tags
	ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/10/2015"
	ms.author="tedway;garye" />

# Complemento de Excel para servicios web de Aprendizaje automático de Azure

Excel facilita la llamada a servicios web directamente sin necesidad de escribir ningún código.

## Pasos para usar un servicio web existente

1. Abra el archivo Excel de ejemplo aquí o desde la pestaña **PANEL** de los servicios web.
2. Elija un servicio web haciendo clic en él: "Predictor de supervivientes del Titanic (complemento de Excel de ejemplo) [puntuación]" en este ejemplo.

    ![Seleccionar un servicio web][01]

3. Esto le llevará a la sección **Predicción**. Seleccione una celda de Excel y haga clic en **Usar datos de ejemplo**. También puede editar los datos en Excel.

	![Sección Predicción][02]

4. Resalte los datos y haga clic en el icono del intervalo de datos de entrada.
5. En **Resultado**, escriba el número de la celda donde desea que se muestre el resultado.
6. Haga clic en **Predicción**.

## Pasos para agregar un nuevo servicio web

1. Publique un servicio web (en [esta página](machine-learning-walkthrough-5-publish-web-service.md) se explica cómo hacerlo) o busque un servicio web existente.
2. Una vez que tenga un servicio web, haga clic en la sección **SERVICIOS WEB** que se encuentra en el panel izquierdo de Estudio de aprendizaje automático y, a continuación, seleccione el servicio web que desee consumir.
3. Desde la pestaña **PANEL** del servicio web, copie la clave de API para el servicio web.
4. En Excel, vaya a la sección **Servicios web** (si se encuentra en la sección **Predicción** y haga clic en la flecha Atrás para ir a la lista de servicios web).

	![Sección Servicios web][03]

5. Haga clic en **Agregar un servicio web**.
6. Pegue la clave en el cuadro de texto denominado **Clave de API**.
7. En la pestaña **PANEL** para el servicio web, haga clic en el vínculo **Página de ayuda de API**.
8. Copie el URI de la solicitud en **Solicitud** y péguelo en el cuadro de texto denominado **URL**.
9. Haga clic en **Agregar**.

	![URL y clave de API][04]

10.	Para usar el servicio web, siga las instrucciones anteriores, "Pasos para usar un servicio web existente".

## Compartir el libro

Si guarda el libro, también se guardarán las claves de API para los servicios web que ha agregado. Esto significa que solo debe compartir el libro con personas de confianza.


[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png

<!---HONumber=August15_HO8-->