---
title: "Instalación del kit de herramientas de Azure para Eclipse | Microsoft Docs"
description: "Descubra cómo instalar el kit de herramientas de Azure para Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 78b298e69a8134a4d784fb84b19688871a944cbc


---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalación del Kit de herramientas de Azure para Eclipse
El kit de herramientas de Azure para Eclipse ofrece plantillas y funciones que permiten crear, desarrollar, probar e implementar aplicaciones de Azure fácilmente con el entorno de desarrollo de Eclipse. El kit de herramientas de Azure para Eclipse es un proyecto de código abierto, cuyo código fuente está disponible con la licencia MIT del sitio del proyecto en GitHub en la siguiente dirección URL:

<https://github.com/microsoft/azure-tools-for-java>

En los pasos siguientes se muestra cómo instalar el Kit de herramientas de Azure para Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Para instalar el Kit de herramientas de Azure para Eclipse
1. Inicie Eclipse.
2. Cuando Eclipse se abra, haga clic en el menú **Help** (Ayuda) y, a continuación, haga clic en **Install New Software** (Instalar nuevo software), como se muestra en la siguiente ilustración.
   
    ![Instalación del Kit de herramientas de Azure para Eclipse][01]
3. En el cuadro de diálogo **Available Software** (Software disponible), en el cuadro de texto **Work with** (Trabajar con), escriba **http://dl.microsoft.com/eclipse** seguido de la tecla **Intro**.
4. En el panel **Nombre**, active **Kit de herramientas de Azure para Eclipse**, y desactive **Ponerse en contacto con todos los sitios de actualización durante la instalación para encontrar el software necesario**. La pantalla debe parecerse a la siguiente:
   
    ![Instalación del Kit de herramientas de Azure para Eclipse][02]
5. Si expande el **kit de herramientas de Azure para Eclipse**, verá los siguientes elementos:
   
   * **Complemento de Application Insights para Java**: este componente permite usar los servicios de análisis y registro de telemetría de Azure para sus aplicaciones e instancias de servidor.
   * **Filtro de servicios de control de acceso de Azure**: este componente proporciona compatibilidad para autenticar a los usuarios de aplicaciones con ACS de Azure, lo que permite poner en práctica escenarios de inicio de sesión único y externalizar la lógica de identidades de la aplicación.
   * **Complemento común de Azure**: este componente proporciona la funcionalidad habitual necesaria para otros componentes del kit de herramientas.
   * **Explorador de Azure para Eclipse**: este componente proporciona la funcionalidad habitual necesaria para otros componentes del kit de herramientas.
   * **Complemento de Azure para Eclipse con Java**: este componente respalda el desarrollo de proyectos que lo ayudan a crear, probar e implementar aplicaciones de Java para la nube de Microsoft Azure en Eclipse y mediante la línea de comandos.
   * **Complemento de las aplicaciones web de Azure con Java**: este componente permite implementar aplicaciones web de Java en los contenedores de aplicaciones web de Microsoft Azure.
   * **Microsoft JDBC Driver 4.2 para SQL Server**: este componente proporciona la API de JDBC API para SQL Server y Base de datos SQL de Microsoft Azure para Java Platform Enterprise Edition 8.
   * **Paquete para bibliotecas de cliente Apache Qpid de JMS**: este componente ofrece el componente de cliente JMS del proyecto de Apache Qpid para habilitar su aplicación para que use la mensajería de AMQP en Microsoft Azure.
   * **Paquete para bibliotecas de Microsoft Azure para Java**: este componente proporciona las API para acceder a los servicios de Microsoft Azure, como almacenamiento, bus de servicio, runtime de servicio, etc.
6. Haga clic en **Siguiente**. (Si se producen retrasos inusuales al instalar el kit de herramientas, asegúrese de que la opción **Ponerse en contacto con todos los sitios de actualización durante la instalación para encontrar el software necesario** está desactivada.)
7. En el cuadro de diálogo **Install Details** (Detalles de instalación), haga clic en **Next** (Siguiente).
   
    ![Revisión de los detalles de la instalación][03]
8. En el cuadro de diálogo **Revisar licencias** , revise los términos de los contratos de licencia. Si acepta los términos de los contratos de licencia, haga clic en **I accept the terms of the license agreements** (Acepto los términos de los contratos de licencia) y luego haga clic en **Finish** (Finalizar). (En los pasos restantes se supone que acepta los términos de los contratos de licencia. Si no acepta los términos de los contratos de licencia, salga del proceso de instalación.)
   
    ![Revisar licencias][04]
   
    Eclipse descargará e instalará los paquetes necesarios.
   
    ![Progreso de la instalación][05]
9. Si se le solicita que reinicie Eclipse para completar la instalación, haga clic en **Sí**.
   
    ![Reinicio del símbolo del sistema][06]

## <a name="see-also"></a>Otras referencias
Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

* [Kit de herramientas de Azure para Eclipse]
  * *Instalación del kit de herramientas de Azure para Eclipse (este artículo)*
  * [Creación de una aplicación web Hello World para Azure en Eclipse]
  * [Novedades del kit de herramientas de Azure para Eclipse]
* [Kit de herramientas de Azure para IntelliJ]
  * [Instalación del kit de herramientas de Azure para IntelliJ]
  * [Creación de una aplicación web Hello World para Azure en IntelliJ]
  * [Novedades del kit de herramientas de Azure para IntelliJ]

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

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->



<!--HONumber=Nov16_HO3-->


