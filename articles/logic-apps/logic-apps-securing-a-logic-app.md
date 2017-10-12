---
title: Acceso seguro a Azure Logic Apps | Microsoft Docs
description: "Aumente la seguridad para proteger el acceso a desencadenadores, entradas y salidas, parámetros de acción y servicios que se utilizan con flujos de trabajo en Azure Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 45a4e476f930e0f5f6633dc5b3b35b66dc6dfa20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="secure-access-to-your-logic-apps"></a>Acceso seguro a aplicaciones lógicas

Hay muchas herramientas disponibles que le ayudarán a proteger la aplicación lógica.

* Protección del acceso para desencadenar una aplicación lógica (desencadenador de solicitud de HTTP)
* Protección del acceso para administrar, editar o leer una aplicación lógica
* Protección del acceso al contenido de entradas y salidas para una ejecución
* Protección de parámetros o entradas en las acciones de un flujo de trabajo
* Protección del acceso a los servicios que reciben solicitudes de un flujo de trabajo

## <a name="secure-access-to-trigger"></a>Acceso seguro al desencadenador

Cuando se trabaja con una aplicación lógica que se activa con una solicitud HTTP ([solicitud](../connectors/connectors-native-reqres.md) o [webhook](../connectors/connectors-native-webhook.md)), puede restringir el acceso a aquellos clientes autorizados que pueden activar la aplicación lógica. Todas las solicitudes en una aplicación lógica se cifrarán y se protegerán mediante SSL.

### <a name="shared-access-signature"></a>Firma de acceso compartido

Cada punto de conexión de la solicitud para una aplicación lógica incluye una [Firma de acceso compartido](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS) como parte de la dirección URL. Cada dirección URL contiene un parámetro de consulta `sp`, `sv` y `sig`. Los permisos se especifican mediante `sp` y se corresponden a los métodos HTTP permitidos, `sv` es la versión utilizada para generar y `sig` se usa para autenticar el acceso al desencadenador. La firma se genera mediante el algoritmo SHA256 con una clave secreta en todas las rutas de acceso de direcciones URL y propiedades. La clave secreta nunca se expone ni se publica, y se mantiene cifrada y almacenada como parte de la aplicación lógica. La aplicación lógica solo autoriza desencadenadores que contienen una firma válida creada con la clave secreta.

#### <a name="regenerate-access-keys"></a>Regenerar las claves de acceso

Puede volver a generar una nueva clave segura en cualquier momento mediante la API de REST o Azure Portal. Todas las direcciones URL actuales generadas anteriormente con la clave antigua se invalidan y dejan de estar autorizadas para activar la aplicación lógica.

1. En Azure Portal, abra la aplicación lógica para la que desea volver a generar una clave.
1. Haga clic en el elemento de menú **Access Keys** (Claves de acceso) en **Settings** (Configuración).
1. Elija la clave que se va a regenerar y complete el proceso.

Las direcciones URL que se recuperan tras la regeneración se firman con la nueva clave de acceso.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Creación de direcciones URL de devolución de llamada con una fecha de expiración

Si va a compartir la dirección URL con terceros, puede generar direcciones URL con claves específicas y fechas de caducidad según sea necesario. De esta forma puede acumular claves sin problema, o asegurarse de que el acceso para activar una aplicación está restringido a un determinado intervalo de tiempo. Puede especificar una fecha de expiración para una dirección URL a través de la [API de REST de Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

En el cuerpo, incluya la propiedad `NotAfter` como una cadena de fecha JSON, que devuelve una dirección URL de devolución de llamada que solo es válida hasta la fecha y hora `NotAfter`.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Creación de direcciones URL con clave secreta principal o secundaria

Al generar o enumerar direcciones URL de devolución de llamada para desencadenadores basados en solicitud, también puede especificar qué clave se va a usar para firmar la dirección URL.  Puede generar una dirección URL firmada por una clave específica a través de la [API de REST de Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), tal como se indica a continuación:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

En el cuerpo, incluya la propiedad `KeyType` como `Primary` o `Secondary`.  Esto devuelve una dirección URL firmada por la clave segura especificada.

### <a name="restrict-incoming-ip-addresses"></a>Restricción de las direcciones IP entrantes

Además de la Firma de acceso compartido, es posible que desee restringir las llamadas a una aplicación lógica solo desde clientes específicos.  Por ejemplo, si administra el punto de conexión a través de Azure API Management, puede restringir la aplicación lógica para que acepte únicamente la solicitud cuando esta provenga de la dirección IP de la instancia de API Management.

Esta opción se puede configurar en la configuración de la aplicación lógica:

1. En Azure Portal, abra la aplicación lógica a la que desea agregar las restricciones de dirección IP
1. Haga clic en el elemento de menú **Access control configuration** (Configuración de control de acceso) en **Settings** (Configuración).
1. Especifique la lista de intervalos de direcciones IP que van a ser aceptados por el desencadenador.

Un intervalo IP válido adopta el formato `192.168.1.1/255`. Si quiere que la aplicación lógica solo se active como una aplicación lógica anidada, seleccione la opción **Solo otras aplicaciones lógicas**. Esta opción escribe una matriz vacía en el recurso, lo que significa que solo las llamadas realizadas desde el mismo servicio (aplicaciones lógicas principales) se activan correctamente.

> [!NOTE]
> Todavía puede ejecutar una aplicación de lógica con un desencadenador de solicitud a través de la API de REST/Management `/triggers/{triggerName}/run`, con independencia de la IP. Este escenario requiere la autenticación con la API de REST de Azure y todos los eventos podrían aparecer en el registro de auditoría de Azure. Establezca las directivas de control de acceso en consecuencia.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Establecimiento de los intervalos de IP en la definición de recursos

Si está usando una [plantilla de implementación](logic-apps-create-deploy-template.md) para automatizar las implementaciones, los valores del intervalo de IP pueden configurarse en la plantilla de recursos.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "triggers": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Adición de Azure Active Directory, OAuth u otra seguridad

Para agregar más protocolos de autorización basados en una aplicación lógica, [Azure API Management](https://azure.microsoft.com/services/api-management/) ofrece supervisión enriquecida, seguridad, directivas y documentación para cualquier punto de conexión con la funcionalidad de exponer una aplicación lógica como una API. Azure API Management puede exponer un punto de conexión público o privado para la aplicación lógica, que podría usar Azure Active Directory, certificados, OAuth u otros estándares de seguridad. Cuando se recibe una solicitud, Azure API Management la reenvía a la aplicación lógica (realizando las transformaciones o restricciones necesarias sobre la marcha). Puede usar los valores del intervalo IP entrantes en la aplicación lógica para permitir solo que la aplicación lógica se desencadene desde API Management.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Protección del acceso para administrar o modificar aplicaciones lógicas

Puede restringir el acceso a las operaciones de administración en una aplicación lógica para que solo determinados usuarios o grupos puedan realizar operaciones en el recurso. Las aplicaciones lógicas usan la característica de Azure [Control de acceso basado en rol (RBAC)](../active-directory/role-based-access-control-configure.md), y se puede personalizar con las mismas herramientas.  Hay algunas funciones integradas que puede asignar también a los miembros de la suscripción:

* **Colaborador de aplicación lógica**: proporciona acceso para consultar, modificar y actualizar una aplicación lógica.  No puede quitar el recurso ni realizar operaciones de administración.
* **Operador de aplicación lógica**: puede consultar la aplicación lógica y el historial de ejecución, así como habilitarla o deshabilitarla.  No puede modificar ni actualizar la definición.

También puede usar el [bloqueo de recurso de Azure](../azure-resource-manager/resource-group-lock-resources.md) para evitar cambiar o eliminar aplicaciones lógicas. Esta funcionalidad es útil para evitar que los recursos de producción se modifiquen o eliminen.

## <a name="secure-access-to-contents-of-the-run-history"></a>Protección del acceso al contenido del historial de ejecución

Puede restringir el acceso al contenido de entradas o salidas de ejecuciones anteriores a intervalos de direcciones IP específicos.  

Todos los datos dentro de una ejecución de flujo de trabajo se cifran en tránsito y en reposo. Cuando se realiza una llamada al historial de ejecución, el servicio autentica la solicitud y proporciona vínculos a las entradas y salidas de las solicitudes y respuestas. Este vínculo se puede proteger para que solo las solicitudes para consultar contenido de un intervalo de direcciones IP designado devuelvan contenido. Puede utilizar esta funcionalidad para el control de acceso adicional. Incluso puede especificar una dirección IP como `0.0.0.0` para que nadie tenga acceso a las entradas o salidas. Solo un usuario con permisos de administrador puede quitar esta restricción, ofreciendo la posibilidad de un acceso 'Just-in-time' al contenido de flujo de trabajo.

Esta opción se puede configurar en la configuración de los recursos de Azure Portal:

1. En Azure Portal, abra la aplicación lógica a la que desea agregar las restricciones de dirección IP
1. Haga clic en el elemento de menú **Access control configuration** (Configuración de control de acceso) en **Settings** (Configuración).
1. Especifique la lista de intervalos de direcciones IP para acceder al contenido.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Establecimiento de los intervalos de IP en la definición de recursos

Si está usando una [plantilla de implementación](logic-apps-create-deploy-template.md) para automatizar las implementaciones, los valores del intervalo de IP pueden configurarse en la plantilla de recursos.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "contents": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Parámetros seguros y entradas dentro de un flujo de trabajo

Puede desear parametrizar algunos aspectos de una definición de flujo de trabajo para una implementación a través de entornos. Además, algunos parámetros pueden ser parámetros seguros que no desea que aparezcan al editar un flujo de trabajo, como un identificador de cliente y el secreto del cliente para la [autenticación de Azure Active Directory](../connectors/connectors-native-http.md#authentication) de una acción HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Uso de parámetros y parámetros seguros

Para acceder al valor de un parámetro de recurso en tiempo de ejecución, el [lenguaje de definición de flujo de trabajo](http://aka.ms/logicappsdocs) proporciona una operación `@parameters()`. Además, puede [especificar parámetros en la plantilla de implementación de recursos](../azure-resource-manager/resource-group-authoring-templates.md#parameters). No obstante, si especifica el tipo de parámetro como `securestring`, el parámetro no se devolverá con el resto de la definición de recurso, lo que significa que no será accesible al visualizar el recurso después de la implementación.

> [!NOTE]
> Si el parámetro se usa en los encabezados o en el cuerpo de una solicitud, el parámetro se puede ver al acceder al historial de ejecución y a la solicitud HTTP saliente. Asegúrese de configurar las directivas de acceso al contenido en consecuencia.
> Los encabezados de autorización nunca son visibles a través de entradas o salidas. Por tanto, si el secreto se usa ahí, este no se puede recuperar.

#### <a name="resource-deployment-template-with-secrets"></a>Plantilla de implementación de recursos con secretos

A continuación se presenta un ejemplo en que se muestra una implementación que hace referencia a un parámetro seguro de `secret` en tiempo de ejecución. En un archivo de parámetros independiente podría especificar el valor de entorno para `secret` o usar el [almacén de claves de Azure Resource Manager](../azure-resource-manager/resource-manager-keyvault-parameter.md) para recuperar los secretos en tiempo de implementación.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
            }
          },
          "triggers": {
            "manual": {
              "type": "Request",
              "kind": "Http",
              "inputs": {
                "schema": {}
              }
            }
          },
          "contentVersion": "1.0.0.0",
          "outputs": {}
        },
        "parameters": {
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Protección del acceso a los servicios mediante solicitudes de un flujo de trabajo

Hay muchas maneras de ayudar a proteger los puntos de conexión a los que la aplicación lógica necesita acceder.

### <a name="using-authentication-on-outbound-requests"></a>Uso de la autenticación en las solicitudes salientes

Cuando se trabaja con una acción HTTP, HTTP + Swagger (Open API) o webhook acción, puede agregar autenticación a la solicitud que se envía. Podría incluir la autenticación básica, la autenticación de certificados o la autenticación de Azure Active Directory. Encontrará detalles sobre cómo configurar esta autenticación [en este artículo](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Restricción del acceso a direcciones IP de aplicación lógica

Todas las llamadas de las aplicaciones lógicas proceden de un conjunto específico de direcciones IP por región. Puede agregar filtrado adicional para aceptar solo las solicitudes procedentes de esas direcciones IP designadas. Para obtener una lista de esas direcciones IP, vea [Límites y configuración de Logic Apps](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Conectividad local

Las aplicaciones lógicas proporcionan integración con varios servicios para ofrecer una comunicación local segura y de confianza.

#### <a name="on-premises-data-gateway"></a>Puerta de enlace de datos local

Muchos de los conectores administrados de las aplicaciones lógicas proporcionan conectividad segura a sistemas locales, como el sistema de archivos, SQL, SharePoint o DB2, entre otros. La puerta de enlace retransmite datos desde orígenes locales en canales cifrados hasta Azure Service Bus. Todo el tráfico se origina como tráfico de salida seguro desde el agente de puerta de enlace. Más información sobre [cómo funciona la puerta de enlace de datos](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Administración de API de Azure

[Azure API Management](https://azure.microsoft.com/services/api-management/) tiene opciones de conectividad local, incluida la integración de ExpressRoute y VPN de sitio a sitio para el proxy protegido y la comunicación en los sistemas locales. En el Diseñador de aplicación lógica, puede seleccionar rápidamente una API expuesta desde Azure API Management dentro de un flujo de trabajo, proporcionando acceso rápido a los sistemas locales.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una plantilla de implementación](logic-apps-create-deploy-template.md)  
[Control de excepciones](logic-apps-exception-handling.md)  
[Supervisar las aplicaciones lógicas](logic-apps-monitor-your-logic-apps.md)  
[Diagnóstico de errores y problemas de las aplicaciones lógicas](logic-apps-diagnosing-failures.md)  
