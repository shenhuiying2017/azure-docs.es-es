<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Entrega continua con Visual Studio Online en Azure" metaKeywords="" description="Aprenda a configurar los proyectos de equipo de Visual Studio Online para que se compilen y se implementen autom&aacute;ticamente en los sitios web de Azure o en los servicios en la nube." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Entrega continua a Azure con Visual Studio Online y Git" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Entrega continua a Azure con Visual Studio Online y Git

Puede usar proyectos de equipo de Visual Studio Online para hospedar en un repositorio Git el código fuente, y compilarlo e implementarlo automáticamente en sitios web o servicios en la nube de Azure cada vez que se inserta una confirmación en el repositorio.

Necesitará Visual Studio 2013 y tener instalado el SDK de Azure. Si todavía no tiene Visual Studio 2013, descárguelo; para ello, haga clic en el vínculo **Empiece de manera gratuita** en [www.visualstudio.com][www.visualstudio.com]. Instale el SDK de Azure desde [aquí][aquí].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Para completar este tutorial, deber&aacute; tener una cuenta de Visual Studio Online:</h5>
<p>Puede <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">abrir una cuenta de Visual Studio Online gratuitamente</a>.</p>
</div>

Para configurar un servicio en la nube que se compile e implemente automáticamente en Azure con Visual Studio Online, siga los pasos que aparecen a continuación:

-   [Paso 1: Creación de un repositorio Git.][Paso 1: Creación de un repositorio Git.]

-   [Paso 2: Cree un proyecto e insértelo en el repositorio Git.][Paso 2: Cree un proyecto e insértelo en el repositorio Git.]

-   [Paso 3: Conecte el proyecto a Azure][Paso 3: Conecte el proyecto a Azure]

-   [Paso 4: Realice cambios, desencadene una recompilación y vuelva a implementar][Paso 4: Realice cambios, desencadene una recompilación y vuelva a implementar]

-   [Paso 5: Vuelva a implementar una compilación anterior (opcional)][Paso 5: Vuelva a implementar una compilación anterior (opcional)]

-   [Paso 6: Cambie la implementación de producción][Paso 6: Cambie la implementación de producción]

-   [Paso 7: Implemente desde una bifurcación de trabajo][Paso 7: Implemente desde una bifurcación de trabajo]

## <a name="step1"></a><span class="short-header">Paso 1: Creación de un repositorio Git.</span>Paso 1: Creación de un repositorio Git

1.  Si aún no tiene una cuenta de Visual Studio Online, siga las instrucciones [aquí][1]. Cuando cree un proyecto de equipo, elija Git como el sistema de control de código fuente. Siga las instrucciones para conectar Visual Studio al proyecto de equipo.

2.  En Team Explorer, elija el vínculo **Clonar este repositorio**.
    ![][0]

3.  Especifique la ubicación de la copia local y elija el botón **Clonar**.

## <a name="step2"> </a><span class="short-header">Cree un proyecto y confírmelo en el repositorio.</span>Paso 2: Cree un proyecto y confírmelo en el repositorio

1.  En Team Explorer, en la sección Soluciones, elija el vínculo Nuevo para crear un nuevo proyecto en el repositorio local.
    ![][2]

2.  Mediante los pasos de este tutorial, puede implementar un sitio web o un servicio en la nube (aplicación de Azure).
    Cree un nuevo proyecto de servicio en la nube de Windows Azure
    o un nuevo proyecto de ASP.NET MVC. Asegúrese de que este proyecto esté dirigido a .NET Framework 4 o 4.5, y si va a crear un proyecto de servicio en la nube, agregue un rol web y un rol de trabajo de ASP.NET MVC.
    Si desea crear un sitio web, elija la plantilla de proyecto Aplicación web ASP.NET y luego elija MVC. Consulte [Introducción a Azure y ASP.NET][Introducción a Azure y ASP.NET].

3.  Abra el menú de acceso directo de la solución y seleccione **Confirmar**.
    ![][3]

4.  Si es la primera vez que usa Git en Visual Studio Online, deberá proporcionar alguna información que le identifique en Git. En el área **Cambios pendientes** de Team Explorer, escriba su nombre de usuario y dirección de correo electrónico. Escriba un comentario para la confirmación y elija **Confirmar**.
    ![][4]

5.  Fíjese en las opciones para incluir o excluir cambios concretos al registrar. Si se excluyen los cambios deseados, haga clic en el vínculo **Incluir todos**.

6.  Ahora ha confirmado los cambios en la copia local del repositorio. A continuación, sincronice esos cambios con el servidor. Elija el vínculo **Sincronizar**.

## <a name="step3"> </a><span class="short-header">Conexión del proyecto a Azure</span>Paso 3: Conecte el proyecto a Azure

1.  Ahora que tiene un repositorio Git en Visual Studio Online con código fuente, esta en disposición de conectarlo a Azure. En el [Portal de Azure][Portal de Azure], seleccione el servicio en la nube o el sitio web, o bien cree uno nuevo haciendo clic en el icono + situado en la parte inferior izquierda y seleccionando **Servicio en la nube** o **Sitio web** y, a continuación, **Creación rápida**.<br.>
    ![][5]

2.  Para servicios en la nube, elija el vínculo **Configurar publicación con Visual Studio Online**. Para sitios web, elija el vínculo **Configurar implementación desde control de código fuente**.
    ![][6]

3.  En el asistente, escriba el nombre de la cuenta de Visual Studio Online en el cuadro de texto y elija el vínculo **Autorizar ahora**. Puede que se le solicite que inicie sesión.
    ![][7]

4.  En el cuadro de diálogo emergente de OAuth, seleccione **Aceptar** para autorizar a Azure para que configure el proyecto de equipo en V Studio Online.
    ![][8]

5.  Si la autorización se realiza correctamente, verá una lista desplegable que contiene los proyectos de equipo de Visual Studio Online. Seleccione el nombre del proyecto de equipo que ha creado en los pasos anteriores y presione el botón de la marca de verificación del asistente.
    ![][9]

La próxima vez que inserte una confirmación en el repositorio, Visual Studio Online creará e implementará su proyecto en Azure.

## <a name="step4"> </a><span class="short-header">Desencadenamiento de una recompilación</span>Paso 4: Desencadene una recompilación y vuelva a implementar su proyecto

1.  En Visual Studio, abra un archivo y cámbielo. Por ejemplo, cambie el archivo \_Layout.cshtml de la carpeta Views\\Shared de un rol web de MVC.
    ![][10]

2.  Edite el texto del pie de página del sitio y guarde el archivo.
    ![][11]

3.  En el Explorador de soluciones, abra el menú de acceso directo del nodo de solución, el nodo de proyecto o el archivo que ha cambiado, y elija **Confirmar**.

4.  Escriba un comentario y elija **Confirmar**.
    ![][4]

5.  Elija el vínculo **Sincronizar**.
    ![][12]

6.  Elija el vínculo **Insertar** para insertar la confirmación en el repositorio en Visual Studio Online. (También puede usar el botón **Sincronizar** para copiar las confirmaciones en el repositorio. La diferencia es que **Sincronizar** también extrae los últimos cambios del repositorio)
    .
    ![][13]

7.  Presione el botón Inicio para volver a la página principal de Team Explorer.
    ![][14]

8.  Elija **Compilaciones** para ver las compilaciones en curso.
    ![][15]
    Team Explorer muestra que hay una compilación activada para su registro.
    ![][16]

9.  Para ver un registro detallado del progreso de la compilación, haga doble clic en el nombre de la compilación en curso.

10. Mientras la compilación está en curso, eche un vistazo a la definición de compilación que se creó al vincular a Azure con el asistente. Abra el menú de acceso directo de la definición de compilación y elija **Editar definición de compilación**.
    ![][17]
    En la pestaña **Desencadenador**, verá que la definición de compilación está establecida, de forma predeterminada, para compilar en cada registro. (Para un servicio en la nube, Visual Studio Online compila e implementa automáticamente la bifurcación principal en el entorno de ensayo. Aún así, tendrá que realizar un paso manual para implementar en el sitio activo. En el caso de que el sitio web no tenga entorno de ensayo, la bifurcación principal se implementa directamente en el sitio activo.
    ![][18]
    En la pestaña **Proceso**, puede ver que el entorno de implementación está establecido en el nombre del servicio en la nube o del sitio web.
    ![][19]
    Especifique valores para las propiedades si desea unos valores diferentes a los predeterminados. Las propiedades para la publicación de Azure se encuentran en la sección Implementación, y es posible que también sea necesario definir los parámetros de MSBuild. Por ejemplo, en un proyecto de servicio en la nube, para especificar una configuración de servicio que no sea "Nube", establezca los parámetros de MSbuild en /p:TargetProfile=*SuPerfil* donde *SuPerfil* coincide con un archivo de configuración de servicio con un nombre como ServiceConfiguration.*SuPerfil*.cscfg.
    En la siguiente tabla se muestran las propiedades disponibles en la sección Implementación:

    <table>

    <tr>
    <td>
    **Propiedad**

    </td>
    <td>
    **Valor predeterminado**

    </td>
    </tr>
    </p>
    > <tr>
    > <td>
    > Permitir certificados que no son de confianza
    >
    > </td>
    > <td>
    > Si el valor es false, los certificados SSL deben estar firmados por una entidad de certificación raíz.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Permitir actualización
    >
    > </td>
    > <td>
    > Permite actualizar una implementación existente en lugar de crear una nueva. Conserva la dirección IP.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > No eliminar
    >
    > </td>
    > <td>
    > Si el valor es true, no sobrescriba una implementación no relacionada existente (la actualización está permitida).
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Ruta de acceso a la configuración de implementación
    >
    > </td>
    > <td>
    > La ruta de acceso al archivo .pubxml de un sitio web, en relación con la carpeta raíz del repositorio. Se ignora para los servicios en la nube.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Entorno de implementación de SharePoint
    >
    > </td>
    > <td>
    > La misma que el nombre de servicio
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Entorno de implementación de Windows Azure
    >
    > </td>
    > <td>
    > Nombre del sitio web del servicio en la nube
    >
    > </td>
    > </tr>
    > </table>

11. Llegados a este punto, la compilación debería haber finalizado correctamente.
    ![][20]

12. Si hace doble clic en el nombre de la compilación, Visual Studio mostrará un **Resumen de la compilación** en el que se incluyen los resultados de las pruebas de los proyectos de prueba de unidades asociadas.
    ![][21]

13. En el [Portal de Azure][Portal de Azure], puede ver la implementación asociada en la pestaña Implementaciones cuando se selecciona el entorno de ensayo.
    ![][22]

14. Vaya a la dirección URL del sitio. Para un servicio web, elija el botón **Examinar** del portal. Para un servicio en la nube, elija la URL en la sección **Vista rápida** de la página **Panel** que muestra el entorno de ensayo. Las implementaciones de la integración continua para los servicios en la nube se publican de forma predeterminada en el entorno de ensayo. Puede cambiarlo configurando la propiedad Entorno del servicio de nube alternativo en Producción. Aquí es donde se encuentra la URL del sitio en la página Panel del servicio en la nube:
    ![][23]
    Se abrirá una nueva pestaña del explorador para mostrar el sitio que se está ejecutando.
    ![][24]

15. Si realiza otros cambios en el proyecto, activará más compilaciones y acumulará varias implementaciones. La última de ellas marcada como Activa.
    ![][25]

## <a name="step5"> </a><span class="short-header">Reimplementación de una compilación anterior</span>Paso 5: Vuelva a implementar una compilación anterior

Este paso es opcional. En el Portal de administración, seleccione una implementación anterior y haga clic en **Volver a implementar** para que su sitio vuelva a un registro anterior. Tenga en cuenta que esto activará una nueva compilación en TFS y se creará una nueva entrada en el historial de implementación.
![][26]

## <a name="step6"> </a><span class="short-header">Cambio de la implementación de producción</span>Paso 6: Cambie la implementación de producción

Cuando esté preparado, puede promover el entorno de ensayo al de producción haciendo clic en el botón **Intercambiar** del Portal de administración. El entorno de ensayo recién implementado se promueve a Producción y el anterior entorno de Producción, si lo hubiera, pasa a entorno de ensayo. La implementación activa puede ser diferente para los entornos de producción y ensayo, pero el historial de implementación de las compilaciones recientes es el mismo independientemente del entorno.
![][27]

## <a name="step7"> </a><span class="short-header">Implemente desde una bifurcación de trabajo</span>Paso 6: Implemente desde una bifurcación de trabajo.

Cuando usa Git, realiza cambios habitualmente en una bifurcación de trabajo y la integra en la bifurcación principal cuando el trabajo de desarrollo alcanza una fecha de finalización. Durante la fase de desarrollo de un proyecto, querrá compilar e implementar la bifurcación de trabajo en Azure.

1.  En Team Explorer, elija el botón **Inicio** y luego elija el botón **Bifurcaciones**.
    ![][28]

2.  Elija el vínculo **Nueva bifurcación**.
    ![][29]

3.  Escriba el nombre de la bifurcación, por ejemplo "trabajo", y elija **Crear bifurcación**. De esta forma se crea una nueva bifurcación local.
    ![][30]

4.  Publique la bifurcación. Elija el nombre de la bifurcación en **Bifurcaciones no publicadas**, y elija **Publicar**.
    ![][31]

5.  De manera predeterminada, solo los cambios realizados en la bifurcación principal activan una compilación continua. Para configurar la compilación continua en una bifurcación de trabajo, elija la página Compilaciones de Team Explorer y elija **Editar definición de compilación**.

6.  Abra la pestaña **Configuración de origen**. En **Bifurcaciones supervisadas para integración y compilación continuas**, elija **Haga clic aquí para agregar una nueva fila**.
    ![][32]

7.  Especifique la bifurcación que ha creado, por ejemplo refs/heads/working.
    ![][33]

8.  Realice un cambio en el código, abra el menú de acceso directo del archivo modificado y elija **Confirmar**.
    ![][4]

9.  Elija el vínculo **Confirmaciones no sincronizadas**, y elija el botón **Sincronizar** o el vínculo **Insertar** para copiar los cambios en la copia de la bifurcación de trabajo en Visual Studio Online.
    ![][12]

10. Vaya a la vista **Compilaciones** y busque la compilación que se acaba de activar para la bifurcación de trabajo.

Para obtener más información, consulte [Visual Studio Online][Visual Studio Online]. Para obtener sugerencias adicionales sobre el uso de Git con Visual Studio Online, consulte [Comparta su código en Git][Comparta su código en Git] y, para obtener información sobre el uso de un repositorio Git que no está administrado por Visual Studio Online para publicar en Azure, consulte [Publicación del control de código fuente a Sitios web Azure][Publicación del control de código fuente a Sitios web Azure].

  [www.visualstudio.com]: http://www.visualstudio.com
  [aquí]: http://go.microsoft.com/fwlink/?LinkId=239540
  [Paso 1: Creación de un repositorio Git.]: #step1
  [Paso 2: Cree un proyecto e insértelo en el repositorio Git.]: #step2
  [Paso 3: Conecte el proyecto a Azure]: #step3
  [Paso 4: Realice cambios, desencadene una recompilación y vuelva a implementar]: #step4
  [Paso 5: Vuelva a implementar una compilación anterior (opcional)]: #step5
  [Paso 6: Cambie la implementación de producción]: #step6
  [Paso 7: Implemente desde una bifurcación de trabajo]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=397665
  [0]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
  [2]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
  [Introducción a Azure y ASP.NET]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-dotnet-get-started/
  [3]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
  [4]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
  [Portal de Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
  [6]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
  [7]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
  [8]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
  [9]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
  [12]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
  [13]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
  [14]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
  [16]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
  [29]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
  [30]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
  [31]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
  [32]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
  [33]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Comparta su código en Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Publicación del control de código fuente a Sitios web Azure]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-publish-source-control
