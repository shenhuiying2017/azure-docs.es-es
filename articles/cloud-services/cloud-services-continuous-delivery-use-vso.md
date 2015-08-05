<properties 
	pageTitle="Entrega continua con Visual Studio Online en Azure" 
	description="Aprenda a configurar los proyectos de equipo de Visual Studio Online para que se compilen y se implementen automáticamente en la característica aplicación web de Servicio de aplicaciones de Azure o en los servicios en la nube." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/08/2015" 
	ms.author="kempb"/>


# Entrega continua a Azure con Visual Studio Online

  Puede configurar los proyectos de equipo de Visual Studio Online para que se compilen y se implementen automáticamente en las aplicaciones web de Azure o en los servicios en la nube. Para obtener información sobre cómo configurar un sistema de compilación e implementación continuas con Team Foundation Server *local*, consulte [Entrega continua para Servicios en la nube de Azure](cloud-services-dotnet-continuous-delivery.md).

En este tutorial se supone que tiene instalados Visual Studio 2013 y el SDK de Azure. Si aún no tiene Visual Studio 2013, descárguelo; para ello, haga clic en el vínculo **Empezar de forma gratuita** en [www.visualstudio.com](http://www.visualstudio.com). Instale el SDK de Azure desde [aquí](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Necesita una cuenta en línea de Visual Studio para completar este tutorial: puede [abrir una cuenta de Visual Studio Online de forma gratuita](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar un servicio en la nube que se compile e implemente automáticamente en Azure con Visual Studio Online, siga los pasos que aparecen a continuación:

-   [Paso 1: crear un proyecto de equipo.][]

-   [Paso 2: proteger un proyecto para el control de código fuente.][]

-   [Paso 3: conectar el proyecto a Azure.][]

-   [Paso 4: realizar cambios, desencadenar una recompilación y volver a implementar.][]

-   [Paso 5: volver a implementar una compilación anterior (opcional)][]

-   [ Paso 6: cambiar la implementación de producción (solo servicios en la nube)][]

-	[Paso 7: ejecutar pruebas unitarias (opcional)][]

## <a name="step1"></a>Paso 1: crear un proyecto de equipo

Siga las instrucciones que se describen [aquí](http://go.microsoft.com/fwlink/?LinkId=512980) para crear el proyecto de equipo y vincularlo a Visual Studio. En este tutorial se supone que usa Team Foundation Version Control (TFVC) como solución de control de código fuente. Si desea usar Git para el control de versiones, consulte [la versión Git de este tutorial](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="step2"> </a>Paso 2: proteger un proyecto para el control de código fuente

1. En Visual Studio, abra la solución que desee implementar o cree una nueva. Puede implementar una aplicación web o un servicio en la nube (aplicación de Azure) siguiendo los pasos que se ofrecen en este tutorial. Si desea crear una nueva solución, cree un proyecto de Servicio de nube de Azure o un proyecto de MVC de ASP.NET. Asegúrese de que el proyecto se dirige a .NET Framework 4 o 4.5. Si está creando un proyecto de Servicio de nube, agregue un rol web de MVC de ASP.NET y un rol de trabajo y seleccione una aplicación de Internet para el rol web. Cuando se le solicite, elija **Aplicación de Internet**. Si desea crear una aplicación web, seleccione la plantilla de proyecto de aplicación web ASP.NET y, a continuación, MVC. Consulte [Crear una aplicación web de ASP.NET en el servicio de aplicaciones de Azure](../web-sites-dotnet-get-started.md)

> [AZURE.NOTE]Actualmente, Visual Studio Online solo admite las implementaciones de integración continua de las aplicaciones web de Visual Studio. Los proyectos de sitio web están fuera del alcance.


2. Abra el menú contextual de la solución y seleccione **Agregar solución al control de código fuente**.<br/>
![][5]

3. Acepte o cambie los valores predeterminados y elija el botón **Aceptar**. Una vez terminado el proceso, aparecerán los iconos de control de código fuente en el Explorador de soluciones.<br/>
![][6]

4. Abra el menú contextual de la solución y elija **Proteger**.<br/>
![][7]

5. En el área Cambios pendientes de Team Explorer, escriba un comentario para la protección y elija el botón **Proteger**.<br/>
![][8]

<br/> Tenga en cuenta las opciones para incluir o excluir cambios concretos al realizar la protección. Si los cambios deseados se excluyen, elija el vínculo **Incluir todo**.<br/>
![][9]

## <a name="step3"> </a>Paso 3: conectar el proyecto a Azure

1. Ahora que tiene un proyecto de equipo de VSO con código fuente en él, está en disposición de conectar el proyecto de equipo a Azure. En el [Portal de Azure](http://manage.windowsazure.com), seleccione el servicio en la nube o el sitio web, o bien cree uno nuevo haciendo clic en el icono + situado en la parte inferior izquierda y seleccionando **Servicio en la nube** o **Aplicación web** y, a continuación, **Creación rápida**. Haga clic en el vínculo **Configurar publicación con Visual Studio Online**.<br/>
![][10]

2. En el asistente, escriba el nombre de la cuenta de Visual Studio Online en el cuadro de texto y haga clic en el vínculo **Autorizar ahora**. Puede que se le solicite que inicie sesión.<br/>
![][11]

3. En el cuadro de diálogo emergente de OAuth, elija **Aceptar** para autorizar a Azure a que configure el proyecto de equipo en VSO.<br/>
![][12]

4. Si la autorización se realiza correctamente, verá una lista desplegable que contiene los proyectos de equipo de Visual Studio Online. Seleccione el nombre del proyecto de equipo que ha creado en los pasos anteriores y presione el botón de la marca de verificación del asistente.<br/>
![][13]

5. Una vez que el proyecto se haya vinculado, verá instrucciones para registrar los cambios en el proyecto de equipo de Visual Studio Online. La próxima vez que se registre, Visual Studio Online compilará e implementará el proyecto en Azure. Para probarlo ahora, haga clic en el vínculo **Proteger desde Visual Studio** y, a continuación, en **Iniciar Visual Studio** (o el botón equivalente de **Visual Studio** situado en la parte inferior de la pantalla del portal).<br/>
![][14]

## <a name="step4"> </a>Paso 4: desencadenar una recompilación y volver a implementar el proyecto

1. En Team Explorer de Visual Studio, haga clic en el vínculo **Explorador de control de código fuente**.<br/>
![][15]

2. Vaya hasta su archivo de solución y ábralo.<br/>
![][16]

3. En el Explorador de soluciones, abra un archivo y cámbielo. Por ejemplo, cambie el archivo _Layout.cshtml de la carpeta Views\Shared de un rol web de MVC.<br/>
![][17]

4. Modifique el logotipo del sitio y presione Ctrl+S para guardar el archivo.<br/>
![][18]

5. En Team Explorer, haga clic en el vínculo **Cambios pendientes**.<br/>
![][19]

6. Escriba un comentario y elija el botón **Proteger**.<br/>
![][20]

7. Presione el botón Inicio para volver a la página principal de Team Explorer.<br/>
![][21]

8. Haga clic en el vínculo **Compilaciones** para ver las compilaciones en curso.<br/>
![][22]
<br/>
Team Explorer indica que se ha desencadenado una compilación para su protección.<br/>
![][23]

9. Haga doble clic en el nombre de la compilación en curso para ver un registro detallado a medida que avanza la compilación.<br/>
![][24]

10. Mientras la compilación está en curso, eche un vistazo a la definición de compilación que se creó al vincular TFS a Azure con el asistente. Abra el menú contextual de la definición de compilación y elija **Editar definición de compilación**.<br/>
![][25]
<br/>
En la pestaña **Desencadenador**, verá que la definición de compilación se ha configurado en cada protección de forma predeterminada.<br/>
![][26]
<br/>
En la pestaña **Proceso**, puede ver que el entorno de implementación se ha configurado con el nombre del servicio en la nube o aplicación web. Si trabaja con aplicaciones web, verá propiedades diferentes a las que se muestran aquí.<br/>
![][27]
<br/>
Especifique los valores para las propiedades si desea que sean diferentes de los predeterminados. Las propiedades para la publicación de Azure se encuentran en la sección Implementación. En la tabla siguiente se muestran las propiedades disponibles en la sección Implementación:
	<table>
<tr><td><b>Propiedad</b></td><td><b>Valor predeterminado</b></td></tr>
><tr><td>Permitir certificados que no son de confianza</td><td>Si el valor es false, los certificados SSL deben estar firmados por una entidad de certificación raíz.</td></tr>
<tr><td>Permitir actualización</td><td>Permite actualizar una implementación existente en lugar de crear una nueva. Conserva la dirección IP.</td></tr><tr><td>No eliminar</td><td>Si el valor es true, no sobrescriba una implementación no relacionada existente (la actualización está permitida).</td></tr>
<tr><td>Ruta de acceso a la configuración de implementación</td><td>La ruta de acceso al archivo .pubxml de una aplicación web, en relación con la carpeta raíz del repositorio. Se ignora para los servicios en la nube.</td></tr>
<tr><td>Entorno de implementación de SharePoint</td><td>La misma que el nombre de servicio</td></tr>
<tr><td>Entorno de implementación de Azure</td><td>Nombre de la aplicación web del servicio en la nube</td></tr>
</table>
<br/>

Si usa varias configuraciones de servicio (archivos .cscfg), puede especificar la configuración de servicio que desee en el valor **Compilación, Avanzada, Argumentos de MSBuild**. Por ejemplo, para usar ServiceConfiguration.Test.cscfg, establezca la opción /p:TargetProfile=Test de la línea de argumentos de MSBuild.<br/>
![][38]

11. Llegados a este punto, la compilación debería haber finalizado correctamente.<br/>
![][28]

12. Si se hace doble clic en el nombre de la compilación, Visual Studio muestra un **Resumen de la compilación** en el que se incluyen los resultados de las pruebas de los proyectos de pruebas unitarias asociados.<br/>
![][29]

13. En el [Portal de Azure](http://manage.windowsazure.com) puede ver la implementación asociada en la pestaña Implementaciones cuando se selecciona el entorno de ensayo.<br/>
![][30]

14.	Vaya a la dirección URL del sitio. Para una aplicación web, simplemente haga clic en el botón Examinar de la barra de comandos. Para un servicio en la nube, elija la dirección URL de la sección **Vista rápida** de la página **Panel** que muestra el entorno de ensayo de un servicio en la nube. Las implementaciones de la integración continua para los servicios en la nube se publican de forma predeterminada en el entorno de ensayo. Puede cambiarlo configurando la propiedad Entorno del servicio de nube alternativo en Producción. Aquí es donde está la dirección URL del sitio en la página del panel del servicio en la nube: <br/>
![][31]
<br/>
Se abrirá una nueva pestaña del explorador para mostrar el sitio en ejecución.<br/>
![][32]

15.	Para los servicios en la nube, si realiza otros cambios en el proyecto, debe desencadenar más compilaciones y acumular varias implementaciones. La última de ellas marcada como Activa.<br/>
![][33]

## <a name="step5"> </a>Paso 5: volver a implementar una compilación anterior

Este paso se aplica a los servicios en la nube y es opcional. En el Portal de administración, seleccione una implementación anterior y haga clic en **Volver a implementar** para devolver el sitio a una protección anterior. Tenga en cuenta que esto desencadenará una nueva compilación en TFS y creará una nueva entrada en el historial de implementación.<br/>
![][34]

## <a name="step6"> </a>Paso 6: cambiar la implementación de producción

Este paso solo se aplica a los servicios en la nube, no a las aplicaciones web. Cuando esté preparado, puede promover el entorno de ensayo al de producción haciendo clic en el botón Intercambiar del Portal de administración. El entorno de ensayo recién implementado se promueve a Producción y el anterior entorno de Producción, si lo hubiera, pasa a entorno de ensayo. La implementación activa puede ser diferente para los entornos de producción y de ensayo, pero el historial de implementación de las compilaciones recientes es el mismo independientemente del entorno.<br/>
![][35]

## <a name="step7"> </a>Paso 7: ejecutar pruebas unitarias

Este paso se aplica únicamente a las aplicaciones web, no a los servicios en la nube. Para establecer una prueba de calidad en su implementación, puede ejecutar pruebas unitarias y, en caso de error, puede detener la implementación.

1.  En Visual Studio, agregue un proyecto de prueba unitaria.<br/>
![][39]

2.  Agregue referencias de proyecto al proyecto que desee probar.<br/>
![][40]

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


5.  Establezca **Suspender compilación tras error de pruebas** en True. Esto significa que la implementación no se producirá a menos que se superen las pruebas.<br/>
![][41]

6.  Ponga en cola una nueva compilación.<br/>
![][42]
<br/>
![][43]

7. Mientras se desarrolla la compilación, compruebe su progreso.<br/>
![][44]
<br/>
![][45]

7. Una vez terminada la compilación, compruebe los resultados de la prueba.<br/>
![][46]
<br/>
![][47]

8.  Pruebe a crear una prueba que provoque un error. Agregue una nueva prueba copiando la primera, cámbiele el nombre y comente que la línea de código que indica NotImplementedException es una excepción esperada.

		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}

9. Proteja el cambio para poner en cola una nueva compilación.<br/>
![][48]

10. Consulte los resultados de la prueba para ver los detalles del error.<br/>
![][49]
<br/>
![][50]

Para obtener más información sobre las pruebas unitarias en Visual Studio Online, consulte [Ejecución de pruebas unitarias en una compilación](http://go.microsoft.com/fwlink/p/?LinkId=510474).

Para obtener más información, consulte [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Si usa Git, consulte [Uso compartido del código en Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Implementación continua mediante GIT en el Servicio de aplicaciones de Azure](../web-sites-publish-source-control.md).

[Paso 1: crear un proyecto de equipo.]: #step1
[Paso 2: proteger un proyecto para el control de código fuente.]: #step2
[Paso 3: conectar el proyecto a Azure.]: #step3
[Paso 4: realizar cambios, desencadenar una recompilación y volver a implementar.]: #step4
[Paso 5: volver a implementar una compilación anterior (opcional)]: #step5
[ Paso 6: cambiar la implementación de producción (solo servicios en la nube)]: #step6
[Paso 7: ejecutar pruebas unitarias (opcional)]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png


[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
 

<!-----HONumber=July15_HO4-->