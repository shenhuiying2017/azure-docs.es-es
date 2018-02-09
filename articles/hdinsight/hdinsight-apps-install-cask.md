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
ms.openlocfilehash: 4b83f2a2228ef0dd7fa56b5a71b267d1e4302620
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Instalación de aplicación publicada: Cask Data Application Platform (CDAP)

En este artículo se describe cómo instalar y ejecutar la aplicación de Hadoop publicada [CDAP](http://cask.co/products/cdap/) en Azure HDInsight. Para información general de la plataforma de aplicaciones HDInsight y una lista de las aplicaciones publicadas de fabricantes de software independiente (ISV) disponibles, consulte [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md). Para obtener instrucciones sobre cómo instalar su propia aplicación, consulte [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

## <a name="about-cdap"></a>Información sobre CDAP

Desarrollar aplicaciones en Hadoop puede ser complicado.  Hay un importante y creciente número de extensiones de la tecnología Hadoop, que puede tardar un tiempo en integrarse. Puede ser necesario compilar una solución independiente para supervisar el flujo de datos y recopilar las métricas.

### <a name="how-does-cdap-help"></a>¿Cómo ayuda CDAP?

Cask Data Application Platform (CDAP) es una plataforma de integración para los macrodatos. CDAP permite centrarse en la compilación de las aplicaciones en lugar de hacerlo en la infraestructura subyacente.

CDAP usa abstracciones y conceptos de alto nivel que resultan familiares para los desarrolladores. Estas abstracciones ocultan las complejidades de los sistemas internos y fomentan la reusabilidad de las soluciones.

Una extensión de CDAP denominada [Cask Hydrator](http://cask.co/products/hydrator/) proporciona una interfaz de usuario para desarrollar y administrar las canalizaciones de datos. Una canalización de datos consta de varios *complementos que llevan a cabo tareas como la adquisición de datos, la transformación, el análisis y operaciones posteriores a la ejecución.

Cada complemento de CDAP tiene una interfaz bien definida, por lo que evaluar tecnologías distintas solo implica reemplazar un complemento por otro, sin tener que tocar el resto de la aplicación.

Las *canalizaciones* de CDAP proporcionan un flujo gráfico de alto nivel de los datos en la aplicación. Esta visualización muestra el flujo completo de los datos, desde la ingesta, pasando por las transformaciones y los análisis de los datos y, finalmente, a un almacén de datos externos.

El ejemplo siguiente de una canalización de datos ingiere datos de Twitter en tiempo real y luego filtra algunos tweets basados en criterios definidos previamente. La canalización de datos transforma los datos de tweets sin procesar y proyecta esos datos en un formato más legible. Luego, agrupa los tweets según un conjunto de valores y escribe los resultados en un almacén de HBase.

![Canalización de CDAP](./media/hdinsight-apps-install-cask/pipeline.png)

Esta canalización completa se compila con la **interfaz de usuario de Cask Hydrator**, mediante el uso de la interfaz del complemento y la funcionalidad de arrastrar y colocar para formar conexiones entre cada fase. Puede aislar y modificar la funcionalidad de cada complemento de manera independiente. Con CDAP, las canalizaciones similares se pueden compilar y validar en cuestión de horas. En el mundo de Hadoop típico, construir ese tipo de soluciones puede tardar varios días.

CDAP también proporciona una extensión denominada [Cask Tracker](http://cask.co/products/tracker/) para hacer un seguimiento visual de los datos a medida que fluyen por la aplicación. Cask Tracker agrega el *gobierno de datos* al sistema, por lo que los recursos de datos se administran de manera formal en toda la aplicación. Puede hacer seguimiento del linaje de cada punto de datos, recopilar las métricas pertinentes y auditar la traza de datos durante todo el proceso.

A continuación, se ilustra cómo fluyen los datos en la canalización anterior:

![Seguimiento de CDAP](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>requisitos previos

Para instalar esta aplicación en un clúster de HDInsight nuevo o en un clúster existente, debe tener la configuración siguiente:

* Nivel de clúster: estándar
* Tipo de clúster: HBase
* Versión del clúster: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Instalación de la aplicación publicada CDAP

Para instrucciones paso a paso sobre cómo instalar esta aplicación y otras aplicaciones de ISV disponibles, lea [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Inicio de CDAP

1. Después de la instalación, inicie CDAP desde su clúster en Azure Portal en el panel **Configuración** y, luego, seleccione **Aplicaciones** en la categoría **General**. En el panel Aplicaciones instaladas, aparecen todas las aplicaciones instaladas.

    ![Aplicación CDAP instalada](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Cuando seleccione CDAP, verá un vínculo a la página web y al punto de conexión HTTP, además de la ruta de acceso del punto de conexión SSH. Seleccione el vínculo WEBPAGE.

3. Cuando se le pida, escriba las credenciales de administrador para el clúster.

    ![Autenticación](./media/hdinsight-apps-install-cask/auth.png)

4. Después de iniciar sesión, verá la página principal de la GUI de Cask CDAP.

    ![Página principal de la GUI de Cask](./media/hdinsight-apps-install-cask/gui.png)

5. Para explorar la interfaz de CDAP, haga clic en el vínculo del menú **Cask Market** (Mercado de Cask) que aparece en la parte superior de la página.

    ![Vínculo de Cask Market](./media/hdinsight-apps-install-cask/cask-market.png)

6. Seleccione **Access Log Sample** (Ejemplo de registro de acceso) en la lista.

    ![Ejemplo de registro de acceso](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Haga clic en **Load** (Cargar) para confirmar.

    ![Clic en Load](./media/hdinsight-apps-install-cask/market-load.png)

8. Aparece una vista de los datos de ejemplo que se incluyen. Seleccione **Next** (Siguiente).

    ![Ejemplo de registro de acceso: vista de datos](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Seleccione **Stream** (Flujo) como el tipo de destino, escriba un nombre de destino y, luego, seleccione **Finish** (Finalizar).

    ![Ejemplo de registro de acceso: selección del destino](./media/hdinsight-apps-install-cask/market-destination.png)

10. Una vez que el paquete de datos se carga correctamente, seleccione **View Stream Details** (Ver detalles del flujo).

    ![El paquete de datos se cargó correctamente](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Para habilitar los metadatos para el espacio de nombres, seleccione **Enable** (Habilitar) en la pestaña Usage (Uso) en la página de detalles del registro de acceso.

    ![Ejemplo de registro de acceso: cargado. Habilitación de los metadatos](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Una vez que se habilitan los metadatos, verá un gráfico que muestra la información de los mensajes de auditoría.

    ![Ejemplo de registro de acceso: metadatos habilitados](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Para explorar los datos del registro, seleccione el icono **Explore** (Explorar) en la parte superior de la página.

    ![Ejemplo de registro de acceso: exploración](./media/hdinsight-apps-install-cask/log-explore.png)

14. Verá una consulta SQL de ejemplo. Haga las modificaciones que considere necesarias y seleccione **Execute** (Ejecutar).

    ![Ejemplo de registro de acceso: exploración del conjunto de datos con una consulta](./media/hdinsight-apps-install-cask/log-query.png)

15. Una vez finalizada la consulta, seleccione el icono **View** (Ver) en la columna de acciones.

    ![Ejemplo de registro de acceso: vista de la consulta completada](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Verá los resultados de la consulta.

    ![Ejemplo del registro de acceso: resultados de la consulta](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>pasos siguientes

* [Documentación de Cask](http://cask.co/resources/documentation/).
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
* [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a usar acciones de script para instalar otras aplicaciones.
* [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md): aprenda a usar un nodo perimetral vacío para acceder a los clústeres de HDInsight, probar aplicaciones de este y hospedarlas.
