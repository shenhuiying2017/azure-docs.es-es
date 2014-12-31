<properties pageTitle="Introducción a las notificaciones de inserción (Appcelerator) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Appcelerator app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# Introducción a las notificaciones de inserción en Servicios móviles (inserción heredada)
<div class="dev-center-tutorial-selector sublanding">
	<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">C# para Tienda Windows</a>
	<a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">JavaScript para Tienda Windows</a>
	<a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
	<a href="/en-us/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
	<a href="/en-us/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/en-us/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

En este tema se muestra cómo usar Servicios móviles de Windows Azure para enviar notificaciones de inserción tanto a aplicaciones iOS como a aplicaciones Android desarrolladas mediante Appcelerator Titanium Studio. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Apple (APNS) y el servicio de mensajería en la nube de Google. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

>[WACOM.NOTE] La solución Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. Este tema admite servicios móviles existentes que todavía no se han actualizado para usar la integración de Centros de notificaciones. Cuando crea un nuevo servicio móvil, esta funcionalidad integrada se habilita automáticamente. Debe actualizar el servicio para usar Centros de notificaciones cuando sea posible. **Trabajaremos para tener un tutorial disponible cuando antes para la inserción de Centros de notificaciones con Appcelerator.**

1.	[Generación del archivo de solicitud de firma de certificado]
2.	[Registro de la aplicación y habilitación para las notificaciones de inserción]
3.	[Creación de un perfil de aprovisionamiento para la aplicación]
4.	[Habilitación del servicio de mensajería en la nube de Google]
5.  [Creación del módulo GCM para Titanium]
6.	[Configuración de Servicios móviles]
7.	[Incorporación de notificaciones de inserción a la aplicación]
8.	[Actualización de scripts para enviar notificaciones de inserción]
9.	[Inserción de datos para recibir notificaciones]

Este tutorial requiere lo siguiente:

* Módulo de Servicios móviles de Azure para Appcelerator
* Appcelerator Titanium Studio 3.2.1 o posterior
* Un dispositivo con funcionalidad iOS 7.0 (o versión posterior) y Android 4.3 (o versión posterior)
* Pertenencia al programa para desarrolladores de iOS
* Una cuenta de Google activa

> [WACOM.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles].

[WACOM.INCLUDE [Habilitación de notificaciones de inserción de Apple](../includes/enable-apple-push-notifications.md)]

## <a name="register-gcm"></a>Habilitación del servicio de mensajería en la nube de Google

>[WACOM.NOTE]Para llevar a cabo este procedimiento, debe tener una cuenta de Google asociada a una dirección de correo electrónico comprobada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

[WACOM.INCLUDE [Habilitación de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##  <a name="gcm-module"></a>Creación del módulo GCM para Titanium

### Preparación de Appcelerator Titanium Studio para la creación del módulo

Si va a crear módulos Android, necesitará instalar la compatibilidad con Java dentro de Appcelerator Titanium Studio. Consulte la [instalación de las herramientas de desarrollo de Java] de Appcelerator para obtener unos pasos breves si todavía no lo ha hecho.

Necesitará instalar el NDK de Android. Descargue el archivo .zip apropiado desde [http://developer.android.com/sdk/ndk/index.html](http://developer.android.com/sdk/ndk/index.html) y extráigalo en alguna ubicación del disco. Recuerde esta ubicación. 

### Creación de un nuevo módulo

1. Abra Appcelerator Titanium Studio.

2. Haga clic en File -> New -> Mobile Module Project.

    ![][0]

3. En la ventana siguiente, escriba los datos de la configuración del proyecto: 

    * **Project name:** en nuestro caso, usamos &quot;notificationhub&quot; (puede ser el mismo).

    * **Module Id:** en nuestro caso, usamos &quot;com.winwire.notificationhub&quot;. También este debe coincidir con nuestro "identificador de aplicación".

    * **Deployment Targets:** en este caso, seleccionamos Android.

    > [WACOM.NOTE] Es importante que el nombre de nuestra área de trabajo no contenga espacios ya que, de lo contrario, tendremos problemas cuando creemos la compilación.

    ![][1]

4. Presione Next y rellene la información del módulo.

    ![][2]

5. Por último, presione Finish.

6. Abra el archivo timodule.xml y agregue los siguientes cambios en la etiqueta Android.


        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>




> [WACOM.NOTE] En el código anterior, debe reemplazar todas las instancias del texto *com.winwire.notificationhub* con el nombre del paquete de la aplicación (identificador del módulo).

7. En el módulo Centro de notificaciones, haga clic con el botón secundario en la carpeta &quot;src&quot;, vaya a &quot;New&quot; y seleccione &quot;folder&quot;. Asigne un nombre a la carpeta, como por ejemplo com.google.android.gcm.

> [WACOM.NOTE] Si no puede ver &quot;folder&quot; en las opciones de &quot;New&quot;, seleccione &quot;Other&quot;, expanda General y, a continuación, seleccione &quot;Folder&quot;.

8. Ahora descargue los archivos &quot;.java&quot; (gcm.zip) desde aquí y copie esos archivos en la carpeta recién creada (com.google.android.gcm).

9. Ahora busque la carpeta con el nombre del identificador de módulo y expándala. En esa carpeta, puede ver una lista de archivos ".java". En esos archivos, abra aquel que tiene el nombre de su proyecto+module.java (por ejemplo, si el nombre de su proyecto es notificationhub, entonces sería &quot;NotificationhubModule.java&quot;) y agregue las líneas de código siguientes en la parte superior.

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

10. En el mismo archivo, busque el constructor y reemplácelo por el código siguiente.

        public NotificationhubModule() {
	        super();
	        _THIS = this;
        }

11. En el mismo archivo, agregue las líneas de código siguientes en la parte inferior.

        @Kroll.method
        public void registerC2dm(HashMap options) {
	        successCallback = (KrollFunction) options.get("success");
	        errorCallback = (KrollFunction) options.get("error");
	        messageCallback = (KrollFunction) options.get("callback");
	        String registrationId = getRegistrationId();
	        if (registrationId != null && registrationId.length() > 0) {
		        sendSuccess(registrationId);
	        } else {
		        GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
	        }
        }
        @Kroll.method
        public void unregister() {
	        GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
	        return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
	        return TiApplication.getInstance().getAppProperties()
	        .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
	        if (successCallback != null) {
		        HashMap data = new HashMap();
		        data.put("registrationId", registrationId);
		        successCallback.callAsync(getKrollObject(), data);
	        }
        }

        public void sendError(String error) {
	        if (errorCallback != null) {
		        HashMap data = new HashMap();
		        data.put("error", error);
		        errorCallback.callAsync(getKrollObject(), data);
	        }
        }

        public void sendMessage(HashMap messageData) {
	        if (messageCallback != null) {
		        HashMap data = new HashMap();
		        data.put("data", messageData);
		        messageCallback.call(getKrollObject(), data);
	        }
        }
        public static NotificationhubModule getInstance() {
	        return _THIS;
        }

12. Ahora descargue el archivo module.zip y copie los archivos en la carpeta cuyo identificador del módulo coincide con su nombre.

> [WACOM.NOTE] En los archivos anteriores, debe reemplazar todas las instancias del texto *com.winwire.notificationhub* con el nombre del paquete de la aplicación (identificador del módulo). Reemplace también &quot;NotificationhubModule&quot; por NombreProyecto+Module (por ejemplo, &quot;NotificationhubModule&quot;).

### Creación y empaquetado de un módulo

Elija **Deploy > Package - Android Module**. No puede crear un módulo BlackBerry usando Studio; use las herramientas BlackBerry NDK CLI o el IDE de Momentics. 

![][3]


Después, puede optar por implementar el módulo para todos los proyectos o para un proyecto específico. Esto sigue las reglas de instalación descritas en el [uso de módulos de Titanium], para resumir:

- Para todos los proyectos: el archivo .zip del módulo se coloca en la raíz de la ubicación de instalación del SDK de Titanium. 

- Para un proyecto concreto: el archivo .zip del módulo se coloca en la raíz del proyecto. 


## <a name="configure"></a>Configuración de Servicios móviles para enviar solicitudes de inserción


[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

5.	Escriba el valor Clave de API obtenido del GCM en el procedimiento anterior y después haga clic en Guardar.

    ![][4]

Su servicio móvil ahora está configurado para trabajar tanto con APNS como con GCM.

## <a name="add-push"></a>Incorporación de notificaciones de inserción a la aplicación

1.	En tiapp.xml, seleccione la pestaña tiapp.xml (puede encontrar esta pestaña en la parte inferior junto a la pestaña &quot;Introducción&quot;) y busque la etiqueta `<android>`. Debajo de la etiqueta, agregue el siguiente código:

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

>[WACOM.NOTE] En el código anterior, debe reemplazar **ModuleId**, **ApplicationId**  por el identificador del módulo, que se proporcionó en el momento de crear el módulo de GCM, y por el identificador de la aplicación, que se escribió en el momento de crear el proyecto.  Asimismo, asegúrese de que **ModuleId** y **ApplicationId** coincidan. También es necesario cambiar **ApplicationId.AppNameActivity**. Debe tener el aspecto com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity.


2.	Copie el módulo de GCM que creó anteriormente y colóquelo en la ubicación siguiente.	

    <table><tr>
    <td>OSX
    </td>
    <td>/Library/Application Support/Titanium or  ~/Library/Application Support/Titanium
    </td>
    </tr>
    <td>Windows 7
    </td>
    <td>C:\Users\username\AppData\Roaming (or C:\ProgramData\Titanium on Titanium Studio 1.0.1 and earlier)
    </td>
    </tr><td>Windows XP
    </td>
    <td>C:\Documents and Settings\username\Application Data (or C:\Documents and Settings\All Users\Application Data\Titanium on Titanium Studio 1.0.1 and earlier)
    </td>
    </tr><td>Linux
    </td>
    <td>~/.titanium
    </td>
    </tr>
    </tr>
    
    </table>

>[WACOM.NOTE]  En Mac OS, Library es una carpeta oculta. Para poder verla, es necesario ejecutar el siguiente comando y, después, volver a iniciar Finder: `**defaults write com.apple.finder AppleShowAllFiles TRUE**`

3.	En Appcelerator Titanium Studio, abra el archivo index.js y agregue el siguiente código en la parte inferior. Este código registrará el dispositivo para notificaciones de inserción.

            Alloy.Globals.tempRegId = '';
    	        if (OS_ANDROID) {
    	        var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }
   

4.	Ahora, abra el archivo TableData.js y busque las siguientes líneas en la función **addOrUpdateDataFromAndroid**

        var request = {
    	    'text' : alertTextField.getValue(),
    	    'complete' : false
        };
  

    Reemplace el código anterior solo en la condición else (para insertar un nuevo elemento).

5.	Para Android, reemplace el código anterior por el siguiente:

       var request = {
    	'text' : alertTextField.getValue(),
    	'complete' : false,
    	'handle' : Alloy.Globals.tempRegId
    }; 
    
    

6.	Ahora ubique las siguientes líneas en la función **updateOrAddData**

           var request = {
    	    'text' : alertTextField.getValue(),
    	    'complete' : false
        };
    
    Reemplace el código anterior solo en la condición else (para insertar un nuevo elemento).

7.	Para iOS, reemplace el código anterior por el siguiente:

       var request = {
    	'text' : alertTextField.getValue(),
    	'complete' : false,
    	'deviceToken' : Alloy.Globals.tempRegId
    };
    
 
Ahora, la aplicación se habrá actualizado para admitir notificaciones de inserción tanto en la plataforma iOS como en la plataforma Android.


## <a name="update-scripts"></a>Actualización del script de inserción registrado en el Portal de administración

1.	En el Portal de administración, haga clic en la pestaña Datos y, a continuación, en la tabla TodoItem.

    ![][5]

2.	En todoItem, haga clic en la pestaña Script y seleccione Insertar.

    ![][6]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla TodoItem.

3.	Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

**Para iOS:**

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }
  
   	> [WACOM.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación de inserción.  

**Para Android:**

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }



Esto registra un nuevo script de inserción, que usa el [objeto push de Servicios móviles] para enviar una notificación de inserción (el texto insertado) al dispositivo indicado en la solicitud de inserción.


<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png

<!-- Anchors. -->
[Generación del archivo de solicitud de firma de certificado]: #certificates
[Registro de la aplicación y habilitación para las notificaciones de inserción]: #register
[Creación de un perfil de aprovisionamiento para la aplicación]: #profile
[Habilitación del servicio de mensajería en la nube de Google]: #register-gcm
[Creación del módulo GCM para Titanium]: #gcm-module
[Configuración de Servicios móviles]: #configure
[Incorporación de notificaciones de inserción a la aplicación]: #add-push
[Actualización de scripts para enviar notificaciones de inserción]: #update-scripts
[Inserción de datos para recibir notificaciones]: #test

<!-- URLs. -->
[Introducción a los Servicios móviles]: /en-us/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
[Uso de módulos de Titanium]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
[Portal de administración de Microsoft Azure]: https://manage.windowsazure.com/
[Objeto push de Servicios móviles]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
[Instalación de las herramientas de desarrollo de Java]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools

<!--HONumber=35_1-->
