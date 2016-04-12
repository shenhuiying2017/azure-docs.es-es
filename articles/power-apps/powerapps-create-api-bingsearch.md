<properties
	pageTitle="Agregar la API de Búsqueda de Bing a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Búsqueda de Bing en el entorno del servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Creación de una nueva API de Búsqueda de Bing en PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/connectors-create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)

Agregue la API de Búsqueda de Bing al entorno del Servicio de aplicaciones de su organización (inquilino).

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][4]

3. En la lista, desplácese para encontrar PowerApps o escriba *powerapps*: ![][5]

4. En **PowerApps**, seleccione **Administrar API**: ![Examinar las API registradas][2]

2. En **Administrar API**, seleccione **Agregar** para agregar la nueva API: ![Add API][3]

3. Escriba un **nombre** descriptivo para la API.

4. En **Origen**, seleccione **API disponible** para seleccionar las API preconfiguradas y seleccione **Búsqueda de Bing**:

	1. Seleccione **Configuración: Configure los ajustes necesarios**.
	2. Escriba el valor de *Clave de cuenta*. Si no tiene una clave de Búsqueda de Bing, cree una [oferta de Búsqueda de Bing][1] gratuita para obtener una clave.
	3. Seleccione **Aceptar**. 

5. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de Búsqueda de Bing en su entorno del servicio de aplicaciones.

## Visualización de las API de REST

Referencia de [API de REST de Búsqueda de Bing](../connectors/connectors-create-api-bingsearch.md).


## Resumen y pasos siguientes
En este tema, la API de Búsqueda de Bing ya está agregada a su servicio PowersApps Enterprise. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_0330_2016-->