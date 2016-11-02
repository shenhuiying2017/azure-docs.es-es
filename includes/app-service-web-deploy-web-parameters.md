Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada Parámetros que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

Al definir parámetros, use el campo **allowedValues** para especificar los valores que un usuario puede proporcionar durante la implementación. Use el campo **defaultValue** para asignar un valor al parámetro, si no se proporciona ningún valor durante la implementación.

Vamos a describir cada parámetro de la plantilla.

### siteName

El nombre de la aplicación web que desea crear.

    "siteName":{
      "type":"string"
    }

### hostingPlanName

El nombre del plan del Servicio de aplicaciones que se va a crear para hospedar la aplicación web.
    
    "hostingPlanName":{
      "type":"string"
    }

### sku

El nivel de precios del plan de hospedaje.

    "sku": {
      "type": "string",
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
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

La plantilla define los valores que se permiten para este parámetro y asigna un valor predeterminado (S1) si no se especifica ningún valor.

### workerSize

El tamaño de la instancia del plan de hospedaje (pequeño, mediano o grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
La plantilla define los valores que se permiten para este parámetro (0, 1 o 2) y asigna un valor predeterminado (0) si no se especifica ningún valor. Los valores corresponden a pequeño, mediano y grande.

