<properties
   pageTitle="Introducción a Microsoft Power BI Embedded"
   description="Power BI Embedded, agregar informes interactivos de Power BI a la aplicación de inteligencia empresarial"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="owend"/>

# Introducción a Microsoft Power BI Embedded

**Power BI Embedded** es un servicio de Azure que permite a los desarrolladores de aplicaciones agregar informes de Power BI interactivos a sus propias aplicaciones. **Power BI Embedded** se puede utilizar con las aplicaciones existentes sin necesidad de modificar el diseño ni de cambiar el modo en que los usuarios inician sesión.

Los recursos de **Microsoft Power BI Embedded** se aprovisionan mediante las [API de ARM de Azure](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que se aprovisiona es una **colección de áreas de trabajo de Power BI**.

![](media\power-bi-embedded-get-started\introduction.png)

## Creación de una colección de áreas de trabajo
Una **colección de áreas de trabajo** es el recurso de Azure de nivel superior, y funciona como un contenedor del contenido que se insertará en la aplicación. Las **colecciones de áreas de trabajo** se pueden crear de dos maneras:

   -	Manualmente mediante el Portal de Azure.
   -	Mediante programación con la API de Azure Resource Manager (ARM).

Vamos a ver cuáles son los pasos necesarios para crear una **colección de áreas de trabajo** mediante el Portal de Azure.

   1.	Abra el **Portal de Azure**, [http://portal.azure.com](http://portal.azure.com), e inicie sesión.

   2.	En el panel superior, haga clic en **+ Nuevo**.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.	En **Datos y análisis**, haga clic en **Power BI Embedded**.
   4.	En la hoja **Creación**, escriba la información necesaria. En **Precios**, consulte los [precios de Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Haga clic en **Crear**.

La **colección de áreas de trabajo** tarda un poco en aprovisionarse. Cuando la operación finalice, accederá automáticamente a la hoja **Colección de áreas de trabajo**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

La hoja **Creación** contiene la información que necesita para llamar a las API que crean las áreas de trabajo e implementan contenido en ellas.

<a name="view-access-keys"/>
## Visualización de las claves de acceso de API de Power BI

Uno de los datos más importantes que se necesitan para llamar a las API de REST de Power BI son las **claves de acceso**. Las claves de acceso se utilizan para generar los **tokens de aplicación** que se emplean para autenticar las solicitudes de API. Para ver las **claves de acceso**, haga clic en **Claves de acceso** en la hoja **Configuración**. Para más información sobre los **tokens de aplicación**, consulte la sección sobre [cómo funciona el flujo de tokens de aplicación](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Verá que tiene dos claves.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Copie estas claves y almacénelas de forma segura en la aplicación. Es muy importante tratar estas claves igual que una contraseña, ya que proporcionan acceso a todo el contenido de la **colección de áreas de trabajo**.

Aunque se muestran dos claves, solo se necesita una cada vez. La segunda clave se proporciona para que pueda regenerar periódicamente las claves sin interrumpir el acceso al servicio.

Ahora que tiene una instancia de Power BI para la aplicación y las **claves de acceso**, puede importar un informe en la propia aplicación. Antes de aprender a importar un informe, en la sección siguiente se describe la creación de conjuntos de datos e informes de Power BI para insertar en una aplicación.

## Creación de conjuntos de datos e informes de Power BI para insertar en una aplicación

Ahora que ha creado una instancia de Power BI para su aplicación y tiene las **claves de acceso**, deberá crear los conjuntos de datos y los informes de Power BI que quiere insertar. Los conjuntos de datos y los informes se pueden crear con **Power BI Desktop**. Puede descargar [Power BI Desktop gratis](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). Por otro lado, si desea empezar a trabajar rápidamente, también puede descargar el [archivo PBIX con un ejemplo de análisis de minoristas](http://go.microsoft.com/fwlink/?LinkID=780547). Para más información acerca de cómo usar **Power BI Desktop**, consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/es-ES/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Con **Power BI Desktop**, para conectarse al origen de datos, puede importar una copia de los datos en **Power BI Desktop** o conectarse directamente al origen de datos mediante **DirectQuery**.

Estas son las diferencias entre usar la **importación** y **DirectQuery**.

|Importación | DirectQuery
|---|---
|Las tablas, las columnas y los *datos* se importan o se copian en **Power BI Desktop**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta una copia de los datos. Para ver los cambios que se han producido en los datos subyacentes, debe actualizar o importar de nuevo un conjunto de datos actual completo.|En **Power BI Desktop**, solo se importan o copian *tablas y columnas*. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta el origen de datos subyacente, lo que significa que siempre verá los datos actuales.

Para más información acerca de cómo conectarse a un origen de datos, consulte [Conectarse a un origen de datos](power-bi-embedded-connect-datasource.md).

Cuando guarde el trabajo en **Power BI Desktop**, se creará un archivo PBIX. Este archivo contiene el informe. Además, si los datos se han importado, el archivo PBIX contendrá el conjunto de datos completo, mientras que, si se ha utilizado **DirectQuery**, el archivo PBIX solamente contendrá un esquema del conjunto de datos. Puede implementar el archivo PBIX mediante programación en su área de trabajo con la [API de importación de Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Power BI Embedded** incluye API adicionales que permiten cambiar el servidor y la base de datos a los que apunta el conjunto de datos y definir una credencial de cuenta de servicio, que será la que use el conjunto de datos para conectarse a la base de datos. Consulte [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) y [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (Revisión de origen de datos de puerta de enlace).

## Pasos siguientes
En los pasos anteriores, ha creado una colección de áreas de trabajo y su primer informe y conjunto de datos. Ahora es el momento de aprender a escribir código en **Power BI Embedded**. Para ayudarle a empezar, hemos creado una aplicación web de ejemplo: [Introducción al ejemplo de Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md). El ejemplo le muestra cómo:

  -	Aprovisionar contenido
      - Crear un área de trabajo
      - Importar un archivo PBIX
      - Actualizar las cadenas de conexión y establecer las credenciales para los conjuntos de datos.

  -	Insertar un informe de forma segura

## Otras referencias
- [Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](power-bi-embedded-get-started-sample.md)
- [What is Microsoft Power BI Embedded (¿Qué es Microsoft Power BI Embedded?)](power-bi-embedded-what-is-power-bi-embedded.md)
- [Introducción a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [Precios Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527)

<!---HONumber=AcomDC_0713_2016-->