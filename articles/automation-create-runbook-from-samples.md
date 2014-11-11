<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Introducción a la automatización de Azure

La automatización de Microsoft Azure ofrece una forma de que los desarrolladores automaticen las tareas manuales, propensas a errores, con una ejecución prolongada y repetidas con frecuencia que se realizan normalmente en un entorno en la nube. Puede crear, supervisar, administrar e implementar recursos en el entorno de Azure mediante runbooks, que son flujos de trabajo de Windows PowerShell que se encuentran incluidos. Para obtener más información acerca de la automatización, consulte la [guía de información general de automatización][guía de información general de automatización].

En este tutorial se realiza un recorrido por los pasos para la importación de un runbook "Hello World" de ejemplo en la automatización de Azure, la ejecución de un runbook y, a continuación, la visualización del resultado.

> [WACOM.NOTE] Para obtener información sobre cómo automatizar las operaciones de Azure mediante los [cmdlet PowerShell de Azure][cmdlet PowerShell de Azure], consulte [Automatización de Azure: autenticación en Azure mediante Azure Active Directory][Automatización de Azure: autenticación en Azure mediante Azure Active Directory].

## Runbooks de utilidades y ejemplos

El equipo de automatización de Azure ha creado una serie de ejemplos de runbook para ayudarle a comenzar con la automatización. Estos ejemplos incluyen conceptos de automatización básicos y se han creado para ayudarle a saber cómo escribir sus propios runbooks.

El equipo de automatización también ha creado runbooks de utilidad que puede usar como bloques de compilación para tareas de automatización más grandes.

> [WACOM.NOTE] Es una práctica recomendada escribir runbooks reutilizables, modulares y pequeños. También recomendamos encarecidamente que cree sus propios runbooks de utilidades para escenarios usados frecuentemente después de que se haya familiarizado con la automatización.

Puede ver y descargar los runbooks de utilidades y ejemplos del equipo de automatización en [Recursos de script para profesionales de TI][Recursos de script para profesionales de TI].

## Comunidad de automatización y comentarios

Los runbooks de la comunidad y de otros equipos de Microsoft también se publican en [Recursos de script para profesionales de TI][1].

**Envíenos sus comentarios.** Si está buscando una solución de runbook o módulo de integración de Automatización, envíe una solicitud de script en el centro de scripts. Si tiene alguna idea de alguna característica nueva para la automatización, publíquela en [User Voice][User Voice].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Pasos de alto nivel para este tutorial

1.  [Suscribirse a la vista previa de Automatización][Suscribirse a la vista previa de Automatización]
2.  [Descarga de un runbook de ejemplo][Descarga de un runbook de ejemplo]
3.  [Importación, ejecución y visualización de la salida del runbook de ejemplo][Importación, ejecución y visualización de la salida del runbook de ejemplo]

## <a name="preview"></a>Suscribirse a la versión preliminar de Automatización de Azure

Para comenzar a usar Automatización, necesitará una suscripción a Azure activa con la característica de vista previa de Automatización de Microsoft Azure habilitada.

-   En la página **Características de vista previa**, haga clic en **Probar ahora**.

    ![Habilitar vista previa][Habilitar vista previa]

## <a name="download-sample"></a>Descarga de un runbook de ejemplo desde el centro de scripts

1.  Diríjase a [Recursos de script para profesionales de TI][Recursos de script para profesionales de TI] y, a continuación, haga clic en **Hello World for Azure Automation**.

2.  Haga clic en el nombre de archivo, **Write-HelloWorld.ps1**, junto a **Download** y, a continuación, guarde el archivo en su equipo.

## <a name="import-sample"></a>Importación, ejecución y visualización de la salida del runbook de ejemplo en Automatización de Azure

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].

2.  En el Portal de administración, haga clic en **Create an Automation Account**.

    > [WACOM.NOTE] Si ya ha creado una cuenta de automatización, puede pasar al paso 4.

    ![Creación de cuenta][Creación de cuenta]

3.  En la página **Add a New Automation Account**, especifique un nombre de cuenta y, a continuación, haga clic en la marca de verificación.

    ![Incorporación de una nueva cuenta][Incorporación de una nueva cuenta]

4.  En la página **Automation**, haga clic en la nueva cuenta que acaba de crear.

    ![Nueva cuenta][Nueva cuenta]

5.  Haga clic en **RUNBOOKS**.

    ![Pestaña Runbooks][Pestaña Runbooks]

6.  Haga clic en **IMPORT**.

    ![Importación][Importación]

7.  Diríjase al script **Write-HelloWorld.ps1** que descargó y, a continuación, haga clic en la marca de verificación.

    ![Examinar][Examinar]

8.  Haga clic en **Write-HelloWorld**.

    ![Runbook importado][Runbook importado]

9.  Haga clic en **AUTHOR** y, a continuación, haga clic en **DRAFT**. Para este runbook, no tiene que realizar modificaciones.

    Ahora puede ver el contenido de **Write-HelloWorld.ps1**. Puede modificar el contenido de un runbook en el modo borrador.

    ![Borrador de autor][Borrador de autor]

10. Haga clic en **PUBLISH** para promocionar el runbook de modo que esté listo para el uso de producción.

    ![Publicar][Publicar]

11. Cuando se le solicite guardar y publicar el runbook, haga clic en **Yes**.

    ![Solicitud de guardado y publicación][Solicitud de guardado y publicación]

12. Haga clic en **PUBLISHED** y, a continuación, haga clic en **START**.

    ![Publicado][Publicado]

13. En la página **Specify the runbook parameter values**, escriba un valor para **Name** que se utilizará como parámetro de entrada para el script Write-HelloWorld.ps1 y, a continuación, haga clic en la marca de verificación.

    ![Parámetros de runbook][Parámetros de runbook]

14. Haga clic en **JOBS** para comprobar el estado del trabajo de runbook que acaba de iniciar y, a continuación, haga clic en la marca de tiempo en la columna **JOB START** para ver el resumen de trabajos.

    ![Estado de runbook][Estado de runbook]

15. En la página **SUMMARY** puede ver el resumen, los parámetros de entrada y el resultado del trabajo.

    ![Resumen de Runbook][Resumen de Runbook]

# Administración de Servicios de Azure desde un runbook

En el ejemplo anterior se muestra un runbook sencillo que no administra los Servicios de Azure. Los [cmdlets de Azure][cmdlet PowerShell de Azure] requieren la autenticación en Azure. Puede seguir las instrucciones facilitadas en [Automatización de Azure: autenticación en Azure mediante Azure Active Directory][Automatización de Azure: autenticación en Azure mediante Azure Active Directory] para configurar la suscripción a Azure y realizar la administración a través de Automatización de Azure.

# Otras referencias

-   [Automatización][Automatización]
-   [Authoring Automation Runbooks][Authoring Automation Runbooks]
-   [Automation Forum][Automation Forum]
-   [Automatización de Azure autenticación en Azure mediante Azure Active Directory][Automatización de Azure: autenticación en Azure mediante Azure Active Directory].

  [guía de información general de automatización]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [cmdlet PowerShell de Azure]: http://msdn.microsoft.com/es-es/library/jj156055.aspx
  [Automatización de Azure: autenticación en Azure mediante Azure Active Directory]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [Recursos de script para profesionales de TI]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [create-account-note]: ../includes/create-account-note.md
  [Suscribirse a la vista previa de Automatización]: #preview
  [Descarga de un runbook de ejemplo]: #download-sample
  [Importación, ejecución y visualización de la salida del runbook de ejemplo]: #import-sample
  [Habilitar vista previa]: ./media/automation/automation_00_EnablePreview.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Creación de cuenta]: ./media/automation/automation_01_CreateAccount.png
  [Incorporación de una nueva cuenta]: ./media/automation/automation_02_addnewautoacct.png
  [Nueva cuenta]: ./media/automation/automation_03_NewAutoAcct.png
  [Pestaña Runbooks]: ./media/automation/automation_04_RunbooksTab.png
  [Importación]: ./media/automation/automation_05_Import.png
  [Examinar]: ./media/automation/automation_06_Browse.png
  [Runbook importado]: ./media/automation/automation_07_ImportedRunbook.png
  [Borrador de autor]: ./media/automation/automation_08_AuthorDraft.png
  [Publicar]: ./media/automation/automation_085_Publish.png
  [Solicitud de guardado y publicación]: ./media/automation/automation_09_SavePubPrompt.png
  [Publicado]: ./media/automation/automation_10_PublishStart.png
  [Parámetros de runbook]: ./media/automation/automation_11_RunbookParams.png
  [Estado de runbook]: ./media/automation/automation_12_RunbookStatus.png
  [Resumen de Runbook]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [Automatización]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Authoring Automation Runbooks]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [Automation Forum]: http://go.microsoft.com/fwlink/p/?LinkId=390561
