Debido al desarrollo en curso, puede que la versión del SDK de Android instalada no coincida con la versión del código. La versión de Android SDK a la que se hace referencia en este tutorial es la 26, la última en el momento de la escritura. El número de versión puede aumentar a medida que aparezcan nuevas versiones del SDK, de modo que se recomienda usar la última versión disponible.

Dos síntomas de error de coincidencia de la versión son los siguientes:

- Al compilar o volver a compilar el proyecto, puede obtener mensajes de error de Gradle como `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Los objetos de Android estándar del código que deben resolverse según las instrucciones `import` pueden generar mensajes de error.

Si aparece cualquiera de estos mensajes, es posible que la versión del SDK de Android instalada en Android Studio no coincida con el destino del SDK del proyecto descargado. Para comprobar la versión, realice los siguientes cambios:

1. En Android Studio, haga clic en **Herramientas** > **Android** > **Administrador de SDK**. Si no ha instalado la última versión de la plataforma de SDK, haga clic para instalarla. Anote el número de versión.

2. En la pestaña **Explorador de proyectos**, en **Scripts de Gradle**, abra el archivo **build.gradle (Module: app)**. Asegúrese de que **compileSdkVersion** y **targetSdkVersion** se establecen en la versión más reciente del SDK instalada. Es posible que `build.gradle` tenga este aspecto:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
