---
title: Tutorial de Power BI para el conector de DocumentDB | Microsoft Docs
description: Use este tutorial de Power BI para importar JSON, crear informes muy precisos y visualizar datos mediante el conector de DocumentDB y Power BI.
keywords: tutorial de power bi, visualizar datos, conector de power bi
services: documentdb
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: cd1b7f70-ef99-40b7-ab1c-f5f3e97641f7
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 089b250e36eb3e091ad63d716acaf6259a958898
ms.openlocfilehash: 3f2103a1d3838b73f10d567721693a9b813d0edb


---
# <a name="power-bi-tutorial-for-documentdb-visualize-data-using-the-power-bi-connector"></a>Tutorial de Power BI para DocumentDB: visualizar datos mediante el conector de Power BI
[PowerBI.com](https://powerbi.microsoft.com/) es un servicio en línea, en el que se pueden crear paneles e informes, y compartirlos, con los datos que son importantes para el usuario y su organización.  Power BI Desktop es una herramienta dedicada de creación de informes que permite recuperar datos de varios orígenes de datos, combinar y transformar los datos, crear visualizaciones e informes eficaces y publicar los informes en Power BI.  Con la versión más reciente de Power BI Desktop, ahora puede conectarse a su cuenta de DocumentDB a través del conector DocumentDB para Power BI.   

En este tutorial de Power BI, se le guiará por los pasos para conectarse a una cuenta de DocumentDB en Power BI Desktop, desplazarse a una colección donde queremos extraer los datos mediante el navegador, transformar datos JSON en formato tabular mediante el Editor de consultas de Power BI Desktop y generar y publicar un informe en PowerBI.com.

Después de completar este tutorial de Power BI, podrá responder a las preguntas siguientes:  

* ¿Cómo genero informes con datos de DocumentDB con Power BI Desktop?
* ¿Cómo puedo conectar a una cuenta de DocumentDB en Power BI Desktop?
* ¿Cómo puedo recuperar datos de una colección en Power BI Desktop?
* ¿Cómo puedo transformar datos JSON anidados en Power BI Desktop?
* ¿Cómo puedo publicar y compartir mis informes en PowerBI.com?

## <a name="prerequisites"></a>Requisitos previos
Antes de seguir las instrucciones de este tutorial de Power BI, asegúrese de contar con lo siguiente:

* [La versión más reciente de Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Acceda a nuestros datos o cuenta de demostración de la cuenta de Azure DocumentDB.
  * La cuenta de demostración se rellena con los datos de los volcanes mostrados en este tutorial. Esta cuenta de demostración no está vinculada a ningún SLA y está pensada únicamente con fines de demostración.  Nos reservamos el derecho de realizar modificaciones en esta cuenta de demostración, incluido sin limitación, la cancelación de la cuenta, el cambio de clave, la restricción del acceso, el cambio y la eliminación de los datos, todo ello en cualquier momento y sin previo aviso.
    * Dirección URL: https://analytics.documents.azure.com
    * Clave de solo lectura: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * O bien, para crear su propia cuenta, consulte [Creación de una cuenta de base de datos de DocumentDB mediante Azure Portal](https://azure.microsoft.com/documentation/articles/documentdb-create-account/). Después, para obtener los datos de los volcanes que son similares a los usados en este tutorial (pero que no contienen los bloques de GeoJSON), visite el [sitio de NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) y luego importe los datos mediante la [herramienta de migración de datos de DocumentDB](https://azure.microsoft.com/documentation/articles/documentdb-import-data/).

Para compartir los informes en PowerBI.com, debe tener una cuenta en PowerBI.com.  Para más información sobre la versión gratuita de Power BI y de Power BI Pro, visite [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Comencemos.
En este tutorial, imaginemos que usted es un geólogo que estudia los volcanes de todo el mundo.  Los datos de los volcanes se almacenan en una cuenta de DocumentDB y los documentos JSON tiene el aspecto que se muestra a continuación.

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Quiere recuperar los datos de los volcanes de la cuenta DocumentDB y visualizar datos en un informe de Power BI interactivo como el siguiente.

![Después de completar este tutorial de Power BI con el conector de Power BI, podrá visualizar los datos con el informe de volcanes de Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

¿Listo para probarlo? Comencemos.

1. Ejecute Power BI Desktop en su estación de trabajo.
2. Una vez iniciado Power BI Desktop, se muestra la pantalla de *inicio de sesión* .
   
    ![Pantalla de inicio de sesión de Power BI Desktop: conector de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_welcome.png)
3. También puede **obtener datos**, consultar **orígenes recientes** o **abrir otros informes** directamente de la pantalla de *inicio de sesión*.  Haga clic en la X de la esquina superior derecha para cerrar la pantalla. Se muestra la vista **Informe** de Power BI Desktop.
   
    ![Vista de informes de Power BI Desktop: conector de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview.png)
4. Seleccione la cinta de opciones **Inicio** y haga clic en **Obtener datos**.  Se muestra la pantalla **Obtener datos** .
5. Haga clic en **Azure**, seleccione **Microsoft Azure DocumentDB (Beta)** y, a continuación, haga clic en **Conectar**.  Se muestra la pantalla de **conexión de Microsoft Azure DocumentDB** .
   
    ![Obtención de datos de Power BI Desktop: conector de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbigetdata.png)
6. Especifique la URL del punto de conexión de la cuenta de DocumentDB desde la que desea recuperar los datos, de tal como se muestra a continuación, y haga clic en **Aceptar**. Puede recuperar la dirección URL en el cuadro URI de la hoja **[Claves](documentdb-manage-account.md#keys)** de Azure Portal, o bien puede usar la cuenta de demostración; en ese caso, la dirección URL es `https://analytics.documents.azure.com`. 
   
    Deje en blanco el nombre de la base de datos, el nombre de la colección y la instrucción SQL. Esos campos son opcionales.  En su lugar, usaremos el navegador para seleccionar la base de datos y la colección para identificar la procedencia de los datos.
   
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Ventana de conexión de Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
7. Si se conecta a este punto de conexión por primera vez, se le pedirá la clave de cuenta.  Puede recuperar la clave del cuadro **Clave principal** de la hoja **[Claves de solo lectura](documentdb-manage-account.md#keys)** de Azure Portal, o bien puede usar la cuenta de demostración; en ese caso, la clave es `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Escriba la clave de cuenta y haga clic en **Conectar**.
   
    Se recomienda usar la clave de solo lectura al generar informes.  De esta forma, evitará una exposición innecesaria de la clave maestra a posibles riesgos de seguridad. La clave de solo lectura está disponible en la hoja [Claves](documentdb-manage-account.md#keys) de Azure Portal, o bien puede usar la información de la cuenta de demostración proporcionada anteriormente.
   
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Clave de cuenta](./media/documentdb-powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
8. Cuando la cuenta está conectada correctamente, se mostrará el **navegador** .  El **navegador** mostrará una lista de bases de datos en la cuenta.
9. Haga clic y expanda la base de datos de donde procederán los datos del informe; si usa la cuenta de demostración, seleccione **volcanodb**.   
10. Ahora, seleccione una colección desde la que va a recuperar los datos. Si usa la cuenta de demostración, seleccione **volcano1**.
    
    El panel de vista previa muestra una lista de elementos **Registro** .  Un documento se representa como un tipo **Registro** en Power BI. De forma similar, un bloque JSON anidado dentro de un documento también es un **Registro**.
    
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Ventana del navegador](./media/documentdb-powerbi-visualize/power_bi_connector_pbinavigator.png)
11. Haga clic en **Editar** para iniciar el Editor de consultas, para que podamos transformar los datos.

## <a name="flattening-and-transforming-json-documents"></a>Eliminación de formato y transformación de documentos JSON
1. En el Editor de consultas de Power BI, debería ver la columna **Documento** en el panel central.
   ![Editor de consultas de Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Haga clic en el botón de expansión en el lado derecho del encabezado de columna **Documento** .  Se muestra el menú contextual con una lista de campos.  Seleccione los campos necesarios para el informe, por ejemplo, nombre de volcán, país, región, ubicación, elevación, tipo, estado y última erupción conocida y, después, haga clic en **Aceptar**.
   
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Expandir Documentos](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. El panel central mostrará una vista previa del resultado con los campos seleccionados.
   
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Resultados sin formato](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. En nuestro ejemplo, la propiedad Ubicación es un bloque de GeoJSON en un documento.  Como puede ver, la ubicación se representa como un tipo **Registro** en Power BI Desktop.  
5. Haga clic en el botón de expansión en el lado derecho del encabezado de columna Ubicación.  Aparecerá el menú contextual con los campos de tipo y coordenadas.  Ahora seleccione el campo de coordenadas y haga clic en **Aceptar**.
   
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Registro de ubicación](./media/documentdb-powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. El panel central muestra ahora una columna de coordenadas de tipo **lista** .  Como se ha indicado al comienzo del tutorial, los datos de GeoJSON son de tipo Point con los valores de latitud y longitud registrados en la matriz de coordenadas.
   
    El elemento coordinates[0] representa la longitud, mientras que el elemento coordinates[1] representa la latitud.
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Lista de coordenadas](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Para eliminar el formato de la matriz de coordenadas, crearemos una **columna personalizada** llamada LatLong.  Seleccione la cinta de opciones **Agregar columna** y haga clic en **Agregar columna personalizada**.  La ventana **Agregar columna personalizada** debería aparecer .
8. Dele un nombre a la nueva columna, por ejemplo, LatLong.
9. Después, especifique la fórmula personalizada para la nueva columna.  En nuestro ejemplo, concatenaremos los valores de latitud y longitud separados por comas, como se muestra a continuación mediante la fórmula siguiente: `Text.From([Document.Location.coordinates]{1})&","&Text.From([Document.Location.coordinates]{0})`. Haga clic en **Aceptar**.
   
    Para más información sobre Expresiones de análisis de datos (DAX), incluidas las funciones DAX, visite [DAX Basic en Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Agregar columna personalizada](./media/documentdb-powerbi-visualize/power_bi_connector_pbicustomlatlong.png)
10. Ahora, el panel central mostrará la nueva columna LatLong rellena con los valores de latitud y longitud separados por una coma.
    
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Columna LatLong personalizada](./media/documentdb-powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Si recibe un error en la columna nueva, asegúrese de que los pasos aplicados en Configuración la consulta coinciden con la siguiente figura:
    
    ![Los pasos aplicados deben ser los siguientes: Source, Navigation, Expanded Document, Expanded Document.Location, Added Custom](./media/documentdb-powerbi-visualize/azure-documentdb-power-bi-applied-steps.png)
    
    Si los pasos son distintos, elimine los pasos adicionales e intente agregar nuevamente la columna personalizada. 
11. Hemos finalizado la eliminación del formato de los datos en formato tabular.  Puede aprovechar todas las características disponibles en el Editor de consultas para dar forma y transformar los datos de DocumentDB.  Si usa el ejemplo, cambie el tipo de datos para elevación a **Número entero**; para ello, cambie **Tipo de datos** en la cinta de opciones **Inicio**.
    
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Cambiar tipo de columna](./media/documentdb-powerbi-visualize/power_bi_connector_pbichangetype.png)
12. Haga clic en **Cerrar y aplicar** para guardar el modelo de datos.
    
    ![Tutorial de Power BI para conector de Power BI de DocumentDB - Cerrar y aplicar](./media/documentdb-powerbi-visualize/power_bi_connector_pbicloseapply.png)

## <a name="build-the-reports"></a>Generación de informes
La vista de informe de Power BI Desktop es donde puede empezar a crear informes para visualizar datos.  Puede crear informes arrastrando y soltando campos en el lienzo del **informe** .

![Vista de informes de Power BI Desktop: conector de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview2.png)

En la vista de informe, debe buscar:

1. El panel **Campos** , que es donde verá una lista de los modelos de datos con campos que puede usar para los informes.
2. El panel **Visualizaciones** . Un informe puede contener una o varias visualizaciones.  Seleccione los tipos visuales que se ajusten a sus necesidades en el panel **Visualizaciones** .
3. El lienzo **Informe** , donde creará los objetos visuales para el informe.
4. La página **Informe** . Puede agregar varias páginas de informes en Power BI Desktop.

A continuación se muestran los pasos básicos para crear un sencillo informe de la vista de mapa interactivo.

1. En nuestro ejemplo, crearemos una vista del mapa que muestra la ubicación de cada volcán.  En el panel **Visualizaciones** , haga clic en el tipo de mapa visual que aparece destacado en la captura de pantalla anterior.  Debería ver el tipo de mapa visual pintado en el lienzo **Informe** .  El panel de **visualizaciones** también debe mostrar un conjunto de propiedades relacionadas con el tipo de mapa visual.
2. Ahora, arrastre y coloque el campo LatLong desde el panel de **campos** a la **bicación** del panel de **visualizaciones**.
3. A continuación, arrastre y coloque el campo del nombre del volcán en la propiedad **Leyenda** .  
4. Luego, arrastre y coloque el campo Elevación en la propiedad **Tamaño** .  
5. Ahora debería ver el mapa visual que muestra un conjunto de burbujas que indican la ubicación de cada volcán, con el tamaño de la burbuja correlacionado con la elevación del volcán.
6. Ahora ha creado un informe básico.  Puede personalizar aún más el informe agregando más visualizaciones.  En nuestro caso, hemos agregado una segmentación del tipo de volcán para que el informe sea interactivo.  
   
    ![Captura de pantalla del informe final de Power BI Desktop tras la finalización del tutorial de Power BI para DocumentDB](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

## <a name="publish-and-share-your-report"></a>Publicación y uso compartido del informe
Para compartir el informe, debe tener una cuenta en PowerBI.com.

1. En Power BI Desktop, haga clic en la cinta de opciones de **inicio** .
2. Haga clic en **Publicar**.  Se le pedirá que escriba el nombre de usuario y la contraseña de su cuenta de PowerBI.com.
3. Una vez que se autentique la credencial, el informe se publicará en el destino que seleccione.
4. Haga clic en **Abrir 'PowerBITutorial.pbix' en Power BI** para ver y compartir su informe en PowerBI.com.
   
    ![La publicación en Power BI se realizó correctamente Abrir tutorial de Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Creación de un panel en PowerBi.com
Ahora que tiene un informe, vamos a compartirlo en PowerBi.com

Cuando publica su informe de Power BI Desktop en PowerBI.com, se genera un **informe** y un **conjunto de datos** en su inquilino de PowerBI.com. Por ejemplo, después de que publique un informe llamado **PowerBITutorial** en PowerBI.com, podrá ver PowerBITutorial tanto en la sección de **informes** como en la sección de **onjuntos de datos** en PowerBI.com.

   ![Captura de pantalla del nuevo Informe y base de datos en PowerBI.com](./media/documentdb-powerbi-visualize/documentdb-powerbi-reports-datasets.png)

Para crear un panel que se puede compartir, haga clic en el botón de la **página Anclar elemento activo** del informe PowerBI.com.

   ![Captura de pantalla del nuevo Informe y base de datos en PowerBI.com](./media/documentdb-powerbi-visualize/azure-documentdb-power-bi-pin-live-tile.png)

Luego, siga las instrucciones que aparecen en [Anclar un icono de un informe](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para crear un panel nuevo. 

También puede realizar modificaciones ad hoc en el informe antes de crear un panel. Sin embargo, se recomienda que use Power BI Desktop para realizar las modificaciones y vuelva a publicar el informe en PowerBI.com.

## <a name="refresh-data-in-powerbicom"></a>Actualización de datos en PowerBI.com
Hay dos formas de actualizar los datos: actualización ad hoc y actualización programada.

En el caso de una actualización ad hoc, solo debe hacer clic en los tres puntos suspensivos (…) junto al **conjunto de datos**; por ejemplo, PowerBITutorial. Debería ver una lista de acciones, incluyendo **Actualizar ahora**. Haga clic en **Actualizar ahora** para actualizar los datos.

![Captura de pantalla de Actualizar ahora en PowerBI.com](./media/documentdb-powerbi-visualize/azure-documentdb-power-bi-refresh-now.png)

Para realizar una actualización programada, haga lo siguiente.

1. Haga clic en **Programar actualización** de la lista de acciones. 
    ![Captura de pantalla de Programar actualización en PowerBI.com](./media/documentdb-powerbi-visualize/azure-documentdb-power-bi-schedule-refresh.png)
2. En la página **Configuración**, expanda **Credenciales del origen de datos**. 
3. Haga clic en **Editar credenciales**. 
   
    Aparecerá el elemento emergente Configurar. 
4. Escriba la clave para conectarse a la cuenta de DocumentDB que corresponde a ese conjunto de base de datos y, luego, haga clic en **Iniciar sesión**. 
5. Expanda **Programar actualización** y configure la programación que desea usar para actualizar el conjunto de datos. 
6. Haga clic en **Aplicar** para terminar de configurar la actualización programada.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre Power BI, consulte [Introducción a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para más información sobre DocumentDB, consulte la [página de aterrizaje de la documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).




<!--HONumber=Feb17_HO3-->


