<properties
   pageTitle="Introducción a la versión preliminar de Microsoft Power BI Embedded"
   description="Power BI Embedded, agregar informes interactivos de Power BI a la aplicación de inteligencia empresarial"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="04/18/2016"
   ms.author="derrickv"/>

# Introducción a la versión preliminar de Microsoft Power BI Embedded

**Microsoft Power BI Embedded** es un servicio de Azure que permite a los desarrolladores de aplicaciones agregar informes de Power BI interactivos a sus propias aplicaciones. **Power BI Embedded** funciona con las aplicaciones existentes sin necesidad de volver a diseñar esas aplicaciones o cambiar la manera en que sus usuarios inician sesión.

Para más información sobre Power BI Embedded, consulte [What is Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md) (¿Qué es Power BI Embedded?).

Como se describe en [What is Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md) (¿Qué es Power BI Embedded?), los recursos de **Microsoft Power BI Embedded** se aprovisionan mediante las [API de ARM de Azure](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que se aprovisiona es una **colección de áreas de trabajo de Power BI**. En la siguiente sección se muestra cómo crear una colección de áreas de trabajo.

![](media\power-bi-embedded-get-started\introduction.png)

## Creación de una colección de áreas de trabajo
Una **colección de áreas de trabajo** es el recurso de Azure de nivel superior y un contenedor para el contenido que se insertará en la aplicación. Una **colección de áreas de trabajo** se puede crear de dos maneras:

   -	Manualmente mediante el Portal de Azure.
   -	Mediante programación con la API de Azure Resource Manager (ARM).

Vamos a ver cuáles son los pasos para crear una **colección de áreas de trabajo** mediante el Portal de Azure.

   1.	Abra e inicie sesión en el **Portal de Azure**: [http://portal.azure.com](http://portal.azure.com).

   2.	Haga clic en **+ Nuevo** en el panel superior.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.	En **Datos y análisis**, haga clic en **Power BI Embedded**.
   4.	En la hoja **Creación**, escriba la información necesaria. En **Precio**, consulte los [precios de Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Haga clic en **Crear**.

La **colección de áreas de trabajo** tarda unos momentos en aprovisionarse. Cuando la operación finalice, llegará a la hoja **Colección de áreas de trabajo**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

La hoja **Creación** contiene la información que necesita para llamar a las API que crean las áreas de trabajo e implementan contenido en ellas.

En la siguiente sección se describe cómo se utilizan las **claves de acceso** para generar **tokens de aplicación** que se emplean para autenticar las solicitudes de API.

<a name="view-access-keys"/>
## Visualización de las claves de acceso de API de Power BI

Uno de los elementos de información más importantes que se necesitan para llamar a las API de REST de Power BI son las **claves de acceso**. Se utilizan para generar los **tokens de aplicación** que se emplean para autenticar las solicitudes de API. Para ver las **claves de acceso**, haga clic en **Claves de acceso** en la hoja **Configuración**. Para más información sobre los **tokens de aplicación**, consulte [Acerca del flujo del token de aplicación en Power BI Embedded](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Verá que tiene dos claves.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Copie estas claves y almacénelas de forma segura en la aplicación. Es muy importante tratar estas claves igual que una contraseña, ya que proporcionan acceso a todo el contenido de su **colección de áreas de trabajo**.

Aunque se muestran dos claves, solo se necesita una cada vez. La segunda clave se proporciona para que pueda regenerar periódicamente las claves sin interrumpir el acceso al servicio.

Ahora que tiene una instancia de Power BI para su aplicación y las **claves de acceso**, puede importar un informe en su propia aplicación. Antes de aprender a importar un informe, en la sección siguiente se describe la creación de conjuntos de datos e informes de Power BI para insertar en una aplicación.

## Creación de conjuntos de datos e informes de Power BI para insertar en una aplicación

Ya ha creado una instancia de Power BI para su aplicación y tiene las **claves de acceso**, ahora deberá crear los conjuntos de datos y los informes de Power BI que quiere insertar. Los conjuntos de datos y los informes se pueden crear con **Power BI Desktop**. Puede descargar [Power BI Desktop de forma gratuita](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). O bien, para empezar a trabajar rápidamente, puede descargar el [archivo PBIX con un ejemplo de análisis minorista](http://go.microsoft.com/fwlink/?LinkID=780547). Para más información sobre cómo usar **Power BI Desktop**, consulte [Getting Started with Power BI Desktop](https://powerbi.microsoft.com/es-ES/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop) (Introducción a Power BI Desktop).

Con **Power BI Desktop**, puede conectarse al origen de datos mediante la importación de una copia de los datos a **Power BI Desktop** o conectándose directamente al origen de datos mediante **DirectQuery**.

Estas son las diferencias entre usar **Importación** y **DirectQuery**.

|Importación | DirectQuery
|---|---
|Las tablas, las columnas y los *datos* se importan o se copian en **Power BI Desktop**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta una copia de los datos. Para ver los cambios que se han producido en los datos subyacentes, debe actualizar o importar de nuevo un conjunto de datos actual completo.|Solo se importan o copian *tablas y columnas* en **Power BI Desktop**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta el origen de datos subyacente, lo que significa que siempre se están viendo los datos actuales.

Para más información sobre cómo conectarse a un origen de datos, consulte [Connect to a data source](power-bi-embedded-connect-datasource.md) (Conexión a un origen de datos).

Después de guardar su trabajo en **Power BI Desktop**, se crea un archivo PBIX. Este archivo contiene el informe. Además, si importa los datos el PBIX contiene el conjunto de datos completo, o si usa **DirectQuery**, el PBIX solo contiene un esquema del conjunto de datos. Implemente el PBIX mediante programación en su área de trabajo con la [API de importación de Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Power BI Embedded** incluye API adicionales para cambiar el servidor y la base de datos a los que apunta el conjunto de datos y definir una credencial de cuenta de servicio que el conjunto de datos usará para conectarse a la base de datos. Consulte [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) y [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (Revisión de origen de datos de puerta de enlace).

## Pasos siguientes
En los pasos anteriores, ha creado una colección de áreas de trabajo y su primer informe y conjunto de datos. Ahora es el momento de aprender a escribir código para **Power BI Embedded**. Para ayudarle a empezar, hemos creado una aplicación web de ejemplo: [Get started with Microsoft Power BI Embedded sample](power-bi-embedded-get-started-sample.md) (Introducción a Microsoft Power BI Embedded: ejemplo). El ejemplo le muestra cómo:

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

<!---HONumber=AcomDC_0420_2016-->