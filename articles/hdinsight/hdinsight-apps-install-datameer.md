---
title: "Instalación de aplicación publicada: Cask Data Application Platform (CDAP) - Azure HDInsight | Microsoft Docs"
description: "Instale y use la aplicación de Hadoop de terceros Cask Data Application Platform."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 8a898b4f82cf2d7e05e8c3895e5eddd8cf02b173
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---datameer"></a>Instalación de aplicación publicada: Datameer

En este artículo se describe cómo instalar y ejecutar la aplicación de Hadoop publicada [Datameer](https://www.datameer.com/) en Azure HDInsight. Para información general de la plataforma de aplicaciones HDInsight y una lista de las aplicaciones publicadas de fabricantes de software independiente (ISV) disponibles, consulte [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md). Para obtener instrucciones sobre cómo instalar su propia aplicación, consulte [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>Información sobre Datameer

Datameer es una aplicación nativa para la plataforma de Hadoop y que amplía las funcionalidades existentes de Azure HDInsight y proporciona una integración rápida, la preparación y el análisis de datos estructurados y no estructurados. Datameer puede acceder a más de 70 orígenes y formatos: estructurados, semiestructurados y no estructurados. Puede cargar los datos de manera directa o usar los vínculos de datos únicos para extraer datos a petición. La interfaz de hoja de cálculo familiar y la funcionalidad de autoservicio de Datameer reduce la complejidad de la tecnología de los macrodatos y permite obtener una perspectiva general más rápida. La interfaz de hoja de cálculo proporciona un mecanismo simple para ingresar fórmulas declarativas que luego se traducen en trabajos optimizados de Hadoop. Con Datameer y sus conocimientos de Excel e inteligencia empresarial (BI), puede usar Hadoop en la nube rápidamente. Para más información, consulte la [documentación de Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>requisitos previos

Para instalar esta aplicación en un clúster de HDInsight nuevo o en un clúster existente, debe tener la configuración siguiente:

* Nivel de clúster: estándar
* Tipo de clúster: Hadoop
* Versión del clúster: 3.4

## <a name="install-the-datameer-published-application"></a>Instalación de la aplicación publicada Datameer

Para instrucciones paso a paso sobre cómo instalar esta aplicación y otras aplicaciones de ISV disponibles, lea [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Inicio de Datameer

1. Después de la instalación, puede iniciar Datameer desde su clúster en Azure Portal en el panel **Configuración** y, luego, haga clic en **Aplicaciones** en la categoría **General**. En el panel Aplicaciones instaladas aparecen las aplicaciones instaladas.

    ![Aplicación Datameer instalada](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Cuando seleccione Datameer, verá un vínculo a la página web y la ruta de acceso del punto de conexión SSH. Seleccione el vínculo WEBPAGE.

3. La primera vez, habrá dos opciones de licencia: una evaluación gratuita durante 14 días o la activación de una licencia existente.

    ![Opciones de licencia](./media/hdinsight-apps-install-datameer/license.png)

4. Después de completar la opción de licencia que seleccionó, verá un formulario de inicio de sesión. En el inicio de sesión, escriba las credenciales predeterminadas que ya se mostraron. Después de iniciar sesión, acepte el contrato de software para continuar.

    ![Inicio de sesión](./media/hdinsight-apps-install-datameer/login.png)

En los pasos siguientes aparece una demostración de "Hola mundo".

1. [Descargue el CSV de ejemplo](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Haga clic en el signo **+** que se encuentra en la parte superior del panel de Datameer y haga clic en **Carga de archivos**.

    ![Carga de archivos](./media/hdinsight-apps-install-datameer/upload.png)

3. En el cuadro de diálogo de carga, examine y seleccione el archivo **Hello World.csv** que descargó. Asegúrese de que el **tipo de archivo** está establecido en CSV / TSV. Seleccione **Siguiente**. Haga clic en **Siguiente** hasta el final del asistente.

    ![Carga de archivos](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Asígnele al archivo el nombre **Hola mundo** bajo una carpeta nueva. Cambie el nombre de la carpeta nueva a "Demo". Seleccione **Guardar**.

    ![Save](./media/hdinsight-apps-install-datameer/save.png)

5. Vuelva a hacer clic en el signo **+** y seleccione **Workbook** (Libro) para crear un libro para los datos.

    ![Agregar libro](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Expanda la carpeta **Data** (Datos), **FileUploads** y la carpeta **Demo** que creó al guardar el archivo "Hola mundo". Seleccione **Hola mundo** en la lista de archivos y haga clic en **Add Data** (Agregar datos).

    ![Save](./media/hdinsight-apps-install-datameer/select-file.png)

7. Verá los datos cargados en una interfaz de hoja de cálculo. Para seleccionar un subconjunto de los datos, seleccione el botón **Filter** (Filtrar) en la barra de herramientas.

    ![Botón de filtro](./media/hdinsight-apps-install-datameer/filter-button.png)

8. En el cuadro de diálogo Apply Filter (Aplicar filtro), seleccione la columna **City** (Ciudad), el operador **equals** y escriba **Chicago** en el cuadro de texto del filtro. Active la casilla de verificación **Create filter in new sheet** (Crear filtro en una hoja nueva) y seleccione **Create Filter** (Crear filtro).

    ![Aplicar filtro](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Guarde el libro con un clic en **File** (Archivo) y, luego, en **Save** (Guardar). Asígnele un nombre, como "Libro Hola mundo".

10. Verá varias opciones sobre cómo y cuándo ejecutar el libro. Por ahora, deje los valores predeterminados de todas las opciones, active la opción **Start calculation process immediately after save** (Iniciar el proceso de cálculo inmediatamente después de guardar) y seleccione **Save** (Guardar).

    ![Guardar libro](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer ofrece herramientas de visualización eficaces. Cree una infografía para mostrar los datos. Seleccione el signo **+** en la parte superior del panel y luego seleccione **Infographic** (Infografía).

    ![Agregar infografía](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Arrastre un widget de gráfico de barras de la lista de widgets que está a la izquierda, como se muestra en el paso 1 del diagrama siguiente. A continuación, navegue por la carpeta de datos en el explorador de datos que aparece a la derecha, expanda el libro y la hoja de cálculo que agregó con el filtro (paso 2). Arrastre la columna **Name** (Nombre) sobre la parte superior del gráfico de barras y colóquela en el destino **Label** (Etiqueta) para establecer la columna de nombre del libro como el campo de etiqueta del gráfico (paso 3).

    ![Infografía](./media/hdinsight-apps-install-datameer/infographic.png)

13. Para establecer la edad como el eje Y del gráfico, arrastre la columna **Age** (Edad) al campo **Data** (Datos) del gráfico.

    ![Infografía](./media/hdinsight-apps-install-datameer/infographic-age.png)

Felicidades. Ha creado una visualización de los datos sin escribir nada de código. Ahora puede explorar las variaciones y visualizaciones adicionales.

## <a name="next-steps"></a>pasos siguientes

* [Documentación de Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
* [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a usar acciones de script para instalar otras aplicaciones.
* [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md): aprenda a usar un nodo perimetral vacío para acceder a los clústeres de HDInsight, probar aplicaciones de este y hospedarlas.
