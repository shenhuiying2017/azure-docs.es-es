<properties
	pageTitle="Agregar la API de usuarios de Office 365 a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de usuarios de Office 365 en el entorno del Servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Creación de una nueva API de usuarios de Office 365 en PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

Agregue la API de usuarios de Office 365 al entorno del Servicio de aplicaciones de su organización (inquilino).

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@*SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas:  
![][14]

3. En la lista, desplácese para encontrar PowerApps o escriba *powerapps*:  
![][15]  

4. En **PowerApps**, seleccione **Administrar API**:    
![Examine las APIs registradas][1]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API:  
![Add API][2]

6. Escriba un **nombre** descriptivo para la API.  
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **Usuarios de Office 365**:  
![seleccionar la api de usuarios de Office 365][3]

8. Seleccione **Configuración: Configure los ajustes necesarios**:  
![establecer la configuración de la API de usuarios de Office 365][4]

9. Escriba el *Id. de cliente* y el *Secreto de cliente* de la aplicación Azure Active Directory (AAD) de Office 365. Si no dispone de estos, consulte la sección "Registrar una aplicación de AAD para su uso con PowerApps" en este tema para crear el identificador y los valores secretos que necesita.

	> [AZURE.IMPORTANT] Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.  

10. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de usuarios de Office 365 al entorno del Servicio de aplicaciones.

## Opcional: registrar una aplicación de AAD para su uso con la API de usuarios de Office 365 de PowerApps

Si no tiene una aplicación AAD existente con los valores de clave y secreto, use los pasos siguientes para crear la aplicación y obtenga los valores que necesita.

1. Abra [Portal de Azure][5].

2. Seleccione **Examinar** y, a continuación, seleccione **Active Directory**:

	> [AZURE.NOTE] De este modo se abre Active Directory en el Portal de Azure clásico.

3. Seleccione el nombre del inquilino de su organización:  
![Iniciar Azure Active Directory][6]

4. Seleccione la pestaña **Aplicaciones** y seleccione **Agregar**:  
![Aplicaciones del inquilino de AAD][7].

5. En **Agregar aplicación**:

	1. Escriba el **nombre** de la aplicación.  
	2. Deje el tipo de aplicación como **Web**.  
	3. Seleccione **Siguiente**.  

	![Agregar aplicación de AAD: información de la aplicación][8]

6. En **Propiedades de la aplicación**:  

	1. Especifique la **URL de inicio de sesión** de la aplicación. Dado que va a realizar la autenticación con AAD para PowerApps, establezca la URL de inicio de sesión en \__https://login.windows.net_.
	2. Escriba un valor válido de **URI de id. de aplicación** para la aplicación.  
	3. Seleccione **Aceptar**.  

	![Agregar aplicación de AAD: propiedades de la aplicación][9]

7. Cuando se finalice correctamente, se le redirigirá a la nueva aplicación de AAD. Seleccione **Configurar**:  
![Aplicación AAD de Contoso][10]

8. Establezca la **Dirección URL de respuesta** de la sección _OAuth 2_ en la dirección URL de redireccionamiento que recibió cuando se agregó la nueva API de usuarios de Office 365 en el Portal de Azure (en este tema). Seleccione **Agregar una aplicación**:  
![Configurar aplicación AAD de Contoso][11]

9. En la ventana **Permisos para otras aplicaciones**, seleccione **API unificada de Office 365 (vista previa)** y seleccione **Aceptar**.

10. De nuevo en la página de configuración, tenga en cuenta que la _API unificada de Office 365 (vista previa)_ se agrega a la lista _Permisos para otras aplicaciones_.

11. Seleccione **Permisos delegados** para _API unificada de Office 365 (vista previa)_ y seleccione el permiso **Leer los perfiles básicos de todos los usuarios**.

Se creará una nueva aplicación de Azure Active Directory. Puede usar esta aplicación en la configuración de la API de usuarios de Office 365 en el Portal de Azure.

Encontrará información válida sobre aplicaciones de AAD en [Cómo y por qué se agregan aplicaciones a Azure AD](../active-directory/active-directory-how-applications-are-added.md).

## Visualización de las API de REST

Referencia de [API de REST de usuarios de Office 365](../connectors/create-api-office365-users.md).

## Resumen y pasos siguientes
En este tema ha agregado la API de Usuarios de Office 365 a su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!----HONumber=AcomDC_0309_2016-->