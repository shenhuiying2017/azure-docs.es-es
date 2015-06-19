<properties 
	pageTitle="Uso de la interfaz de la línea de comandos entre plataformas de Microsoft Azure con Resource Manager" 
	description="Usar la interfaz de la línea de comandos entre plataformas de Microsoft Azure con el Administrador de recursos" 
	editor="tysonn" 
	manager="timlt" 
	documentationCenter="" 
	authors="squillace" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="rasquill"/>

#Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/documentation/articles/xplat-cli-azure-resource-manager.md" title="Cross-Platform CLI" class="current">CLI entre plataformas</a></div>

En este artículo, aprenderá a usar la interfaz de la línea de comandos entre plataformas (xplat-cli) de Azure para trabajar con Resource Manager. 

>[AZURE.NOTE] Resource Manager está actualmente en vista previa y no proporciona el mismo nivel de capacidades de administración que Azure Service Management. 
>
> Si todavía no ha instalado ni configurado xplat-cli, consulte [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Microsoft Azure][xplatsetup] para conocer más pasos sobre instalación, configuración y uso de xplat-cli.


##Resource Manager

Resource Manager permite administrar un grupo de _recursos_ (entidades administradas por el usuario, como un servidor de base de datos, una base de datos o un sitio web) como una sola unidad lógica o _grupo de recursos_. Por ejemplo, un grupo de recursos podría contener recursos de una Base de datos SQL y de un sitio web.

Para admitir una forma más declarativa de descripción de los cambios en los recursos dentro de un grupo de recursos, Resource Manager utiliza  *templates*, que son documentos JSON. El lenguaje de la plantilla también le permite describir parámetros que se pueden completar ya sea en línea cuando se ejecuta un comando o mientras están almacenados en un archivo JSON independiente. Esto le permite crear fácilmente recursos nuevos con la misma plantilla brindando simplemente parámetros distintos. Por ejemplo, una plantilla que crea un sitio web tendrá parámetros para el nombre del sitio, la región en que estará ubicado el sitio web y otros parámetros en común.

>[AZURE.NOTE] En este momento no están documentados los detalles específicos del lenguaje de la plantilla. Una vez que dicha documentación esté disponible, se actualizará este tema para brindar un vínculo a la documentación de referencia.
>
> Sin embargo, puede utilizar el comando  `azure group template download` para descargar y modificar plantillas proporcionadas por Microsoft y asociados desde la galería de plantillas.

Cuando se usa una plantilla para modificar o crear un grupo, se crea una implementación, que luego se aplica al grupo.

##Autenticación

Actualmente, trabajar con el Resource Manager a través de la xplat-cli requiere que se autentique en Microsoft Azure con una cuenta profesional o educativa. No servirá autenticarse con una cuenta Microsoft o con un certificado instalado a través de un archivo .publishsettings.

Para obtener más información sobre la autenticación con una cuenta de organización, consulte [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Microsoft Azure][xplatsetup].

##Localización y configuración de una plantilla de grupo de recursos

1. Dado que el modo de Resource Manager no está habilitado de forma predeterminada, debe utilizar el siguiente comando para habilitar comandos xplat-cli de Resource Manager.

		azure config mode arm

	>[AZURE.NOTE] Los modos Resource Manager y Azure Service Management se excluyen mutuamente. Es decir, los recursos creados en un modo no se pueden administrar desde el otro.

2. Cuando trabaje con plantillas, puede crear una propia o usar una de la galería de plantillas. En este caso, vamos a usar una de la Galería de plantillas. Para ver las plantillas disponibles en la galería, utilice el comando siguiente. (Debido a que hay miles de plantillas, asegúrese de paginar los resultados o usar **grep** o **findstr** [en Windows] o su comando de búsqueda de cadenas favorito para buscar plantillas interesantes. Como alternativa, puede utilizar la opción **--json** y descargar toda la lista en formato JSON para facilitar la búsqueda. En el ejemplo siguiente se utiliza la plantilla denominada **Microsoft.WebSiteSQLDatabase.0.2.6 preview**.)	

		azure group template list

	La respuesta mostrará el publicador y el nombre de la plantilla y tendrá un aspecto similar al siguiente (aunque podría haber muchos más).

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
		data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
		data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3. Para ver los detalles de una plantilla que creará un sitio web de Azure, use el comando siguiente.

		azure group template show Microsoft.WebSiteSQLDatabase.0.2.6-Vista previa

	Esto devolverá información descriptiva acerca de la plantilla. 

4. Una vez que haya seleccionado una plantilla (**azure group template show Microsoft.WebSiteSQLDatabase.0.2.6-preview**), puede descargarla con el siguiente comando.

		azure group template download Microsoft.WebSiteSQLDatabase.0.2.6-preview

	Descargar una plantilla le permite personalizarla para adecuarse mejor a sus requisitos. Por ejemplo, agregar otro recurso a la plantilla.

	>[AZURE.NOTE] Si modifica la plantilla, utilice el comando  `azure group template validate` para validarla antes de utilizarla para crear o modificar un grupo de recursos existente.

5. Para configurar la plantilla de grupo de recursos para su uso, abra el archivo de plantilla en un editor de texto. Observe la recopilación JSON de **parámetros** que aparece cerca de la parte superior. Contiene una lista de los parámetros que esta plantilla espera para crear los recursos que describe. Algunos parámetros, como **sku**, tienen valores predeterminados, mientras que otros simplemente especifican el tipo del valor, como **siteName**. 
	
	Cuando utiliza una plantilla, puede suministrar parámetros como parte de los parámetros de la línea de comandos o bien especificar un archivo que contenga los valores de parámetro. En cualquier caso, los parámetros deben estar en formato JSON y debe proporcionar sus propios valores para las claves que no tienen valores predeterminados.

	Por ejemplo, para crear un archivo que contiene los parámetros para la plantilla **Microsoft.WebSiteSQLDatabase.0.2.6 preview**, utilice los siguientes datos para crear un archivo denominado **params.json**. Reemplace los valores siguientes empezando por **_My_** como **_MyWebSite_** con sus propios valores. El valor **siteLocation** debe especificar una región de Azure cercana, como **Europa del Norte** o **Centro-Sur de EE. UU**. (Este ejemplo utiliza **Oeste de EE.UU.**)

		{
		  "siteName": {
		    "value": "MyWebSite"
		  },
		  "hostingPlanName": {
		    "value": "MyHostingPlan"
		  },
		  "siteLocation": {
		    "value": "West US"
		  },
		  "serverName": {
		    "value": "MySQLServer"
		  },
		  "serverLocation": {
		    "value": "West US"
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


1. Después de guardar el archivo **params.json**, utilice el siguiente comando para crear un grupo de recursos nuevo basado en la plantilla. El parámetro `-e` especifica el archivo **params.json** que se creó en el paso anterior. Reemplace el valor **MyGroupName** por el nombre de grupo que desee utilizar y **MyDataCenter** por el valor de **siteLocation** especificado en el archivo de parámetros de la plantilla **params.json**.

		azure group create MyGroupName "West US" -f Microsoft.WebSiteSQLDatabase.0.2.6-preview.json -d MyDeployment -e params.json

	>[AZURE.NOTE] Este comando se ejecutará correctamente una vez que se haya cargado la implementación, pero antes de que se aplique a los recursos del grupo. Utilice el comando siguiente para comprobar el estado de la implementación.
	>
	> `azure group deployment show MyGroupName MyDeployment`
	> 
	> El valor **ProvisioningState** muestra el estado de la implementación.
	> 
	> Si se da cuenta de que la configuración no es correcta y necesita detener una implementación de ejecución prolongada, utilice el comando siguiente.
	> 
	> `azure group deployment stop MyGroupName MyDeployment`
	> 
	> Si no proporciona un nombre de implementación, se creará uno automáticamente basado en el nombre del archivo de plantilla. Se devolverá como parte de la salida del comando  `azure group create`.

3. Para ver el grupo, use el siguiente comando.

		azure group show MyGroupName

	Este comando devuelve información acerca de los recursos del grupo. Si tiene varios grupos, puede utilizar el comando  `azure group list` para recuperar una lista de nombres de grupos y, a continuación,  `azure group show` para ver detalles de un grupo específico.

##Trabajo con recursos

A pesar de que las plantillas le permiten declarar cambios de configuración en todo el grupo, a veces es necesario trabajar solo con un recurso específico. Puede hacerlo si utiliza los comandos  `azure resource`.

> [AZURE.NOTE] Cuando utilice comandos  `azure resource` distintos del comando  `list`, debe especificar la versión de API del recurso con que está trabajando mediante el parámetro . Si no está seguro sobre la versión de API que debe utilizar, consulte el archivo de plantilla y busque el campo **apiVersion** correspondiente al recurso.

1. Utilice el comando siguiente para ver todos los recursos de un grupo.

		azure resource list MyGroupName

1. Para ver recursos individuales, como el sitio web, dentro del grupo, utilice el siguiente comando.

		azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

	Observe el parámetro **Microsoft.Web/sites**. Indica el tipo del recurso sobre el que solicita información. Si observa el archivo de plantilla que se descargó anteriormente, observará que este mismo valor se utiliza para definir el tipo de recurso de sitio web descrito en la plantilla.

	Este comando devuelve información relacionada con el sitio web. Por ejemplo, el campo **hostNames** debe contener la dirección URL del sitio web. Utilícela con el explorador para comprobar que el sitio web esté en ejecución.

2. Cuando se consultan los detalles de un recurso, resulta útil usar el parámetro `--json`, porque permite que la salida se pueda leer mejor, dado que algunos valores son estructuras anidadas o recopilaciones. La siguiente línea demuestra los resultados obtenidos con el comando show como un documento JSON.

		azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

	>[AZURE.NOTE] Puede guardar los datos JSON en archivo si utiliza el carácter &gt; para canalizar la salida al archivo. Por ejemplo:
	>
	> `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

3. Para eliminar un recurso existente, utilice el comando siguiente.

		azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

##Registro

Para ver información registrada sobre operaciones realizadas en un grupo, utilice el comando  `azure group log show`. De manera predeterminada, indicará la última operación realizada en el grupo. Para ver todas las operaciones, utilice el parámetro opcional `--all`. Para ver la última implementación, utilice `--last-deployment`. Para ver una implementación específica, utilice `--deployment` y especifique el nombre de la implementación. El ejemplo siguiente devuelve un registro de todas las operaciones realizadas en relación al grupo  'MyGroup'.

	azure group log show mygroup --all

##Pasos siguientes

* Para obtener más información sobre el uso de la interfaz de la línea de comandos entre plataformas de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Microsoft Azure][xplatsetup].
* Para obtener información sobre el trabajo con Resource Manager con Azure PowerShell, consulte [Introducción al uso de Windows PowerShell con Resource Manager][psrm]

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[xplatsetup]: /documentation/articles/xplat-cli/
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!--HONumber=47-->
 