<properties
   pageTitle="Uso de Análisis de transmisiones de Azure con Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar Análisis de transmisiones de Azure con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# Uso de Análisis de transmisiones de Azure con Almacenamiento de datos SQL

Análisis de transmisiones de Azure es un servicio totalmente administrado que proporciona un procesamiento completo de eventos de baja latencia, alta disponibilidad y escalable a través el streaming de datos en la nube. Para aprender los conceptos básicos, lea [Introducción a Análisis de transmisiones de Azure][]. A continuación, puede aprender a crear una solución de extremo a extremo con Análisis de transmisiones con el [Tutorial de introducción][].

En este artículo, aprenderá a usar la base de datos de Almacenamiento de datos SQL de Azure como receptor de salida para los trabajos de Análisis de transmisiones.

## Requisitos previos

En primer lugar, ejecute los pasos siguientes en el [Tutorial introductorio][].

1. Creación de una entrada de Centro de eventos
2. Configuración e inicio de la aplicación del generador de eventos
3. Aprovisionamiento de un trabajo de Stream Analytics
4. Especificación de una consulta y entrada de trabajo

Luego cree una base de datos de Almacenamiento de datos SQL de Azure.

## Especifique la salida de trabajo: base de datos de Almacenamiento de datos SQL de Azure.

### Paso 1
En el trabajo de Análisis de transmisiones, haga clic en **SALIDA** en la parte superior de la página y luego en **AGREGAR SALIDA**.

### Paso 2
Seleccione Base de datos SQL y haga clic en Siguiente. ![][Add Output]

### Paso 3
Escriba estos valores en la página siguiente - Alias de salida: escriba un nombre descriptivo para esta salida de trabajo. - Suscripción: - Si la base de datos Almacenamiento de datos SQL se encuentra en la misma suscripción que el trabajo de Análisis de transmisiones, seleccione Usar la base de datos SQL de la suscripción actual. - Si la base de datos se encuentra en otra suscripción, seleccione Usar la base de datos SQL de otra suscripción. - Base de datos: especifique el nombre de una base de datos de destino. - Nombre del servidor: especifique el nombre del servidor de la base de datos que acaba de especificar. Para encontrar estos valores, puede usar el Portal de Azure.

![][Server Name]

- Nombre de usuario: especifique el nombre de usuario de una cuenta que tiene permisos de escritura para la base de datos.
- Contraseña: proporcione la contraseña de la cuenta de usuario especificada.
- Tabla: especifique el nombre de la tabla de destino en la base de datos.

![][Add Database]

### Paso 4
Haga clic en el botón de comprobación para agregar esta salida de trabajo y comprobar que Análisis de transmisiones puede conectarse correctamente a la base de datos.

![][Test Connection]

Cuando la conexión a la base de datos se realice correctamente, verá una notificación en la parte inferior del portal. Puede hacer clic en Probar conexión en la parte inferior para probar la conexión con la base de datos.




## Pasos siguientes
Para obtener información general sobre la integración, consulte [Información general de la integración de Almacenamiento de datos SQL][]. Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][].

<!--Image references-->
[Add Output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[Server Name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[Add Database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[Test Connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->
[Introducción a Análisis de transmisiones de Azure]: ./stream-analytics-introduction/
[Tutorial de introducción]: ./articles/stream-analytics-get-started/
[Tutorial introductorio]: ./articles/stream-analytics-get-started/
[información general sobre desarrollo de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-develop/
[Información general de la integración de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=July15_HO4-->