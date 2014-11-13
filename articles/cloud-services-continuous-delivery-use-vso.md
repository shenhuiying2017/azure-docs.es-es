<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Entrega continua con Visual Studio Online en Azure" metaKeywords="" description="Aprenda a configurar los proyectos de equipo de Visual Studio Online para que se compilen y se implementen autom&aacute;ticamente en los sitios web de Azure o en los servicios en la nube." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Entrega continua a Azure con Visual Studio Online" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Entrega continua a Azure con Visual Studio Online

Puede configurar los proyectos de equipo de Visual Studio Online para que se compilen y se implementen automáticamente en los sitios web Azure o en los servicios en la nube. (Para obtener información sobre cómo configurar un sistema de compilación e implementación continuas con Team Foundation Server *local*, consulte [Entrega continua para Servicios en la nube de Azure][Entrega continua para Servicios en la nube de Azure].)

En este tutorial se supone que tiene instalados Visual Studio 2013 y el SDK de Azure. Si todavía no tiene Visual Studio 2013, descárguelo; para ello, haga clic en el vínculo **Empiece de manera gratuita** en [www.visualstudio.com][www.visualstudio.com]. Instale el SDK de Azure desde [aquí][aquí].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Para completar este tutorial, deber&aacute; tener una cuenta de Visual Studio Online:</h5>
<p>Puede <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">abrir una cuenta de Visual Studio Online gratuitamente</a>.</p>
</div>

Para configurar un servicio en la nube que se compile e implemente automáticamente en Azure con Visual Studio Online, siga los pasos que aparecen a continuación:

-   [Paso 1: Cree un proyecto de equipo.][Paso 1: Cree un proyecto de equipo.]

-   [Paso 2: Registre un proyecto para el control de código fuente][Paso 2: Registre un proyecto para el control de código fuente]

-   [Paso 3: Conecte el proyecto a Azure][Paso 3: Conecte el proyecto a Azure]

-   [Paso 4: Realice cambios, desencadene una recompilación y vuelva a implementar][Paso 4: Realice cambios, desencadene una recompilación y vuelva a implementar]

-   [Paso 5: Vuelva a implementar una compilación anterior (opcional)][Paso 5: Vuelva a implementar una compilación anterior (opcional)]

-   [Paso 6: Cambie la implementación de producción (solo servicios en la nube)][Paso 6: Cambie la implementación de producción (solo servicios en la nube)]

-   [Paso 7: Ejecutar pruebas unitarias (opcional)][Paso 7: Ejecutar pruebas unitarias (opcional)]

## <a name="step1"></a><span class="short-header">Creación de un proyecto de equipo.</span>Paso 1: Creación de un proyecto de equipo

Siga las instrucciones que aparecen [aquí][1] para crear su proyecto de equipo y vincularlo a Visual Studio. En este tutorial se supone que usa Team Foundation Version Control (TFVC) como solución de control de código fuente. Si desea usar Git para el control de versiones, vea [la versión de Git de este tutorial][la versión de Git de este tutorial].

## <a name="step2"> </a><span class="short-header">Registro de un proyecto para el control de código fuente</span>Paso 2: Registre un proyecto para el control de código fuente

1.  En Visual Studio, abra la solución que desee implementar o cree una nueva.
    Puede implementar un sitio web o un servicio en la nube (aplicación de Azure) siguiendo los pasos que se ofrecen en este tutorial.
    Si desea crear una nueva solución, cree un proyecto de Servicio en la nube de Azure
    o un proyecto de MVC de ASP.NET. Asegúrese de que el proyecto se dirige a .NET Framework 4 o 4.5. Si está creando un proyecto de Servicio de nube, agregue un rol web de MVC de ASP.NET y un rol de trabajo y seleccione una aplicación de Internet para el rol web. Cuando se le solicite, elija **Aplicación de Internet**.
    Si desea crear un sitio web, seleccione la plantilla de proyecto de aplicación web ASP.NET y, a continuación, MVC. Consulte [Introducción a Azure y ASP.NET][Introducción a Azure y ASP.NET].

2.  Abra el menú contextual de la solución y seleccione **Agregar solución al control de código fuente**.
    ![][0]

3.  Acepte o cambie los valores predeterminados y seleccione el botón **OK**. Una vez terminado el proceso, aparecerán los iconos de control de código fuente en el Explorador de soluciones.
    ![][2]

4.  Abra el menú de acceso directo de la solución y seleccione **Check In**.
    ![][3]

5.  En el área Cambios pendientes de Team Explorer, escriba un comentario para el registro y seleccione el botón **Check In**.
    ![][4]

 Fíjese en las opciones para incluir o excluir cambios concretos al registrar. Si se excluyen los cambios deseados, haga clic en el vínculo **Include All**.
![][5]

## <a name="step3"> </a><span class="short-header">Conexión del proyecto a Azure</span>Paso 3: Conecte el proyecto a Azure

1.  Ahora que tiene un proyecto de equipo de VSO con código fuente en él, esta en disposición de conectar el proyecto de equipo a Azure. En el [Portal de Azure][Portal de Azure], seleccione el servicio en la nube o el sitio web, o bien cree uno nuevo haciendo clic en el icono + situado en la parte inferior izquierda y seleccionando **Servicio en la nube** o **Sitio web** y, a continuación, **Creación rápida**. Haga clic en el vínculo **Configurar publicación con Visual Studio Online**.
    ![][6]

2.  En el asistente, escriba el nombre de la cuenta de Visual Studio Online en el cuadro de texto y haga clic en el vínculo **Authorize Now**. Puede que se le solicite que inicie sesión.
    ![][7]

3.  En el diálogo emergente de OAuth, seleccione **Accept** para autorizar a Azure para que configure el proyecto de equipo en VSO.
    ![][8]

4.  Si la autorización se realiza correctamente, verá una lista desplegable que contiene los proyectos de equipo de Visual Studio Online. Seleccione el nombre del proyecto de equipo que ha creado en los pasos anteriores y presione el botón de la marca de verificación del asistente.
    ![][9]

5.  Una vez que el proyecto se haya vinculado, verá instrucciones para registrar los cambios en el proyecto de equipo de Visual Studio Online. La próxima vez que se registre, Visual Studio Online compilará e implementará el proyecto en Azure. Pruebe esto ahora haciendo clic en el vínculo **Proteger en Visual Studio** y, a continuación, en **Launch Visual Studio** (o el botón equivalente de **Visual Studio** situado en la parte inferior de la pantalla del portal).
    ![][10]

## <a name="step4"> </a><span class="short-header">Desencadenamiento de una recompilación</span>Paso 4: Desencadene una recompilación y vuelva a implementar su proyecto

1.  En Team Explorer de Visual Studio, haga clic en el vínculo **Explorador de control de código fuente**.
    ![][11]

2.  Vaya hasta su archivo de solución y ábralo.
    ![][12]

3.  En el Explorador de soluciones, abra un archivo y cámbielo. Por ejemplo, cambie el archivo \_Layout.cshtml de la carpeta Views\\Shared de un rol web de MVC.
    ![][13]

4.  Modifique el logotipo del sitio y presione Ctrl+S para guardar el archivo.
    ![][14]

5.  En Team Explorer, haga clic en el vínculo **Cambios pendientes**.
    ![][15]

6.  Escriba un comentario y presione el botón **Check In**.
    ![][16]

7.  Presione el botón Inicio para volver a la página principal de Team Explorer.
    ![][17]

8.  Elija el vínculo **Compilaciones** para ver las compilaciones en progreso.
    ![][18]
    Team Explorer indica que se ha desencadenado una compilación para su protección.
    ![][19]

9.  Haga doble clic en el nombre de la compilación en curso para ver un registro detallado a medida que avanza la compilación.
    ![][20]

10. Mientras la compilación está en curso, eche un vistazo a la definición de compilación que se creó al vincular TFS a Azure con el asistente. Abra el menú de acceso directo de la definición de compilación y seleccione **Editar definición de compilación**.
    ![][21]
    En la pestaña **Desencadenar**, verá que la definición de compilación se ha configurado en cada protección de forma predeterminada.
    ![][21]
    En la pestaña **Proceso**, podrá ver que el entorno de implementación se ha configurado con el nombre del servicio en la nube o sitio web. Si trabaja con sitios web, verá propiedades diferentes a las que se muestran aquí.
    ![][22]
    Especifique los valores para las propiedades si desea que sean diferentes de los predeterminados. Las propiedades de publicación de Azure están en la sección Implementación.
    En la tabla siguiente se muestran las propiedades disponibles en la sección Implementación:

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

Si usa varias configuraciones de servicio (archivos .cscfg), puede especificar la configuración de servicio que desee en el valor **Compilación, Avanzada, Argumentos de MSBuild**. Por ejemplo, para usar ServiceConfiguration.Test.cscfg, establezca la opción /p:TargetProfile=Test de la línea de argumentos de MSBuild.
![][23]

1.  Llegados a este punto, la compilación debería haber finalizado correctamente.
    ![][24]

2.  Si hace doble clic en el nombre de la compilación, Visual Studio mostrará un **Resumen de la compilación** en el que se incluyen los resultados de las pruebas de los proyectos de prueba de unidades asociadas.
    ![][25]

3.  En el [Portal de Azure][Portal de Azure], puede ver la implementación asociada en la pestaña Implementaciones cuando se selecciona el entorno de ensayo.
    ![][26]

4.  Vaya a la dirección URL del sitio. Para un sitio web, simplemente haga clic en el botón Examinar de la barra de comandos. Para un servicio en la nube, seleccione la dirección URL de la sección **Vista rápida** de la página **Panel** que muestra el entorno de ensayo de un servicio en la nube. Las implementaciones de la integración continua para los servicios en la nube se publican de forma predeterminada en el entorno de ensayo. Puede cambiarlo configurando la propiedad Entorno del servicio de nube alternativo en Producción. Esta captura de pantalla indica en qué parte del sitio en la página del panel del servicio en la nube está la dirección URL:
    ![][27]
    Se abrirá una nueva pestaña del explorador para mostrar el sitio que se está ejecutando.
    ![][28]

5.  Para los servicios en la nube, si realiza otros cambios en el proyecto, debe desencadenar más compilaciones y acumular varias implementaciones. La última de ellas marcada como Activa.
    ![][29]

## <a name="step5"> </a><span class="short-header">Reimplementación de una compilación anterior</span>Paso 5: Vuelva a implementar una compilación anterior

Este paso se aplica a los servicios en la nube y es opcional. En el Portal de administración, seleccione una implementación anterior y haga clic en el botón **Volver a implementar** para que su sitio vuelva a un registro anterior. Tenga en cuenta que esto desencadenará una nueva compilación en TFS y se creará una nueva entrada en el historial de implementación.
![][30]

## <a name="step6"> </a><span class="short-header">Cambio de la implementación de producción</span>Paso 6: Cambie la implementación de producción

Este paso solo se aplica a los servicios en la nube, no a los sitios web. Cuando esté preparado, puede promover el entorno de ensayo al de producción haciendo clic en el botón Intercambiar del Portal de administración. El entorno de ensayo recién implementado se promueve a Producción y el anterior entorno de Producción, si lo hubiera, pasa a entorno de ensayo. La implementación activa puede ser diferente para los entornos de producción y ensayo, pero el historial de implementación de las compilaciones recientes es el mismo independientemente del entorno.
![][31]

## <a name="step7"> </a><span class="short-header">Ejecutar pruebas unitarias</span>Paso 7: Ejecutar pruebas unitarias

Para establecer una prueba de calidad en sus implementaciones dinámicas o de ensayo, puede ejecutar pruebas unitarias y, si estas fallan, puede detener la implementación.

1.  En Visual Studio, agregue un proyecto de prueba unitaria.
    ![][32]

2.  Agregue referencias de proyecto a los proyectos que desee probar.
    ![][33]

3.  Agregue algunas pruebas unitarias. Para empezar, haga una prueba ficticia que siempre se supere.

        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }

4.  Edite la definición de la compilación, elija la pestaña Proceso y expanda el nodo Prueba.

5.  Establezca **Suspender compilación tras error de pruebas** en True. Esto significa que la implementación no se producirá a menos que se superen las pruebas.
    ![][34]

6.  Ponga en cola una nueva compilación.
    ![][35]
    ![][36]

7.  Mientras se desarrolla la compilación, compruebe su progreso.
    ![][37]
    ![][38]

8.  Una vez terminada la compilación, compruebe los resultados de la prueba.
    ![][39]
    ![][40]

9.  Pruebe a crear una prueba que provoque un error. Agregue una nueva prueba copiando la primera, cámbiele el nombre y comente la línea de código que lanza NotImplementedException.

        [TestMethod]
        [ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            //throw new NotImplementedException();
        }

10. Registre el cambio para poner en cola una nueva compilación.
    ![][41]

11. Consulte los resultados de la prueba para ver los detalles del error.
    ![][42]
    ![][43]

Para obtener más información sobre las pruebas unitarias en Visual Studio Online, vea [Ejecutar pruebas unitarias en una compilación][Ejecutar pruebas unitarias en una compilación].

Para obtener más información, consulte [Visual Studio Online][Visual Studio Online]. Si usa Git, consulte [Comparta su código en Git][Comparta su código en Git] y [Publicación en Sitios web Azure desde el control de código fuente][Publicación en Sitios web Azure desde el control de código fuente].

  [Entrega continua para Servicios en la nube de Azure]: ../cloud-services-dotnet-continuous-delivery
  [www.visualstudio.com]: http://www.visualstudio.com
  [aquí]: http://go.microsoft.com/fwlink/?LinkId=239540
  [Paso 1: Cree un proyecto de equipo.]: #step1
  [Paso 2: Registre un proyecto para el control de código fuente]: #step2
  [Paso 3: Conecte el proyecto a Azure]: #step3
  [Paso 4: Realice cambios, desencadene una recompilación y vuelva a implementar]: #step4
  [Paso 5: Vuelva a implementar una compilación anterior (opcional)]: #step5
  [Paso 6: Cambie la implementación de producción (solo servicios en la nube)]: #step6
  [Paso 7: Ejecutar pruebas unitarias (opcional)]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=512980
  [la versión de Git de este tutorial]: http://go.microsoft.com/fwlink/p/?LinkId=397358
  [Introducción a Azure y ASP.NET]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-dotnet-get-started/
  [0]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
  [2]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
  [3]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
  [4]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
  [5]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
  [Portal de Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
  [7]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
  [8]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
  [12]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
  [13]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [29]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [30]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [31]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [32]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
  [33]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
  [34]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
  [35]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
  [36]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
  [37]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
  [38]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
  [39]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
  [40]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
  [41]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
  [42]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
  [43]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
  [Ejecutar pruebas unitarias en una compilación]: http://go.microsoft.com/fwlink/p/?LinkId=510474
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Comparta su código en Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Publicación en Sitios web Azure desde el control de código fuente]: http://www.windowsazure.com/es-es/documentation/articles/web-sites-publish-source-control
