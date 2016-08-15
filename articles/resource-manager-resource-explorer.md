<properties
   pageTitle="Explorador de recursos de Azure | Microsoft Azure"
   description="Describe el Explorador de recursos de Azure y cómo se puede usar para ver y actualizar las implementaciones mediante Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# Uso del Explorador de recursos de Azure para ver y modificar recursos
El [Explorador de recursos de Azure](https://resources.azure.com) es una excelente herramienta para buscar recursos que ya haya creado en su suscripción. Con esta herramienta, puede comprender cómo se estructuran los recursos y ver las propiedades asignadas a cada recurso. Puede aprender acerca de las operaciones de API de REST y los cmdlets de PowerShell que están disponibles para un tipo de recurso y puede emitir comandos a través de la interfaz. El Explorador de recursos puede resultar especialmente útil al crear plantillas de Azure Resource Manager porque le permite visualizar las propiedades de los recursos existentes.

El código fuente de la herramienta Explorador de recursos está disponible en [github](https://github.com/projectkudu/ARMExplorer), que proporciona una excelente referencia si necesita implementar un comportamiento similar en sus propias aplicaciones.

## Visualización de recursos
Vaya a [https://resources.azure.com](https://resources.azure.com) e inicie sesión con las mismas credenciales que usa para el [Portal de Azure](https://portal.azure.com).

Una vez cargada, la vista de árbol de la izquierda le permite profundizar en sus suscripciones y grupos de recursos:

![vista de árbol](./media/resource-manager-resource-explorer/are-01-treeview.png)

A medida que se desplace a un grupo de recursos verá los proveedores para los que hay recursos en ese grupo:

![providers](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

Desde ahí puede comenzar a obtener detalles de las instancias de recursos. En la siguiente captura de pantalla, puede ver la instancia de SQL Server `sltest` en la vista de árbol. En el lado derecho, aparece información acerca de las solicitudes de API de REST que se pueden usar con ese recurso. Si se desplaza hasta el nodo de un recurso, el Explorador de recursos envía automáticamente la solicitud GET para recuperar información sobre el recurso. En el área de texto grande debajo de la dirección URL, verá la respuesta de la API.

A medida que se familiarice con las plantillas de Resource Manager, el contenido del cuerpo le empezará a resultar familiar. La sección **Propiedades** de la respuesta coincide con los valores que pueden proporcionarse en la sección **Propiedades** de la plantilla.

![sql server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

El Explorador de recursos permite mantener la obtención de detalles para explorar los recursos secundarios. En el caso del servidor de Base de datos SQL, hay recursos secundarios para elementos como bases de datos y reglas de firewall.

Al explorar una base de datos, se muestran sus propiedades. En la siguiente captura de pantalla, vemos que la base de datos `edition` es `Standard` y `serviceLevelObjective` (o nivel de base de datos) es `S1`.

![base de datos sql](./media/resource-manager-resource-explorer/are-04-database-get.png)

## Cambio de recursos

Una vez localizado un recurso, puede seleccionar el botón Editar para editar el contenido del archivo JSON. Después, puede usar el Explorador de recursos para editar el archivo JSON y enviar una solicitud PUT para cambiar el recurso. Por ejemplo, la siguiente imagen muestra el nivel de base de datos cambiado a `S0`:

![base de datos - solicitud PUT](./media/resource-manager-resource-explorer/are-05-database-put.png)

Al seleccionar **PUT**, envía la solicitud.

Una vez enviada la solicitud, el Explorador de recursos vuelve a emitir la solicitud GET para actualizar el estado. En este caso, vemos que `requestedServiceObjectiveId` se ha actualizado y es diferente de `currentServiceObjectiveId`, que indica que una operación de escalado está en curso. Puede hacer clic en el botón GET para actualizar el estado manualmente.

![base de datos - solicitud GET 2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## Realización de acciones en los recursos

La pestaña **Acciones** le permite ver y realizar otras operaciones REST. Por ejemplo, si ha seleccionado un recurso de sitio web, la pestaña Acciones presenta una larga lista de operaciones disponibles, algunas de las cuales se muestran a continuación.

![web - solicitud POST](./media/resource-manager-resource-explorer/are-web-post.png)

## Invocación de la API mediante PowerShell
La pestaña PowerShell del Explorador de recursos muestra los cmdlets que debe usar para interactuar con el recurso que está explorando actualmente. Según el tipo de recurso seleccionado, el script de PowerShell que se muestra puede oscilar entre cmdlets simples (como `Get-AzureRmResource` y `Set-AzureRmResource`) y cmdlets más complejos (como el intercambio de ranuras en un sitio web).

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Para más información sobre los cmdlets de Azure PowerShell, consulte [Uso de Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).

## Resumen
Cuando trabaje con Resource Manager, el Explorador de recursos puede ser una herramienta muy útil. Es una excelente forma de encontrar maneras de usar PowerShell para consultar y realizar cambios. Si está trabajando con la API de REST, es una excelente forma de comenzar a trabajar y probar rápidamente las llamadas a API antes de empezar a escribir código. Y si escribe plantillas, puede ser una excelente forma de comprender la jerarquía de recursos y encontrar la ubicación de la configuración: puede realizar un cambio en el Portal y, a continuación, buscar las entradas correspondientes en el Explorador de recursos.

Para obtener más información, vea el [vídeo de Channel 9 con Scott Hanselman y David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)

<!---HONumber=AcomDC_0803_2016-->