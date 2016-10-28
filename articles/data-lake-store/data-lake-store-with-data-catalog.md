<properties
   pageTitle="Registro de datos del Almacén de Data Lake en el Catálogo de datos de Azure | Azure"
   description="Registro de datos del Almacén de Data Lake en el Catálogo de datos de Azure"
   services="data-lake-store,data-catalog" 
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
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Registro de datos del Almacén de Data Lake en el Catálogo de datos de Azure

En este artículo aprenderá a integrar el Almacén de Azure Data Lake con el Catálogo de datos de Azure para hacer que los datos sean reconocibles dentro de una organización mediante la integración con el Catálogo de datos. Para más información sobre la catalogación de datos, consulte [¿Qué es el Catálogo de datos de Azure?](../data-catalog/data-catalog-what-is-data-catalog.md). Para saber en qué escenarios se puede utilizar el Catálogo de datos, consulte [Escenarios comunes del Catálogo de datos de Azure](../data-catalog/data-catalog-common-scenarios.md).

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

- **Habilite su suscripción de Azure** para la versión preliminar pública de Azure Data Lake Store. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).

- **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md). Para este tutorial, vamos a crear una cuenta de Almacén de datos de Data Lake denominada **datacatalogstore**.

	Una vez creada la cuenta, cargue un conjunto de datos de ejemplo en ella. En este tutorial, vamos a cargar todos los archivos .csv en la carpeta **AmbulanceData** del [repositorio Git de Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Puede utilizar varios clientes, como el [explorador de Almacenamiento de Azure](http://storageexplorer.com/), para cargar datos en un contenedor de blobs.

- **Catálogo de datos de Azure**. Su organización ya debe tener un Catálogo de datos de Azure creado. Se permite solo un catálogo por cada organización.

## Registro del Almacén de Data Lake como origen para el Catálogo de datos

>[AZURE.VIDEO adcwithadl] 

1. Vaya a `https://azure.microsoft.com/services/data-catalog` y haga clic en **Iniciado**.

2. Inicie sesión en el portal del Catálogo de datos de Azure y haga clic en **Publicar datos**.

	![Registro de un origen de datos](./media/data-lake-store-with-data-catalog/register-data-source.png "Registro de un origen de datos")

3. En la siguiente página, haga clic en **Iniciar aplicación**. Esto descargará el archivo de manifiesto de la aplicación en el equipo. Haga doble clic en este archivo para iniciar la aplicación.

4. En la página principal, haga clic en **Iniciar sesión** y escriba sus credenciales.

	![Pantalla principal](./media/data-lake-store-with-data-catalog/welcome.screen.png "Pantalla principal")

5. En la página Seleccionar un origen de datos, seleccione **Azure Data Lake**, y, a continuación, haga clic en **Siguiente**.

	![Selección de origen de datos](./media/data-lake-store-with-data-catalog/select-source.png "Selección de origen de datos")

6. En la siguiente página, proporcione el nombre de la cuenta del Almacén de Data Lake que desea registrar en el Catálogo de datos. Deje las demás opciones con el valor predeterminado y, a continuación, haga clic en **Conectar**.

	![Conexión a orígenes de datos](./media/data-lake-store-with-data-catalog/connect-to-source.png "Conexión a orígenes de datos")

7. La página resultante se puede dividir en los siguientes segmentos.

	a. El cuadro **Jerarquía de servidor** representa la estructura de carpetas de la cuenta del Almacén de Data Lake. **$Root** representa la raíz de la cuenta del Almacén de Data Lake y **AmbulanceData** representa la carpeta creada en la raíz de dicha cuenta.

	b. El cuadro **Objetos disponibles** muestra los archivos y carpetas de la carpeta **AmbulanceData**.

	c. El cuadro **Objetos que se registrarán** enumera los archivos y carpetas que desea registrar en el Catálogo de datos de Azure.

	![Visualización de la estructura de datos](./media/data-lake-store-with-data-catalog/view-data-structure.png "Visualización de la estructura de datos")

8. Para este tutorial, deberá registrar todos los archivos del directorio. Para ello, haga clic en el botón (![movimiento de objetos](./media/data-lake-store-with-data-catalog/move-objects.png "Movimiento de objetos")) para mover todos los archivos al cuadro **Objetos que se registrarán**.

	Dado que los datos se registrarán en un catálogo de datos de toda la organización, es recomendable agregar algunos metadatos que podrá utilizar posteriormente para localizar rápidamente los datos. Por ejemplo, puede agregar una dirección de correo electrónico del propietario de los datos (por ejemplo, la persona que está cargando los datos) o agregar una etiqueta para identificar los datos. La captura de pantalla siguiente muestra una etiqueta que se ha agregado a los datos.

	![Visualización de la estructura de datos](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Visualización de la estructura de datos")

	Haga clic en **Registrar**.

8. La captura de pantalla siguiente indica que los datos se han registrado correctamente en el Catálogo de datos.

	![Registro completado](./media/data-lake-store-with-data-catalog/registration-complete.png "Visualización de la estructura de datos")

9. Haga clic en **Ver portal** para volver atrás al portal del Catálogo de datos y comprobar que ya puede acceder a los datos registrados desde el portal. Para realizar búsquedas en los datos, puede utilizar la etiqueta que usó al registrar los datos.

	![Búsqueda de datos en el catálogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Búsqueda de datos en el catálogo")

10. Ahora ya puede realizar operaciones como agregar anotaciones y documentación a los datos. Para más información, consulte los vínculos siguientes:
	* [Anotación de orígenes de datos](../data-catalog/data-catalog-how-to-annotate.md)
	* [Orígenes de datos de documentos](../data-catalog/data-catalog-how-to-documentation.md)

## Otras referencias

* [Anotación de orígenes de datos](../data-catalog/data-catalog-how-to-annotate.md)
* [Orígenes de datos de documentos](../data-catalog/data-catalog-how-to-documentation.md)
* [Integración del Almacén de Data Lake con otros servicios de Azure](data-lake-store-integrate-with-other-services.md)

<!---HONumber=AcomDC_0914_2016-->