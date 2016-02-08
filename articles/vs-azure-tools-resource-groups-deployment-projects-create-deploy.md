<properties
   pageTitle="Creación e implementación de proyectos de Visual Studio del Grupo de recursos de Azure | Microsoft Azure"
   description="Use Visual Studio para crear un proyecto del grupo de recursos de Azure e implementar los recursos en Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="douge"
   editor="" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/13/2015"
   ms.author="tomfitz" />

# Creación e implementación de grupos de recursos de Azure mediante Visual Studio

La plantilla del proyecto de implementación **Grupo de recursos de Azure** está disponible en Visual Studio cuando se instala Azure SDK 2.6. Los proyectos de grupo de recursos de Azure permiten agrupar y publicar varios recursos de Azure relacionados en una única operación de implementación. Los proyectos de grupo de recursos de Azure usan una tecnología denominada **Administrador de recursos de Azure** para hacer su trabajo. El **Administrador de recursos de Azure** es un servicio de la API de REST que permite definir grupos de recursos de Azure que contienen varios recursos de Azure que normalmente se usan conjuntamente y tienen un ciclo de vida similar. Mediante el uso de grupos de recursos, puede operar en todos los recursos de un grupo con una sola llamada a función, en lugar de llamar a funciones diferentes para cada recurso individual. Para obtener más información sobre los grupos de recursos de Azure, consulte [Uso del Portal de vista previa de Azure para administrar los recursos de Azure](resource-group-portal.md). Para ver un escenario más detallado de la implementación integral del grupo de recursos de Azure, consulte la página sobre el [grupo de recursos de Azure para Visual Studio](https://azure.microsoft.com/blog/azure-resource-manager-2-5-for-visual-studio/).

Los proyectos de grupo de recursos de Azure contienen plantillas de JSON del Administrador de recursos de Azure, que definen los elementos que se implementan en un grupo de recursos. Consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md) para obtener más información.

El Administrador de recursos de Azure tiene muchos proveedores de recursos diferentes disponibles que se pueden usar para implementar recursos, como Ubuntu Server y Windows Server 2012 R2. En este tema se usa un recurso de **aplicaciones web**, que implementa un sitio web básico vacío en Azure.

## Creación de proyectos de grupo de recursos de Azure

En este procedimiento, aprenderá a crear un proyecto de grupo de recursos de Azure con una plantilla **Aplicación web**.

### Para crear un proyecto de grupo de recursos de Azure

1. En Visual Studio, elija **Archivo**, **Nuevo proyecto** y **C#** o **Visual Basic**. A continuación, elija **Nube** y después el proyecto **Grupo de recursos de Azure**.

    ![Proyecto de implementación de la nube](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Elija la plantilla que desea implementar en el Administrador de recursos de Azure. En este ejemplo, elegimos la plantilla **Aplicación web**.

    ![Seleccionar una plantilla](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796669.png)

    También puede agregar más recursos al grupo de recursos en otro momento.

    >[AZURE.NOTE] La lista de plantillas disponibles se recupera en línea y puede cambiar.

    Visual Studio crea un proyecto de implementación de grupo de recursos de Azure para una aplicación web.

1. Expanda los nodos del proyecto de implementación para ver lo que se ha creado.

    Dado que elegimos la plantilla de aplicación web para este ejemplo, vea los siguientes archivos.

    |Nombre de archivo|Descripción|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Un script de PowerShell que invoca los comandos de PowerShell que se implementarán en el Administrador de recursos de Azure.<br />** Nota** Este script de PowerShell se usa por Visual Studio para implementar su plantilla. Los cambios que realice a este script también afectarán a la implementación en Visual Studio, por tanto, tenga cuidado.|
    !WebSite.json|Plantilla que define la infraestructura que quiere implementar en Azure.|
    |WebSite.param.dev.json|Un archivo de parámetros que contiene valores específicos necesarios para el archivo de configuración.|
    |AzCopy.exe|Herramienta usada por el script de PowerShell para copiar archivos desde la ruta de colocación del almacenamiento local al contenedor de cuentas de almacenamiento. Esta herramienta se usa solamente si configura el proyecto de implementación para implementar el código junto con la plantilla.|

    Todos los proyectos de implementación de grupo de recursos de Azure contienen estos cuatro archivos básicos. Otros proyectos pueden contener archivos adicionales para admitir otras funcionalidades.

## Personalización de un proyecto de grupo de recursos de Azure

Puede personalizar un proyecto de implementación mediante la modificación de los archivos de plantilla JSON que describen los recursos de Azure que desea implementar. JSON significa JavaScript Object Notation y es un formato de datos serializados con el que resulta sencillo trabajar.

Los proyectos de grupo de recursos de Azure tienen dos archivos de plantilla en el nodo **Plantillas** del Explorador de soluciones que se pueden modificar: un archivo de plantilla del Administrador de recursos de Azure y un archivo de parámetros.

- Los **archivos de plantilla del Administrador de recursos de Azure** (con la extensión .json) especifican los archivos que contienen los recursos que desea, así como los parámetros necesarios para el proyecto de implementación, como el nombre del sitio y la ubicación. También especifican las dependencias de los componentes en el grupo de recursos de Azure y sus propiedades, como nombres, etiquetas y reglas de desencadenadores. Puede modificar este archivo para agregar su propia funcionalidad. Por ejemplo, podría agregar una base de datos a la plantilla. Consulte la documentación de cada proveedor de recursos para averiguar los parámetros que debe proporcionar. Vea [Proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790572.aspx) para más información.

- Los **archivos de parámetros** (con la extensión `.param.*.json`) contienen valores para los parámetros especificados en el archivo de configuración que son necesarios para cada proveedor de recursos. En este ejemplo, el archivo de configuración para una aplicación web (WebSite.json) define los parámetros de *siteName* y *siteLocation*. Durante la implementación, se le pedirá que proporcione valores para los parámetros en el archivo de plantilla, y estos valores se almacenan en el archivo de parámetros. También puede editar el archivo de parámetros directamente.

Los archivos JSON se pueden editar en el editor de Visual Studio. Si instala las [Herramientas de PowerShell para Visual Studio](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597), obtendrá también resaltado de sintaxis, coincidencia de llaves e IntelliSense, para facilitar la lectura y la edición de scripts de PowerShell. Si aún no están instaladas, aparecerá un vínculo para instalar las herramientas de PowerShell en la parte superior del editor.

![Instalar las herramientas de PowerShell](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796671.png)

Los archivos JSON usan un esquema al que se hace referencia en la parte superior de cada archivo. Puede descargar el esquema y analizarlo para así comprenderlo mejor. El esquema define qué elementos se permiten, los tipos y los formatos de los campos, los valores posibles de los valores enumerados, etc.

Si quiere implementar en distintas configuraciones o cambiar la configuración con frecuencia, puede crear diferentes copias del archivo *param*. Pruebe a usar la misma plantilla para todos los entornos.

## Implementación de un proyecto de grupo de recursos de Azure en un grupo de recursos de Azure

Al implementar un proyecto de grupo de recursos de Azure, lo implementa en un grupo de recursos de Azure, que es simplemente una agrupación lógica de los recursos de Azure, como aplicaciones web, bases de datos, etc.

1. En el menú contextual del nodo de proyecto de implementación, elija **Implementar**, **Nueva implementación**.

    ![Implementar, elemento de menú Nueva implementación](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    Aparece el cuadro de diálogo **Implementar en grupo de recursos**.

    ![Cuadro de diálogo Implementar en grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796673.png)

1. En el cuadro desplegable **Grupo de recursos**, elija un grupo de recursos existente o cree uno nuevo. Para crear un grupo de recursos, abra el cuadro desplegable **Grupo de recursos** y elija **<Create New...>**.

    Aparece el cuadro de diálogo **Crear grupo de recursos**.

    ![Cuadro de diálogo Crear grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796674.png)

    >[AZURE.NOTE] Normalmente, cuando se inicia un nuevo proyecto de implementación, creará un nuevo grupo de recursos como destino de la implementación.

1. Escriba un nombre y una ubicación para el grupo de recursos y después elija el botón **Crear**.

    La ubicación del grupo de recursos no tiene que ser la misma que la ubicación de los recursos, ya que los recursos de un grupo pueden abarca varias regiones.

1. Elija los archivos de configuración de plantilla y de parámetros que desee usar para esta implementación, o bien acepte los valores predeterminados.

    Puede editar las propiedades de un recurso si elige el botón **Editar parámetros**. En caso de que falten parámetros necesarios al realizar la implementación, aparece el cuadro de diálogo **Editar parámetros**, donde puede proporcionarlos. Los parámetros a los que les faltan valores muestran **<null>** en el cuadro **Valor**. En este ejemplo (un recurso de aplicación web), los parámetros necesarios incluyen el nombre del sitio, el plan de hospedaje y la ubicación de sitio. (Si lo recuerda, estos valores de parámetro se establecen en null de forma predeterminada en el archivo de parámetros). Los demás parámetros tienen valores predeterminados.

    ![Cuadro de diálogo Editar parámetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796675.png)

1. En el cuadro de diálogo **Editar parámetros**, escriba el nombre del sitio, la ubicación del sitio y el nombre del plan de hospedaje, y compruebe los valores de las demás propiedades. Cuando haya terminado, elija el botón **Guardar**.

    - El parámetro *siteName* es la primera parte de la dirección URL de la página web. Por ejemplo, para la dirección URL misitioweb.azurewebsites.net, el nombre del sitio es **misitioweb**.

    - El parámetro *hostingPlanName* especifica el plan de hospedaje. En este ejemplo, puede usar "Gratis". Para obtener más información sobre los planes de hospedaje, consulte [Introducción detallada sobre los planes de Servicio de aplicaciones de Azure](https://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).

    - El parámetro *siteLocation* hace referencia a la región de Azure donde se hospeda el sitio, por ejemplo, "Oeste de EE. UU.". Para obtener una lista de regiones disponibles, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

1. Elija el botón **Implementar** para implementar el proyecto en Azure.

    Puede ver el progreso de la implementación en la ventana **Resultados**. La implementación puede tardar varios minutos en completarse, dependiendo de la configuración.

    >[AZURE.NOTE] Puede que se le pida que instale los cmdlets de Microsoft Azure PowerShell. Como estos cmdlets son necesarios para implementar grupos de recursos de Azure, deberá instalarlos.

1. En el explorador, abra el [Portal de vista previa de Azure](https://portal.azure.com/). Dado que se trata de un nuevo cambio, debe aparecer un nuevo mensaje de notificación en la pestaña **Notificaciones**. Elija si desea ver los detalles sobre el nuevo grupo de recursos de Azure. Para ver una lista de todos los grupos de recursos disponibles, elija la pestaña **Examinar** y después **Grupos de recursos**.

    ![El grupo de recursos de Azure aprovisionado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796676.png)

1. Si realiza cambios y desea volver a implementar el proyecto, puede elegir el grupo de recursos existente directamente desde el menú contextual del proyecto de grupo de recursos de Azure. En el menú contextual, elija **Implementar** y después el grupo de recursos en el que acaba de implementar.

    ![Grupo de recursos de Azure implementado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796677.png)

    Con la implementación de un grupo de recursos de Azure solo se implementa ese proyecto. Si su solución tiene un proyecto de código o cualquier otro proyecto, deberá implementarlos por separado.

## Uso de proyectos de implementación de la nube de Azure SDK 2.5 con Azure SDK 2.6

Si usa proyectos de implementación de la nube que se crearon con el SDK de Azure 2.5, debería actualizar al SDK de Azure 2.6 o posterior, para poder usar las nuevas características de edición e implementación de plantillas de recursos de Azure. La manera más fácil de reutilizar las plantillas creadas con el SDK de Azure 2.5 es crear la versión del proyecto para el SDK de Azure 2.6 o posterior, mover las plantillas a ese proyecto y realizar algunas modificaciones.

### Para usar proyectos de implementación de la nube del SDK de Azure 2.5 con el SDK de Azure 2.6 o posterior

1. Agregue un proyecto de grupo de recursos de Azure del SDK de Azure 2.6 o posterior a la solución.

    1. Abra la solución que contiene el proyecto de implementación de la nube de Azure SDK 2.5.

    1. En el menú **Archivo**, elija **Nuevo**, **Proyecto**.

    1. En el cuadro de diálogo **Nuevo proyecto**, busque el proyecto **Grupo de recursos de Azure** en **Visual C#**/**Nube** o **Visual Basic**/**Nube**.

         El nombre de la plantilla de proyecto se cambió de **Implementación de la nube** a Grupo de recursos de Azure.

    1. Asigne un nombre al proyecto.

    1. Cambie el cuadro desplegable de soluciones a **Agregar a solución**.

    1. A continuación, se le solicitará que elija una plantilla. Puesto que va a mover plantillas existentes desde el proyecto de implementación de la nube de Azure SDK 2.5, puede elegir cualquier plantilla, así que vamos a seleccionar la plantilla en blanco en la parte inferior de la lista.

    1. Elija el botón **Aceptar**.

        El nuevo proyecto se agrega a la solución.

1. Copie los archivos de plantilla y de parámetros del proyecto de implementación de la nube del SDK de Azure 2.5 en el proyecto de grupo de recursos del SDK de Azure 2.6 o posterior.

    1. En el Explorador de soluciones, busque los archivos de plantilla y de parámetros que desea copiar en el proyecto de implementación de Azure SDK 2.5, selecciónelos y cópielos.

    2. Pegue los archivos en la carpeta **Plantillas** del nuevo proyecto de grupo de recursos del SDK de Azure 2.6 o posterior.

1. En el Explorador de soluciones, busque los archivos de plantilla y de parámetros que desea copiar en el proyecto de implementación de Azure SDK 2.5, selecciónelos y cópielos.

1. Pegue los archivos en la carpeta Plantillas del nuevo proyecto de grupo de recursos del SDK de Azure 2.6 o posterior.

1. Si también va a implementar una aplicación web con la plantilla, deberá crear una referencia desde el nuevo proyecto de grupo de recursos del SDK de Azure 2.6 o posterior hacia la aplicación web.

    1. En el menú contextual del nodo **Referencias** del nuevo proyecto de grupo de recursos del SDK de Azure 2.6 o posterior en el Explorador de soluciones, elija **Agregar referencia**.

    1. Active la casilla situada junto a la aplicación web en la lista de proyectos y después elija el botón **Aceptar**.

1. En el menú contextual del nodo Referencias del nuevo proyecto de grupo de recursos del SDK de Azure 2.6 o posterior en el Explorador de soluciones, elija Agregar referencia.

1. Active la casilla situada junto a la aplicación web en la lista de proyectos y, luego, elija el botón Aceptar.

1. Cambie todas las apariciones de los parámetros *dropLocation* y *dropLocationSasToken* por *\_artifactsLocation* y *\_artifactsLocationSasToken*.

1. Si no piensa usarlos, puede eliminar los archivos de plantilla y de parámetros vacíos que se agregaron automáticamente al proyecto del SDK de Azure 2.6 o posterior al crearlo.

    1. Elimine el archivo DeployTemplate.json.

    1. Elimine el archivo DeploymentTemplate.param.dev.json.

1. Si realizó cambios en el script Publish-AzureResourceGroup.ps1 en el proyecto del SDK de Azure 2.5, deberá moverlos al script Deploy-AzureResourceGroup.ps1 en el proyecto del SDK de Azure 2.6 o posterior.

    Ahora puede implementar la plantilla mediante el comando de implementación en el proyecto de grupo de recursos del SDK de Azure 2.6 o posterior y aprovechar las nuevas características de edición de plantillas en el SDK de Azure 2.6. Una vez que el proyecto de la versión 2.6 o posterior funciona a su gusto, puede quitar el proyecto del SDK de Azure 2.5 de la solución.

## ¿Por qué el proyecto necesita actualización?

Se realizaron algunos cambios en las plantillas implementadas en el SDK de Azure 2.6 que hacen que el script y las plantillas de implementación del SDK de Azure 2.5 sean incompatibles. El primero de ellos, y de mayor envergadura, es la implementación que se inicia. En el SDK de Azure 2.5, se compiló código que usaba las API de REST de Azure para cargar la plantilla e iniciar la implementación. Los comentarios de los desarrolladores indicaban que preferirían que Visual Studio iniciara simplemente el script de PowerShell que se incluye en el proyecto. Por lo tanto, en el SDK de Azure 2.6, el comando deploy inicia el script de PowerShell que se incluye en el proyecto para implementar la plantilla. Esto le permite personalizar la implementación sin que esas personalizaciones dejen de ejecutarse, tanto si implementa desde la línea de comandos mediante Azure PowerShell como a través de Visual Studio con el comando deploy. Para implementar desde Visual Studio, deberá usar el script de PowerShell de implementación del SDK de Azure 2.6 (o posterior) cuando tenga instalado el SDK de Azure 2.6 o posterior.

También se realizaron ajustes en algunos nombres de variables y tareas de compilación para que se adaptaran mejor a las convenciones de nomenclatura de las compilaciones automatizadas de TFS y otros proyectos de Microsoft. El código de Visual Studio que recopila las variables y los valores necesarios para iniciar el script de PowerShell buscará estos nuevos nombres.

## Pasos siguientes

Para información sobre cómo agregar recursos a su Grupo de recursos de Azure en Visual Studio, vea [Edición de plantillas del Administrador de recursos con Visual Studio](vs-azure-tools-resource-group-adding-resources.md).

<!---HONumber=AcomDC_0128_2016-->