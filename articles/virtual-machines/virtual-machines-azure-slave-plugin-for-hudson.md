<properties
	pageTitle="Uso del complemento subordinado de Azure con Hudson Continuous Integration | Microsoft Azure"
	description="Describe cómo usar el complemento esclavo de Azure con Hudson Continuous Integration."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="robmcm"/>

# Uso del complemento esclavo de Azure con Hudson Continuous Integration

El complemento esclavo de Azure para Hudson le permite aprovisionar los nodos subordinados en Azure cuando se ejecutan compilaciones distribuidas.

## Instalación del complemento subordinado de Azure

1. En el panel de Hudson, haga clic en **Manage Hudson** (Administrar Hudson).

1. En la página **Manage Hudson** (Administrar Hudson), haga clic en **Manage Plugins** (Administrar complementos).

1. Haga clic en la pestaña **Available** (Disponible).

1. Haga clic en **Buscar** y escriba **Azure** para limitar la lista de complementos relevantes.

	Si opta por desplazarse por la lista de complementos disponibles, encontrará el complemento secundario de Azure en la sección **Administración de clústeres y compilación distribuida** de la pestaña **Otros**.

1. Active la casilla **Azure Slave Plugin** (Complemento esclavo de Azure).

1. Haga clic en **Instalar**.

1. Reinicie Hudson.

Ahora que está instalado el complemento, los siguientes pasos serían configurarlo con el perfil de suscripción de Azure y crear una plantilla que se usará en la creación de la máquina virtual para el nodo subordinado.

## Configuración del complemento esclavo de Azure con el perfil de suscripción

Un perfil de suscripción, también conocido como configuración de publicación, es un archivo XML que contiene credenciales seguras y alguna información adicional que necesitará para trabajar con Azure en el entorno de desarrollo. Para configurar el complemento esclavo de Azure, necesitará:

* Su id. de suscripción
* Un certificado de administración para la suscripción

Estos se pueden encontrar en su [perfil de suscripción]. A continuación se muestra un ejemplo de un perfil de suscripción.

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Una vez que tiene el perfil de suscripción, siga estos pasos para configurar el complemento esclavo de Azure.

1. En el panel de Hudson, haga clic en **Manage Hudson** (Administrar Hudson).

1. Haga clic en **Configure System** (Configurar sistema).

1. Desplácese hacia abajo en la página para encontrar la sección **Cloud** (Nube).

1. Haga clic en **Add new cloud > Microsoft Azure** (Agregar nueva nube > Microsoft Azure).

    ![agregar nube nueva][add new cloud]

    Se mostrarán los campos donde debe especificar los detalles de suscripción.

    ![configurar perfil][configure profile]

1. Copie el certificado de administración y el id. de suscripción del perfil de suscripción y péguelos en los campos correspondientes.

    Al copiar el certificado de administración y el id. de suscripción, **no** incluya las comillas que delimitan los valores.

1. Haga clic en **Verify configuration** (Comprobar configuración).

1. Cuando se compruebe que la configuración es correcta, haga clic en **Save** (Guardar).

## Configuración de una plantilla de máquina virtual para el complemento esclavo de Azure

Una plantilla de máquina virtual define los parámetros que usará el complemento para crear un nodo subordinado en Azure. En los pasos siguientes crearemos plantilla para una VM de Ubuntu.

1. En el panel de Hudson, haga clic en **Manage Hudson** (Administrar Hudson).

1. Haga clic en **Configure System** (Configurar sistema).

1. Desplácese hacia abajo en la página para encontrar la sección **Cloud** (Nube).

1. Dentro de la sección **Cloud** (Nube), busque **Add Azure Virtual Machine Template** (Agregar plantilla de máquina virtual de Azure) y haga clic en el botón **Add** (Agregar).

    ![agregar plantilla de vm][add vm template]

1. Especifique un nombre de servicio en la nube en el campo **Name** (Nombre). Si el nombre que especifique se refiere a un servicio en la nube existente, la máquina virtual se aprovisionará en dicho servicio. De lo contrario, Azure creará uno nuevo.

1. En el campo **Description** (Descripción), escriba el texto que describe la plantilla que está creando. Esta información es solo para fines documentales y no se usa en el aprovisionamiento de una máquina virtual.

1. En el campo **Labels** (Etiquetas), escriba **linux**. Esta etiqueta se usa para identificar la plantilla que está creando y posteriormente para hacer referencia a la plantilla al crear un trabajo de Hudson.

1. Seleccione una región donde se creará la máquina virtual.

1. Seleccione el tamaño adecuado de la máquina.

1. Especifique una cuenta de almacenamiento donde se creará la máquina virtual. Asegúrese de que se encuentra en la misma región que el servicio en la nube que se va a usar. Si desea crear nuevo almacenamiento, puede dejar este campo en blanco.

1. El tiempo de retención especifica el número de minutos antes de que Hudson elimine un subordinado inactivo. Deje el valor predeterminado de 60.

1. En **Usage** (Uso), seleccione la condición adecuada de uso de este nodo subordinado. Por ahora, seleccione **Utilize this node as much as possible** (Usar este nodo tanto como sea posible).

    En este punto, su formulario sería parecido al siguiente:

    ![configuración de plantilla][template config]

1. En **Image Family or Id** (Familia o identificador de imagen), debe especificar la imagen de sistema que se instalará en la máquina virtual. Puede seleccionar de una lista de familias de imagen o especificar una imagen personalizada.

    Si desea seleccionar de una lista de familias de imagen, escriba el primer carácter (distingue mayúsculas de minúsculas) del nombre de familia de imagen. Por ejemplo, al escribir **U** aparecerá una lista de familias de Ubuntu Server. Una vez que seleccione de la lista, Jenkins usará la versión más reciente de esa imagen de sistema de esa familia al aprovisionar la máquina virtual.

    ![lista de familias de SO][OS family list]

    Si tiene una imagen personalizada que desea usar en su lugar, escriba su nombre. Los nombres de imagen personalizada no se muestran en una lista, por lo que debe asegurarse de escribir el nombre correctamente.

    Para este tutorial, escriba **U** para mostrar una lista de imágenes de Ubuntu y seleccione **Ubuntu Server 14.04 LTS**.

1. Para **Launch method** (Método de inicio), seleccione **SSH**.

1. Copie el siguiente script y péguelo en el campo **Init script** (Script de inicialización).

		# Install Java

		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk

		# Install git

		sudo apt-get install -y git

		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

    El **script de inicialización** se ejecutará después de crearse la máquina virtual. En este ejemplo, el script instala Java, git y ant.

1. En los campos **Username** (Nombre de usuario) y **Password** (Contraseña), escriba los valores preferidos para la cuenta de administrador que se creará en la máquina virtual.

1. Haga clic en **Verify Template** (Comprobar plantilla) para comprobar si los parámetros especificados son válidos.

1. Haga clic en **Save** (Guardar).

## Creación de un trabajo de Hudson que se ejecuta en un nodo subordinado en Azure

En esta sección, creará una tarea de Hudson que se ejecutará en un nodo subordinado en Azure.

1. En el panel de Hudson, haga clic en **New Job** (Nuevo trabajo).

1. Escriba un nombre para el trabajo que se va a crear.

1. Para el tipo de trabajo, seleccione **Build a free-style software job** (Crear un trabajo de software de estilo libre).

1. Haga clic en **Aceptar**.

1. En la página de configuración del trabajo, seleccione **Restrict where this project can be run** (Restringir dónde se puede ejecutar este proyecto).

1. Seleccione **Node and label menu** (Menú de nodo y etiqueta) y seleccione **linux** (esta etiqueta se especificó al crear la plantilla de máquina virtual en la sección anterior).

1. En la sección **Build** (Compilar), haga clic en **Add build step** (Agregar paso de compilación) y seleccione **Execute shell** (Ejecutar shell).

1. Edite el siguiente script; para ello, sustituya **(el nombre de la cuenta de github)**, **(el nombre del proyecto)** y **(el directorio del proyecto)** por los valores adecuados y pegue el script editado en el área de texto que aparece.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e {your project directory} ]; then

  			cd {your project directory}

  			git pull origin master

		else

  			git clone https://github.com/{your github account name}/{your project name}.git

		fi

		# change directory to project

		cd $currentDir/{your project directory}

		#Execute build task

		ant

1. Haga clic en **Save** (Guardar).

1. En el panel de Hudson, busque el trabajo que acaba de crear y haga clic en icono **Schedule a build** (Programar una compilación).

Hudson creará luego un nodo subordinado con la plantilla que creó en la sección anterior y ejecutará el script especificado en el paso de compilación de esta tarea.

## Pasos siguientes

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[perfil de suscripción]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

<!---HONumber=AcomDC_0921_2016-->