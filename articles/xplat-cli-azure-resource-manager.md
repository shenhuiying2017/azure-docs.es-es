<properties urlDisplayName="Microsoft Azure Cross-Platform Command-Line Interface" pageTitle="Uso de la interfaz de la línea de comandos entre plataformas de Microsoft Azure con Resource Manager" title="Uso de la interfaz de la línea de comandos entre plataformas de Microsoft Azure con Resource Manager" metaKeywords="interfaz línea comandos entre plataformas microsoft azure Resource Manager, resource manager línea comandos microsoft azure, resource manager línea comandos azure, azure cli resource manager" description="Usar la interfaz de la línea de comandos entre plataformas de Microsoft Azure con el Administrador de recursos" metaCanonical="http://www.windowsazure.com/es-es/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="need to identify contact" documentationCenter="" solutions="" authors="larryfr" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

#Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/es-es/documentation/articles/xplat-cli-azure-resource-manager.md" title="Cross-Platform CLI" class="current">CLI entre plataformas</a></div>

Recientemente presentamos una vista previa de Resource Manager, que es una nueva manera de administrar Microsoft Azure. En este artículo, aprenderá a usar la interfaz de la línea de comandos entre plataformas (xplat-cli) de Azure para trabajar con Resource Manager. 

>[WACOM.NOTE] Resource Manager está actualmente en vista previa y no proporciona el mismo nivel de capacidades de administración que Azure Service Management.

>[WACOM.NOTE] Si todavía no ha instalado ni configurado xplat-cli, consulte [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Microsoft Azure][xplatsetup] para conocer más pasos sobre instalación, configuración y uso de xplat-cli.

##Resource Manager

Resource Manager permite administrar un grupo de _recursos_ (entidades administradas por el usuario, como un servidor de base de datos, una base de datos o un sitio web) como una sola unidad lógica o _grupo de recursos_. Por ejemplo, un grupo de recursos podría contener recursos de una Base de datos SQL y de un sitio web.

Para admitir una forma más declarativa de describir los cambios en los recursos dentro de un grupo de recursos, Resource Manager utiliza *plantillas*, que son documentos JSON. El lenguaje de la plantilla también le permite describir parámetros que se pueden completar ya sea en línea cuando se ejecuta un comando o mientras están almacenados en un archivo JSON independiente. Esto le permite crear fácilmente recursos nuevos con la misma plantilla brindando simplemente parámetros distintos. Por ejemplo, una plantilla que crea un sitio web tendrá parámetros para el nombre del sitio, la región en que estará ubicado el sitio web y otros parámetros en común.

>[WACOM.NOTE] En este momento no están documentados los detalles específicos del lenguaje de la plantilla. Una vez que dicha documentación esté disponible, se actualizará este tema para brindar un vínculo a la documentación de referencia.
>
> Sin embargo, puede utilizar el comando `azure group template download` para descargar y modificar plantillas proporcionadas por Microsoft y asociados desde la galería de plantillas.

Cuando se usa una plantilla para modificar o crear un grupo, se crea una implementación, que luego se aplica al grupo.

##Autenticación

Actualmente, trabajar con Resource Manager a través de xplat-cli requiere que se autentique con Microsoft Azure con una cuenta de la organización. No servirá autenticarse con una cuenta de Microsoft o con un certificado instalado a través de un archivo .publishsettings.

Para obtener más información sobre la autenticación con una cuenta de organización, consulte [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Microsoft Azure][xplatsetup].

##Trabajo con grupos y plantillas

1. Resource Manager actualmente está en vista previa, por lo que los comandos de xplat-cli para trabajar con él no están habilitados de manera predeterminada. Utilice el comando siguiente para habilitar los comandos.

		azure config mode arm

	>[WACOM.NOTE] Los modos Resource Manager y Azure Service Management se excluyen mutuamente. Es decir, los recursos creados en un modo no se pueden administrar desde el otro.

2. Cuando trabaje con plantillas, puede crear una propia o usar una de la galería de plantillas. Para ver las plantillas disponibles en la galería, utilice el comando siguiente.

		azure group template list

	La respuesta mostrará el nombre del publicador y de la plantilla y presentará un formato similar al siguiente.

 datos:    Publicador               Nombre
 datos:    ----------------------------------------------------------------------------
 datos:    Microsoft               Microsoft.WebSite.0.1.0-preview1
 datos:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
 datos:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
 datos:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
 datos:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3. Para ver detalles de una plantilla que creará un sitio web de Azure, use el comando siguiente.

		azure group template show Microsoft.WebSiteSQLDatabase.0.1.0-preview1

	Esto devolverá información descriptiva acerca de la plantilla.

4. Una vez que haya seleccionado una plantilla, puede descargarla con el siguiente comando.

		azure group template download Microsoft.WebSiteSQLDatabase.0.1.0-preview1

	Descargar una plantilla le permite personalizarla para adecuarse mejor a sus requisitos. Por ejemplo, agregar otro recurso a la plantilla.

	>[WACOM.NOTE] Si modifica la plantilla, utilice el comando `azure group template validate` para validarla antes de utilizarla para crear o modificar un grupo de recursos existente.

5. Abra el archivo de la plantilla en un editor de texto. Observe la recopilación de **parámetros** que aparece cerca de la parte superior. Contiene una lista de los parámetros que esta plantilla espera para crear los recursos que describe. Algunos parámetros, como **sku**, tienen valores predeterminados, mientras que otros simplemente especifican el tipo del valor, como **siteName**. Cuando utiliza una plantilla, puede suministrar parámetros como parte de los parámetros de la línea de comandos o bien especificar un archivo que contenga los valores de parámetro. De cualquier modo, los parámetros deben estar en formato JSON.

	Para crear un archivo que contiene parámetros para la plantilla Microsoft.WebSiteSQLDatabase.0.1.0-preview1, utilice los siguientes datos y cree un archivo llamado **params.json**. Reemplace los valores que comienzan con **My**, como **MyWebSite**, por sus propios valores. El valor **siteLocation** debe especificar una región de Azure cercana, como **Europa del Norte** o **Centro-Sur de EE. UU**.

		{
		  "siteName": {
		    "value": "MyWebSite"
		  },
		  "hostingPlanName": {
		    "value": "MyHostingPlan"
		  },
		  "siteLocation": {
		    "value": "North Europe"
		  },
		  "serverName": {
		    "value": "MySQLServer"
		  },
		  "serverLocation": {
		    "value": "North Europe"
		  },
		  "administratorLogin": {
		    "value": "MySQLAdmin"
		  },
		  "administratorLoginPassword": {
		    "value": "MySQLAdminPassword"
		  },
		  "databaseName": {
		    "value": "MySQLDB"
		  }
		}

1. Después de guardar el archivo **params.json**, utilice el siguiente comando para crear un grupo de recursos nuevo basado en la plantilla. El parámetro `-e` especifica el archivo **params.json** que se creó en el paso anterior.

		azure group create MyGroupName "MyDataCenter" -y Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -d MyDeployment -e params.json

	Reemplace el valor **MyGroupName** por el nombre de grupo que desee utilizar y **MyDataCenter** por el valor de **siteLocation** especificado en la plantilla.

	>[WACOM.NOTE] Este comando se ejecutará correctamente una vez que se haya cargado la implementación, pero antes de que se aplique a los recursos del grupo. Utilice el comando siguiente para comprobar el estado de la implementación.
	>
	> `azure group deployment show MyGroupName MyDeployment`
	> 
	> El valor **ProvisioningState** muestra el estado de la implementación.
	> 
	> Si se da cuenta de que la configuración no es correcta y necesita detener una implementación de ejecución prolongada, utilice el comando siguiente.
	> 
	> `azure group deployment stop MyGroupName MyDeployment`
	> 
	> Si no proporciona un nombre de implementación, se creará uno automáticamente basado en el nombre del archivo de plantilla. Se devolverá como parte de la salida del comando `azure group create`.

3. Para ver el grupo, use el siguiente comando.

		azure group show MyGroupName

	Este comando devuelve información acerca de los recursos del grupo. Si tiene varios grupos, puede utilizar el comando `azure group list` para recuperar una lista de nombres de grupos y, a continuación, `azure group show` para ver detalles de un grupo específico.

##Trabajo con recursos

A pesar de que las plantillas le permiten declarar cambios de configuración en todo el grupo, a veces es necesario trabajar solo con un recurso específico. Puede hacerlo si utiliza los comandos `azure resource`.

> [WACOM.NOTE] Cuando utilice los comandos `azure resource` distintos del comando `list`, debe especificar la versión de API del recursos con que está trabajando mediante el parámetro `-o`. Si no está seguro sobre la versión de API que debe utilizar, consulte el archivo de plantilla y busque el campo **apiVersion** correspondiente al recurso.

1. Utilice el comando siguiente para ver todos los recursos de un grupo.

		azure resource list MyGroupName

1. Para ver recursos individuales, como el sitio web, dentro del grupo, utilice el siguiente comando.

		azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

	Observe el parámetro **Microsoft.Web/sites**. Indica el tipo del recurso sobre el que solicita información. Si observa el archivo de plantilla que se descargó anteriormente, observará que este mismo valor se utiliza para definir el tipo de recurso de sitio web descrito en la plantilla.

	Este comando devuelve información relacionada con el sitio web. Por ejemplo, el campo **hostNames** debe contener la dirección URL del sitio web. Utilícela con el explorador para comprobar que el sitio web esté en ejecución.

2. Cuando se consultan los detalles de un recurso, resulta útil usar el parámetro `--json`, porque permite que la salida se pueda leer mejor, dado que algunos valores son estructuras anidadas o recopilaciones. La siguiente línea demuestra los resultados obtenidos con el comando show como un documento JSON.

		azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

	>[WACOM.NOTE] Puede guardar los datos JSON en archivo si utiliza el carácter &gt; para canalizar la salida al archivo. Por ejemplo:
	>
	> `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

3. Para eliminar un recurso existente, utilice el comando siguiente.

		azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

##Registro

Para ver información registrada sobre operaciones realizadas en un grupo, utilice el comando `azure group log show`. De manera predeterminada, indicará la última operación realizada en el grupo. Para ver todas las operaciones, utilice el parámetro opcional `--all`. Para ver la última implementación, utilice `--last-deployment`. Para ver una implementación específica, utilice `--deployment` y especifique el nombre de la implementación. El ejemplo siguiente devuelve un registro de todas las operaciones realizadas en relación al grupo "MyGroup".

	azure group log show mygroup --all

##Pasos siguientes

* Para obtener más información sobre el uso de la interfaz de la línea de comandos entre plataformas de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Microsoft Azure][xplatsetup].
* Para obtener información sobre el trabajo con Resource Manager con Azure PowerShell, consulte [Introducción al uso de Windows PowerShell con Resource Manager][psrm]

[signuporg]: http://www.windowsazure.com/es-es/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/es-es/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[xplatsetup]: /es-es/documentation/articles/xplat-cli/
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760
