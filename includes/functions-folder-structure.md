
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

El archivo *host.json* contiene alguna configuración específica del tiempo de ejecución y se coloca en la carpeta principal de la aplicación de función. Para obtener información sobre las opciones que están disponibles, consulte [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en la wiki de repositorio de WebJobs.Script.

Cada función tiene una carpeta que contiene uno o varios archivos de código, la configuración de function.json y otras dependencias.

