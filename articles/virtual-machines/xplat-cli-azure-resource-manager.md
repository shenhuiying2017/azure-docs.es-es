<properties
	pageTitle="CLI para Windows, Linux y Mac | Microsoft Azure"
	description="Uso de la CLI de Microsoft Azure para Mac, Linux y Windows con el Administrador de recursos de Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services"" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="06/09/2015" ms.author="danlep"/>

# Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la administración de recursos con el modelo de implementación del Administrador de recursos.

Este artículo describe cómo crear, administrar y eliminar los recursos y las máquinas virtuales de Azure con la CLI de Azure para Mac, Linux y Windows con el modo Administrador de recursos de Azure.

>[AZURE.NOTE]Para crear y administrar recursos de Azure en la línea de comandos, necesitará una cuenta de Azure ([evaluación gratuita aquí](http://azure.microsoft.com/pricing/free-trial/)). También necesitará [instalar la CLI de Azure](../xplat-cli-install.md) e [iniciar sesión para usar recursos de Azure asociados a su cuenta](../xplat-cli-connect.md). Si ha realizado estas acciones, ya está preparado para comenzar.

## Recursos de Azure

Use el Administrador de recursos de Azure para administrar un grupo de _recursos_ (entidades administradas por el usuario, como una máquina virtual, un servidor de base de datos, una base de datos o un sitio web) como una sola unidad lógica o _grupo de recursos_. Puede crear, administrar y eliminar estos recursos de forma imperativa en la línea de comandos, igual que en el modo asm.

Mediante el modo Administrador de recursos de Azure, también puede administrar los recursos de Azure de forma _declarativa_ describiendo la estructura y las relaciones de un grupo de recursos en *plantillas* de JSON que se pueden implementar. La plantilla describe parámetros que se pueden completar ya sea en línea cuando se ejecuta un comando o cuando están almacenados en un archivo JSON azuredeploy-parameters.json independiente. Esto le permite crear fácilmente recursos nuevos con la misma plantilla brindando simplemente parámetros distintos. Por ejemplo, una plantilla que crea un sitio web tendrá parámetros para el nombre del sitio, la región en que estará ubicado el sitio web y otros parámetros en común.

Cuando se usa una plantilla para modificar o crear un grupo, se crea una _implementación_, que luego se aplica al grupo. Para obtener más información sobre el Administrador de recursos de Azure, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).

## Autenticación

Trabajar con el Administrador de recursos de Azure a través de la CLI de Azure requiere que se autentique en Microsoft Azure con una cuenta profesional o educativa. No servirá autenticarse con un certificado instalado a través de un archivo .publishsettings.

Para obtener más información acerca de cómo autenticarse con una cuenta profesional o educativa, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).

> [AZURE.NOTE]Debido a que usa una cuenta profesional o educativa, administrada por Azure Active Directory, también puede utilizar el Control de acceso basado en roles de Azure (RBAC) para administrar el acceso y el uso de recursos de Azure. Para obtener más información, [Administración y auditoría del acceso a los recursos](../resource-group-rbac.md)

## Configuración del modo Administrador de recursos de Azure

Dado que el modo Administrador de recursos de Azure no está habilitado de forma predeterminada, debe usar el siguiente comando para habilitar los comandos del Administrador de recursos de la CLI de Azure.

	azure config mode arm

>[AZURE.NOTE]Los modos Administrador de recursos de Azure y Administración de servicios de Azure se excluyen mutuamente. Es decir, los recursos creados en un modo no se pueden administrar desde el otro.

## Búsqueda de ubicaciones

La mayoría de los comandos del Administrador de recursos de Azure necesitan una ubicación válida en los que crear o buscar un recurso. Puede encontrar todas las ubicaciones disponibles mediante el siguiente comando.

	azure location list

Mostrara una lista de ubicaciones específicas de regiones como "Oeste de EE. UU.", "Este de EE. UU.", etc.

## Creación de un grupo de recursos

Un grupo de recursos es una agrupación lógica de red, almacenamiento y otros recursos. Casi todos los comandos en el modo Administrador de recursos de Azure necesitan un grupo de recursos. Puede crear un grupo de recursos denominado _testrg_; por ejemplo, mediante el siguiente comando.

	azure group create -n "testrg" -l "West US"

Puede empezar a agregar recursos a este grupo después de esto y usarlo para configurar una nueva máquina virtual.

## Creación de máquinas virtuales

Hay dos maneras de crear máquinas virtuales en el modo Administrador de recursos de Azure:

1. Mediante comandos individuales de la CLI de Azure.
2. Mediante plantillas de grupo de recursos.

Asegúrese de crear al menos un grupo de recursos antes de comenzar con cualquiera de estos métodos.

### Uso de comandos individuales de CLI de Azure

Este es el enfoque básico para configurar y crear una máquina virtual según sus necesidades. En modo Administrador de recursos de Azure, deberá configurar algunos recursos obligatorios, como las redes, para poder utilizar el comando **vm create**.

>[AZURE.NOTE]Si va a crear recursos por primera vez en la línea de comandos para la suscripción, se le podría pedir que se registrara para determinados proveedores de recursos. Si esto sucede, es fácil registrar el proveedor e intentar ejecutar de nuevo el comando que ha generado un error, como se muestra en el siguiente ejemplo.
>
> `azure provider register Microsoft.Storage`
>
> Puede encontrar la lista de proveedores registrados para la suscripción ejecutando el siguiente comando.
>
> `azure provider list`


#### Creación de un recurso de dirección IP pública.

Necesitará crear una IP pública para que pueda ejecutar SSH en la nueva máquina virtual para cualquier trabajo significativo. Crear una dirección IP pública es sencillo. El comando necesita un grupo de recursos, un nombre para el recurso IP público y una ubicación, en ese orden.

	azure network public-ip create "testrg" "testip" "westus"

#### Creación de un recurso de tarjeta de interfaz de red

Para la tarjeta de interfaz de red o NIC, es necesario crear primero una subred y una red virtual. Cree una red virtual en una determinada ubicación y un grupo de recursos mediante el comando **network vnet create**.

	azure network vnet create "testrg" "testvnet" "westus"

Luego puede crear una subred en esta red virtual mediante el comando **network vnet subnet create**.

	azure network vnet subnet create "testrg" "testvnet" "testsubnet"

Debe poder crear una NIC usando estos recursos mediante el comando **network nic create**.

	azure network nic create "testrg" "testnic" "westus" -k "testsubnet" -m "testvnet" -p "testip"

>[AZURE.NOTE]Aunque es opcional, es muy importante pasar el nombre IP público como un parámetro al comando **network nic create** ya que esto enlaza la NIC a esta dirección IP, que se utilizará más adelante para SSH en la máquina virtual creada mediante esta NIC.

Para obtener más información sobre los comandos **network**, consulte la ayuda de línea de comandos o [Uso de la CLI de Azure con el Administrador de recursos de Azure](azure-cli-arm-commands.md).

#### Búsqueda de una imagen del sistema operativo

Actualmente, solo puede buscar un sistema operativo basado en el publicador de la imagen. En otras palabras, debe ejecutar el siguiente comando para buscar una lista de publicadores de imágenes del sistema operativo en la ubicación deseada.

	azure vm image list-publishers "westus"

A continuación, elija un publicador de la lista y busque la lista de imágenes de ese publicador mediante el siguiente comando.

	azure vm image list "westus" "CoreOS"

Por último, elija una imagen del sistema operativo de la lista con un aspecto similar al siguiente ejemplo.

	info:    Executing command **vm image list**
	warn:    The parameters --offer and --sku if specified will be ignored
	+ Getting virtual machine image offers (Publisher: "Canonical" Location: "westus")
	data:    Publisher  Offer        Sku          Version          Location  Urn

	data:    ---------  -----------  -----------  ---------------  --------- ----------------------------------------
	data:    CoreOS     CoreOS       Alpha        475.1.0          westus    CoreOS:CoreOS:Alpha:475.1.0
	data:    CoreOS     CoreOS       Alpha        490.0.0          westus    CoreOS:CoreOS:Alpha:490.0.0

Anote el nombre URN de la imagen que desea cargar en la máquina virtual. Lo usará más adelante en el artículo.

#### Creación de una máquina virtual

Ya está listo para crear una máquina virtual ejecutando el comando **vm create** y pasando la información necesaria. Es opcional pasar la dirección IP pública en esta etapa, porque la NIC ya tiene esta información. Su comando puede ser similar al siguiente ejemplo, donde _testvm_ es el nombre de la máquina virtual creada en el grupo de recursos _testrg_.

	azure-cli@0.8.0:/# azure vm create "testrg" "testvm" "westus" "Linux" -Q "CoreOS:CoreOS:Alpha:660.0.0" -u "azureuser" -p "Pass1234!" -N "testnic"
	info:    Executing command vm create
	+ Looking up the VM "testvm"
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli02f696bbfda6d83414300" in "westus"
	+ Looking up the storage account cli02f696bbfda6d83414300
	+ Looking up the NIC "testnic"
	+ Creating VM "testvm"
	info:    vm create command OK

Debe poder iniciar esta máquina virtual mediante la ejecución del siguiente comando.

	azure vm start "testrg" "testvm"

A continuación, aplíquele SSH mediante el comando **ssh username@ipaddress**. Para buscar rápidamente la dirección IP de su recurso de IP pública, use el siguiente comando.

	azure network public-ip show "testrg" "testip"

La administración de esta máquina virtual es fácil con comandos **vm**. Para obtener más información, consulte [Uso de la CLI de Azure con el Administrador de recursos de Azure](azure-cli-arm-commands.md).

### Método abreviado vm quick-create

El nuevo método abreviado **vm quick-create** recorta la mayoría de los pasos del método imperativo de creación de máquinas virtuales. Esto resulta útil cuando desea probar la creación de máquinas virtuales sencillas o si no le interesan las configuraciones de red. Es un comando interactivo y necesita buscar solo el URN de la imagen del sistema operativo antes de ejecutarla.

	azure-cli@0.8.0:/# azure vm quick-create
	info:  Executing command vm quick-create
	Resource group name: CLIRG
	Virtual machine name: myqvm
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): CoreOS:CoreOS:Alpha:660.0.0
	User name: azureuser
	Password: ********
	Confirm password: ********

La CLI de Azure creará una máquina virtual con el tamaño de máquina virtual predeterminado. También creará una cuenta de almacenamiento, una NIC, una red virtual y una subred, así como una dirección IP pública. Puede ejecutar SSH en la máquina virtual con la dirección IP pública después de arrancar.

### Uso de plantillas de grupo de recursos

#### Localización y configuración de una plantilla de grupo de recursos

1. Cuando trabaje con plantillas, puede crear una plantilla propia, usar una de la galería de plantillas o emplear plantillas disponibles en [GitHub](https://github.com/azurermtemplates/azurermtemplates). Para empezar, vamos a usar una plantilla denominada CoreOS.CoreOSStable.0.2.40-preview de la galería de plantillas. Para ver las plantillas disponibles en la galería, utilice el comando siguiente. Como hay miles de plantillas, puede paginar los resultados o usar **grep** o **findstr** (en Windows) o su comando de búsqueda de cadenas favorito para buscar plantillas interesantes. Como alternativa, puede utilizar la opción **--json** y descargar toda la lista en formato JSON para facilitar la búsqueda.

		azure group template list

	La respuesta mostrará el publicador y el nombre de la plantilla y tendrá un aspecto similar al siguiente (aunque podría haber muchos más).

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    CoreOS                  CoreOS.CoreOSStable.0.2.40-preview
		data:    CoreOS                  CoreOS.CoreOSAlpha.0.2.39-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer12.2.0.36-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer11SP3PremiumImage0.2.54-preview

2. Para ver los detalles de la plantilla, utilice el siguiente comando.

		azure group template show CoreOS.CoreOSStable.0.2.40-preview

	Esto devolverá información descriptiva acerca de la plantilla. La plantilla que usamos creará una máquina virtual Linux.

3. Una vez que haya seleccionado una plantilla, puede descargarla con el siguiente comando.

		azure group template download CoreOS.CoreOSStable.0.2.40-preview

	Descargar una plantilla le permite personalizarla para adecuarse mejor a sus requisitos. Por ejemplo, agregar otro recurso a la plantilla.

	>[AZURE.NOTE]Si modifica la plantilla, utilice el comando `azure group template validate` para validarla antes de utilizarla para crear o modificar un grupo de recursos existente.

4. Para configurar la plantilla de grupo de recursos para su uso, abra el archivo de plantilla en un editor de texto. Observe la recopilación JSON de **parámetros** que aparece cerca de la parte superior. Contiene una lista de los parámetros que esta plantilla espera para crear los recursos que describe. Algunos parámetros podrían tener valores predeterminados, mientras que otros especifican el tipo del valor o un intervalo de valores permitidos.

	Cuando utiliza una plantilla, puede suministrar parámetros como parte de los parámetros de la línea de comandos o bien especificar un archivo que contenga los valores de parámetro. También puede escribir sus campos **valor** directamente dentro de la sección **parámetros** en la plantilla, aunque eso haría que la plantilla estuviera estrechamente enlazada a una implementación determinada y no sería reutilizable fácilmente. En cualquier caso, los parámetros deben estar en formato JSON y debe proporcionar sus propios valores para las claves que no tienen valores predeterminados.

	Por ejemplo, para crear un archivo que contiene parámetros para la plantilla CoreOS.CoreOSStable.0.2.40-preview template, use los siguientes datos para crear un archivo denominado params.json. Reemplace los valores utilizados en este ejemplo por sus propios valores. El valor **Location** debe especificar una región de Azure cercana, como **Europa del Norte** o **Centro-Sur de EE. UU**. (En este ejemplo se usa **Oeste de EE. UU.**).

		{
		  "newStorageAccountName": {
			"value": "testStorage"
		  },
		  "newDomainName": {
			"value": "testDomain"
		  },
		  "newVirtualNetworkName": {
			"value": "testVNet"
		  },
		  "vnetAddressSpace": {
			"value": "10.0.0.0/11"
		  },
		  "hostName": {
			"value": "testHost"
		  },
		  "userName": {
			"value": "azureUser"
		  },
		  "password": {
			"value": "Pass1234!"
		  },
		  "location": {
			"value": "West US"
		  },
		  "hardwareSize": {
			"value": "Medium"
		  }
	    }

5. Después de guardar el archivo params.json, utilice el siguiente comando para crear un grupo de recursos nuevo basado en la plantilla. El parámetro `-e` especifica el archivo params.json que se creó en el paso anterior. Reemplace **testRG** por el nombre del grupo que desea utilizar y **testDeploy** por el nombre de la implementación. La ubicación debe ser la misma que la especificada en el archivo de parámetros de plantilla params.json.

		azure group create "testRG" "West US" -f CoreOS.CoreOSStable.0.2.40-preview.json -d "testDeploy" -e params.json

	Este comando se ejecutará correctamente una vez que se haya cargado la implementación, pero antes de que se aplique a los recursos del grupo. Utilice el comando siguiente para comprobar el estado de la implementación.

		azure group deployment show "testRG" "testDeploy"

	El valor **ProvisioningState** muestra el estado de la implementación.

	Si la implementación se realizó correctamente, verá un resultado similar al siguiente ejemplo.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-04-27T07:49:18.5237635Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ----------------
		data:    newStorageAccountName  String        testStorage
		data:    newDomainName          String        testDomain
		data:    newVirtualNetworkName  String        testVNet
		data:    vnetAddressSpace       String        10.0.0.0/11
		data:    hostName               String        testHost
		data:    userName               String        azureUser
		data:    password               SecureString  undefined
		data:    location               String        West US
		data:    hardwareSize           String        Medium
		info:    group deployment show command OK

	>[AZURE.NOTE]Si se da cuenta de que la configuración no es correcta y necesita detener una implementación de ejecución prolongada, utilice el comando siguiente.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Si no proporciona un nombre de implementación, se creará uno automáticamente basado en el nombre del archivo de plantilla. Se devolverá como parte de la salida del comando `azure group create`.

6. Para ver el grupo, use el siguiente comando.

		azure group show "testRG"

	Este comando devuelve información acerca de los recursos del grupo. Si tiene varios grupos, puede utilizar el comando `azure group list` para recuperar una lista de nombres de grupos y, a continuación, usar `azure group show` para ver detalles de un grupo específico.

7. También puede usar plantillas más recientes directamente desde GitHub, en lugar de descargarlas de la biblioteca de plantillas. Abra [GitHub.com](http://www.github.com) y busque AzureRmTemplates. Seleccione el repositorio AzureRmTemplates y busque las plantillas que encuentre interesantes, por ejemplo, _101-simple-vm-from-image_. Si hace clic en la plantilla, verá que contiene azuredeploy.json entre otros archivos. Esta es la plantilla que desea usar en el comando mediante una opción **--template-url**. Ábrala en modo _sin procesar_ y copie la dirección URL que aparece en la barra de direcciones del explorador. A continuación, puede usar esta dirección URL directamente para crear una implementación, en lugar de descargar desde una biblioteca de plantillas, mediante un comando similar al siguiente ejemplo.

		azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw/githubusercontent.com/azurermtemplates/azurermtemplates/master/101-simple-vm-from-image/azuredeploy.json

	> [AZURE.NOTE]Es importante abrir la plantilla de json en modo _sin procesar_. La dirección URL que aparece en la barra de direcciones del explorador es diferente de la que aparece en el modo normal. Para abrir el archivo en modo _sin procesar_ mientras lo ve en GitHub, haga clic en **Raw** (Sin procesar) en la esquina superior derecha.

#### Trabajo con recursos

A pesar de que las plantillas le permiten declarar cambios de configuración en todo el grupo, a veces es necesario trabajar solo con un recurso específico. Puede hacerlo si utiliza los comandos `azure resource`.

> [AZURE.NOTE]Cuando utilice comandos `azure resource` distintos del comando `list`, debe especificar la versión de API del recurso con que está trabajando mediante el parámetro `-o`. Si no está seguro sobre la versión de API que debe utilizar, consulte el archivo de plantilla y busque el campo **apiVersion** correspondiente al recurso.

1. Utilice el comando siguiente para ver todos los recursos de un grupo.

		azure resource list "testRG"

2. Para ver recursos individuales, dentro del grupo, utilice el siguiente comando.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

	Observe el parámetro **Microsoft.ClassicCompute/virtualMachines**. Indica el tipo del recurso sobre el que solicita información. Si observa el archivo de plantilla que se descargó anteriormente, observará que este mismo valor se usa para definir el tipo de recurso de máquina virtual descrito en la plantilla.

	Este comando devuelve información relacionada con la máquina virtual.

3. Cuando se consultan los detalles de un recurso, a menudo resulta útil usar el parámetro `--json` porque permite que la salida se pueda leer mejor, dado que algunos valores son estructuras anidadas o recopilaciones. El siguiente ejemplo demuestra los resultados obtenidos con el comando **show** como un documento JSON.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json

	>[AZURE.NOTE]Puede guardar los datos JSON en archivo si utiliza el carácter &gt; para canalizar la salida al archivo. Por ejemplo:
	>
	> `azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json > myfile.json`

4. Para eliminar un recurso existente, utilice el comando siguiente.

		azure resource delete "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

## Registro

Para ver información registrada sobre operaciones realizadas en un grupo, utilice el comando `azure group log show`. De manera predeterminada, indicará la última operación realizada en el grupo. Para ver todas las operaciones, utilice el parámetro opcional `--all`. Para ver la última implementación, utilice `--last-deployment`. Para ver una implementación específica, utilice `--deployment` y especifique el nombre de la implementación. El ejemplo siguiente devuelve un registro de todas las operaciones realizadas en relación al grupo MyGroup.

	azure group log show mygroup --all

## Pasos siguientes

* Para obtener más información acerca del uso de la interfaz de la línea de comandos de Azure (CLI de Azure), vea [Instalación y configuración de la CLI de Azure][clisetup].
* Para obtener información sobre el trabajo con el Administrador de recursos de Azure con Azure PowerShell, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)
* Para obtener información sobre cómo trabajar con el Administrador de recursos de Azure desde el Portal de Azure, consulte [Uso de grupos de recursos para administrar los recursos de Azure][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[clisetup]: ../xplat-cli-install.md
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=Oct15_HO1-->