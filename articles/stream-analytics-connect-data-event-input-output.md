<properties 
	pageTitle="Conexión a entradas y salidas | Azure" 
	description="Obtenga información acerca de cómo conectarse a y configurar los orígenes de entrada y salida de destinos para soluciones de Análisis de transmisiones." 
	documentationCenter="" 
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Conexión a entradas y salidas
En este documento detectará los diferentes métodos de configuración de los orígenes de entrada y los destinos de salida para soluciones de Análisis de transmisiones.

##Uso de SQL como salida

Puede utilizar las bases de datos SQL de Azure como salida de datos que son relacionales por naturaleza o de aplicaciones que dependen del contenido hospedado en una base de datos.

[http://Azure.Microsoft.com/Services/SQL-Database/](http://azure.microsoft.com/services/sql-database/)

Para empezar a usar una base de datos SQL de Azure, debe tener la siguiente información acerca de la base de datos:

1. Nombre del servidor.
2. Nombre de la base de datos.
3. Una combinación de nombre de usuario y contraseña válidos.
4. Nombre de la tabla de salida.

![graphic1][graphic1]

Vaya a la pestaña de resultados del trabajo y haga clic en el comando "AGREGAR SALIDA" y haga clic en siguiente.

![graphic2][graphic2]


Elija "Base de datos SQL" como salida.

![graphic3][graphic3]

Escriba la información de la base de datos en la página siguiente. El alias de salida es el nombre que puede usar en la consulta para dirigir la salida de la consulta a esta base de datos. El alias predeterminado si tiene una salida es "salida".

![graphic4][graphic4]

Si usa una base de datos que existe dentro de la misma suscripción que  utiliza, puede seleccionar "Usar base de datos SQL de la suscripción actual" y seleccione la base de datos en la lista desplegable.

![graphic5][graphic5]

Haga clic en siguiente para agregar esta salida. Debería ver iniciarse dos operaciones; la primera es agregar la salida.

![graphic6][graphic6]

La segunda es probar la conexión. Análisis de transmisiones de Azure intentará conectarse a esa base de datos SQL y verificar que las credenciales que ha escrito sean correctas y que la tabla sea accesible. Debería ver uno de dos mensajes después de que se complete.

![graphic7][graphic7]

![graphic8][graphic8]



Si ese fue el último, haga clic en "DETALLES" para ver los detalles exactos del error.

![graphic9][graphic9]

En este ejemplo, las credenciales proporcionadas son incorrectas. Puede corregir las credenciales y volver a probarlas; para ello, haga clic en el botón "PROBAR CONEXIÓN".

![graphic10][graphic10]

##Uso de centros de eventos

###Información general
Los centros de eventos son ingestores de eventos altamente escalables; normalmente son la manera más común para introducir datos de Análisis de transmisiones. Están diseñados para recopilar secuencias de eventos de una serie de diferentes dispositivos y servicios. Los centros de eventos y el Análisis de transmisiones juntos proporcionan a los clientes una solución de extremo a extremo para el análisis en tiempo real; los centros de eventos permiten a los clientes incluir eventos en Azure en tiempo real y los trabajos de Análisis de transmisiones pueden procesarlos en tiempo real. Por ejemplo, los clientes pueden publicar clics en la web, lecturas del sensor, eventos de registro en línea en los centros de eventos y crear trabajos de Análisis de transmisiones para usar centros de eventos como las secuencias de datos de entrada para filtrar, agregar y unir en tiempo real. Los centros de eventos pueden usarse también para la salida de datos. El uso más común del controlador de eventos es cuando la salida de un trabajo de Análisis de transmisiones será la entrada de otro trabajo de transmisión.

###Grupos de consumidores
Cada entrada de trabajo de Análisis de transmisiones debe configurarse para tener su propio grupo de consumidores del centro de eventos. Cuando un trabajo contiene autocombinación o varias salidas, es posible que más un lector lea alguna entrada, lo que hace que el número total de lectores en un solo grupo de consumidores supere el límite del centro de eventos de 5 lectores por grupo de consumidores. En este caso, la consulta deberá dividirse en varias consultas y resultados intermedios que se enrutan a través de centros de eventos adicionales. Tenga en cuenta que también hay un límite de 20 grupos de consumidores por centro de eventos. Para obtener más información, consulte la Guía para desarrolladores del centro de eventos.

 
###Parámetros
Hay unos cuantos parámetros que los clientes deben configurar para secuencias de datos del centro de eventos. Estos parámetros se aplican a secuencias de datos de entrada y salida de un centro de eventos, a menos que se indique lo contrario.

1. Espacio de nombres de Bus de servicio: espacio de nombres de Bus de servicio del centro de eventos. Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres del Bus de servicio. 
2. Nombre de centro de eventos: el nombre del centro de eventos. Es el nombre que especificó al crear un nuevo centro de eventos. 
3. Nombre de directiva de centro de eventos: nombre de la directiva de acceso compartido para tener acceso al centro de eventos. Las directivas de acceso compartido pueden configurarse para un centro de eventos en la Pestaña Configurar. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso.
4. Clave de la directiva de centro de eventos: clave principal o secundaria de la directiva de acceso compartido para tener acceso al centro de eventos. 
5. Grupo de consumidores del centro de eventos: parámetro opcional para las entradas del centro de eventos. El grupo de consumidores para introducir datos desde el centro de eventos. Si no se especifica, los trabajos de Análisis de transmisiones utilizan el grupo de consumidores predeterminado para introducir datos desde el centro de eventos. Se recomienda usar un grupo de consumidores distinto para cada trabajo de Análisis de transmisiones.

Columna de clave de partición: parámetro opcional para las salidas del centro de eventos. La columna de atributos de datos que se utiliza como clave de partición para la salida del centro de eventos.

##Uso de la salida de la tabla de Azure

La tabla de Azure se puede utilizar para los datos estructurados con menos restricciones en el esquema. El almacenamiento de tablas puede utilizarse para almacenar los datos para una persistencia y una recuperación eficaz. Para obtener más información, consulte: [Introducción al almacenamiento de Azure](http://azure.microsoft.com/storage-introduction/)
 
Para empezar a usar un almacenamiento de tablas de Azure, debe tener la siguiente información acerca de la tabla:

1. Nombre de la cuenta de almacenamiento \(si este almacenamiento está en una suscripción diferente de su trabajo de transmisión\).
2. Clave de la cuenta de almacenamiento \(si este almacenamiento está en una suscripción diferente de su trabajo de transmisión\).
3. Nombre de la tabla de salida \(se creará si no existe\).
4. Clave de partición \(obligatoria\).
5.   Clave de fila \(actualmente es necesaria; de acuerdo con los comentarios de los clientes, planeamos que sea opcional\)

Para un mejor diseño de la clave de partición y la clave de fila, consulte el siguiente artículo [Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).


![graphic11][graphic11]


Vaya a la pestaña de resultados del trabajo y haga clic en el comando "AGREGAR SALIDA" y haga clic en siguiente.


![graphic12][graphic12]


Elija "Almacenamiento de tabla" en la salida.


![graphic13][graphic13]


Escriba la información de la tabla de Azure en la página siguiente. El alias de salida es el nombre que puede usar en la consulta para dirigir la salida de la consulta a esta tabla.


![graphic14][graphic14] ![graphic15][graphic15]

Tamaño del lote es el número de registros de una operación por lotes; déjelo como valor predeterminado si no está familiarizado de él, o bien consulte [https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obtener más información


Si usa un almacenamiento de Azure que existe dentro de la misma suscripción que  utiliza, puede seleccionar "Usar cuenta de almacenamiento de la suscripción actual" y seleccione la cuenta de almacenamiento en la lista desplegable.

Haga clic en siguiente para agregar esta salida. Debería ver iniciarse dos operaciones; la primera es agregar la salida.

![graphic16][graphic16]

La segunda es probar la conexión. Análisis de transmisiones de Azure intentará conectarse a esa cuenta de almacenamiento y verificar que las credenciales que ha escrito sean correctas. Debería ver uno de dos mensajes después de que se complete.

![graphic17][graphic17]

Si ese fue el último, haga clic en "DETALLES" para ver los detalles exactos del error.

![graphic18][graphic18]

En este ejemplo, las credenciales proporcionadas son incorrectas. Puede corregir las credenciales y volver a probarlas; para ello, haga clic en el botón "PROBAR CONEXIÓN".

![graphic19][graphic19]

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-input-output/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-input-output/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-input-output/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-input-output/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-input-output/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-input-output/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-input-output/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-input-output/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-input-output/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-input-output/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-input-output/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-input-output/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-input-output/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-input-output/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-input-output/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-input-output/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-input-output/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-input-output/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-input-output/19-stream-analytics-connect-data-event-input-output.png

<!--HONumber=52-->
