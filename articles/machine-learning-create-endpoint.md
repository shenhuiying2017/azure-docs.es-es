<properties 
	pageTitle="Creación de extremos" 
	description="Creación de extremos de servicio web en Aprendizaje automático de Azure" 
	services="machine-learning" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/19/2015"
	ms.author="himad"/>


# Creación de extremos

Aprendizaje automático de Azure le permite crear varios extremos para un servicio web publicado. Cada extremo se trata, limita y administra individualmente, independientemente de los otros extremos de ese servicio web. Hay una clave de autorización y una dirección URL única para cada extremo.

Esto permite a los usuarios de Aprendizaje automático de Azure crear servicios web que, a continuación, pueden vender a sus clientes. Cada extremo puede personalizarse de forma individual con sus propios modelos formados mientras continúa vinculado al experimento que creó este servicio web. Además, las actualizaciones para el experimento pueden aplicarse de manera selectiva a un extremo sin sobrescribir las personalizaciones.

## Pasos de creación de extremos
- Abra manage.windowsazure.com, haga clic en Aprendizaje automático en la columna izquierda. Haga clic en el área de trabajo que dispone del servicio web en el que está interesado.
![Navigate to workspace](./media/machine-learning-create-endpoint/figure-1.png)


- Haga clic en la ficha "Servicios web".
![Navigate to web services](./media/machine-learning-create-endpoint/figure-2.png)


- Haga clic en el servicio web en el que esté interesado para ver la lista de extremos disponibles.
![Navigate to endpoint](./media/machine-learning-create-endpoint/figure-3.png)


- Haga clic en el botón de Agregar extremo en la parte inferior. Introduzca un nombre y una descripción, asegúrese de que no hay ningún otro extremo con el mismo nombre eneste servicio web. Deje el nivel de la limitación con su valor predeterminado a menos que tenga requisitos especiales.
Si desea obtener más información acerca de la limitación, visite la página [Extremos de la API de escala](machine-learning-scaling-endpoints.md).
![Create endpoint](./media/machine-learning-create-endpoint/figure-4.png)


Una vez creado el extremo, puede consumirlo a través de API sincrónicas, API de lotes y hojas de cálculo de Excel.

<!--HONumber=49-->