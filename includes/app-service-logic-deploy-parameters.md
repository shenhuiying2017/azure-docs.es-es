Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada Parámetros que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

Al definir parámetros, use el campo **allowedValues** para especificar los valores que un usuario puede proporcionar durante la implementación. Use el campo **defaultValue** para asignar un valor al parámetro, si no se proporciona ningún valor durante la implementación.

Vamos a describir cada parámetro de la plantilla.

### logicAppName

El nombre de la aplicación lógica que se va a crear.

    "logicAppName": {
        "type": "string"
    }

### hostingPlanName

El nombre del plan del Servicio de aplicaciones que se va a crear para hospedar la aplicación lógica.
    
    "hostingPlanName": {
      "type": "string",
      "metadata": {
        "description": "The name of the App Service plan to create for hosting the logic app."
      }
    }

### flowSkuName

El nivel de precios de la aplicación lógica.

    "flowSkuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Free",
        "Basic",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "The pricing tier for the logic app."
      }
    },


La plantilla define los valores permitidos para este parámetro (Gratis, Básico, Estándar o Premium) y asigna un valor predeterminado (Estándar) si no se especifica ningún valor.

### hostingSkuName

El plan de tarifa del plan del Servicio de aplicaciones.

    "hostingSkuName": {
      "type": "string",
      "defaultValue": "S1",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "metadata": {
        "description": "Describes plan's pricing tier and instance size."
      }
    },


### hostingSkuCapacity

El tamaño de instancia de la aplicación.

    "hostingSkuCapacity": {
      "type": "int",
      "defaultValue": 1,
      "minValue": 1,
      "metadata": {
        "description": "Describes plan's instance count"
      }
    },


La plantilla define los valores que se permiten para este parámetro (0, 1 o 2) y asigna un valor predeterminado (0) si no se especifica ningún valor. Los valores corresponden a pequeño, mediano y grande.

