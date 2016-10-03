<properties
	pageTitle="Mover datos hacia y desde el almacenamiento de blobs de Azure | Microsoft Azure"
	description="Mover datos hacia y desde el almacenamiento de blobs de Azure"
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="bradsev;sachouks" />

# Mover datos hacia y desde el almacenamiento de blobs de Azure

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

A continuación se ofrecen vínculos de orientación sobre las tecnologías que se usan para mover datos hacia o desde el almacenamiento de blobs de Azure:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
El método más adecuado para usted dependerá de su escenario. El artículo [Escenarios para análisis avanzado en Aprendizaje automático de Azure](machine-learning-data-science-plan-sample-scenarios.md) lo ayudará a determinar los recursos que necesita para una variedad de flujos de trabajo de ciencia de datos utilizados en el proceso de análisis avanzado.

> [AZURE.NOTE] Para ver una introducción completa a Azure Blob Storage, consulte [Aspectos básicos de Azure Blob](../storage/storage-dotnet-how-to-use-blobs.md) y [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).

Como alternativa, puede usar [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para:

- crear y programar una canalización que descarga los datos desde Azure Blob Storage,
- pasarla a un servicio web Azure Machine Learning publicado,
- recibir los resultados de análisis predictivo y
- cargar los resultados al almacenamiento.

Consulte [Creación de canalizaciones predictivas mediante Factoría de datos de Azure y Aprendizaje automático de Azure](../data-factory/data-factory-azure-ml-batch-execution-activity.md) para obtener más información.

## Requisitos previos

En este documento se supone que tiene una suscripción de Azure y una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta.

- Para configurar una suscripción a Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
- Para obtener instrucciones sobre la creación de una cuenta de almacenamiento y para obtener información de cuentas y claves, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

<!---HONumber=AcomDC_0921_2016-->