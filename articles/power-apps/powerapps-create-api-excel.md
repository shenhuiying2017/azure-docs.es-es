<properties
	pageTitle="Agregar la API de Excel a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Excel en el entorno del servicio de aplicaciones de la organización"
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

# Creación de una nueva API de Excel en PowerApps Enterprise

Agregue la API de Excel al entorno del Servicio de aplicaciones de su organización (inquilino).

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][4]

3. En la lista, desplácese para encontrar PowerApps o escriba *powerapps*: ![][5]

4. En **PowerApps**, seleccione **Administrar APIs**: ![Examinar las API registradas][1]

5. En **Administrar APIs**, seleccione **Agregar** para agregar la nueva API: ![Add API][2]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **Excel**: ![Seleccionar la API de Excel][3]

8. Seleccione **Aceptar** para completar estos pasos.

Cuando termine, se agrega una nueva API de Excel en el entorno de servicio de aplicaciones.

## Resumen y pasos siguientes
En este tema, la API de Excel ya está agregada a su servicio PowersApps Enterprise. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)



<!--References-->
[1]: ./media/powerapps-create-api-excel/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-excel/add-api.PNG
[3]: ./media/powerapps-create-api-excel/select-excel-api.PNG
[4]: ./media/powerapps-create-api-excel/browseall.png
[5]: ./media/powerapps-create-api-excel/allresources.png

<!---HONumber=AcomDC_0309_2016-->