<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Roles web y de trabajo de Python con Python Tools 2.1 para Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Información general sobre el uso de Python Tools para Visual Studio para crear servicios en la nube de Azure, incluidos roles web y roles de trabajo." metaCanonical="" services="" documentationCenter="Python" title="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Roles web y de trabajo de Python con Python Tools 2.1 para Visual Studio

Esta guía proporciona información general sobre el uso de roles web y de trabajo de Python usando [Python Tools para Visual Studio][].

+ [Requisitos previos](#prerequisites)
+ [¿Qué son los roles web y de trabajo de Python?](#what-are-python-web-and-worker-roles)
+ [Creación de un proyecto](#project-creation)
+ [Ejecución en modo local](#run-locally)
+ [Publicación en Azure](#publish-to-azure)
+ [Pasos siguientes](#next-steps)

##<a name="prerequisites"></a>Requisitos previos

 - Visual Studio 2012 o 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Herramientas del SDK de Azure para VS 2013][] o [Herramientas del SDK de Azure para VS 2012][]
 - [Python 2.7 de 32 bits][] o [Python 3.4 de 32 bits][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="what-are-python-web-and-worker-roles"></a>¿Qué son los roles web y de trabajo de Python?

Azure proporciona tres modelos informáticos para la ejecución de aplicaciones: [Sitios web Azure][execution model-web sites], [Máquinas virtuales de Azure][execution model-vms] y [Servicios en la nube de Azure][execution model-cloud services]. Los tres modelos admiten Python. Servicios en la nube, que incluye roles web y de trabajo, ofrece el modelo *Plataforma como servicio (PaaS)*. En un servicio en la nube, un rol web ofrece un servidor web dedicado de Internet Information Services (IIS) para hospedar aplicaciones web front-end, mientras que un rol de trabajo puede ejecutar tareas asincrónicas, de ejecución prolongada o perpetuas independientes de la entrada o la interacción del usuario.

Para obtener más información, consulte [¿Qué es un servicio en la nube?].

<div class="dev-callout"><strong>¿Desea compilar un sitio web sencillo?</strong>
<p>Si su escenario tan solo requiere un sitio web de front-end sencillo, considere el uso de un sitio web de Azure ligero. Puede actualizar a un Servicio en la nube más adelante, cuando su sitio web sea más grande y sus requisitos cambien. Consulte en el <a href="/en-us/develop/python/">Centro para desarrolladores de Python</a> artículos que describen el desarrollo de sitios web de Azure.</p>
</div>
<br />


##<a name="project-creation"></a>Creación de un proyecto

En Visual Studio, seleccione **Servicio en la nube de Azure** en el cuadro de diálogo **Nuevo proyecto**, bajo **Python**. 

![New Project Dialog](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

En el asistente Servicio en la nube de Azure, puede elegir crear roles web y de trabajo nuevos.

![Azure Cloud Service Dialog](./media/cloud-services-python-ptvs/new-service-wizard.png)

La plantilla de rol de trabajo incluye código reutilizable que conecta a una cuenta de almacenamiento o el Bus de servicio de Azure.

![Cloud Service Solution](./media/cloud-services-python-ptvs/worker.png)

Puede agregar roles web o de trabajo a un servicio en la nube que ya existe en cualquier momento.  Puede optar por agregar proyectos existentes a su solución o por crear otros nuevos. 

![Add Role Command](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Su servicio en la nube puede contener roles implementados en diferentes lenguajes.  Por ejemplo, puede tener un rol web de Python implementado con Django, con roles de trabajo de Python y C#.  Puede comunicarse fácilmente entre sus roles usando colas del Bus de servicio o colas de almacenamiento.

##<a name="run-locally"></a>Ejecución en modo local

Si establece su proyecto de servicio en la nube como proyecto de inicio y presiona F5, el servicio en la nube se ejecuta en el emulador de Azure local.

Aunque PTVS admite el inicio en el emulador, la depuración (puntos de interrupción, etc.) no funcionará.

Para depurar roles web y de trabajo, puede establecer el proyecto de rol como proyecto de inicio y depurarlo.  También puede establecer varios proyectos de inicio.  Haga clic con el botón secundario y seleccione **Establecer proyectos de inicio**.

![Solution Startup Project Properties](./media/cloud-services-python-ptvs/startup.png)

##<a name="publish-to-azure"></a>Publicación en Azure

Para publicar, haga clic con el botón secundario en el proyecto del servicio en la nube y seleccione **Publicar**.

![Microsoft Azure Publish Sign In](./media/cloud-services-python-ptvs/publish-sign-in.png)

En la página de configuración, seleccione el servicio en la nube en el que desea publicar.

![Microsoft Azure Publish Settings](./media/cloud-services-python-ptvs/publish-settings.png)

Puede crear un nuevo servicio en la nube si aún no tiene uno disponible.

![Create Cloud Service Dialog](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Es muy útil también habilitar las conexiones con Escritorio remoto a máquinas para depurar errores.

![Remote Desktop Configuration Dialog](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Cuando haya terminado la configuración, haga clic en **Publicar**.

En la ventana de salida se verá cierto progreso y después se verá la ventana Registro de actividad de Microsoft Azure.

![Microsoft Azure Activity Log Window](./media/cloud-services-python-ptvs/publish-activity-log.png)

La implementación tardará varios minutos. Después, sus roles web y/o de trabajo estarán ejecutándose en Azure.

##<a name="next-steps"></a>Pasos siguientes

Para obtener información detallada sobre el trabajo con roles web y de trabajo en Python Tools para Visual Studio, consulte la documentación de PTVS:

- [Proyectos de servicio en la nube][]

Para obtener más información sobre el uso de servicios de Azure desde roles web y de trabajo, como el uso de almacenamiento o el Bus de servicio de Azure, consulte las siguientes guías:
 
- [Servicio BLOB][]
- [Servicio Tabla][]
- [Servicio Cola][]
- [Colas del Bus de servicio][]
- [Temas de Bus de servicio][]


<!--Link references-->

[¿Qué es un servicio en la nube?]: /en-us/manage/services/cloud-services/what-is-a-cloud-service/
[execution model-web sites]: /en-us/documentation/articles/fundamentals-application-models/#WebSites
[execution model-vms]: /en-us/documentation/articles/fundamentals-application-models/#VMachine
[execution model-cloud services]: /en-us/documentation/articles/fundamentals-application-models/#CloudServices
[Centro para desarrolladores de Python]: /en-us/develop/python/

[Servicio BLOB]: /en-us/documentation/articles/storage-python-how-to-use-blob-storage/
[Servicio Cola]: /en-us/documentation/articles/storage-python-how-to-use-queue-storage/
[Servicio Tabla]: /en-us/documentation/articles/storage-python-how-to-use-table-storage/
[Colas del Bus de servicio]: /en-us/documentation/articles/service-bus-python-how-to-use-queues/
[Temas de Bus de servicio]: /en-us/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/


<!--External Link references-->

[Python Tools para Visual Studio]: http://pytools.codeplex.com
[Documentación de Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation 
[Proyectos de servicio en la nube]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191

<!--HONumber=35.1-->
