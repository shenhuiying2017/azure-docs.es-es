<properties
   pageTitle="Uso de Power BI con Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar Power BI con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="lodipalm"/>

# Uso de Power BI con Almacenamiento de datos SQL
De manera similar a lo que sucede con Base de datos SQL de Azure, Conexión directa de Almacenamiento de datos SQL permite que el usuario aproveche la aplicación lógica y eficaz junto con las funcionalidades analíticas de Power BI. Con Conexión directa, las consultas se envían de vuelta a la instancia de Almacenamiento de datos SQL de Azure en tiempo real mientras explora los datos. Esto, en combinación con la escala de Almacenamiento de datos SQL, permite que los usuarios creen, en minutos, informes dinámicos sobre terabytes de datos. Además, la introducción del botón Abrir en Power BI permite que los usuarios conecten directamente Power BI a su instancia de Almacenamiento de datos SQL sin recopilar información de otras partes de Azure.

Cuando use Conexión directa, tenga en cuenta lo siguiente:

+ Cuando se conecte, especifique el nombre completo del servidor (consulte a continuación para obtener más detalles).
+ Asegúrese de que las reglas del firewall de la base de datos estén configuradas para "Permitir el acceso a los servicios de Azure".
+ Cada acción, como seleccionar una columna o agregar un filtro, enviará directamente una consulta al almacenamiento de datos. 
+ Los iconos se actualizan, aproximadamente, cada 15 minutos (no es necesario programar la actualización).
+ La sección de Preguntas y respuestas no está disponible para los conjuntos de datos de Conexión directa.
+ Los cambios de esquema no se seleccionan automáticamente.

Estas restricciones y notas pueden cambiar a medida que continuamos mejorando las experiencias. A continuación, se detallan los pasos para realizar la conexión.

## Con el botón "Abrir en Power BI"
La manera más fácil de trasladarse entre la instancia de Almacenamiento de datos SQL y Power BI es con el botón Abrir en Power BI. Este botón le permiten comenzar a crear sin problemas paneles nuevos en Power BI.

1.	Para comenzar, vaya a su instancia de Almacenamiento de datos SQL en el Portal de Azure.
2.	Haga clic en el botón Abrir en Power BI.
3.	Si no puede iniciar sesión directamente, o bien no tiene una cuenta de Power BI, deberá iniciar sesión.  
4.	Se le dirigirá a la página de conexión a Almacenamiento de datos SQL, con la información de su instancia de Almacenamiento de datos SQL previamente rellenada.
5.  Después de escribir las credenciales, estará completamente conectado a su instancia de Almacenamiento de datos SQL. 

## Conexión a través del Portal de Power BI
Además de utilizar el botón Abrir en Power BI, los usuarios también pueden conectarse a su instancia de Almacenamiento de datos SQL a través del Portal de Power BI.

1.   En la parte inferior del panel de navegación, haga clic en Obtener datos.
2.  Seleccione Big Data y más.
3.  Cuando esté en la página Big Data y más, seleccione la instancia de Almacenamiento de datos SQL.
4.  Escriba la información de conexión necesaria. La sección Búsqueda de parámetros que aparece a continuación muestra dónde se pueden encontrar estos datos.  
5.	Mediante el desglose del conjunto de datos, puede explorar todas las tablas y columnas de su base de datos. Al seleccionar una columna se enviará una consulta de vuelta al origen, creando dinámicamente su objeto visual. Estos objetos visuales se pueden guardar en un informe nuevo y se vuelven a anclar en el panel.

## Búsqueda de valores de parámetro
Puede encontrar el nombre completo del servidor y el nombre de la base de datos en el Portal de Azure. Tenga en cuenta que, por el momento, el Almacenamiento de datos SQL solo está presente en el Portal de vista previa de Azure.

## Pasos siguientes
Para obtener información general sobre la integración, consulte [Información general de la integración de Almacenamiento de datos SQL][]. Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-develop/
[Información general de la integración de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->