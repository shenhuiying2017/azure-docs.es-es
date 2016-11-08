---
title: Caso de uso - Generación de perfiles de clientes
description: Obtenga información sobre cómo se usa Factoría de datos de Azure para crear un flujo de trabajo orientado a datos (canalización) para generar perfiles de clientes de juegos.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: spelluru

---
# Caso de uso - Generación de perfiles de clientes
Data Factory de Azure es uno de los muchos servicios que se usan para implementar el conjunto de aplicaciones Cortana Intelligence de aceleradores de soluciones. Para más información sobre Cortana Intelligence, visite [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) (conjunto de aplicaciones de Cortana Intelligence). En este documento se describe un caso de uso sencillo para que pueda comprender cómo Factoría de datos de Azure puede resolver los problemas comunes de análisis.

Todo lo que necesita para tener acceso a este caso de uso sencillo y probarlo es una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/). Puede implementar un ejemplo que ponga en marcha este caso de uso siguiendo los pasos descritos en el artículo [Ejemplos](data-factory-samples.md).

## Escenario
Contoso es una empresa de juegos que crea juegos para varias plataformas: consolas de juegos, dispositivos portátiles y PC. A medida que los usuarios juegan a estos juegos, se generan grandes volúmenes de datos de registro que realizan el seguimiento de los patrones de uso, estilo de juegos y preferencias del usuario. Cuando se combinan con datos demográficos, regionales y de productos, Contoso realiza análisis para guiar a los usuarios sobre cómo mejorar su experiencia y orientarles sobre actualizaciones y compras en el juego.

El objetivo de Contoso es identificar oportunidades de aumento de ventas potenciales y ventas cruzadas basadas en el historial de juegos de sus usuarios y agregar características atractivas para impulsar el crecimiento del negocio y ofrecer una mejor experiencia a los clientes. Para este caso de uso, usamos una empresa de juegos como ejemplo de empresa. La empresa desea optimizar sus juegos según el comportamiento de los jugadores. Estos principios se aplican a cualquier empresa que desee atraer a sus clientes hacia sus productos y servicios y mejorar la experiencia de estos.

## Desafíos
## Información general de la solución
Este caso de uso sencillo puede usarse como un ejemplo de cómo puede usar Factoría de datos de Azure para recopilar, preparar, transformar, analizar y publicar datos.

![Flujo de trabajo de un extremo a otro](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Esta ilustración describe cómo las canalizaciones de datos aparecen en Azure Portal una vez implementadas.

1. **PartitionGameLogsPipeline** lee los eventos de juegos sin procesar de un almacenamiento de blobs y crea particiones basadas en el año, el mes y el día.
2. **EnrichGameLogsPipeline** combina eventos de juegos con particiones con datos de referencia de código geográfico y enriquece los datos mediante la asignación de direcciones IP a las ubicaciones geográficas correspondientes.
3. La canalización **AnalyzeMarketingCampaignPipeline** utiliza los datos enriquecidos y los procesa con los datos de publicidad para crear el resultado final que contiene la eficacia de la campaña de marketing.

En este ejemplo, Data Factory se utiliza para coordinar las actividades que copian los datos de entrada, transforman y procesan los datos y envían los datos finales a Azure SQL Database. También puede visualizar la red de canalizaciones de datos, administrarla y supervisar su estado desde la interfaz de usuario.

## Ventajas
Al optimizar el análisis de su perfil de usuario y armonizarlo con los objetivos empresariales, la empresa de juegos puede recopilar rápidamente los patrones de uso y analizar la eficacia de sus campañas de marketing.

<!---HONumber=AcomDC_0907_2016-->