<properties
   pageTitle="Microsoft Power BI Embedded - Conectarse a un origen de datos"
   description="Power BI Embedded, conectarse a orígenes de datos"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/28/2016"
   ms.author="owend"/>

# Conectarse a un origen de datos

Con **Power BI Embedded**, puede insertar informes en su propia aplicación. Cuando inserta un informe de Power BI en su aplicación, el informe se conecta a los datos subyacentes **importando** una copia de los datos o **conectándose directamente** al origen de datos mediante **DirectQuery**.

Estas son las diferencias entre usar **Importación** y **DirectQuery**.

|Importación | DirectQuery
|---|---
|Las tablas, columnas *y datos* se importan o se copian en el conjunto de datos del informe. Para ver los cambios que se han producido en los datos subyacentes, debe actualizar o importar de nuevo un conjunto de datos actual completo.|Solo las *tablas y columnas* se importan o se copian en el conjunto de datos del informe. Siempre verá los datos más actuales.

La sección siguiente describe las ventajas y limitaciones del uso de **DirectQuery**.

## Ventajas del uso de DirectQuery

Existen dos ventajas principales al usar **DirectQuery**:

   -	**DirectQuery** le permite crear visualizaciones sobre conjuntos de datos muy grandes, cuando, de lo contrario, sería inviable importar por primera vez todos los datos.
   -	Los cambios en los datos subyacentes pueden requerir una actualización de datos, y para algunos informes, la necesidad de mostrar los datos actuales puede requerir grandes transferencias de datos, haciendo inviable la reimportación de datos. Por el contrario, los informes de **DirectQuery** siempre usan datos actuales.

## Limitaciones de DirectQuery

   Existen algunas limitaciones al usar **DirectQuery**:

   -	Todas las tablas deben proceder de una base de datos única.
   -	Si la consulta es demasiado compleja, se producirá un error. Para solucionar el error debe refactorizar la consulta, de forma que sea menos compleja. Si la consulta debe ser compleja, necesitará importar los datos en lugar de usar **DirectQuery**.
   -	El filtrado de relación se limita a una dirección única, en lugar de a ambas direcciones.
   -	No puede cambiar el tipo de datos de una columna.
   -	De forma predeterminada, las limitaciones se colocan en expresiones DAX permitidas en las medidas. Consulte [DirectQuery y medidas](#measures).

<a name="measures"/>
## DirectQuery y medidas

Para asegurarse de que las consultas que se envían al origen de datos subyacente tienen un rendimiento adecuado, se imponen limitaciones en las medidas. Al usar **Power BI Desktop**, los usuarios avanzados pueden elegir derivar esta limitación eligiendo **Archivo > Opciones y configuración > Opciones**. En el cuadro de diálogo **Opciones**, elija **DirectQuery** y seleccione la opción **Permitir medidas sin restricciones en el modo DirectQuery**. Cuando se selecciona esta opción, se puede usar cualquier expresión DAX que sea válida para una medida. Sin embargo, los usuarios deben tener en cuenta que algunas expresiones que se ejecutan correctamente cuando se importan los datos, pueden provocar consultas muy lentas en el origen de back-end del modo **DirectQuery**. Para obtener más información acerca de cómo usar **Power BI Desktop**, consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/).

## Otras referencias
- [Get started with Microsoft Power BI Embedded preview (Introducción a la versión preliminar de Microsoft Power BI Embedded)](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [Introducción a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)

<!---HONumber=AcomDC_0629_2016-->