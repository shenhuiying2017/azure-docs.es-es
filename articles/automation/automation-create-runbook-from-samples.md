<properties
	pageTitle="Introducción a Automatización de Azure | Microsoft Azure"
	description="Vea cómo importar y ejecutar un trabajo de automatización en Azure."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="09/08/2015"
	ms.author="bwren"/>


# Introducción a la Automatización de Azure

## ¿Qué es Automatización de Azure?

Automatización de Microsoft Azure ofrece una forma de que los usuarios automaticen las tareas manuales, propensas a errores, con una ejecución prolongada y repetidas con frecuencia que se realizan normalmente en un entorno en la nube. Puede crear, supervisar, administrar e implementar recursos en el entorno de Azure mediante runbooks, que se basan en flujos de trabajo de Windows PowerShell. En este artículo, recorrerá un tutorial para ejecutar un runbook de ejemplo sencillo. A continuación, encontrará recursos para explorar las capacidades más avanzadas del servicio.

## Tutorial
En este tutorial se enseña a crear una cuenta de automatización, a importar un runbook "Hello World" de ejemplo en Automatización de Azure, a ejecutar dicho runbook y, a continuación, a ver el resultado.

Para completar este tutorial, deberá tener una suscripción de Azure. Si aún no la tiene, puede [activar los beneficios de la suscripción a MSDN](../pricing/member-offers/msdn-benefits-details/), o bien [registrarse para obtener una evaluación gratuita](../pricing/free-trial.md)</a>.

[AZURE.INCLUDE [automation-note-authentication](../../includes/automation-note-authentication.md)]

## Tutorial en vídeo

Se trata del tutorial en formato de vídeo.

[AZURE.VIDEO get-started-with-azure-automation]

## <a name="automationaccount"></a>Creación de una cuenta de automatización

Una cuenta de automatización es un contenedor para los recursos de automatización de Azure. Proporciona una manera de separar los entornos u organizar aún más los flujos de trabajo. Para obtener más información, consulte [Cuentas de automatización](http://aka.ms/runbookauthor/azure/automationaccounts) en la biblioteca de automatización. Si ya ha creado una cuenta de automatización, puede omitir este paso.

1.	Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com).

2.	En el Portal de Azure, haga clic en **Creación de una cuenta de Azure**.

	![Creación de cuenta](./media/automation-create-runbook-from-samples/automation_01_CreateAccount.png)

3.	En la página **Agregar una nueva cuenta de automatización**, escriba un nombre y elija una región para la cuenta. La región especifica dónde se almacenarán los recursos de automatización de la cuenta. Esto no afectará a la funcionalidad de su cuenta, pero sus runbooks se pueden ejecutar más rápidamente si la región de la cuenta está cerca de donde se almacenan los demás recursos de Azure. Cuando esté listo, haga clic en la marca de verificación.

	![Incorporación de una nueva cuenta](./media/automation-create-runbook-from-samples/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>Importación de un runbook de la Galería de runbooks

La [Galería de runbooks](http://aka.ms/runbookgallery) incluye runbooks de ejemplo que se pueden importar directamente en una cuenta de Automatización de Azure, lo que le permite aprovechar el trabajo de otros usuarios de PowerShell y de Automatización de Azure. En este paso, usará la galería para importar el runbook de ejemplo "Hello World".

4.	En la página **Automatización**, haga clic en la nueva cuenta que acaba de crear.

	![Nueva cuenta](./media/automation-create-runbook-from-samples/automation_03_NewAutoAcct.png)

5.	Haga clic en **RUNBOOKS**.

	![Pestaña Runbooks](./media/automation-create-runbook-from-samples/automation_04_RunbooksTab.png)

6.	Haga clic en **Nuevo** > **Runbook** > **Desde la galería**.

	![Galería de runbooks](./media/automation-create-runbook-from-samples/automation_05_ImportGallery.png)

7.  Seleccione la categoría **Tutorial** y después **Hello World para Automatización de Azure**. Haga clic en el botón de flecha derecha.

	![Importación del runbook](./media/automation-create-runbook-from-samples/automation_06_ImportRunbook.png)

8.  Revise el contenido del runbook y haga clic en el botón de flecha derecha.

	![Definición del runbook](./media/automation-create-runbook-from-samples/automation_07_RunbookDefinition.png)

8.	Revise los detalles del runbook y haga clic en el botón de marca de verificación.

	![Detalles del runbook](./media/automation-create-runbook-from-samples/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>Publicación del runbook

El runbook se importa por primera vez en el modo Borrador. Esto significa que puede continuar trabajando en él antes de autorizarlo como una nueva versión que se puede ejecutar. Puesto que este runbook de ejemplo no requiere configuración adicional, lo publicará ahora como está. Para obtener más información, vea [Publicación de un Runbook](http://aka.ms/runbookauthor/azure/publishrunbook).

9.	Cuando el runbook haya terminado de importarse, haga clic en **Write-HelloWorld**.

	![Runbook importado](./media/automation-create-runbook-from-samples/automation_07_ImportedRunbook.png)

9.	Haga clic en **AUTOR** y, a continuación, haga clic en **BORRADOR**.

	Puede modificar el contenido de un runbook en el modo Borrador. Para este runbook, no tiene que realizar modificaciones.

	![Borrador de autor](./media/automation-create-runbook-from-samples/automation_08_AuthorDraft.png)

10.	Haga clic en **PUBLICAR** para promocionar el runbook, marcándolo como preparado para que esté listo para el uso de producción.

	![Publicar](./media/automation-create-runbook-from-samples/automation_085_Publish.png)

11.	Cuando se le pida confirmación, haga clic en **Sí**.

	![Solicitud de guardado y publicación](./media/automation-create-runbook-from-samples/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>Inicio del runbook

Con el runbook importado y publicado, ahora puede ejecutarlo y, a continuación, revisar la salida. Para obtener más información, consulte [Inicio de un runbook](http://aka.ms/runbookauthor/azure/startrunbook) y [Salida y mensajes de un runbook](http://aka.ms/runbookauthor/azure/runbookoutput).

12.	Con el runbook **Write-HelloWorld** abierto, haga clic en **INICIAR**.

	![Publicado](./media/automation-create-runbook-from-samples/automation_10_PublishStart.png)

13.	En la página **Especificar los valores de parámetros del runbook**, escriba un valor para **Nombre** que se usará como parámetro de entrada para el script Write-HelloWorld.ps1 y, a continuación, haga clic en la marca de verificación.

	![Parámetros de runbook](./media/automation-create-runbook-from-samples/automation_11_RunbookParams.png)

14.	Haga clic en **JOBS** para comprobar el estado del trabajo de runbook que acaba de iniciar y, a continuación, haga clic en la marca de tiempo en la columna **INICIO DEL TRABAJO** para ver el resumen del trabajo.

	![Estado de runbook](./media/automation-create-runbook-from-samples/automation_12_RunbookStatus.png)

15.	En la página **RESUMEN** puede ver el resumen, los parámetros de entrada y la salida del trabajo.

	![Resumen de Runbook](./media/automation-create-runbook-from-samples/automation_13_RunbookSummary_callouts.png)

¡Enhorabuena! Ha finalizado el tutorial.

## <a name="nextsteps"></a>Pasos siguientes
1. El runbook simple de este tutorial *no administra los servicios de Azure*. La mayoría de los runbooks usarán [cmdlets de Azure](http://msdn.microsoft.com/library/jj156055.aspx) a tal efecto, lo que requiere autenticación para la suscripción de Azure. Siga las instrucciones de [Configuración de Azure para la administración de runbooks](http://aka.ms/azureautomationauthentication) para configurar su suscripción de Azure para trabajar con estos cmdlets.  
2. Consulte los [recursos](#resources) enumerados a continuación para obtener más información acerca de las capacidades de Automatización de Azure.
3. Suscríbase al [blog de Automatización de Azure](http://azure.microsoft.com/blog/tag/azure-automation) para mantenerse al día con las novedades del equipo de Automatización de Azure.

## <a name="resources"></a>Recursos

Hay una amplia gama de recursos disponibles para aprender mucho más acerca de Automatización de Azure y crear sus propios runbooks.

- La [biblioteca de Automatización de Azure](http://go.microsoft.com/fwlink/p/?LinkId=392860) proporciona documentación completa sobre la configuración y administración de Automatización de Azure y para crear sus propios runbooks.
- Los [cmdlets de Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) proporcionan información para automatizar operaciones de Azure mediante Windows PowerShell. Los runbooks usan estos cmdlets para trabajar con recursos de Azure.
- El [blog de Automatización de Azure](http://azure.microsoft.com/blog/tag/azure-automation) proporciona la información más reciente sobre Automatización de Azure de Microsoft.
- El [foro de automatización](http://go.microsoft.com/fwlink/p/?LinkId=390561) le permite publicar preguntas acerca de Automatización de Azure que serán atendidas por Microsoft y la comunidad de automatización.


## Runbooks de utilidades y ejemplos

Microsoft y la comunidad de Automatización de Azure proporcionan runbooks de ejemplos, que pueden ayudarle a empezar a crear sus propias soluciones, y runbooks de utilidades, que puede usar como bloques de creación para tareas de automatización más grandes. Puede descargar estos runbooks desde el [Centro de scripts](http://azure.microsoft.com/documentation/scripts/) o importarlos directamente en Automatización de Azure mediante la [Galería de runbooks](http://aka.ms/runbookgallery).


## Comentarios

**Envíenos sus comentarios.** Si está buscando una solución de runbook o un módulo de integración de Automatización de Azure, publique una solicitud de script en el centro de scripts. Si tiene comentarios o solicitudes de características para Automatización de Azure, publíquelos en [Voz del usuario](http://feedback.windowsazure.com/forums/34192--general-feedback). Gracias.

<!---HONumber=Sept15_HO4-->