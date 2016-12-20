---
title: Novedades del kit de herramientas de Azure para IntelliJ | Microsoft Docs
description: "Obtenga información acerca las características más recientes del kit de herramientas de Azure para IntelliJ."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 46ed791f-df59-416a-809e-f52345ad973c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm;asirveda;martinsawicki
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3ed0ef3d245ff8b5cddc2e26bc8574bf4ae67bec


---
# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Novedades del kit de herramientas de Azure para IntelliJ
## <a name="azure-toolkit-for-intellij-releases"></a>Kit de herramientas de Azure para versiones de IntelliJ
Este artículo contiene información sobre las diversas versiones y actualizaciones más recientes para el kit de herramientas de Azure para IntelliJ.

> [!NOTE]
> También hay un kit de herramientas de Azure para el IDE de Eclipse. Para obtener más información, consulte [Kit de herramientas de Azure para Eclipse].
> 
> 

### <a name="august-26-2016"></a>26 de agosto de 2016
En la versión de agosto de 2016, el Kit de herramientas de Azure para IntelliJ incluye las siguientes mejoras:

* **Distribuciones de JDK personalizadas**. El Kit de herramientas de Azure para IntelliJ ahora admite especificar e implementar una versión de JDK arbitraria en el contenedor de Azure WebApp:
  * Además de los JDK que proporciona Azure, puede elegir entre una gran variedad de versiones de Zulu OpenJDK que pone a disposición Azul Systems en Azure.
  * También puede especificar su propia distribución de JDK si la carga como un archivo ZIP en su cuenta de almacenamiento.
* **Mejoras en la vista del Explorador de Azure**:
  * Compatibilidad con la administración de máquinas virtuales mediante el nuevo modelo de Azure Resource Manager: puede crear y eliminar máquinas virtuales basadas en Resource Manager sin salir del IDE, así como incluirlas en listas.
  * Compatibilidad con la administración de blobs de cuentas de almacenamiento mediante Azure Resource Manager, lo que complementa la funcionalidad existente para administrar las cuentas de almacenamiento clásicas.
* **Microsoft JDBC Driver 6.0 para SQL Server**. Esta actualización incluye la versión más reciente de Microsoft JDBC Driver para Microsoft SQL Server (6.0), que ahora se incluye como una biblioteca que se puede agregar fácilmente a los proyectos de Java, con lo que sustituye a la versión anterior.

### <a name="june-29-2016"></a>29 de junio de 2016
El kit de herramientas de Azure para IntelliJ, versión de junio de 2016, incluye las siguientes mejoras:

* **Requisito de Java 8**. El Kit de herramientas de Azure para IntelliJ ahora requiere Java 8, aunque este requisito es solo para el kit de herramientas: las aplicaciones pueden seguir usando todas las versiones de Java compatibles con Azure.
* **Compatibilidad para el JDK más reciente de Java**. Las versiones más recientes de los JDK de Java ahora admiten el Kit de herramientas de Azure para IntelliJ.
* **Compatibilidad con la versión 2.9.1 del SDK de Azure**. La versión más reciente del SDK de Azure ahora es el requisito previo mínimo para el kit de herramientas de Azure para IntelliJ.
* **Ejemplos integrados**. El Kit de herramientas de Azure para IntelliJ ahora cuenta con varias aplicaciones de ejemplo para ayudar a los desarrolladores a empezar a trabajar.
* **Integración de herramientas de HDInsight**. Las herramientas de HDInsight de Azure ahora están incluidas en el Kit de herramientas de Azure para IntelliJ. Para más información, consulte [Complemento Herramientas de HDInsight para IntelliJ].
* **Depuración remota de aplicaciones web de Java**. El Kit de herramientas de Azure para IntelliJ ahora admite la depuración remota de aplicaciones web de Java en el Servicio de aplicaciones de Azure.

### <a name="april-12-2016"></a>12 de abril de 2016
El kit de herramientas de Azure para IntelliJ, versión de abril de 2016, incluye las siguientes mejoras:

* **Compatibilidad con la versión 2.9.0 del SDK de Azure**. La versión más reciente del SDK de Azure ahora es el requisito previo mínimo para el kit de herramientas de Azure para IntelliJ.
* **Mejoras variadas en la facilidad de uso, la capacidad de respuesta y el rendimiento relacionadas con la compatibilidad de aplicaciones web de Azure**. Diversas optimizaciones de rendimiento con respecto a la manera en que el kit de herramientas se comunica con Azure generan una IU con mayor capacidad de respuesta.
* **Capacidad de eliminar un contenedor de aplicaciones web en Azure desde IntelliJ**. El kit de herramientas de Azure para IntelliJ ahora le permite eliminar un contenedor web de Azure existente sin salir de IntelliJ.

## <a name="see-also"></a>Otras referencias
Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

* [Kit de herramientas de Azure para Eclipse]
  * [Instalación del Kit de herramientas de Azure para Eclipse]
  * [Creación de una aplicación web Hello World para Azure en Eclipse]
  * [Novedades del kit de herramientas de Azure para Eclipse]
* [Kit de herramientas de Azure para IntelliJ]
  * [Instalación del kit de herramientas de Azure para IntelliJ]
  * [Creación de una aplicación web Hello World para Azure en IntelliJ]
  * *Novedades del kit de herramientas de Azure para IntelliJ (este artículo)*

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Creación de una aplicación web Hello World para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creación de una aplicación web Hello World para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalación del Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalación del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novedades del kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Complemento Herramientas de HDInsight para IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md



<!--HONumber=Nov16_HO3-->


