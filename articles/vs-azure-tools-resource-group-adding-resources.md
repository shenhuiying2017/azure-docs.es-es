<properties 
   pageTitle="Edición de una plantilla del Administrador de recursos con Visual Studio | Microsoft Azure"
   description="Aprenda a agregar recursos a una plantilla del Administrador de recursos de Azure mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="11/13/2015"
   ms.author="kempb" />

# Edición de plantillas del Administrador de recursos con Visual Studio

Visual Studio le permite editar la plantilla del Administrador de recursos para el grupo de recursos. Puede realizar cambios a la plantilla mediante la ventana Esquema JSON de Visual Studio o directamente en la sintaxis de la plantilla.

## Incorporación de recursos a un grupo de recursos mediante la ventana Esquema JSON

### Para agregar recursos a un grupo de recursos

1. En el Explorador de soluciones, elija el archivo JSON para el grupo de recursos de Azure. El archivo JSON aparece en el editor y la ventana **Esquema JSON** también aparece junto al editor. Si cierra la ventana Esquema de JSON, no se volverá a abrir automáticamente hasta que elija el comando Mostrar contorno en el menú contextual de un archivo de plantilla JSON (no un archivo de parámetros).

    ![Archivo JSON para un grupo de recursos de Azure](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. En la ventana **Esquema JSON**, elija el **Recursos** y luego elija el comando **Agregar nuevo recurso** en el menú contextual o elija el botón **Agregar recurso** situado en la parte superior de la ventana Esquema de JSON.

    ![Incorporación de un nuevo recurso a un grupo de recursos](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. En la lista de recursos en el cuadro de diálogo **Agregar recurso**, elija el recurso que desea agregar, proporcione la información requerida por el recurso y, a continuación, elija el botón **Agregar**. Por ejemplo, si agrega una cuenta de almacenamiento, deberá proporcionar un nombre base para los parámetros que se crearán.
 
    ![Cuadro de diálogo Agregar recurso](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    Se agrega el recurso a las listas de recursos en la ventana **Esquema JSON** y aparece en el archivo el nuevo JSON que representa al recurso. También se pueden agregar parámetros o variables relacionados.


    ![Recurso agregado al archivo JSON](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. Implemente el nuevo recurso en el grupo de recursos de Azure. En el menú contextual del proyecto del grupo de recursos en el Explorador de soluciones, elija **Implementar** y, a continuación, elija el nombre del grupo de recursos.

    ![Grupo de recursos de Azure implementado](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    Aparece el cuadro de diálogo **Implementar en grupo de recursos**.


1. Elija el botón **Implementar**.

1. Si es necesario que especifique algún parámetro, aparece el cuadro de diálogo **Editar parámetros**. Escriba los valores requeridos y, a continuación, elija el botón **Guardar**. El nuevo recurso se implementa en el grupo de recursos de Azure.

## Edición de la sintaxis de la plantilla

Con Visual Studio, también puede editar la plantilla directamente. Cuando empiece a editar valores de la plantilla, el editor le ayudará sobre los valores posibles que puede proporcionar.

![Editar plantilla](./media/vs-azure-tools-resource-group-adding-resources/arm-edit-template.png)

Para más información sobre la estructura de la plantilla, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).

## Otras referencias

[Creación e implementación de grupos de recursos de Azure mediante Visual Studio](vs-azure-tools-resource-groups-deployment-project-create-deploy.md)

[Cmdlets de Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx)

[Uso de Windows PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager/)

[Vídeo de Channel9: Administrador de recursos de Azure](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=AcomDC_1210_2015-->