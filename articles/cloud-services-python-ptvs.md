<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Roles web y de trabajo de Python con Python Tools 2.1 para Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Informaci&oacute;n general sobre el uso de Python Tools para Visual Studio para crear servicios en la nube de Azure, incluidos roles web y roles de trabajo." metaCanonical="" services="" documentationCenter="Python" title="Roles web y de trabajo de Python con Python Tools 2.1 para Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />

# Roles web y de trabajo de Python con Python Tools 2.1 para Visual Studio

Esta guía proporciona información general sobre el uso de roles web y de trabajo de Python usando [Python Tools para Visual Studio][Python Tools para Visual Studio].

-   [Requisitos previos][Requisitos previos]
-   [¿Qué son los roles web y de trabajo de Python?][¿Qué son los roles web y de trabajo de Python?]
-   [Creación de un proyecto][Creación de un proyecto]
-   [Ejecución en modo local][Ejecución en modo local]
-   [Publicación en Azure][Publicación en Azure]
-   [Pasos siguientes][Pasos siguientes]

## <a name="prerequisites"></a>Requisitos previos

-   Visual Studio 2012 o 2013
-   [Python Tools 2,1 para Visual Studio (en inglés)][Python Tools 2,1 para Visual Studio (en inglés)]
-   [Herramientas del SDK de Azure para VS 2013][Herramientas del SDK de Azure para VS 2013] o [Herramientas del SDK de Azure para VS 2012][Herramientas del SDK de Azure para VS 2012]
-   [Python 2.7 de 32 bits][Python 2.7 de 32 bits] o [Python 3.4 de 32 bits][Python 3.4 de 32 bits]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>¿Qué son los roles web y de trabajo de Python?

Azure proporciona tres modelos informáticos para la ejecución de aplicaciones: [Sitios web Azure][Sitios web Azure], [Máquinas virtuales de Azure][Máquinas virtuales de Azure] y [Servicios en la nube de Azure][Servicios en la nube de Azure]. Los tres modelos admiten Python. Servicios en la nube, que incluye roles web y de trabajo, ofrece el modelo *plataforma como servicio (PaaS)*. En un servicio en la nube, un rol web ofrece un servidor web dedicado de Internet Information Services (IIS) para hospedar aplicaciones web front-end, mientras que un rol de trabajo puede ejecutar tareas asincrónicas, de ejecución prolongada o perpetuas independientes de la entrada o la interacción del usuario.

Para obtener más información, consulte [¿Qué es un servicio en la nube?][¿Qué es un servicio en la nube?].

<div class="dev-callout"><strong>&iquest;Desea compilar un sitio web sencillo?</strong>
<p>Si su escenario tan solo requiere un sitio web de front-end sencillo, considere el uso de un sitio web de Azure ligero. Puede actualizar a un Servicio en la nube m&aacute;s adelante, cuando su sitio web sea m&aacute;s grande y sus requisitos cambien. Consulte el <a href="/es-es/develop/python/">Centro para desarrolladores de Python</a> para obtener art&iacute;culos sobre el desarrollo de sitios web de Azure.</p>
</div>

## <a name="project-creation"></a>Creación de un proyecto

En Visual Studio, seleccione **Servicio en la nube de Azure** en el cuadro de diálogo **Nuevo proyecto**, bajo **Python**.

![Cuadro de diálogo de nuevo proyecto][Cuadro de diálogo de nuevo proyecto]

En el asistente Servicio en la nube de Azure, puede elegir crear roles web y de trabajo nuevos.

![Cuadro de diálogo Servicio en la nube de Azure][Cuadro de diálogo Servicio en la nube de Azure]

La plantilla de rol de trabajo incluye código reutilizable que conecta a una cuenta de almacenamiento o el Bus de servicio de Azure.

![Solución de servicio en la nube][Solución de servicio en la nube]

Puede agregar roles web o de trabajo a un servicio en la nube que ya existe en cualquier momento. Puede optar por agregar proyectos existentes a su solución o por crear otros nuevos.

![Comando Agregar rol][Comando Agregar rol]

Su servicio en la nube puede contener roles implementados en diferentes lenguajes. Por ejemplo, puede tener un rol web de Python implementado con Django, con roles de trabajo de Python y C#. Puede comunicarse fácilmente entre sus roles usando colas del Bus de servicio o colas de almacenamiento.

## <a name="run-locally"></a>Ejecución en modo local

Si establece su proyecto de servicio en la nube como proyecto de inicio y presiona F5, el servicio en la nube se ejecuta en el emulador de Azure local.

Aunque PTVS admite el inicio en el emulador, la depuración (puntos de interrupción, etc.) no funcionará.

Para depurar roles web y de trabajo, puede establecer el proyecto de rol como proyecto de inicio y depurarlo. También puede establecer varios proyectos de inicio. Haga clic con el botón secundario y seleccione **Establecer proyectos de inicio**.

![Propiedades del proyecto de inicio de la solución][Propiedades del proyecto de inicio de la solución]

## <a name="publish-to-azure"></a>Publicación en Azure

Para publicar, haga clic con el botón secundario en el proyecto del servicio en la nube y seleccione **Publicar**.

![Inicio de sesión para publicación de Microsoft Azure][Inicio de sesión para publicación de Microsoft Azure]

En la página de configuración, seleccione el servicio en la nube en el que desea publicar.

![Configuración de publicación de Microsoft Azure][Configuración de publicación de Microsoft Azure]

Puede crear un nuevo servicio en la nube si aún no tiene uno disponible.

![Cuadro de diálogo Crear servicio en la nube][Cuadro de diálogo Crear servicio en la nube]

Es muy útil también habilitar las conexiones con Escritorio remoto a máquinas para depurar errores.

![Cuadro de diálogo Configuración de Escritorio remoto][Cuadro de diálogo Configuración de Escritorio remoto]

Cuando haya terminado la configuración, haga clic en **Publicar**.

En la ventana de salida se verá cierto progreso y después se verá la ventana Registro de actividad de Microsoft Azure.

![Ventana Registro de actividad de Microsoft Azure][Ventana Registro de actividad de Microsoft Azure]

La implementación tardará varios minutos. Después, sus roles web y/o de trabajo estarán ejecutándose en Azure.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información detallada sobre el trabajo con roles web y de trabajo en Python Tools para Visual Studio, consulte la documentación de PTVS:

-   [Proyectos de servicio en la nube][Proyectos de servicio en la nube]

Para obtener más información sobre el uso de servicios de Azure desde roles web y de trabajo, como el uso de almacenamiento o el Bus de servicio de Azure, consulte las siguientes guías:

-   [Servicio BLOB][Servicio BLOB]
-   [Servicio Tabla][Servicio Tabla]
-   [Servicio Cola][Servicio Cola]
-   [Colas del Bus de servicio][Colas del Bus de servicio]
-   [Temas de Bus de servicio][Temas de Bus de servicio]

<!--Link references--> <!--External Link references-->

  [Python Tools para Visual Studio]: http://pytools.codeplex.com
  [Requisitos previos]: #prerequisites
  [¿Qué son los roles web y de trabajo de Python?]: #what-are-python-web-and-worker-roles
  [Creación de un proyecto]: #project-creation
  [Ejecución en modo local]: #run-locally
  [Publicación en Azure]: #publish-to-azure
  [Pasos siguientes]: #next-steps
  [Python Tools 2,1 para Visual Studio (en inglés)]: http://go.microsoft.com/fwlink/?LinkId=517189
  [Herramientas del SDK de Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
  [Herramientas del SDK de Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
  [Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
  [Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
  [Sitios web Azure]: /es-es/documentation/articles/fundamentals-application-models/#WebSites
  [Máquinas virtuales de Azure]: /es-es/documentation/articles/fundamentals-application-models/#VMachine
  [Servicios en la nube de Azure]: /es-es/documentation/articles/fundamentals-application-models/#CloudServices
  [¿Qué es un servicio en la nube?]: /es-es/manage/services/cloud-services/what-is-a-cloud-service/
  [Cuadro de diálogo de nuevo proyecto]: ./media/cloud-services-python-ptvs/new-project-cloud-service.png
  [Cuadro de diálogo Servicio en la nube de Azure]: ./media/cloud-services-python-ptvs/new-service-wizard.png
  [Solución de servicio en la nube]: ./media/cloud-services-python-ptvs/worker.png
  [Comando Agregar rol]: ./media/cloud-services-python-ptvs/add-new-or-existing-role.png
  [Propiedades del proyecto de inicio de la solución]: ./media/cloud-services-python-ptvs/startup.png
  [Inicio de sesión para publicación de Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-sign-in.png
  [Configuración de publicación de Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-settings.png
  [Cuadro de diálogo Crear servicio en la nube]: ./media/cloud-services-python-ptvs/publish-create-cloud-service.png
  [Cuadro de diálogo Configuración de Escritorio remoto]: ./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png
  [Ventana Registro de actividad de Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-activity-log.png
  [Proyectos de servicio en la nube]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
  [Servicio BLOB]: /es-es/documentation/articles/storage-python-how-to-use-blob-storage/
  [Servicio Tabla]: /es-es/documentation/articles/storage-python-how-to-use-table-storage/
  [Servicio Cola]: /es-es/documentation/articles/storage-python-how-to-use-queue-storage/
  [Colas del Bus de servicio]: /es-es/documentation/articles/service-bus-python-how-to-use-queues/
  [Temas de Bus de servicio]: /es-es/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/
