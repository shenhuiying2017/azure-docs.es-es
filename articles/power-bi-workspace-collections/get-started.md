---
title: "Introducción a las colecciones de áreas de trabajo de Power BI"
description: "Las colecciones de áreas de trabajo de Power BI son un servicio de Azure que permite a los desarrolladores de aplicaciones agregar informes de Power BI interactivos a sus propias aplicaciones."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: asaxton
ms.openlocfilehash: 4ee113ed25142507f381d8c9d49b25ee6553c525
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Introducción a las colecciones de áreas de trabajo de Power BI

Las **colecciones de áreas de trabajo de Power BI** son un servicio de Azure que permite a los desarrolladores de aplicaciones agregar informes de Power BI interactivos a sus propias aplicaciones. Las **colecciones de áreas de trabajo de Power BI** se pueden utilizar con las aplicaciones existentes sin necesidad de modificar el diseño ni de cambiar el modo en que los usuarios inician sesión.

> [!IMPORTANT]
> Las colecciones de áreas de trabajo de Power BI están en desuso y estarán disponibles hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de la colección de áreas de trabajo de Power BI Embedded a Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Los recursos para las **colecciones de áreas de trabajo de Microsoft Power BI** se aprovisionan mediante las [API de Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que se aprovisiona es una **colección de áreas de trabajo de Power BI**.

![Flujo general de colecciones de áreas de trabajo de Power BI](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Creación de una colección de áreas de trabajo

Una **colección de áreas de trabajo** es el recurso de Azure de nivel superior, y funciona como un contenedor del contenido que se insertará en la aplicación. Una **colección de áreas de trabajo** se puede crear de dos maneras:

* Manualmente mediante Azure Portal.
* Mediante programación con las API de Azure Resource Manager.

Vamos a ver cuáles son los pasos necesarios para crear una **colección de áreas de trabajo** mediante Azure Portal.

1. Abra **Azure Portal**, [http://portal.azure.com](http://portal.azure.com), e inicie sesión.
2. Seleccione **+ Nuevo** en el panel superior.
   
   ![+ Nuevo en Azure Portal](media/get-started/create-workspace-1.png)
3. En **Datos y análisis**, seleccione **Colección de áreas de trabajo de Power BI**.
4. En el mensaje de inicio, si ya tiene una suscripción existente a una colección de áreas de trabajo de Power BI, seleccione **Crear una colección de áreas de trabajo** en la parte inferior.

5. En **Colección de áreas de trabajo**, escriba la información necesaria.
   
   ![Creación de una colección de áreas de trabajo](media/get-started/create-workspace-2.png)
1. Seleccione **Crear**.

La **colección de áreas de trabajo** tarda un poco en aprovisionarse. Cuando finalice la operación, se le dirigirá a **Colección de áreas de trabajo**.

   ![Colección de áreas de trabajo en Azure Portal](media/get-started/create-workspace-3.png)

Los resultados de **creación** contienen la información que necesita para llamar a las API que crean las áreas de trabajo e implementan contenido en ellas.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Visualización de las claves de acceso de API de Power BI

Uno de los datos más importantes que se necesitan para llamar a las API de REST de Power BI son las **claves de acceso**. Las claves de acceso se utilizan para generar los **tokens de aplicación** que se emplean para autenticar las solicitudes de API. Para ver las **claves de acceso**, haga clic en **Claves de acceso** en **Configuración**. Para más información sobre los **tokens de aplicación**, consulte [Authenticating and authorizing with Power BI Workspace Collections](app-token-flow.md) (Autenticación y autorización con Colecciones de áreas de trabajo de Power BI).

   ![Claves de acceso en la configuración de colecciones de áreas de trabajo de Azure Portal](media/get-started/access-keys.png)

Verá que tiene dos claves.

   ![Dos claves dentro de Claves de acceso](media/get-started/access-keys-2.png)

Copie estas claves y almacénelas de forma segura en la aplicación. Es importante tratar estas claves igual que una contraseña, ya que proporcionan acceso a todo el contenido de la **colección de áreas de trabajo**.

Aunque se muestran dos claves, solo se necesita una cada vez. La segunda clave se proporciona para que pueda regenerar periódicamente las claves sin interrumpir el acceso al servicio.

Ahora que tiene una instancia de Power BI para la aplicación y las **claves de acceso**, puede importar un informe en la propia aplicación. Antes de aprender a importar un informe, en la sección siguiente se describe la creación de conjuntos de datos e informes de Power BI para insertar en una aplicación.

## <a name="working-with-workspaces"></a>Trabajo con áreas de trabajo

Después de haber creado la colección de áreas de trabajo, debe crear un área de trabajo que hospede los informes y conjuntos de datos. Para crear un área de trabajo, debe usar la [API de REST de Post Workspace](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Creación de conjuntos de datos e informes de Power BI para insertar en una aplicación mediante Power BI Desktop

Ahora que ha creado una instancia de Power BI para su aplicación y tiene las **claves de acceso**, debe crear los conjuntos de datos y los informes de Power BI que quiere insertar. Los conjuntos de datos y los informes se pueden crear con **Power BI Desktop**. Puede descargar [Power BI Desktop gratis](https://go.microsoft.com/fwlink/?LinkId=521662). Por otro lado, si desea empezar a trabajar rápidamente, también puede descargar el [archivo PBIX con un ejemplo de análisis de minoristas](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Para aprender más acerca de cómo usar **Power BI Desktop**, consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Con **Power BI Desktop**, para conectarse al origen de datos, puede importar una copia de los datos en **Power BI Desktop** o conectarse directamente al origen de datos mediante **DirectQuery**.

Estas son las diferencias entre usar **Importación** y **DirectQuery**.

| Importar | DirectQuery |
| --- | --- |
| Las tablas, las columnas y los *datos* se importan o se copian en **Power BI Desktop**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta una copia de los datos. Para ver los cambios que se han producido en los datos subyacentes, debe actualizar o importar de nuevo un conjunto de datos actual completo. |En *Power BI Desktop* , solo se importan o copian **tablas y columnas**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta el origen de datos subyacente, lo que significa que siempre verá los datos actuales. |

Para más información acerca de cómo conectarse a un origen de datos, consulte [Conectarse a un origen de datos](connect-datasource.md).

Cuando guarde el trabajo en **Power BI Desktop**, se creará un archivo PBIX. Este archivo contiene el informe. Además, si los datos se han importado, el archivo PBIX contendrá el conjunto de datos completo, mientras que, si se ha utilizado **DirectQuery**, el archivo PBIX solamente contendrá un esquema del conjunto de datos. Puede implementar el archivo PBIX mediante programación en su área de trabajo con la [API de importación de Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> Las **Colecciones de áreas de trabajo de Power BI** incluyen API adicionales que permiten cambiar el servidor y la base de datos a los que apunta el conjunto de datos y definir una credencial de cuenta de servicio, que será la que use el conjunto de datos para conectarse a la base de datos. Consulte [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) y [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (Revisión de origen de datos de puerta de enlace).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Creación de conjuntos de datos e informes de Power BI con las API

### <a name="datasets"></a>CONJUNTOS DE DATOS

Puede crear conjuntos de datos dentro de colecciones de áreas de trabajo de Power BI mediante la API de REST. Luego, puede insertar datos en el conjunto de datos. De esta forma puede trabajar con datos sin necesidad de Power BI Desktop. Para más información, consulte [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx) (Publicación de conjuntos de datos).

### <a name="reports"></a>Informes

Puede crear un informe a partir de un conjunto de datos directamente en la aplicación mediante la API de JavaScript. Para más información, consulte [Create a new report from a dataset in Power BI Workspace Collections](create-report-from-dataset.md) (Creación de un nuevo informe a partir de un conjunto de datos en Colecciones de áreas de trabajo de Power BI).

## <a name="see-also"></a>Otras referencias

[Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](get-started-sample.md)  
[Authenticating and authorizing in Power BI Workspace Collections](app-token-flow.md) (Autenticación y autorización con Colecciones de áreas de trabajo de Power BI)  
[Embed a report in Power BI Embedded](embed-report.md) (Inserción de un informe en Power BI Embedded)  
[Create a new report from a dataset in Power BI Workspace Collections](create-report-from-dataset.md) (Creación de un nuevo informe a partir de un conjunto de datos en Colecciones de áreas de trabajo de Power BI) 
[Guardar informes](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Ejemplo de inserción de JavaScript)  

¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)

