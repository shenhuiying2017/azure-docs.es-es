
<properties
   pageTitle="Guía de Elasticsearch en Azure | Microsoft Azure"
   description="Guía de Elasticsearch en Azure."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Guía de Elasticsearch en Azure

Elasticsearch es una base de datos y un motor de búsqueda de código abierto de gran escalabilidad. Es adecuado para situaciones que requieren un análisis y un descubrimiento rápidos de la información contenida en grandes conjuntos de datos. Esta guía analiza algunos aspectos claves a tener en cuenta al diseñar un clúster de Elasticsearch:

- **[Orientación general][]**: se proporciona una breve introducción a la estructura general de Elasticsearch y se describe cómo implementar un clúster de Elasticsearch con Azure.
- **[Guía de ingesta de datos][]**: se describen las opciones de implementación y configuración que se deben tener en cuenta para un clúster de Elasticsearch del que se espera una alta tasa de ingesta de datos.
- **[Guía de pruebas de rendimiento][]**: se describe el modo de configurar un entorno para probar el rendimiento de un clúster de Elasticsearch.
- **[Guía de JMeter][]**: se describe cómo crear y utilizar una muestra de JUnit que pueda generar y cargar datos en un clúster de Elasticsearch.
- **[Consideraciones de JMeter][]**: resume las experiencias claves adquiridas con la generación y ejecución de ingestas de datos y planes de prueba de consultas. 

  > [AZURE.NOTE] En esta guía, se da por supuesto que conoce los conceptos básicos de Elasticsearch. Para obtener más información, visite el [sitio web de Elasticsearch](https://www.elastic.co/products/elasticsearch).

[Orientación general]: guidance-elasticsearch.md
[Guía de ingesta de datos]: guidance-elasticsearch-data-ingestion.md
[Guía de pruebas de rendimiento]: guidance-elasticsearch-performance-testing-environment.md
[Guía de JMeter]: guidance-elasticsearch-implementing-jmeter.md
[Consideraciones de JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->