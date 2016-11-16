---
title: "Creación de módulos de integración de Azure Automation | Microsoft Docs"
description: "En este tutorial se explica cómo crear y probar módulos de integración en Azure Automation y se proporcionan ejemplos de uso."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 27798efb-08b9-45d9-9b41-5ad91a3df41e
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ca2343c8915690184e63396afa4e45a22a16ec2b


---
# <a name="azure-automation-integration-modules"></a>Módulos de integración de Automatización de Azure
PowerShell es la principal tecnología que se esconde detrás de Automatización de Azure. Desde que Automatización de Azure se integró en PowerShell, los módulos de PowerShell resultan clave para la extensibilidad de Automatización de Azure. En este artículo, explicaremos los aspectos específicos sobre el uso de los módulos de PowerShell en Automatización de Azure, lo que se conoce como “Módulos de integración”, y los procedimientos recomendados para que pueda crear sus propios módulos de PowerShell y asegurarse de que funcionan como módulos de integración en Automatización de Azure. 

## <a name="what-is-a-powershell-module"></a>¿Qué son los módulos de PowerShell?
Un módulo de PowerShell es un grupo de cmdlets de PowerShell, como **Get-Date** o **Copy-Item**, que se puede usar en la consola, los scripts, los flujos de trabajo y los Runbooks de PowerShell, y un grupo de recursos de DSC de PowerShell, como WindowsFeature o File, que se puede utilizar en las configuraciones de DSC de PowerShell. Toda la funcionalidad de PowerShell se expone a través de cmdlets y recursos de DSC. Todos los cmdlets y recursos de DSC se guardan en una copia de seguridad en un módulo de PowerShell, y muchos de los ellos se envían al propio PowerShell. Por ejemplo, el cmdlet **Get-Date** forma parte del módulo Microsoft.PowerShell.Utility de PowerShell, el cmdlet **Copy-Item** forma parte del módulo Microsoft.PowerShell.Management de PowerShell y el recurso Package de DSC forma parte del módulo PSDesiredStateConfiguration de PowerShell. Estos módulos se suministran con PowerShell. Sin embargo, muchos otros módulos no están incluidos en PowerShell y se distribuyen a través de productos propios o de terceros, como System Center 2012 Configuration Manager, o a través de los lugares que conforman la vasta comunidad de PowerShell, como la Galería de PowerShell.  Los módulos resultan muy útiles, ya que simplifican las tareas complejas gracias a su funcionalidad encapsulada.  Puede obtener más información sobre los [módulos de PowerShell en MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>¿Qué son los módulos de integración de Automatización de Azure?
Los módulos de integración se parecen mucho a los módulos de PowerShell. Sencillamente, se trata de módulos de PowerShell que, de manera opcional, pueden contener un archivo adicional: un archivo de metadatos que especifica un tipo de conexión de Automatización de Azure que se va a utilizar con los cmdlets del módulo en los Runbooks. Tanto si incluyen como si no el archivo opcional, estos módulos de PowerShell pueden importarse en Automatización de Azure para que sus cmdlets estén disponibles y puedan usarse en Runbooks y para que sus recursos de DSC estén disponibles y puedan usarse en las configuraciones de DSC. Automatización de Azure almacena estos módulos en segundo plano y, en tiempo de ejecución del trabajo de compilación de DSC y del trabajo de Runbook, los carga en espacios aislados, donde se ejecutan los Runbooks y se compilan las configuraciones de DSC.  Todos los recursos de DSC de los módulos se sitúan automáticamente en el servidor de extracción de DSC de Automatización para que las máquinas que intentan aplicar las configuraciones de DSC puedan extraerlos.  Azure Automation contiene una serie de módulos de Azure PowerShell listos para usar con los que podrá automatizar directamente la administración de Azure. No obstante, también puede importar fácilmente módulos de PowerShell para cualquier sistema, servicio o herramienta con los que quiera integrarlos. 

> [!NOTE]
> Algunos módulos se suministran como "módulos globales" en el servicio de Automatización. Estos módulos globales están disponibles de fábrica cuando se crea una cuenta de Automatización, y los actualizamos a veces, lo que los expulsa automáticamente de su cuenta de Automatización. Si no desea que se actualicen automáticamente, siempre puede importar el mismo módulo, y que tendrá prioridad sobre la versión del módulo global de dicho módulo que se incluye en el servicio. 
> 
> 

El formato para importar un paquete de módulo de integración es un archivo comprimido que tiene el mismo nombre del módulo y una extensión .zip. El paquete contiene el módulo de Windows PowerShell y todos los archivos auxiliares, como el archivo de manifiesto (.psd1), si es que el módulo tiene uno.

Si el módulo necesita un tipo de conexión de Automatización de Azure, también debería incluir un archivo con el nombre *<ModuleName>*-Automation.json que especifique las propiedades del tipo de conexión. Este archivo json se encuentra dentro de la carpeta del módulo del archivo .zip y contiene los campos de una “conexión” que es necesaria para poder conectarse al sistema o servicio que el módulo representa. De este modo, se creará un tipo de conexión en Automatización de Azure. Con este archivo, puede configurar los nombres y los tipos de campo del tipo de conexión del módulo, además de especificar si los campos deben cifrarse o el cifrado es opcional. A continuación, se incluye una plantilla con el formato del archivo json:

```
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Si ha implementado Service Management Automation y ha creado paquetes de módulos de integración para los Runbooks de Automatización, esta plantilla le resultará familiar. 

## <a name="authoring-best-practices"></a>Procedimientos recomendados sobre la creación de los módulos
El hecho de que los módulos de integración sean básicamente módulos de PowerShell no significa que no existan unos procedimientos específicos para crearlos. Hay una serie de recomendaciones que debe tener en cuenta al crear módulos de PowerShell para facilitar su uso en Automatización de Azure. Algunas de estas recomendaciones son específicas de Automatización de Azure, mientras que otras resultan útiles para que los módulos funcionen bien en el flujo de trabajo de PowerShell, con independencia de si se usa o no Automatización de Azure. 

1. Incluya una sinopsis, una descripción y un identificador URI de ayuda para todos los cmdlets del módulo. En PowerShell, puede definir cierta información de ayuda para los cmdlets de forma que al usuario le resulte más fácil usarlos con el cmdlet **Get-Help** . Por ejemplo, a continuación le mostramos cómo puede definir una sinopsis y un identificador URI de ayuda para un módulo de PowerShell escrito en un archivo .psm1.<br>  
   
    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
   <br> Esta información no solo sirve para proporcionar ayuda cuando se usa el cmdlet **Get-Help** en la consola de PowerShell, sino que también proporcionará ayuda en Automatización de Azure; por ejemplo, al insertar actividades durante la creación de un Runbook. Al hacer clic en “Ver ayuda detallada”, el identificador URI de ayuda se abrirá en otra pestaña del explorador web que esté utilizando para acceder a Automatización de Azure.<br>![Ayuda para los módulos de integración](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. Si el módulo se ejecuta en un sistema remoto, en primer lugar, debería incluir un archivo de metadatos del módulo de integración que defina la información necesaria para conectarse a ese sistema remoto (es decir, el tipo de conexión). b. En segundo lugar, cada cmdlet del módulo debería poder tomar como parámetro un objeto de conexión (una instancia del tipo de conexión).  
    Los cmdlets del módulo resultan más fáciles de usar en Automatización de Azure si permite que se les pase como parámetro un objeto con los campos del tipo de conexión. De este modo, cada vez que los usuarios invoquen un cmdlet, no tendrán que asignar parámetros del recurso de conexión a los parámetros correspondientes del cmdlet. En el ejemplo de Runbook anterior, se utiliza un recurso de conexión Twilio denominado «CorpTwilio» para acceder a Twilio y devolver todos los números de teléfono de la cuenta.  Observe cómo se asignan los campos de la conexión a los parámetros del cmdlet.<br>
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
   <br>
    Existe otra forma más fácil y eficaz de hacer esto, y consiste en pasar directamente el objeto de conexión al cmdlet.
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   <br>
    Para habilitar un comportamiento como este en sus cmdlets, permítales que acepten directamente un objeto de conexión como parámetro en lugar de usar solamente campos de conexión en los parámetros. Normalmente, necesitará un conjunto de parámetros para cada uno de ellos, de forma que los usuarios que no utilicen Automatización de Azure puedan llamar a los cmdlets sin necesidad de crear una tabla hash que actúe como objeto de conexión. En el fragmento de código siguiente, el conjunto de parámetros **SpecifyConnectionFields** se utiliza para pasar las propiedades de los campos de conexión de una en una. **UseConnectionObject** permite pasar directamente la conexión. Como puede ver, el cmdlet Send-TwilioSMS del [módulo de PowerShell de Twilio](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) permite utilizar cualquiera de estos mecanismos para pasar valores: 
   
    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```
   <br>
3. Defina el tipo de salida de todos los cmdlets del módulo. Definir un tipo de salida en un cmdlet permite que IntelliSense le ayude a determinar, en tiempo de diseño, las propiedades de salida del cmdlet, lo que resultará útil durante la creación. Esto resulta especialmente útil durante la creación gráfica de un Runbook de Automatización, donde la información en tiempo de diseño resulta esencial para facilitar la experiencia del usuario con el módulo.<br> ![Tipo de salida de runbooks gráficos](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Esto proporciona una funcionalidad similar a la funcionalidad "type ahead" de la salida de los cmdlets de PowerShell ISE sin necesidad de ejecutarla.<br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. Los cmdlets del módulo no deben tomar tipos de objetos complejos como parámetros. El flujo de trabajo de PowerShell se diferencia del propio PowerShell en que almacena tipos complejos con formato deserializado. Los tipos primitivos seguirán siendo primitivos. Sin embargo, los tipos complejos se convierten en sus versiones deserializadas, por lo que básicamente constituyen bolsas de propiedades. Por ejemplo, si utiliza el cmdlet **Get-Process** en un Runbook (o, en este caso, en un flujo de trabajo de PowerShell), el cmdlet devolverá un objeto de tipo [Deserialized.System.Diagnostic.Process] en lugar del tipo esperado: [System.Diagnostic.Process]. Este tipo tiene las mismas propiedades que el tipo no deserializado, pero no contiene ninguno de los métodos. Además, si intenta pasar este valor como parámetro a un cmdlet (donde el cmdlet espera un valor [System.Diagnostic.Process] para este parámetro), aparecerá el siguiente error: *No se puede procesar la transformación del argumento del parámetro 'process'. Error: "No se puede convertir el valor "System.Diagnostics.Process (CcmExec)" de tipo "Deserialized.System.Diagnostics.Process" al tipo "System.Diagnostics.Process".*   Esto se debe a que hay un error de concordancia de tipos entre el tipo esperado, [System.Diagnostic.Process], y el tipo proporcionado, [Deserialized.System.Diagnostic.Process]. El modo de solucionar este problema es garantizar que los cmdlets del módulo no toman tipos complejos como parámetros. Este es el modo incorrecto de hacerlo.
   
    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
   <br>
    Y este es el modo correcto, donde se toma un primitivo que el cmdlet puede usar internamente para captar el objeto complejo y utilizarlo. Como los cmdlets se ejecutan en el contexto de PowerShell, y no en el del flujo de trabajo de PowerShell, el cmdlet $process se transforma en el tipo adecuado, [System.Diagnostic.Process].  
   
    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Los recursos de conexión de los Runbooks son tablas hash, que son de tipo complejo. Sin embargo, parece que estas tablas hash pueden pasarse sin problemas a los cmdlets como parámetro –Connection, sin ninguna excepción de conversión. Técnicamente, algunos tipos de PowerShell pueden convertirse correctamente de su formato serializado al formato deserializado y, por tanto, pueden pasarse a los cmdlets en los parámetros que aceptan tipos no deserializados. Las tablas hash son uno de ellos. Es posible que los tipos definidos por el creador de un módulo se implementen de forma que también puedan deserializarse correctamente, pero hay que hacer algunos sacrificios. El tipo debe tener un constructor predeterminado, todas sus propiedades deben ser públicas y debe tener un objeto PSTypeConverter. Sin embargo, en los tipos que ya están definidos y que no son propiedad del creador del módulo, no hay forma de “corregirlos” y, por tanto, se recomienda evitar tipos complejos en todos los parámetros. Sugerencia sobre la creación de Runbooks: si, por alguna razón, los cmdlets deben tomar un parámetro de tipo complejo o está utilizando el módulo de otra persona que requiere un parámetro de este tipo, la solución para los Runbooks del flujo de trabajo de PowerShell y los flujos de trabajo de instancias de PowerShell locales es encapsular el cmdlet que genera el tipo complejo y el cmdlet que lo consume en la misma actividad de InlineScript. Dado que InlineScript ejecuta su contenido como PowerShell y no como el flujo de trabajo de PowerShell, el cmdlet que genera el tipo complejo crearía el tipo correcto, y no el tipo complejo deserializado.
5. Cree todos los cmdlets del módulo sin estado. El flujo de trabajo de PowerShell ejecuta cada uno de los cmdlets que se invocan durante el flujo de trabajo en una sesión diferente. Esto significa que los cmdlets que dependan de un estado de sesión creado o modificado por otros cmdlets del mismo módulo no funcionarán en los Runbooks del flujo de trabajo de PowerShell.  A continuación se muestra un ejemplo de lo que no hay que hacer:
   
    ```
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```
   <br>
6. El módulo debe estar incluido en su totalidad en un paquete compatible con Xcopy. Como los módulos de Automatización de Azure se distribuyen en espacios aislados de Automatización cuando es necesario ejecutar los Runbooks, no pueden depender del host en el que se ejecutan. Esto significa que debería poder comprimir el paquete del módulo en un zip, trasladarlo a otro host que tenga la misma versión de PowerShell, u otra posterior, y conseguir que funcione normalmente cuando se importe en el entorno de PowerShell de dicho host. Para que esto ocurra, el módulo no debe depender de ningún archivo que esté fuera de la carpeta del módulo (la carpeta que se comprime al importar en Automatización de Azure) ni de ninguna configuración única del Registro de un host, como las que se establecen al instalar un producto. Si no se sigue este procedimiento recomendado, el módulo no podrá utilizarse en Automatización de Azure.  

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
* Para más información sobre la creación de módulos de PowerShell, consulte [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)




<!--HONumber=Nov16_HO2-->


