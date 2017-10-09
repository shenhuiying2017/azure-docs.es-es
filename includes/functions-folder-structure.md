
El código para todas las funciones de una aplicación de función dada se encuentra en una carpeta raíz que contiene un archivo de configuración de host y una o varias subcarpetas, cada una con el código de una función diferente, como se indica en el ejemplo siguiente:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

El archivo *host.json* contiene alguna configuración específica del tiempo de ejecución y se coloca en la carpeta principal de la aplicación de función. Para información sobre la configuración disponible, consulte la [referencia de host.json](../articles/azure-functions/functions-host-json.md).

Cada función tiene una carpeta que contiene uno o varios archivos de código, la configuración de function.json y otras dependencias.

