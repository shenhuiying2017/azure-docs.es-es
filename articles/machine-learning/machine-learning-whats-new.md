<properties
	pageTitle="Novedades de Aprendizaje automático | Microsoft Azure"
	description="Nuevas características disponibles en Aprendizaje automático de Azure."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2016"
	ms.author="v-donglo"/>

# Novedades de Aprendizaje automático de Azure

Las actualizaciones de Aprendizaje automático de Microsoft Azure (ML) de julio de 2016 proporcionan las siguientes características:

* Los servicios web ahora se administran como recursos de Azure administrados a través de interfaces de [Azure Resource Manager](../azure-portal/resource-group-overview.md) (ARM), con lo que se aportan las siguientes mejoras:
* Nuevas [API de REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) para implementar y administrar los servicios web basados en ARM
* Nuevo portal de [servicios web de Aprendizaje automático de Microsoft Azure](https://services.azureml.net/) donde pueden administrarse todos los aspectos de los servicios web. I
* Incorpora un nuevo modelo de implementación de servicios web en varias regiones y mediante suscripción que utiliza API basadas en ARM que emplean el RP de ARM en los servicios web.
* Introduce nuevos [planes de precios](https://azure.microsoft.com/pricing/details/machine-learning/) y funcionalidades de administración de planes que utilizan el nuevo RP de ARM para las tareas de facturación.
	* Ahora puede [implementar el servicio web en varias regiones](machine-learning-how-to-deploy-to-multiple-regions.md) sin necesidad de crear una nueva suscripción en cada región.
* Proporciona las[estadísticas de uso](machine-learning-manage-new-webservice.md) de los servicios web.
* Simplifica las pruebas de la función RRS de Aprendizaje automático de Azure mediante el uso de datos de ejemplo.
* Proporciona una nueva página de prueba de BES con historial de envío de trabajos y datos de ejemplo.

Además, Estudio de aprendizaje automático de Microsoft Azure se ha actualizado para poder implementar según el modelo de servicios web nuevos o seguir implementando con el modelo de servicios web clásicos.

<!---HONumber=AcomDC_0720_2016-->