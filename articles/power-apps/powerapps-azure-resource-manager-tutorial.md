<properties
	pageTitle="Tutorial: Creación de una API personalizada con Azure Resource Manager en PowerApps y flujos de lógica | Microsoft Azure"
	description="Tutorial de Azure Resource Manager para crear una API personalizada en PowerApps y flujos de lógica"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>


# Tutorial: Creación de una API de ARM protegida de AAD personalizada para PowerApps y flujos de lógica 

Este tutorial le guiará a través de los pasos necesarios para registrar un archivo Swagger que describe la [API de ARM][6] y, luego, conectarse a la API personalizada en PowerApps.

>[AZURE.IMPORTANT] Este tema se ha movido a powerapps.microsoft.com en [Tutorial: Create a custom AAD protected ARM API for PowerApps and Flows](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) (Tutorial: Creación de una API personalizada de ARM protegida de AAD para PowerApps y flujos). Vaya a PowerApps para ver la versión más reciente. Este vínculo de Azure está en proceso de archivo.


## Qué necesita para empezar

- Una suscripción de Azure
- Una cuenta de PowerApps

## Habilitar la autenticación en Azure Active Directory

En primer lugar, se debe crear una aplicación de Azure Active Directory (AAD) que realice la autenticación mientras se llama al punto de conexión de API de ARM.

1. Para crear una aplicación AAD, inicie sesión en su [suscripción a Azure][7] y vaya a **Active Directory**: ![](./media/powerapps-azure-resource-manager-tutorial/azureaad.png "Azure AAD")

2. En esta página, elija el directorio en el que desea crear su aplicación AAD. Seleccione el directorio, vaya a la pestaña **Aplicaciones** y seleccione **Agregar**: ![](./media/powerapps-azure-resource-manager-tutorial/azureapplication.png "Aplicación de Azure")

3. Escriba un nombre para la aplicación, seleccione **Aplicación web y/o API web** y, luego, seleccione **Siguiente**: ![](./media/powerapps-azure-resource-manager-tutorial/newapplication.png "Aplicación nueva")

4. En **URL de inicio de sesión**, escriba: **http://login.windows.net**. En **URI de id. de aplicación**, escriba cualquier URI único. Luego, seleccione **Completar**: ![](./media/powerapps-azure-resource-manager-tutorial/newapplication2.png "Segunda aplicación nueva")

5. Una vez que se crea la aplicación AAD, vaya a la pestaña **Configurar**. En esta pestaña, configuramos los permisos de la aplicación.

6. En los **permisos para otras aplicaciones**, seleccione **Agregar aplicación** y escriba los siguientes permisos, tal como se muestra: ![](./media/powerapps-azure-resource-manager-tutorial/permissions.png "Permisos")

	Asegúrese de delegar los permisos necesarios para su aplicación: ![](./media/powerapps-azure-resource-manager-tutorial/permissions2.png "Delegar permisos")

7. En **Claves**, seleccione cualquier duración. **Copie y guarde la clave en una ubicación segura**; la necesitaremos más adelante. Anote también el __id. de cliente__: ![](./media/powerapps-azure-resource-manager-tutorial/configurekeys.png "Configurar las claves")

8. En **Inicio de sesión único**, escriba la siguiente dirección URL en __reply-URL__: https://msmanaged-na.consent.azure-apim.net/redirect: ![](./media/powerapps-azure-resource-manager-tutorial/redirecturl.png "URL de redireccionamiento")

9. **Guarde** los cambios. **Copie y guarde la clave en una ubicación segura**.

## Agregar la conexión en PowerApps o flujos de lógica

Ahora que la aplicación AAD está configurada, agreguemos la API personalizada.

1. Abra el [portal web de PowerApps][1], vaya a la pestaña **Connections** (Conexiones) y, luego, seleccione __Add a connection__ (Agregar una conexión) en la esquina superior derecha: ![](./media/powerapps-azure-resource-manager-tutorial/createnewconnection.png "Crear API personalizada")

2. Seleccione __Add a Custom API__ (Agregar una API personalizada): ![](./media/powerapps-azure-resource-manager-tutorial/connecttocustomapi.png "Crear API personalizada")

3. Cargue el archivo Swagger de ARM, que está disponible [para descarga][8]\: ![](./media/powerapps-azure-resource-manager-tutorial/createcustom.png "Crear API personalizada")

4. En la siguiente pantalla, dado que se detecta el archivo Swagger para usar la autenticación de AAD, debe escribir el id. de cliente de AAD, el secreto de cliente (la **clave** que almacenó en una ubicación segura) y otras configuraciones: ![](./media/powerapps-azure-resource-manager-tutorial/oauthsettings.png "Configuración de OAuth")

5. Si todos los ajustes están correctamente configurados, puede usar la API personalizada de ARM. Para ello, cree una conexión y, luego, haga referencia a ella cuando compile su instancia de PowerApp o de flujo de lógica: ![](./media/powerapps-azure-resource-manager-tutorial/createdcustomapi.png "CustomAPI creada")

De forma similar, puede obtener acceso a los datos que estén expuestos mediante el uso de API de RESTful y autenticados a través de OAuth2 de AAD.

Para obtener información más detallada sobre la creación de PowerApps y flujos de lógica, consulte lo siguiente:

- [Connect to Office 365, Twitter, and Microsoft Translator (Conexión a Office 365, Twitter y Microsoft Translator)][5]
- [Show data from Office 365 (Visualización de datos de Office 365)][4]
- [Create an app from data (Creación de una aplicación a partir de datos)][3]
- [Get started with logic flows (Introducción a los flujos de lógica)][2]

Si tiene alguna pregunta o comentario sobre las API personalizadas, envíe un correo electrónico a [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).


<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: https://msdn.microsoft.com/library/azure/dn790568.aspx
[7]: https://manage.windowsazure.com
[8]: http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json

<!---HONumber=AcomDC_0713_2016-->