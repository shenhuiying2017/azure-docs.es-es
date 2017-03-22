---
title: "Introducción a Microsoft Power BI Embedded"
description: "Power BI Embedded, agregar informes interactivos de Power BI a la aplicación de inteligencia empresarial"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 4afa8d2c7f8ec1942521ba5fa131967dfd581c91
ms.lasthandoff: 03/14/2017

---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introducción a Microsoft Power BI Embedded

**Power BI Embedded** es un servicio de Azure que permite a los desarrolladores de aplicaciones agregar informes de Power BI interactivos a sus propias aplicaciones. **Power BI Embedded** se puede utilizar con las aplicaciones existentes sin necesidad de modificar el diseño ni de cambiar el modo en que los usuarios inician sesión.

Los recursos de **Microsoft Power BI Embedded** se aprovisionan mediante las [API de ARM de Azure](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que se aprovisiona es una **colección de áreas de trabajo de Power BI**.

![](media/power-bi-embedded-get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Creación de una colección de áreas de trabajo

Una **colección de áreas de trabajo** es el recurso de Azure de nivel superior, y funciona como un contenedor del contenido que se insertará en la aplicación. Las **colecciones de áreas de trabajo** se pueden crear de dos maneras:

* Manualmente mediante el Portal de Azure.
* Mediante programación con la API de Azure Resource Manager (ARM).

Vamos a ver cuáles son los pasos necesarios para crear una **colección de áreas de trabajo** mediante el Portal de Azure.

1. Abra el **Portal de Azure**, [http://portal.azure.com](http://portal.azure.com), e inicie sesión.
2. En el panel superior, haga clic en **+ Nuevo** .
   
   ![](media/power-bi-embedded-get-started/create-workspace-1.png)
3. En **Datos y análisis**, haga clic en **Power BI Embedded**.
4. En la **hoja Colección de áreas de trabajo de Power BI**, escriba la información necesaria. En **Precios**, consulte los [precios de Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).
   
   ![](media/power-bi-embedded-get-started/create-workspace-2.png)
5. Haga clic en **Crear**.

La **colección de áreas de trabajo** tarda un poco en aprovisionarse. Cuando la operación finalice, accederá automáticamente a la hoja **Colección de áreas de trabajo**.

   ![](media/power-bi-embedded-get-started/create-workspace-3.png)

La hoja **Creación** contiene la información que necesita para llamar a las API que crean las áreas de trabajo e implementan contenido en ellas.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Visualización de las claves de acceso de API de Power BI

Uno de los datos más importantes que se necesitan para llamar a las API de REST de Power BI son las **claves de acceso**. Las claves de acceso se utilizan para generar los **tokens de aplicación** que se emplean para autenticar las solicitudes de API. Para ver las **claves de acceso**, haga clic en **Claves de acceso** en la hoja **Configuración**. Para más información sobre los **tokens de aplicación**, consulte [Autenticación y autorización con Power BI Embedded](power-bi-embedded-app-token-flow.md).

   ![](media/power-bi-embedded-get-started/access-keys.png)

Verá que tiene dos claves.

   ![](media/power-bi-embedded-get-started/access-keys-2.png)

Copie estas claves y almacénelas de forma segura en la aplicación. Es muy importante tratar estas claves igual que una contraseña, ya que proporcionan acceso a todo el contenido de la **colección de áreas de trabajo**.

Aunque se muestran dos claves, solo se necesita una cada vez. La segunda clave se proporciona para que pueda regenerar periódicamente las claves sin interrumpir el acceso al servicio.

Ahora que tiene una instancia de Power BI para la aplicación y las **claves de acceso**, puede importar un informe en la propia aplicación. Antes de aprender a importar un informe, en la sección siguiente se describe la creación de conjuntos de datos e informes de Power BI para insertar en una aplicación.

## <a name="working-with-workspaces"></a>Trabajo con áreas de trabajo

Después de haber creado la colección de áreas de trabajo, debe crear un área de trabajo que hospede los informes y conjuntos de datos. Para crear un área de trabajo, debe usar la [API de REST de Post Workspace](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Creación de conjuntos de datos e informes de Power BI para insertar en una aplicación mediante Power BI Desktop

Ahora que ha creado una instancia de Power BI para su aplicación y tiene las **claves de acceso**, deberá crear los conjuntos de datos y los informes de Power BI que quiere insertar. Los conjuntos de datos y los informes se pueden crear con **Power BI Desktop**. Puede descargar [Power BI Desktop gratis](https://go.microsoft.com/fwlink/?LinkId=521662). Por otro lado, si desea empezar a trabajar rápidamente, también puede descargar el [archivo PBIX con un ejemplo de análisis de minoristas](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Para aprender más acerca de cómo usar **Power BI Desktop**, consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Con **Power BI Desktop**, para conectarse al origen de datos, puede importar una copia de los datos en **Power BI Desktop** o conectarse directamente al origen de datos mediante **DirectQuery**.

Estas son las diferencias entre usar **Importación** y **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Las tablas, las columnas y los *datos* se importan o se copian en **Power BI Desktop**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta una copia de los datos. Para ver los cambios que se han producido en los datos subyacentes, debe actualizar o importar de nuevo un conjunto de datos actual completo. |En *Power BI Desktop* , solo se importan o copian **tablas y columnas**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta el origen de datos subyacente, lo que significa que siempre verá los datos actuales. |

Para más información acerca de cómo conectarse a un origen de datos, consulte [Conectarse a un origen de datos](power-bi-embedded-connect-datasource.md).

Cuando guarde el trabajo en **Power BI Desktop**, se creará un archivo PBIX. Este archivo contiene el informe. Además, si los datos se han importado, el archivo PBIX contendrá el conjunto de datos completo, mientras que, si se ha utilizado **DirectQuery**, el archivo PBIX solamente contendrá un esquema del conjunto de datos. Puede implementar el archivo PBIX mediante programación en su área de trabajo con la [API de importación de Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> **Power BI Embedded** incluye API adicionales que permiten cambiar el servidor y la base de datos a los que apunta el conjunto de datos y definir una credencial de cuenta de servicio, que será la que use el conjunto de datos para conectarse a la base de datos. Consulte [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) y [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (Revisión de origen de datos de puerta de enlace).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Creación de conjuntos de datos e informes de Power BI con las API

### <a name="datsets"></a>Conjuntos de datos

Puede crear conjuntos de datos en Power BI Embedded mediante la API de REST. Luego, puede insertar datos en el conjunto de datos. De esta forma puede trabajar con datos sin necesidad de Power BI Desktop. Para más información, consulte [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx) (Publicación de conjuntos de datos).

### <a name="reports"></a>Informes

Puede crear un informe a partir de un conjunto de datos directamente en la aplicación mediante la API de JavaScript. Para más información, consulte [Create a new report from a dataset in Power BI Embedded](power-bi-embedded-create-report-from-dataset.md) (Creación de un nuevo informe a partir de un conjunto de datos en Power BI Embedded).

## <a name="see-also"></a>Otras referencias

[Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](power-bi-embedded-get-started-sample.md)  
[Autenticación y autorización con Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Inserción de un informe](power-bi-embedded-embed-report.md)  
[Create a new report from a dataset in Power BI Embedded](power-bi-embedded-create-report-from-dataset.md) (Creación de un nuevo informe a partir de un conjunto de datos en Power BI Embedded) 
[Save reports](power-bi-embedded-save-reports.md) (Guardar informes)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Ejemplo de JavaScript insertado)  
¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)


