Debido al desarrollo en curso, la versión del SDK de Android instalada en Eclipse puede no coincidir con la versión en el código. El SDK de Android al que se hace referencia en este tutorial es la versión 19, la última en el momento de la escritura. El número de versión puede aumentar a medida que aparezcan nuevas versiones del SDK, de modo que se recomienda usar la última versión disponible.

Dos síntomas de error de coincidencia de la versión son los siguientes:

1.  Mire en la consola de Eclipse en el panel inferior. Es posible que vea los mensajes de error del tipo "**Unable to resolve target 'android-n'**".

2.  Los objetos de Android estándar del código que deben resolverse según las instrucciones `import` pueden generar mensajes de error.

Si observa alguna de estas señales, es posible que la versión del SDK de Android instalado en Eclipse no coincida con el destino del SDK del proyecto descargado. Para comprobar la versión, realice los siguientes cambios:

1.  En Eclipse, haga clic en **Window** y, a continuación, en **Android SDK Manager**. Si no ha instalado la última versión de la plataforma de SDK, haga clic para instalarla. Anote el número de versión.

2.  Abra el archivo de proyecto **AndroidManifest.xml**. Asegúrese de que en el elemento **uses-sdk**, **targetSdkVersion** se haya establecido en la última versión instalada. Es posible que la etiqueta **uses-sdk** tenga el siguiente aspecto:

            <uses-sdk
                android:minSdkVersion="8"
                android:targetSdkVersion="19" />

3.  En el Explorador de paquetes de Eclipse, haga clic con el botón secundario en el nodo de proyecto, seleccione **Properties**, y en la columna izquierda seleccione **Android**. Asegúrese de que **Project Build Target** se haya establecido en la misma versión de SDK que **targetSdkVersion**.


