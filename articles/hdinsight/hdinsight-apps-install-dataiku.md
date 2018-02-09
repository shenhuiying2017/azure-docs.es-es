---
title: "Instalación de aplicación publicada: Dataiku DDS - Azure HDInsight | Microsoft Docs"
description: "Instale y use la aplicación de Hadoop de terceros Dataiku DDS."
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
ms.openlocfilehash: 835f433e0bf79e8bc4d9b30963196f65bc53cd0e
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---dataiku-dds"></a>Instalación de aplicación publicada: Dataiku DDS

En este artículo se describe cómo instalar y ejecutar la aplicación de Hadoop publicada [Dataiku DDS](https://www.dataiku.com/) en Azure HDInsight. Para información general de la plataforma de aplicaciones HDInsight y una lista de las aplicaciones publicadas de fabricantes de software independiente (ISV) disponibles, consulte [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md). Para obtener instrucciones sobre cómo instalar su propia aplicación, consulte [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

## <a name="about-dataiku-dss"></a>Información sobre Dataiku DSS

Dataiku [Data Science Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/) es una plataforma de ciencia de datos que permite a los científicos de datos compilar y entregar soluciones de análisis. Ofrecer DSS como una aplicación HDInsight permite usar la ciencia de datos para compilar soluciones de macrodatos y ejecutarlas a escala y a nivel empresarial.

Puede usar DSS para implementar una solución de análisis completa, comenzando con la ingesta de datos, la preparación y el procesamiento. Una solución DSS también puede incluir el entrenamiento y la aplicación de modelos de aprendizaje automático, la visualización y, luego, el funcionamiento.

Puede instalar DSS en HDInsight mediante clústeres de Hadoop o de Spark. Puede instalar DSS en clústeres en ejecución existentes o al crear clústeres nuevos. DSS también admite usar Azure Blob Storage como un conector para leer los datos.

Puede usar DSS para compilar proyectos los que, luego, pueden generar trabajos de MapReduce o de Spark. Estos trabajos se ejecutan como trabajos de MapReduce o de Spark normales en HDInsight, por lo que puede escalar el clúster a petición.

## <a name="prerequisites"></a>requisitos previos

Para instalar esta aplicación en un clúster de HDInsight nuevo o en un clúster existente, debe tener la configuración siguiente:

* Niveles de clúster : estándar, Premium
* Tipos de clúster: Hadoop, Spark
* Versiones de clúster: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Instalación de la aplicación publicada Dataiku DSS

Para instrucciones paso a paso sobre cómo instalar esta aplicación y otras aplicaciones de ISV disponibles, lea [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Inicio de Dataiku DSS

1. Después de la instalación, puede iniciar DSS desde su clúster en Azure Portal en el panel **Configuración** y, luego, haga clic en **Aplicaciones** en la categoría **General**. En el panel Aplicaciones instaladas aparecen las aplicaciones instaladas.

    ![Aplicación Dataiku DSS instalada](./media/hdinsight-apps-install-dataiku/app.png)

2. Cuando seleccione DSS en HDInsight, verá un vínculo a la página web y la ruta de acceso del punto de conexión SSH. Seleccione el vínculo WEBPAGE.

3. La primera vez, verá un formulario para crear una nueva cuenta de Dataiku gratis o para iniciar sesión en una cuenta existente. También tiene la opción para iniciar una evaluación gratuita de dos semanas de [Enterprise Edition](https://www.dataiku.com/dss/editions/). Desde este punto, tiene la opción de continuar e ingresar una clave de licencia para Enterprise Edition o de usar la edición Community Edition.

4. Después de completar la opción de licencia que seleccionó, verá un formulario de inicio de sesión. En el inicio de sesión, escriba las credenciales predeterminadas que ya se mostraron.

En los pasos siguientes se proporciona una demostración simple.

1. [Descargue el CSV de pedidos de ejemplo](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. En el panel de DSS, seleccione el vínculo **+** (Nuevo proyecto) que aparece en el menú de la izquierda para crear un proyecto nuevo.

    ![Vínculo de proyecto nuevo](./media/hdinsight-apps-install-dataiku/new-project.png)

3. En el formulario de proyecto nuevo, escriba un **nombre**. La **clave de proyecto** se rellena automáticamente con un valor sugerido. En este caso, escriba "Pedidos". Haga clic en **CREATE** (Crear).

    ![Formulario de proyecto nuevo](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Seleccione **+ IMPORT YOUR FIRST DATASET** (Importar el primer conjunto de datos) en la página del proyecto nuevo.

    ![Carga de archivos](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Seleccione **Upload your files** (Cargar archivos) en el conjunto de datos **Files** (Archivos). Aparecerá el cuadro de diálogo Upload (Cargar). Haga clic en Add a file (Agregar un archivo), seleccione el archivo `haiku_shirt_sales.csv` que descargó y realice la validación.

6. El archivo se carga en DSS. Compruebe si DSS detectó correctamente el formato CSV con un clic en el botón Vista previa.

    ![Carga de archivos](./media/hdinsight-apps-install-dataiku/preview.png)

7. La importación es casi perfecta. El archivo CSV usa un separador de tabulación. Puede ver que los datos están en formato tabular, con columnas denominadas características y líneas que representan observaciones. El error es que, al parecer, el archivo contenía una línea en blanco entre el encabezado y los datos. Para corregir este error, escriba `1` en el campo **Skip next lines** (Omitir las líneas siguientes).

    ![Save](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Asigne un nombre al conjunto de datos nuevo. Escriba **haiku_shirt_sales** en el campo que se encuentra en la parte superior de la pantalla y, luego, seleccione el botón **Crear**.

9. Verá una vista tabular de los datos donde puede comenzar a explorarlos. Para cada columna, debe ver que Dataiku Science Studio detectó un tipo de datos, en _azul_. En este caso, texto, número o fecha (sin analizar). Un medidor indica la relación de la columna para la cual los valores no parecen coincidir con el tipo (en rojo) o no se encuentran (en blanco). En este conjunto de datos de ejemplo, el departamento tiene tanto valores vacíos como datos no válidos.

    ![Vista tabular](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Manipulación de datos

Los datos reales casi siempre son desordenados y es poco frecuente que estén empaquetados de manera organizada. Eliminar los datos habitualmente requiere una cadena de scripts y una lógica de negocios asociada. Dataiku DSS proporciona una herramienta de **laboratorio** dedicada para que esta tarea sea más fácil de realizar.

1. Haga clic en **Lab** (Laboratorio) en la esquina superior derecha.

    ![Botón Lab](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. Se abre la ventana Lab (Laboratorio). El laboratorio es el lugar donde se trabaja de manera iterativa en el conjunto de datos para aprovecharlo mejor. En este tutorial se muestra el aspecto del análisis visual. Seleccione el botón **Nuevo** bajo Análisis visual. Se le pedirá especificar un nombre para el análisis. Por ahora, deje el nombre predeterminado y, luego, haga clic en **CREATE** (Crear).

    ![Creación del laboratorio](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Seleccione el botón **Quick columns stats** (Estadísticas rápidas de columnas) en la esquina superior derecha de la página.

    ![Estadísticas rápidas de columnas](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Verá estadísticas de los valores y tipos de datos que aparecen en gráficos basados en escala de tiempo en el panel **Columns quick view** (Vista rápida de columnas).

    ![Vista rápida de columnas](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Ahora puede explorar DSS con los datos de ejemplo. Puede limpiar los datos y trabajar con ellos, y crear visualizaciones nuevas.

Para tutoriales detallados, lea [Learn Dataiku DSS](https://www.dataiku.com/learn/) (Información sobre Dataiku DSS).

## <a name="next-steps"></a>pasos siguientes

* [Documentación sobre Dataiku DSS](https://doc.dataiku.com/dss/latest/).
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
* [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a usar acciones de script para instalar otras aplicaciones.
* [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md): aprenda a usar un nodo perimetral vacío para acceder a los clústeres de HDInsight, probar aplicaciones de este y hospedarlas.
