> [AZURE.IMPORTANT]Para recibir notificaciones de inserción de Mobile Engagement, debe habilitar `Silent Remote Notifications` en su aplicación. Deberá agregar el valor remote-notification a la matriz UIBackgroundModes en el archivo Info.plist.

1. Abra el archivo `info.plist` del proyecto
2. Haga clic con el botón derecho en el elemento superior de la lista (`Information Property List`) y agregue una nueva fila

	![][1]

3. En la nueva fila, escriba `Required background modes`

	![][2]

4. Haga clic en la flecha izquierda para expandir la fila
5. Agregue el siguiente valor al elemento 0 `App downloads content in response to push notifications`

	![][3]

Una vez realizado el cambio, info.plist XML debe contener la clave y el valor siguientes:

    <key>UIBackgroundModes</key>
        <array>
            <string>remote-notification</string>
        </array>
    ...

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png
[2]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png
[3]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png

<!---HONumber=August15_HO6-->