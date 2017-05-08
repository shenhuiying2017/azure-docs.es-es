
### <a name="update-manifest-file-to-enable-notifications"></a>Actualización del archivo de manifiesto para habilitar las notificaciones
Copie los siguientes recursos de mensajería en aplicación en Manifest.xml, entre las etiquetas `<application>` y `</application>`.

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

### <a name="specify-an-icon-for-notifications"></a>Especificación de un icono para las notificaciones
Pegue el siguiente fragmento XML en su Manifest.xml entre las etiquetas `<application>` y `</application>`.

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Esto define el icono que se muestra tanto en el sistema como en las notificaciones en aplicación. Es opcional para las notificaciones en aplicación y obligatorio para las notificaciones del sistema. Android rechazará las notificaciones del sistema con iconos no válidos.

Asegúrese de que está usando un icono que se encuentra en una de las carpetas de recursos **dibujables** (como ``engagement_close.png``). **mipmap** no se admite.

> [!NOTE]
> No debería usar el icono del **iniciador** . Tiene una resolución diferente y normalmente está en las carpetas mipmap, que no se admiten.
> 
> 

Para las aplicaciones reales, puede usar un icono adecuado para notificaciones según las [directrices de diseño de Android](http://developer.android.com/design/patterns/notifications.html).

> [!TIP]
> Para asegurarse de usar las resoluciones de icono correctas, consulte [estos ejemplos](https://www.google.com/design/icons).
> Desplácese hasta la sección **Notificación**, haga clic en un icono y luego haga clic en `PNGS` para descargar el conjunto de iconos dibujables. Puede ver qué carpetas de recursos dibujables usar con qué resolución para cada versión del icono.
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>Habilitar la aplicación para recibir notificaciones de inserción de GCM
1. Pegue lo siguiente entre las etiquetas `<application>` y `</application>` del archivo Manifest.xml después de reemplazar el **identificador de remitente** obtenido en la consola del proyecto Firebase. \n se introduce de manera intencionada para asegurarse de finalizar el número de proyecto con él.
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. Pegue el código siguiente en su Manifest.xml entre las etiquetas `<application>` y `</application>`. Reemplace el nombre del paquete <Your package name>.
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>
3. Agregue el último conjunto de permisos resaltados antes de la etiqueta `<application>` . Reemplace `<Your package name>` por el nombre real del paquete de la aplicación.
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

