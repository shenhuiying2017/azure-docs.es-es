Cuando el host de funciones se ejecuta localmente, escribe registros en la ruta de acceso siguiente:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

En Windows, `<DefaultTempDirectory>` es el primer valor que se encuentra de las variables de entorno TMP, TEMP, USERPROFILE o del directorio de Windows.
En Mac OS o Linux, `<DefaultTempDirectory>` es la variable de entorno TMPDIR.

[!Note]
Cuando se inicia el host de funciones, este sobrescribirá la estructura de archivos existentes en el directorio.