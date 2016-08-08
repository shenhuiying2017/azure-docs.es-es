<properties 
	pageTitle="Creación de definiciones de aplicación lógica | Microsoft Azure" 
	description="Obtenga información sobre cómo escribir la definición de JSON para aplicaciones lógicas." 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
	
# Creación de definiciones de aplicación lógica
En este tema se explica cómo usar definiciones de [Azure Logic Apps](app-service-logic-what-are-logic-apps.md), que es un lenguaje JSON declarativo simple. Si no lo ha hecho todavía, consulte primero [Creación de una aplicación lógica nueva](app-service-logic-create-a-logic-app.md). También puede leer el [material de referencia completo del lenguaje de definición en MSDN](http://aka.ms/logicappsdocs).

## Varios pasos que se repiten en una lista

Puede aprovechar el [tipo foreach](app-service-logic-loops-and-scopes.md) para repetir los elementos de una matriz de hasta 10 000 elementos y realizar una acción para cada uno.

## Un paso de control de errores si algo va mal

Normalmente desea poder escribir un *paso de corrección*; cierta lógica que se ejecuta, si, **y solo si**, una o varias llamadas no se han podido realizar correctamente. En este ejemplo, obtenemos datos desde diversos lugares, pero si se produce un error en la llamada, es necesario PUBLICAR un mensaje en alguna parte para poder localizar ese error más adelante:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://myurl"
			}
		},
		"postToErrorMessageQueue": {
			"type": "ApiConnection",
			"inputs": "...",
			"runAfter": {
				"readData": ["Failed"]
			}
		}
	},
	"outputs": {}
}
```

Puede usar la propiedad `runAfter` para especificar que el elemento `postToErrorMessageQueue` solo debe ejecutarse cuando `readData` genere un **error**. También podría ser una lista de posibles valores, por lo que `runAfter` podría ser `["Succeeded", "Failed"]`.

Por último, dado que ahora se ha controlado el error, la ejecución ya no genera ningún **error**. Como puede ver aquí, esta ejecución es **correcta** a pesar de que antes generara errores, porque escribí este paso para solucionar este error.

## Dos pasos (o más) que se ejecutan en paralelo

Para tener varias acciones que se ejecuten en paralelo, la propiedad `runAfter` debe ser equivalente en tiempo de ejecución.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://myurl"
			}
		},
		"branch1": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		},
		"branch2": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		}
	},
	"outputs": {}
}
```

Como puede ver en el ejemplo anterior, `branch1` y `branch2` se establecen para ejecutarse después de `readData`. Como resultado, ambas ramas se ejecutarán en paralelo:

![Paralelo](./media/app-service-logic-author-definitions/parallel.png)

Puede ver que la marca de tiempo para ambas bifurcaciones es idéntica.

## Unión de dos ramas paralelas

Puede unir dos acciones que se han establecido para ejecutarse en paralelo al agregar elementos a la propiedad `runAfter` similares a los anteriores.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Paralelo](./media/app-service-logic-author-definitions/join.png)

## Asignación de elementos de una lista a una configuración diferente

A continuación, supongamos que queremos obtener contenido completamente diferente según un valor de una propiedad. Podemos crear una asignación de valores a destinos como un parámetro:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"specialCategories": {
			"defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
			"type": "Array"
		},
		"destinationMap": {
			"defaultValue": {
				"science": "http://www.nasa.gov",
				"microsoft": "https://www.microsoft.com/es-ES/default.aspx",
				"google": "https://www.google.com",
				"robots": "https://en.wikipedia.org/wiki/Robot",
				"NSA": "https://www.nsa.gov/"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"getArticles": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
			},
			"conditions": []
		},
		"getSpecialPage": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
			},
			"conditions": [{
				"expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
			}],
			"forEach": "@body('getArticles').responseData.feed.entries"
		}
	}
}
```

En este caso, en primer lugar, obtenemos una lista de artículos y, a continuación, el segundo paso busca en una asignación, según la categoría que se ha definido como un parámetro, la dirección URL en la que obtener el contenido.

Prestemos atención aquí a dos elementos: la función [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) se utiliza para comprobar si la categoría coincide con una de las categorías conocidas definidas. En segundo lugar, una vez que obtenemos la categoría, podemos extraer el elemento de la asignación mediante corchetes: `parameters[...]`.

## Trabajo con cadenas

Existen diversas funciones que pueden usarse para manipular la cadena. Veamos un ejemplo donde tenemos una cadena que se va a pasar a un sistema, pero no estamos seguros de que la codificación de caracteres se controlará correctamente. Una opción consiste en codificar esta cadena con base64. Sin embargo, para evitar escapes en una dirección URL, vamos a reemplazar algunos caracteres.

También queremos una subcadena del nombre del pedido, porque los cinco primeros caracteres no se usan.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1",
				"orderer": "NAME=Stèphén__Šīçiłianö"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"order": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
			}
		}
	},
	"outputs": {}
}
```

Trabajar desde dentro hacia fuera:

1. Obtener la [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) del nombre del solicitante; esto devuelve el número total de caracteres.

2. Restar 5 (porque desearemos una cadena más corta).

3. Obtener realmente la [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Comenzamos con el índice `5` y pasamos al resto de la cadena.

4. Convertir esta subcadena en una cadena [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) todos los caracteres `+` por `-`.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) todos los caracteres `/` por `_`.

## Trabajo con fechas

Las fechas puede ser útiles, especialmente cuando intenta extraer datos de un origen de datos que no admite de forma natural **desencadenadores**. También puede utilizar las fechas para averiguar cuánto duran los distintos pasos.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"order": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?id=@{parameters('order').id}"
			}
		},
		"timingWarning": {
			"actions" {
				"type": "Http",
				"inputs": {
					"method": "GET",
					"uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
				},
				"runAfter": {}
			}
			"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
		}
	},
	"outputs": {}
}
```

En este ejemplo, vamos a extraer el valor `startTime` del paso anterior. A continuación, vamos a obtener la hora actual y le vamos a restar un segundo:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (podría utilizar otras unidades de tiempo como `minutes` o `hours`). Por último, podemos comparar estos dos valores. Si el primero es menor que el segundo, significa que ha transcurrido más de un segundo desde la primera vez que se realizó el pedido.

Observe también que podemos utilizar formateadores de cadena para dar formato a fechas: en la cadena de consulta utilizo [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) para obtener el RFC1123. Todos los formatos de fecha [están documentados en MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## Uso de parámetros de tiempo de implementación para entornos diferentes

Es habitual tener un ciclo de vida de implementación donde tiene un entorno de desarrollo, un entorno de ensayo y, a continuación, un entorno de producción. En todos ellos puede querer la misma definición, pero usar bases de datos distintas, por ejemplo. Del mismo modo, es posible que desee utilizar la misma definición en muchas regiones diferentes para lograr una alta disponibilidad, pero que cada instancia de aplicación lógica se comunique con la base de datos de dicha región.

Tenga en cuenta que esto es diferente del uso de parámetros distintos en el *tiempo de ejecución*, para lo que debe usar la función `trigger()` según se ha descrito anteriormente.

Puede empezar con una definición muy sencilla, como esta:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"uri": {
			"type": "string"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('uri')"
			}
		}
	},
	"outputs": {}
}
```

A continuación, en la solicitud `PUT` real para la aplicación lógica, puede proporcionar el parámetro `uri`. Tenga en cuenta que, como ya no hay ningún valor predeterminado, este parámetro es necesario en la carga de la aplicación lógica:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

En cada entorno puede proporcionar un valor diferente para el parámetro `connection`.

Consulte la [documentación sobre la API de REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) para conocer todas las opciones disponibles para crear y administrar aplicaciones lógicas.

<!---HONumber=AcomDC_0727_2016-->