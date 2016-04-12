<properties
	pageTitle="Agregar la API de Microsoft Translator a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Microsoft Translator en el entorno del Servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Creación de una nueva API de Microsoft Translator en PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/connectors-create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

Agregue la API de Microsoft Translator al entorno del Servicio de aplicaciones de la organización(inquilino).

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas:  
![][7]

3. En la lista, desplácese para encontrar PowerApps o escriba *powerapps*:  
![][8]  

4. En **PowerApps**, seleccione **Administrar API**:  
![Examine las APIs registradas][1]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API:  
![Add API][2]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **Microsoft Translator**:  
![seleccionar la api de Microsoft Translator][3]

8. Seleccione **Configuración: Configure los ajustes necesarios**:  
![establecer la configuración de la API de Microsoft Translator][4]

9. Escriba el *Id. de cliente* y el *Secreto de cliente* de la aplicación Microsoft Translator. Si no dispone de estos, consulte la sección "Registrar una aplicación Microsoft Translator para su uso con PowerApps" en este tema para crear el identificador y los valores secretos que necesita.

9. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de Microsoft Translator al entorno del Servicio de aplicaciones.


## Opcional: registrar una aplicación Microsoft Translator para su uso con PowerApps

Si no tiene una aplicación Microsoft Translator existente con los valores de Id. y secreto, use los pasos siguientes para crear la aplicación y obtenga los valores que necesita.


1. Vaya a la [página del programador de Azure Data Market][5] e inicie sesión con su Cuenta de Microsoft. 

2. Seleccione **Registrar**.

3. En **Registrar su aplicación**:

	1. Escriba un valor en **Id. de cliente**.  
	2. Escriba el **nombre** de la aplicación.  
	3. Escriba un valor ficticio en **URL de redireccionamiento**. Por ejemplo, escriba: *https://contosoredirecturl*.  
	4. Escriba una **descripción**.  
	5. Seleccione **Crear**.  

	![Registrar su aplicación][6]

Se creará una nueva aplicación Microsoft Translator. Puede usar esta aplicación en la configuración de la API de Microsoft Translator en el Portal de Azure.

## Visualización de las API de REST

Referencia de [API de REST de Microsoft Translator](../connectors/connectors-create-api-microsofttranslator.md).

## Resumen y pasos siguientes
En este tema, ha agregado la API de Microsoft Translator a su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!---HONumber=AcomDC_0330_2016-->