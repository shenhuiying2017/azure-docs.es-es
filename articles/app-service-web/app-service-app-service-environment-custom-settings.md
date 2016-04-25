<properties 
	pageTitle="Configuración personalizada para Entornos del Servicio de aplicaciones" 
	description="Opciones de configuración personalizada para Entornos del Servicio de aplicaciones" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="stefsch"/>

# Opciones de configuración personalizada para Entornos del Servicio de aplicaciones

## Información general ##
Puesto que los Entornos del Servicio de aplicaciones están aislados en un solo cliente, hay ciertas opciones de configuración que se pueden aplicar exclusivamente a un Entorno del Servicio de aplicaciones. En este artículo se documentan las distintas personalizaciones específicas del Entorno del Servicio de aplicaciones que hay disponibles.

Las personalizaciones del Entorno del Servicio de aplicaciones se almacenan usando una matriz en el nuevo atributo "clusterSettings" que se encuentra en el diccionario "Properties" de la entidad ARM *hostingEnvironments*.

El siguiente fragmento de código abreviado de la plantilla ARM muestra el atributo "clusterSettings":


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

El atributo "clusterSettings" puede incluirse en una plantilla ARM para actualizar el Entorno del Servicio de aplicaciones.

También se puede actualizar el valor del atributo con el [Explorador de recursos de Azure](https://resources.azure.com). En el Explorador de recursos de Azure, navegue hasta el nodo del Entorno del Servicio de aplicaciones (subscriptions --> resourceGroups --> providers --> Micrososft.Web --> hostingEnvironments) y haga clic en el entorno específico que quiere actualizar.

En la ventana derecha del explorador, haga clic en "Read/Write" en la barra de herramientas superior para permitir la edición interactiva en Azure Resource Explorer. Después, haga clic en el botón azul "Edit" para que pueda modificar la plantilla ARM. Desplácese hasta la parte inferior de la ventana derecha. El atributo "clusterSettings" se encuentra en la parte inferior, donde puede especificar o actualizar su valor.

Escriba (o copie y pegue) la matriz de valores de configuración que quiera en el atributo "clusterSettings". Después, haga clic en el botón verde "PUT" situado en la parte superior de la ventana derecha para confirmar el cambio en el Entorno del Servicio de aplicaciones.

Independientemente del enfoque elegido para actualizar el Entorno del Servicio de aplicaciones, una vez que se envía el cambio, este tendrá efecto en aproximadamente 30 minutos, multiplicados por el número de servidores front-end del Entorno del Servicio de aplicaciones. Por ejemplo si un Entorno del Servicio de aplicaciones tiene cuatro front-end, se tardará aproximadamente dos horas en completar la actualización de la configuración. Mientras se implementa el cambio de configuración, no será posible realizar otras operaciones de escalado o de cambio de configuración en el Entorno del Servicio de aplicaciones.

## Deshabilitación de TLS 1.0 ##
Una petición recurrente de los clientes, especialmente de aquellos con auditorías de cumplimiento de PCI, es la capacidad de deshabilitar explícitamente TLS 1.0 para sus aplicaciones.

TLS 1.0 se puede deshabilitar con la siguiente entrada de *clusterSettings*:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## Introducción
El sitio de plantillas de inicio rápido de Azure ARM incluye una plantilla con la definición base para [crear un Entorno del Servicio de aplicaciones](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)


<!-- LINKS -->

<!-- IMAGES -->
 

<!---HONumber=AcomDC_0413_2016-->