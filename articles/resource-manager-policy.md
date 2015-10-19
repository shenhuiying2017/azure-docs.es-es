<properties
	pageTitle="Directiva del Administrador de recursos de Azure | Microsoft Azure"
	description="Describe cómo usar la directiva del Administrador de recursos de Azure para evitar infracciones en distintos ámbitos como, por ejemplo, la suscripción, los grupos de recursos o los recursos individuales."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="10/06/2015"
	ms.author="gauravbh;tomfitz"/>

# Uso de directivas para administrar los recursos y controlar el acceso

El Administrador de recursos de Azure permite controlar el acceso mediante directivas personalizadas. Una directiva representa una o más infracciones que se pueden evitar en el ámbito deseado. El ámbito, en este caso, puede ser una suscripción, un grupo de recursos o un recurso individual.

Las directivas son un sistema de permisos predeterminado. Las directivas se definen mediante definición de directiva y se aplican con una asignación de directivas. Las asignaciones de directiva permiten controlar el ámbito de aplicación de una directiva.

En este artículo se explica la estructura básica del lenguaje de definición de directivas que se puede usar para crear directivas. A continuación, describiremos cómo es posible aplicar estas directivas en distintos ámbitos y, finalmente, mostraremos algunos ejemplos de aplicación mediante la API de REST. Pronto se agregará compatibilidad con PowerShell.

## Escenarios comunes

Un escenario común es exigir etiquetas departamentales para contracargos. Es posible que una organización solo quiera permitir operaciones cuando se asocie el centro de coste correcto.; en caso contrario, denegará la solicitud. Esto ayudaría a aplicar el cargo al centro de costes correcto para las operaciones realizadas.

Otro escenario común es que la organización que desee controlar las ubicaciones en las que se crean los recursos. O bien, puede que desee controlar el acceso a los recursos al permitir el aprovisionamiento solo de determinados tipos de recursos.

Del mismo modo, una organización puede controlar el catálogo de servicios o aplicar las convenciones de nombre deseadas para los recursos.

Con las directivas, estos escenarios pueden conseguirse fácilmente tal como se describe a continuación.

## Estructura de definición de directiva

La definición de la directiva se crea mediante JSON. Consta de uno o más operadores lógicos/condicionales que definen las acciones y un efecto que indica lo que ocurre cuando se cumplen las condiciones.

Básicamente, una directiva contiene lo siguiente:

**Operadores lógicos/condicionales:** contiene un conjunto de condiciones que se pueden manipular mediante un conjunto de operadores lógicos.

**Efecto:** describe cuál será el efecto cuando se cumpla la condición: denegar o auditoría. Un efecto de auditoría emitirá un registro de servicio de eventos de advertencia. Por ejemplo, un administrador puede crear una directiva que provoque una auditoría si alguien crea una máquina virtual grande, y revisar los registros más adelante.

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## Operadores lógicos

A continuación se muestran los operadores lógicos admitidos junto con la sintaxis:

| Nombre del operador | Sintaxis |
| :------------- | :------------- |
| Not | "not" : {&lt;condición&gt;} |
| Y | "allOf" : [ {&lt;condición1&gt;},{&lt;condición2&gt;}] |
| O | "anyOf" : [ {&lt;condición1&gt;},{&lt;condición2&gt;}] |


## Condiciones

A continuación se muestran las condiciones admitidas junto con la sintaxis:

| Nombre de la condición | Sintaxis |
| :------------- | :------------- |
| Equals | "equals" : "&lt;valor&gt;" |
| Like | "like" : "&lt;valor&gt;" |
| Contains | "contains" : "&lt;valor&gt;"|
| En el | "in" : [ "&lt;valor1&gt;","&lt;valor2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;nombre de clave&gt;" |


## Campos y orígenes

Las condiciones se crean mediante el uso de campos y orígenes. Estos son los campos y orígenes admitidos:

Campos: **name**, **kind**, **type**, **location**, **tags**, **tags.***.

Orígenes: **action**

## Ejemplos de definición de directivas

A continuación veremos cómo se va a definir la directiva para obtener los escenarios mencionados anteriormente.

### Contracargo: requiere etiquetas departamentales

La directiva siguiente rechaza todas las solicitudes que no tengan una etiqueta que contiene la clave "costCenter".

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }


### Cumplimiento geográfico: garantiza las ubicaciones de los recursos

El ejemplo siguiente muestra una directiva que denegará todas las solicitudes cuya ubicación no sea Europa del Norte o Europa Occidental.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["north europe" , "west europe"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Selección de servicio: selecciona el catálogo de servicios

El ejemplo siguiente muestra el uso del origen. Muestra que solo se permiten acciones en los servicios de tipo Microsoft.Resources/*, Microsoft.Compute/*, Microsoft.Storage/* y Microsoft.Network/*. Se denegará todo lo demás.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Convención de nomenclatura

El ejemplo siguiente muestra el uso de caracteres comodín compatibles con la condición "like". La condición indica que se denegará la solicitud si el nombre coincide con el patrón mencionado (namePrefix*nameSuffix).

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

## Asignación de directiva

Las directivas se pueden aplicar en distintos ámbitos como, por ejemplo, la suscripción, los grupos de recursos y los recursos individuales. Todos los recursos secundarios heredan las directivas. De este modo, si una directiva se aplica a un grupo de recursos, será aplicable a todos los recursos de dicho grupo de recursos.

## Creación de una directiva

En esta sección se ofrecen detalles sobre cómo se puede crear una directiva usando la API de REST.

### Crear una definición de directiva con la API de REST

Puede crear una directiva con la [API de REST para definiciones de directiva](https://msdn.microsoft.com/library/azure/mt588471.aspx). La API de REST permite crear y eliminar definiciones de directiva, así como recuperar información sobre las definiciones existentes.

Para crear una nueva directiva, ejecute lo siguiente:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Con un cuerpo de solicitud similar al siguiente:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


La definición de la directiva puede definirse como uno de los ejemplos anteriores. Para obtener la versión de la API, use *2015-10-01-preview*. Para obtener más ejemplos y más detalles, vea la [API de REST para definiciones de directiva](https://msdn.microsoft.com/library/azure/mt588471.aspx).

## Aplicación de una directiva

### Asignación de directivas con la API de REST

Puede aplicar la definición de la directiva en el ámbito deseado a través de la [API de REST para asignaciones de directivas](https://msdn.microsoft.com/library/azure/mt588466.aspx). La API de REST permite crear y eliminar asignaciones de directiva, así como recuperar información sobre las asignaciones existentes.

Para crear una nueva asignación de directiva, ejecute:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} es el nombre de la asignación de directiva. Para obtener la versión de la API, use *2015-10-01-preview*.

Con un cuerpo de solicitud similar al siguiente:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

Para obtener más ejemplos y más detalles, consulte la [API de REST para asignaciones de directivas](https://msdn.microsoft.com/library/azure/mt588466.aspx).

<!---HONumber=Oct15_HO2-->