<properties
   pageTitle="Análisis de datos en el Almacén de Data Lake con Power BI | Azure"
   description="Uso de Power BI para analizar datos almacenados en el Almacén de Azure Data Lake"
   services="data-lake-store" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/18/2016"
   ms.author="nitinme"/>

# Análisis de datos en Almacén de Data Lake mediante Power BI

En este artículo aprenderá a usar Power BI Desktop para analizar y visualizar los datos almacenados en el Almacén de Azure Data Lake.

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

- **Habilite su suscripción de Azure** para la versión preliminar pública de Azure Data Lake Store. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).

- **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md). En este artículo se supone que ya creó una cuenta del Almacén de Data Lake denominada **mybidatalakestore** y le cargó un archivo de datos de ejemplo (**Drivers.txt**). Este archivo de ejemplo está disponible para su descarga en [Azure Data Lake Git Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt) (Repositorio Git de Azure Data Lake).

- **Power BI Desktop**. Puede descargarla del [Centro de descarga de Microsoft](https://www.microsoft.com/es-ES/download/details.aspx?id=45331).


## Creación de un informe en Power BI Desktop

1. Inicie Power BI Desktop en el equipo.

2. En la cinta **Inicio**, haga clic en **Obtener datos** y luego en Más. En el cuadro de diálogo **Obtener datos**, haga clic en **Azure**, en **Almacén de Azure Data Lake** y luego en **Conectar**.

	![Conexión al Almacén de Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Conexión al Almacén de Data Lake")

3. Si ve un cuadro de diálogo que indica que el conector está en fase de desarrollo, opte por continuar.

4. En el cuadro de diálogo **Almacén de Microsoft Azure Data Lake**, indique la dirección URL a su cuenta del Almacén de Data Lake y haga clic en **Aceptar**.

	![Dirección URL del Almacén de Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Dirección URL del Almacén de Data Lake")

5. En el siguiente cuadro de diálogo, haga clic en **Iniciar sesión** para iniciar sesión en la cuenta del Almacén de Data Lake. Se le redirigirá a la página de inicio de sesión de su organización. Siga las indicaciones para iniciar sesión en la cuenta.

	![Inicio de sesión en el Almacén de Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Inicio de sesión en el Almacén de Data Lake")

6. Cuando haya iniciado sesión correctamente, haga clic en **Conectar**.

	![Conexión al Almacén de Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Conexión al Almacén de Data Lake")

7. El siguiente cuadro de diálogo muestra el archivo que cargó a su cuenta del Almacén de Data Lake. Compruebe la información y haga clic en **Cargar**.

	![Carga de datos del Almacén de Data Lake](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Carga de datos del Almacén de Data Lake")

8. Después de cargar correctamente los datos en Power BI, verá los siguientes campos en la pestaña **Campos**.

	![Campos importados](./media/data-lake-store-power-bi/imported-fields.png "Campos importados")

	Pero, para visualizar y analizar los datos, es preferible que estos estén disponibles por los campos siguientes.

	![Campos deseados](./media/data-lake-store-power-bi/desired-fields.png "Campos deseados")

	En los pasos siguientes, se actualizará la consulta para convertir los datos importados al formato deseado.

9. En la cinta **Inicio**, haga clic en **Editar consultas**.

	![Edición de consultas](./media/data-lake-store-power-bi/edit-queries.png "Edición de consultas")

10. En el Editor de consultas, en la columna **Contenido**, haga clic en **Binario**.

	![Edición de consultas](./media/data-lake-store-power-bi/convert-query1.png "Edición de consultas")

11. Verá un icono de archivo, que representa el archivo **Drivers.txt** que cargó. Haga clic en el archivo y luego en **CSV**.

	![Edición de consultas](./media/data-lake-store-power-bi/convert-query2.png "Edición de consultas")

12. Debería ver una salida como la siguiente. Los datos están ahora disponibles en un formato que puede usar para crear visualizaciones.

	![Edición de consultas](./media/data-lake-store-power-bi/convert-query3.png "Edición de consultas")

13. En la cinta **Inicio**, haga clic en **Cerrar y aplicar** y luego en **Cerrar y aplicar**.

	![Edición de consultas](./media/data-lake-store-power-bi/load-edited-query.png "Edición de consultas")

14. Después de actualizar la consulta, la pestaña **Campos** mostrará los nuevos campos disponibles para su visualización.

	![Campos actualizados](./media/data-lake-store-power-bi/updated-query-fields.png "Campos actualizados")

15. Vamos a crear un gráfico circular para representar los conductores de cada ciudad de un país determinado. Para ello, realice las selecciones siguientes.

	1. En la pestaña Visualizaciones, haga clic en el símbolo de un gráfico circular.

		![Creación de un gráfico circular](./media/data-lake-store-power-bi/create-pie-chart.png "Creación de un gráfico circular")

	2. Las columnas que se van a usar son **Columna 4** (nombre de la ciudad) y **Columna 7** (nombre del país). Arrastre estas columnas de la pestaña **Campos** a la pestaña **Visualizaciones**, tal y como se muestra a continuación.

		![Crear visualizaciones](./media/data-lake-store-power-bi/create-visualizations.png "Crear visualizaciones")

	3. El gráfico circular ahora debe ser similar al que se muestra a continuación.

		![Gráfico circular](./media/data-lake-store-power-bi/pie-chart.png "Crear visualizaciones")

16. Si selecciona un país específico en los filtros de nivel de página, ahora puede ver el número de conductores de cada ciudad del país seleccionado. Por ejemplo, en la pestaña **Visualizaciones**, en **Filtros de nivel de página**, seleccione **Brazil**.

	![Selección de un país](./media/data-lake-store-power-bi/select-country.png "Selección de un país")

17. El gráfico circular se actualiza automáticamente para mostrar los conductores de las ciudades de Brasil.

	![Conductores de un país](./media/data-lake-store-power-bi/driver-per-country.png "Conductores por país")

18. En el menú **Archivo**, haga clic en **Guardar** para guardar la visualización como archivo de Power BI Desktop.

## Publicación del informe en el servicio Power BI

Después de crear las visualizaciones en Power BI Desktop, puede compartirlas con otros usuarios publicándolas en el servicio Power BI. Para obtener instrucciones sobre cómo hacerlo, consulte [Publicar desde Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## Otras referencias

* [Análisis de datos en el Almacén de Data Lake con Análisis de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

<!---HONumber=AcomDC_0914_2016-->