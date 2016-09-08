<properties
   pageTitle="Interacción con informes mediante la API de JavaScript | Microsoft Azure"
   description="Power BI Embedded, interacción con informes mediante la API de JavaScript"
   services="power-bi-embedded"
   documentationCenter=""
   authors="jocaplan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="08/26/2016"
   ms.author="jocaplan"/>

# Interacción con informes de Power BI mediante la API de JavaScript

La API de JavaScript de Power BI permite insertar fácilmente informes de Power BI en las aplicaciones. Con ella, las aplicaciones pueden interactuar mediante programación con distintos elementos de los informes, como las páginas y filtros. Esta interactividad hace que los informes Power BI se integren mejor en las aplicaciones.

Dichos informes se insertan en las aplicaciones mediante un iframe que se aloja como parte de la aplicación. El iframe hace de límite entre la aplicación y el informe, como se puede ver en la siguiente imagen.

![iframe de Power BI Embedded sin la API de Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

El iframe facilita mucho el proceso de integración, pero sin la API de JavaScript el informe y la aplicación no pueden interactuar entre sí. Esta falta de interacción puede hacer parecer que el informe no forma parte de la aplicación. El informe y la aplicación necesitan comunicarse bidireccionalmente, como se muestra en la siguiente imagen.

![iframe de Power BI Embedded con la API de Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

La API de JavaScript de Power BI permite escribir código que puede pasar de forma segura a través del límite del iframe. Esto permite que la aplicación realice una acción en un informe mediante programación y que escuche eventos de acciones que realizan los usuarios dentro del informe.

## ¿Qué se puede hacer con la API de JavaScript de Power BI?
Con la API de JavaScript se pueden administrar informes, navegar a las distintas páginas de un informe, filtrar un informe y controlar eventos de inserción. En el siguiente diagrama se muestra la estructura de la API.

![Diagrama de la API de JavaScript de Power BI](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### Administración de informes
La API de Javascript permite administrar el comportamiento en el nivel de página y de informe:

- Insertar un informe específico de Power BI de forma segura en la aplicación (pruebe la [aplicación de demostración integrada](http://azure-samples.github.io/powerbi-angular-client/#/scenario1))
  - Establecer un token de acceso
- Configurar el informe
  - Habilitar y deshabilitar el panel de filtro y el panel de navegación de página (pruebe la [aplicación de demostración de actualización de configuración](http://azure-samples.github.io/powerbi-angular-client/#/scenario6))
  - Establecer los valores predeterminados de las páginas y los filtros: pruebe la [demostración de establecimiento de valores predeterminados](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Entrar y salir de modo de pantalla completa

[Más información acerca de cómo insertar informes](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### Navegación a las páginas de un informe
La API de JavaScript permite para detectar todas las páginas de un informe y establecer la página actual. Pruebe la [aplicación de demostración de navegación](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Más información acerca de la navegación por la página](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### Filtro de un informe
La API de JavaScript proporciona funcionalidades de filtro básicas y avanzadas para informes páginas de informes integrados. Pruebe la [aplicación de demostración de filtro](http://azure-samples.github.io/powerbi-angular-client/#/scenario4) y revise aquí el código de introducción.


#### Filtros básicos
Los filtros básicos se colocan en un nivel de jerarquía o de columna, y contienen la lista de valores que se incluyen o excluyen.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### Filtros avanzados
Los filtros avanzados de utilizan los operadores lógicos AND y OR, y aceptan una o dos condiciones, cada una de ellas con su propio operador y valor. Estas son las condiciones que se admiten:

- None
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- Contains
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Is
- IsNot
- IsBlank
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Más información acerca de los filtros](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### Control de eventos
Además de enviar información al iframe, la aplicación también puede recibir información sobre los siguientes eventos que proceda de iframe:

- Insertar
  - loaded
  - error
- Informes
  - pageChanged
  - dataSelected (próximamente)

[Más información sobre el control de eventos](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## Pasos siguientes
Para más información acerca de la API de JavaScript de Power BI, consulte los siguientes vínculos:

- [Wiki de API de JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Referencia de modelo de objeto](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Muestras
  - [Angular](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Demostración en directo](https://microsoft.github.io/PowerBI-JavaScript/demo/)

<!---HONumber=AcomDC_0831_2016-->