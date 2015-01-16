<properties title="Scripting Elastic Scale with Scripts" pageTitle="Scripting de Escalado elástico con scripts" description="Tareas de escala elástica de script con runbooks de PowerShell y el servicio de automatización de Azure." metaKeywords="Azure SQL Database, elastic scale, powershell scripts" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Administración de Escalado elástico con scripts


## Servicio de automatización de Azure 

[Automatización de Azure](http://azure.microsoft.com/es-es/documentation/services/automation/) brinda un servicio de ejecución de flujo de trabajo de PowerShell poderoso y muy necesario a la plataforma de Azure. Ahora puede automatizar las tareas de mantenimiento que resultan difíciles desde dentro de la experiencia común del portal de Azure.  Simplemente cree un flujo de trabajo de PowerShell (llamado **runbook** en Automatización de Azure), cárguelo a la nube y programe cuándo desee que se ejecute el runbook. Este documento proporciona configuración de un extremo a otro de Automatización de Azure para un puñado de ejemplos de elasticidad de partición. Para obtener más información, consulte el [anuncio de vista previa](http://blogs.technet.com/b/in_the_cloud/archive/2014/04/15/announcing-the-microsoft-azure-automation-preview.aspx). O bien, puede registrarse para tener una [suscripción](https://account.windowsazure.com/PreviewFeatures?fid=automation) a Azure.

En este ejemplo, Automatización de Azure se usa como el marco de trabajo de programación y ejecución de la carga de trabajo. Piense en Automatización de Azure como su [Agente SQL en la nube](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/). 

Además de este documento, presentamos otros recursos:

* [Introducción a la automatización de Azure](http://azure.microsoft.com/es-es/documentation/articles/automation-create-runbook-from-samples/)
* [Paso a paso: Introducción a la NUEVA característica de vista previa de Automatización de Microsoft Azure](http://blogs.technet.com/b/keithmayer/archive/2014/04/04/step-by-step-getting-started-with-windows-azure-automation.aspx) 
* [Automatización de Microsoft Azure](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx) 
* Formule preguntas específicas sobre Automatización de Azure en el [foro sobre Automatización](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).  


## Requisitos previos

[Suscríbase](http://azure.microsoft.com/es-es/services/preview/) y [familiarícese](http://azure.microsoft.com/es-es/documentation/articles/automation-create-runbook-from-samples/) con el servicio de vista previa de Automatización de Microsoft Azure. 


## Archivos de PowerShell de elasticidad de partición

El siguiente conjunto de archivos de PowerShell contiene los comandos básicos para llevar a cabo los escenarios de escalado vertical y horizontal mediante Automatización de Azure. 

Estos ejemplos ilustran cómo usar los módulos de ejemplo de PowerShell para realizar tareas básicas de la elasticidad de partición. En combinación con el servicio Automatización de Microsoft Azure y los runbooks de Automatización de Azure correspondientes, puede crear trabajos automatizados y programados que aprovisione una nueva partición y/o cambien el nivel de rendimiento de particiones específicas basándose en un conjunto de reglas. 

**SetupShardedEnvironment.ps1**: este runbook de PowerShell realiza una configuración única de un entorno particionado completo con un administrador de mapa de particiones y un mapa de particiones de intervalos. 

**ProvisionByDate.ps1**: aprovisiona una base de datos nueva antes de la carga de trabajo del día siguiente. Se crea la base de datos con nombre basada en la marca de fecha (AAAAMMDD) y se registra con el administrador de mapa de particiones como un intervalo [AAAAMMDD, AAAAMMDD + 1D). 

**ProvisionBySize.ps1**: aprovisiona una base de datos nueva cuando la base de datos actual queda sin capacidad. 

**ReduceServiceTier.ps1**: se itera a través de las particiones en un mapa de particiones proporcionado y determina si cada partición individual es candidata para la reducción del nivel de rendimiento. Dos criterios determinan si la partición es o no candidato: 1) el nivel de servicio actual de la partición y 2) la antigüedad de la base de datos.  

**ShardManagement.psm1**: proporciona un conjunto de métodos para interactuar con el administrador de mapa de particiones. 

**SqlDatabaseHelpers.psm1**: proporciona un conjunto de métodos para interactuar con las bases de datos SQL de Azure. 

**ShardElasticity.psm1**: proporciona un conjunto de métodos para realizar operaciones de escalado horizontal y también de escalado vertical. 

**ShardElasticityModule.psd1**: proporciona un conjunto de métodos para interactuar con Escalado elástico y Base de datos SQL de Azure. 

## Costos

Tenga en cuenta que la ejecución de los scripts de ejemplo de PowerShell generará la creación de bases de datos que significarán costes reales para el propietario de la suscripción. Las bases de datos de Base de datos SQL de Azure subyacentes se [cobrarán a una tarifa](http://azure.microsoft.com/es-es/pricing/details/sql-database/) no distinta a la de otra base de datos de Base de datos SQL de Azure.  A partir del 1° de noviembre, los costos son: 

* El runbook SetupShardedEnvironment crea el administrador de mapa de particiones en una base de datos básica (US$0,0069/hora) y aprovisiona también la primera partición en una base de datos básica (US0,0069/hora). 

* Tanto el runbook ProvisionBySize como el runbook ProvisionByDate aprovisionarán una base de datos S0 estándar (US$0,0208/hora).  Para contrarrestrar estos costos, si se ejecutan en conjunto con el runbook ReduceServiceTier, el nivel de servicio de la base de datos recién aprovisionada se reducirá desde un S0 estándar (US$0,0208/hora) a una básica (US$0,0069/hora) después del plazo de un día. 

Por último, dentro del ámbito de los ejemplos proporcionados, el uso de [Automatización de Azure](http://azure.microsoft.com/es-es/pricing/details/automation/) actualmente no cobrará ningún cargo al propietario de la suscripción.  Consulte la [página de precios de Automatización de Azure](http://azure.microsoft.com/es-es/pricing/details/automation/) para obtener más detalles. 

## Carga de los runbooks 

1. Descargue el archivo **ShardElasticity.zip** y extraiga el contenido.
2. [Agregue referencias a los archivos binarios de Escalado elástico usando NuGet.](./sql-database-elastic-scale-add-references-visual-studio.md)
3. Encuentre el binario de cliente de Escalado elástico (**Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll**).
4. Coloque el DLL en la carpeta ShardElasticityModule y comprima la carpeta. 
3. En la cuenta de Automatización de Azure, cargue el archivo ShardElasticityModule.zip como un **recurso**. 
4. En Automatización de Azure, cree una **credencial de recurso** llamada *ElasticScaleCredential* que contiene el nombre de usuario y la contraseña para el servidor de Base de datos SQL de Azure. 
5. Cree una **variable de recurso** llamada *SqlServerName* para el nombre del servidor de Base de datos SQL de Azure completo. 
5. Cargue **SetupShardedEnvironment.ps1**, **ProvisionBySize.ps1**, **ProvisionByDate.ps1** y **ProvisionByDate.ps1** como runbooks. 
6. Como una operación única, pruebe el runbook **SetupShardedEnvironment.ps1** para configurar el entorno particionado. 
7. Publique uno o más de los runbooks restantes y vincule los runbooks a una programación. 
8. Observe la salida del runbook a través de la pestaña **TRABAJOS**. 

Si las instrucciones de ejemplo rápido no fueron correctas, consulte las instrucciones de ejemplo detallado que aparecen a continuación.  

##  Utilización de runbooks

1. Cree y empaquete un módulo de PowerShell 
2. Cree una cuenta de Automatización de Microsoft Azure 
3. Cargue el módulo de PowerShell en Automatización de Azure como recurso 
4. Cree una credencial de Automatización de Azure y recursos variables 
5. Cargue runbooks de PowerShell en Automatización de Azure 
6. Configure un entorno particionado 
7. Pruebe los runbooks de Automatización 
8. Publicar los runbooks 
9. Programar los runbooks 


## Cree y empaquete un módulo de PowerShell 

El primer paso es crear un módulo de PowerShell que hace referencia a los ensamblados de Escalado elástico y empaquete este módulo de manera tal que esté listo para cargarlo al servicio Automatización de Azure como recurso. 

1. Descargue el archivo "ShardElasticity.zip".
2. Extraiga todo el contenido.

    ![Extract all][1]
3. Obtenga el DLL de cliente de Escalado elástico (es decir, Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll) y copie los siguientes archivos en la carpeta "ShardElasticityModule" local que descargó en el paso 1. Puede hacer esto de dos maneras: 1) descargue el DLL a través del paquete NuGet de Escalado elástico[vínculo] o 2) desde el proyecto Kit de inicio de Escalado elástico [vínculo] (debe estar integrado); vaya a \bin\Debug\ para obtener el DLL.

    ![Obtain Dll][2]

4. Comprima la carpeta ShardElasticityModule. 

Nota: Automatización de Azure requiere varias convenciones de nombres: dado el nombre del módulo ShardElasticityModule.psm1, el nombre del archivo .zip debe coincidir exactamente (ShardElasticityModule.zip). El archivo .zip contiene la carpeta ShardElasticityModule (el nombre coincide con el nombre del módulo) el que, a su vez, contiene el archivo psm1. Si no se sigue esta estructura, Automatización de Azure no podrá desempaquetar el módulo.

5.    Una vez que haya comprobado que el contenido y la estructura de la carpeta comprimida coinciden con los requisitos, pase al siguiente paso. Debe verse de la siguiente manera:

    ![dll][3]


## Suscríbase a la vista previa de Automatización de Azure

1. Vaya a [Características de vista previa de Azure](http://azure.microsoft.com/es-es/services/preview/).
    
2. Haga clic en **Pruébelo**.

    ![Click Try It][8]

2. Vaya al[ Portal de Microsoft Azure](https://manage.windowsazure.com/microsoft.onmicrosoft.com).
3. Haga clic en **Automatización**.

    ![Automation][4]
4. En la parte inferior de la pantalla, haga clic en **Crear**. 
5. En el mensaje que aparece a continuación, escriba un nombre de cuenta válido y haga clic en la marca de verificación que aparece en la esquina inferior derecha del cuadro.

    ![Prompt][5] 
5. Siga con el paso siguiente. Si se realiza correctamente, debe verse como el gráfico que aparece a continuación.

    ![success][6]

## Cargue el módulo de PowerShell en Automatización de Azure como recurso 

Cargue el módulo de PowerShell anterior a su cuenta de Automatización de Azure. Por ejemplo, el módulo contiene un conjunto de funciones de Elasticidad de partición y DLL de Escalado elástico a los que se pueden hacer referencia desde los runbooks. 

1. Haga clic en **RECURSOS** en la cinta que aparece en la parte superior de la pantalla.
2. Haga clic en **IMPORTAR MÓDULO** en la parte inferior de la página. 
3. Haga clic en **BUSCAR ARCHIVO...** y ubique el archivo **ShardElasticityModule.zip** anterior. 
4. Una vez elegido el archivo correcto, haga clic en la marca de verificación que aparece en la esquina inferior derecha del cuadro para importarlo. El servicio Automatización de Azure importa el módulo. 
5. Siga con el paso siguiente. Si se realiza correctamente, debe verse como este gráfico. Si el módulo no se importó correctamente, asegúrese de que el archivo .zip coincide con las convenciones descritas anteriormente.

    ![Assets][10] 
      
## Cree una credencial de Automatización de Azure y recursos variables 

En lugar de codificar de forma rígida las credenciales y variables de uso común en los runbooks. Automatización de Azure puede crear credenciales y recursos variables respectivamente a los que se puede hacer referencia a través de muchos runbooks. Por ejemplo, cambiar una contraseña luego ocurre en solo una ubicación. 

1. Seleccione la nueva cuenta de Automatización de Azure que acaba de crear.
2. En la cuenta **ShardElasticityExamples**, haga clic en **RECURSOS** en la cinta.
3. Haga clic en **AGREGAR CONFIGURACIÓN** en la parte inferior de la pantalla.  
4. Haga clic en **AGREGAR CREDENCIAL**. 

    ![Add credential][9]
4. Seleccione **Credencial de Windows PowerShell** como el **TIPO DE CREDENCIAL** y **ElasticScaleCredential** como el nombre. La descripción es opcional.  
5. Haga clic en la flecha que aparece en la esquina inferior derecha del cuadro. 

Nota: Para usar los runbooks sin modificaciones, use el texto de nombres de variables indicado en las instrucciones. Los runbooks hacen referencia a los nombres de variables. 
5. Inserte el nombre del usuario y la contraseña (dos veces) del servidor de Base de datos SQL de Azure en el que desea ejecutar los ejemplos de Elasticidad de partición. 
 
6. Para crear el recurso variable, haga clic en **AGREGAR CONFIGURACIÓN** y, a continuación, seleccione **AGREGAR VARIABLE**. 

    ![Add variable][7]
7. Para este tutorial, cree una variable para el nombre de servidor de Base de datos SQL de Azure completo bajo el cual residirán el administrador de mapa de particiones y las bases de datos de particiones. Seleccione **Cadena** como el **TIPO DE VARIABLE** y escriba **SqlServerName**. Haga clic en la flecha para continuar. 
8. Escriba el nombre de servidor de Base de datos SQL de Azure completo como el VALOR y haga clic en la marca de verificación. 
9. Ahora ha creado una credencial y un recurso de variable que se usarán en los runbooks de Elasticidad de partición.  Siga con el paso siguiente. Si se realiza correctamente, debe verse de la siguiente manera: 
    

## Cargue runbooks de PowerShell en Automatización de Azure 

Cargue los cuatro runbooks de PowerShell de ejemplo proporcionados. 

1. Para cargar un nuevo runbook de Automatización de Azure, haga clic en **RUNBOOKS** en la cinta. 
2. En la parte inferior de la pantalla, haga clic en **IMPORTAR**. 
3. Navegue a la carpeta que contiene el archivo, seleccione **SetupShardedEnvironment.ps1** y haga clic en la marca de verificación. 
4. Repita los pasos 2 y 3 para los tres runbooks de PowerShell restantes (**ProvisionByDate.ps1**, **ProvisionBySize.ps1** y **ReduceServiceTier.ps1**). 
5. Siga con el paso siguiente. 

## Configure un entorno particionado 

El siguiente paso es ejecutar el runbook **SetupShardedEnvironment** que aprovisiona un entorno particionado, completo con una base de datos de administrador de mapa de particiones, un mapa de particiones de intervalo y una partición para el día actual.   

1. Para seleccionar el runbook **SetupShardedEnvironment**, haga clic en su nombre. 
2. Haga clic en **AUTOR** en la cinta. 
3. En esta pantalla verá el código (y podrá editarlo, si así lo desea) que compone el runbook. Para configurar el entorno particionado, haga clic en el botón **PROBAR** que aparece en la parte inferior de la pantalla. Confirme que desea guardar y probar el runbook. 
4. Puede ver el estado del trabajo en el panel de salida. Cuando termine, el servidor de Base de datos SQL de Azure que especificó se rellenará con una base de datos de administrador de mapa de particiones, además de una base de datos de particiones. El runbook **SetupShardedEnvironment** tiene como única finalidad aprovisionar el entorno particionado y no su ejecución en una programación recurrente. Siga con el paso siguiente.  

## Pruebe los runbooks de Automatización 

Pruebe la ejecución correcta de cada uno de los runbooks antes de publicar y programar el runbook. 

1. Haga clic en **RUNBOOK** en la cinta que aparece en la parte superior de la página. 
2. Haga clic en el runbook **ProvisionByDate**.
3. Haga clic en **AUTOR** en la cinta que aparece en la parte superior de la página. 
4. Haga clic en **GUARDAR **y luego en **PROBAR**.
5. Repita para **ReduceServiceTier**. 

    Tenga en cuenta que, dado que tanto **ProvisionBySize** como **ProvisionByDate** aprovisionan nuevas particiones (usando algoritmos distintos), no es necesario ejecutar **ProvisionByDate** en este momento. 

## Publicar los runbooks 
El siguiente paso es publicar el runbook para que se pueda programar para ejecutarse de manera periódica. 

1. Haga clic en **PUBLICAR** en la parte inferior de la página. 
2. Haga clic en **Publicado**. 
3. Siga con el paso siguiente.
 
## Programar los runbooks 

El paso final es crear y vincular una programación al runbook publicado anteriormente. 

1. Haga clic en **PROGRAMAR** en la parte superior de la página. 
2. Haga clic en **VÍNCULO A UNA NUEVA PROGRAMACIÓN**.
3. Póngale un nombre adecuado a la programación y haga clic en el botón de flecha a la derecha.
4. Configure la programación.
5. Cuando termine, haga clic en la marca de verificación que aparece en la parte inferior del cuadro.
6. Una vez que se ha ejecutado el trabajo según la programación establecida anteriormente, haga clic en la opción **TRABAJOS **de la cinta que aparece en la parte superior de la página y, a continuación, seleccione el trabajo programado. 

## Conclusión 

Ha creado y empaquetado correctamente un módulo de PowerShell, ha cargado el módulo de PowerShell a Automatización de Azure como recurso y ha creado, probado, publicado y programado un runbook.  Para supervisar el estado del runbook, use las pestañas del panel y de los trabajos. 

Los ejemplos proporcionados solo describen una pequeña parte de lo que es posible realizar cuando se combinan Escalado elástico, Base de datos SQL de Azure y Automatización de Azure. Experimente y básese en estos ejemplos para permitir que su aplicación de Escalado elástico de Base de datos SQL de Azure escale de manera horizontal, vertical o ambas. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-scripting/success.png
[7]: ./media/sql-database-elastic-scale-scripting/add-variable.png
[8]: ./media/sql-database-elastic-scale-scripting/sign-up.png
[9]: ./media/sql-database-elastic-scale-scripting/add-credential.png
[10]: ./media/sql-database-elastic-scale-scripting/assets.png
