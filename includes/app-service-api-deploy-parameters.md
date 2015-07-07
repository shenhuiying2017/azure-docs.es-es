Con el Administrador de recursos de Azure, puede definir parámetros para los valores que quiera especificar al implementar la plantilla. La plantilla incluye una sección denominada Parámetros que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

Vamos a describir cada parámetro de la plantilla.

### gatewayName

El nombre de la puerta de enlace que desea crear. La aplicación de API se registra en esta puerta de enlace.

    "gatewayName": {
      "type": "string"
    }

### apiAppName

El nombre de la aplicación de API que se va a crear.
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

El secreto de la aplicación de API. Este valor debe ser una cadena codificada en base64.

    "apiAppSecret": {
      "type": "securestring"
    }

### location

La ubicación de la nueva aplicación de API.

    "location": {
      "type": "string"
    }

<!---HONumber=62-->