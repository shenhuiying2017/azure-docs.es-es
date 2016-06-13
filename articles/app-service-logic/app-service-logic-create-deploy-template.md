<properties
   pageTitle="Creación de plantillas de implementación de Aplicaciones lógicas | Microsoft Azure"
   description="Obtenga información acerca de cómo se crean las plantillas de implementación de Aplicaciones lógicas y cómo se pueden usar para la administración de versiones."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/25/2016"
   ms.author="jehollan"/>
   
# Creación de plantillas de implementación de Aplicaciones lógicas

Una vez creada una aplicación lógica, puede que desee crearla en forma de una plantilla de recursos de Azure, con el fin de poder implementar fácilmente la aplicación lógica en cualquier entorno o grupo de recursos que necesite. Para obtener una introducción a las plantillas de Resource Manager, asegúrese de consultar los artículos [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md) e [Implementación de recursos con plantillas de Azure Resource Manager](../resource-group-template-deploy.md)

## Información general de las plantillas de implementación de aplicaciones lógicas

Una aplicación lógica consta de 3 componentes básicos:

* **Recurso de aplicación lógica**: recurso que contiene información sobre aspectos como el plan de precios, la ubicación y la definición de flujo de trabajo.
* **Definición de flujo de trabajo**: lo que se ve al seleccionar **Código-Vista** (la definición de los pasos del flujo y cómo debe ejecutarlos el motor). Se trata de la propiedad `definition` del recurso de la aplicación lógica.
* **Conexiones**: separe los recursos con el fin de almacenar los metadatos de forma segura en torno a conexiones del conector como cadenas de conexión y tokens de acceso. Se hace referencia a ellas en una aplicación lógica, en la propiedad `parameters` del recurso de la aplicación lógica.

Puede ver todas estas piezas de las aplicaciones lógicas existentes mediante herramientas como el [Explorador de recursos de Azure](http://resources.azure.com).

Para crear una plantilla para una aplicación lógica que pueda trabajar con implementaciones de grupos de recursos, es preciso definir los recursos y parametrizarlos según sea necesario. Por ejemplo, si va a realizar la implementación en un entorno de desarrollo, prueba y producción, es probable que desee usar cadenas de conexión diferentes para una base de datos SQL en cada entorno, o bien realizar la implementación en diferentes suscripciones o grupos de recursos.

## Creación de una implementación de aplicación lógica

Hay algunas herramientas que le ayudarán a crear una plantilla de implementación de aplicación lógica. Para crearla manualmente, tome los recursos anteriores y cree los parámetros necesarios. También puede usar el módulo de PowerShell [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator). El módulo de código abierto funciona mediante la evaluación de la aplicación lógica y las conexiones que utiliza, y la generación de recursos de la plantilla con los parámetros necesarios para implementarlos. Por ejemplo, si tuviera una aplicación lógica que recibiera un mensaje de una cola de bus de servicio y agregara datos a una base de datos de SQL Azure, la herramienta conservaría toda la lógica de orquestación y parametrizaría las cadenas de conexión de SQL y del bus de servicio, con el fin de que se puedan establecer en la implementación.

>[AZURE.NOTE] Las conexiones deben estar dentro del mismo grupo de recursos que la aplicación lógica

### Instalación del módulo de PowerShell de plantilla de aplicación lógica

La forma más fácil de realizar la instalación es a través de la [Galería de PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) con el comando `Install-Module -Name LogicAppTemplate`.

También se puede instalar manualmente:

1. Descargue la versión más reciente de [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Extraiga la carpeta en su carpeta del módulo de PowerShell (normalmente `%UserProfile%\Documents\WindowsPowerShell\Modules`)

Para que el módulo funcione con cualquier token de acceso de inquilino y suscripción, se recomienda utilizarlo con la herramienta de línea de comandos [armclient](https://github.com/projectkudu/ARMClient). [Aquí](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) se puede encontrar más información sobre `armclient`.

### Generación de una plantilla de aplicación lógica a través de PowerShell

Después de la instalación se puede generar una plantilla con el siguiente comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

Donde `$SubscriptionId` es el identificador de la suscripción de Azure. Esta línea, en primer lugar, obtiene un token de acceso a través de `armclient`, lo canaliza al script de PowerShell y, luego, genera la plantilla en un archivo `.json`.

## Adición de parámetros a una plantilla de aplicación lógica

Una vez que tenga una plantilla de aplicación lógica, puede agregar o modificar todos aquellos parámetros adicionales que pueda necesitar. Por ejemplo, si la definición incluye un identificador de recurso a la función o flujo de trabajo anidado de Azure en el que planea implementar en una sola implementación, agregue más recursos a la plantilla y parametrice los identificadores según sea necesario. Lo mismo sucede con las referencias a las API personalizadas o a los puntos de conexión de Swagger que espera implementar con cada grupo de recursos.

## Implementación de una plantilla de aplicación lógica

Una vez que tenga una plantilla, puede realizar la implementación mediante cualquier número de herramientas, entre las que se incluyen PowerShell, API de REST, Visual Studio, Release Management o la implementación de plantillas del Portal de Azure. [Aquí](../resource-group-template-deploy.md) puede encontrar más información sobre la implementación. También se recomienda crear un [archivo de parámetros](../resource-group-template-deploy.md#parameter-file) para almacenar los valores del parámetro.

### Autorización de conexiones de OAuth

Después de la implementación, la aplicación lógica funcionará completamente con parámetros válidos, pero será preciso que las conexiones de OAuth estén autorizadas para generar un token de acceso válido. Para ello, abra la aplicación lógica en el diseñador y autorice las conexiones, o bien si desea automatizar la operación, también puede usar un script para consentir cada conexión de OAuth. En nuestro GitHub, en el proyecto [Connection Auth](https://github.com/logicappsio/LogicAppConnectionAuth), se puede encontrar un script de ejemplo.

## Uso de Visual Studio Release Management

Un escenario común para implementar y administrar entornos es usar una herramienta como Visual Studio Release Management con una plantilla de implementación de aplicación lógica. VSTS incluye una tarea de [implementación de grupos de recursos de Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que se puede agregar a cualquier canalización de la compilación o versión. Es preciso tener una [entidad de servicio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) para implementar la autorización y, a continuación, poder generar la definición de la versión.

1. En **Liberar**, seleccione iniciar un **nueva definición** con una definición **vacía**.

    ![][1]

1. Elija cualquier artefacto que necesite. Es probable que sea la plantilla de aplicación lógica generada manualmente o como parte del proceso de compilación.
1. Agregue una tarea **Implementación de un grupo de recursos de Azure**.
1. Configure una [entidad de servicio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) y haga referencia a los archivos de **plantilla** y de **parámetros de la plantilla**.
1. Siga compilando los pasos del proceso de lanzamiento de otros entornos, pruebas automatizadas o aprobadores según sea necesario.
    
<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

<!---HONumber=AcomDC_0601_2016-->