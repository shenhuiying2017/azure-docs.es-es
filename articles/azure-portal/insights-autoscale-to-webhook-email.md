<properties
	pageTitle="Azure Insights: uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook | Microsoft Azure"
	description="Descubra cómo usar las acciones de escalado automático para llamar a direcciones URL web o enviar notificaciones de correo electrónico en Azure Insights. "
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="ashwink"/>

# Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Insights

En este artículo se muestra cómo configurar desencadenadores para que pueda llamar a direcciones URL web específicas o enviar mensajes de correo electrónico en función de las acciones de escalado automático en Azure.

## Webhooks
Los Webhook permiten enrutar las notificaciones de alerta de Azure a otros sistemas para procesarlas posteriormente o notificaciones personalizadas. Un ejemplo de esto puede ser el enrutamiento de la alerta a los servicios que pueden controlar una solicitud web entrante para enviar SMS, registrar errores o notificar a un equipo mediante servicios de chat y mensajería. El identificador URI de webhook debe ser un punto de conexión HTTP o HTTPS válido.

## Email
Se pueden enviar mensajes de correo electrónico a cualquier dirección de correo electrónico válida. También se enviará una notificación a los administradores y los coadministradores de la suscripción en la que se ejecuta la regla.


## Aplicaciones web y servicios en la nube
Puede participar desde el portal de Azure en servicios en la nube y granjas de servidores (aplicaciones web).

- Elija la **Escalar por**.

![Escalar por](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## Conjuntos de escalado de máquina virtual
Para las máquinas virtuales más recientes creadas con Resource Manager (conjuntos de escala de máquina virtual), puede configurar esto con la API de REST, las plantillas de Resource Manager, PowerShell y CLI. Aún no se encuentra disponible una interfaz de portal. Al utilizar la API de REST o la plantilla de Resource Manager, incluya el elemento de notificaciones con las siguientes opciones.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|Campo |¿Obligatorio?|	Descripción|
|---|---|---|
|operación |yes |el valor debe ser "Scale"|
|sendToSubscriptionAdministrator |yes |el valor debe ser "true" o "false"|
|sendToSubscriptionCoAdministrators |yes |el valor debe ser "true" o "false"|
|customEmails |yes |el valor puede ser null o una cadena de matriz de mensajes de correo electrónico|
|webhooks |yes |el valor puede ser null o un identificador URI válido|
|serviceUri |yes |un identificador URI de https válido|
|propiedades |yes |el valor debe ser {} vacío o puede contener pares de clave y valor|


## Autenticación en Webhook
Hay dos formas de identificador URI de autenticación:

1. Autenticación basada en token, en la que se guarda el identificador URI de webhook con un identificador de token como parámetro de consulta. Por ejemplo, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Autenticación básica, en la que se usa un identificador de usuario y una contraseña. Por ejemplo, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## Esquema de carga de Webhook de notificación de escalado automático
Cuando se genera la notificación de escalado automático, los metadatos siguientes se incluyen en la carga de Webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Campo |¿Obligatorio?|	Descripción|
|---|---|---|
|status |yes |Estado que indica que se ha generado una acción de escalado automático.|
|operación|	yes |Para un aumento de instancias, será "Escalar horizontalmente"; para una disminución de instancias, "Reducir horizontalmente".|
|contexto|	yes |Contexto de la acción de escalado automático.|
|timestamp|	yes |Marca de tiempo cuando se desencadena la acción de escalado automático.|
|id |Sí|	Id. de Resource Manager de la configuración de escalado automático|
|name |Sí|	Nombre de la configuración de escalado automático.|
|detalles|	Sí |Explicación de la acción que realizó el servicio de escalado automático y el cambio en el recuento de instancias.|
|subscriptionId|	Sí |Id. de suscripción del recurso de destino que se está escalando.|
|resourceGroupName|	Sí|	Nombre del grupo de recursos del recurso de destino que se está escalando.|
|resourceName |Sí|	Nombre del recurso de destino que se está escalando.|
|resourceType |Sí|	Los tres valores que se admiten son: "microsoft.classiccompute/domainnames/slots/roles" (roles de servicios en la nube), "microsoft.compute/virtualmachinescalesets" (conjuntos de escalado de máquina virtual) y "Microsoft.Web/serverfarms " (aplicación web).|
|resourceId |Sí|Id. de Resource Manager del recurso de destino que se está escalando|
|portalLink |Sí |Vínculo del portal de Azure a la página de resumen del recurso de destino.|
|oldCapacity|	Sí |Recuento de instancias (antiguo) actual cuando el escalado automático ha realizado una acción de escalado.|
|newCapacity|	Sí |Nuevo recuento de instancias al que el escalado automático escaló el recurso.|
|Propiedades|	No|	Opcional. Conjunto de pares <Clave, Valor> (por ejemplo, Diccionario <Cadena, Cadena>). El campo de propiedades es opcional. En una interfaz de usuario personalizada o un flujo de trabajo basado en una aplicación lógica, puede escribir las claves y los valores que se pueden transmitir utilizando la carga. La forma alternativa para transmitir propiedades personalizadas a la llamada de Webhook de salida es mediante el propio URI de Webhook (como parámetros de consulta).|

<!---HONumber=AcomDC_0720_2016-->