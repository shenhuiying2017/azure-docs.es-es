
### <a name="cacheskuname"></a>cacheSKUName
El nivel de precios de la nueva Caché en Redis de Azure.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

La plantilla define los valores permitidos para este parámetro ( Básico o Estándar) y asigna un valor predeterminado (Básico) si no se especifica ningún valor. Básico ofrece un único nodo con varios tamaños disponibles hasta 53 GB.
Estándar proporciona dos nodos, principal/réplica, con varios tamaños disponibles de hasta 53 GB y un contrato de nivel de servicio del 99,9%.

### <a name="cacheskufamily"></a>cacheSKUFamily
La familia de la SKU.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
El tamaño de la nueva instancia de Caché en Redis de Azure. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


La plantilla define los valores permitidos para este parámetro (0, 1, 2, 3, 4, 5 o 6) y asigna un valor predeterminado (1) si no se especifica ningún valor. Los números corresponden a los siguientes tamaños de caché: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

