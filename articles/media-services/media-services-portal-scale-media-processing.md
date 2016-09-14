<properties
	pageTitle=" Escalado del procesamiento de medios mediante el Portal de Azure | Microsoft Azure"
	description="Este tutorial lo guiará a través de los pasos de escalado de procesamiento de medios con el Portal de Azure."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="juliako"/>

# Cambio del tipo de unidad reservada

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-scale-media-processing.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## Información general

Para obtener más información sobre cómo escalar el procesamiento de medios, consulte este tema de [introducción](media-services-scale-media-processing-overview.md).

## Escalar el procesamiento multimedia

Para cambiar el tipo de unidad reservada y el número de unidades reservadas, haga lo siguiente:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. En la ventana **Configuración**, seleccione **Unidades reservadas de medios**.

	Para cambiar el número de unidades reservadas para el tipo de unidad reservada seleccionada, use el control deslizante **Unidades reservadas de medios**.

	Para cambiar el **TIPO DE UNIDAD RESERVADA**, haga clic en S1, S2 o S3.

	![Página de procesadores](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)

3. Presione el botón GUARDAR para guardar los cambios.

	Las nuevas unidades reservadas se asignan en cuanto presiona GUARDAR.

##Pasos siguientes

Consulte las rutas de aprendizaje de Servicios multimedia.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->