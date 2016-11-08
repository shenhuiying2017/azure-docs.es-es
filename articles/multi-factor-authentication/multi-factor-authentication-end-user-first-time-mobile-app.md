---
title: Uso de una aplicación móvil como método de contacto con Azure Multi-Factor Authentication | Microsoft Docs
description: Este artículo muestra cómo utilizar una aplicación móvil como método de contacto principal para Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: kgremban

---
# Uso de una aplicación móvil como método de contacto con Azure Multi-Factor Authentication
Este artículo puede serle útil si desea usar la aplicación Microsoft Authenticator como método de contacto principal. La información que aquí se ofrece le guiará a través de la configuración de Azure Multi-Factor Authentication para usar la aplicación móvil como método de contacto principal.

La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Uso de Microsoft Authenticator como método de contacto
1. En la pantalla **Comprobación de seguridad adicional**, seleccione **Aplicación móvil** en la lista desplegable.
2. Seleccione **Notificación** o **Contraseña de un solo uso** y seleccione **Configurar**.
   
    ![Pantalla Comprobación de seguridad adicional](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)
3. En el teléfono que tenga instalada la aplicación Microsoft Authenticator, abra la aplicación y seleccione **+** para agregar una cuenta.
4. Especifique que quiere agregar una cuenta profesional o educativa. Se abrirá el escáner de código QR. Si la cámara no funciona correctamente, puede seleccionar escribir la información de su compañía manualmente. Para más información, consulte [Incorporación manual de una cuenta](#add-an-account-manually).
   
    ![Pantalla para seleccionar una cuenta](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)
   
    ![Pantalla para seleccionar si desea digitalizar un código QR o especificar una cuenta manualmente](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)
5. Digitalice la imagen de código QR que apareció con la pantalla para configurar la aplicación móvil. Seleccione **Listo** para cerrar la pantalla de código QR.
   
    ![Pantalla de código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)
   
    Si no puede digitalizar el código QR, puede escribir la información manualmente.
   
    ![Pantalla para escribir la información manualmente](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)
6. Cuando termine la activación por teléfono, seleccione **Contact me** (Contacto). Con este paso se envía una notificación o un código de verificación al teléfono. Seleccione **Comprobar**.
   
    ![Pantalla para seleccionar la comprobación de inicio de sesión](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)
7. Si su empresa requiere un PIN para aprobar la comprobación de inicio de sesión, escríbalo.
   
    ![Cuadro para escribir un NIP](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)
8. Cuando haya terminado de escribir el PIN, seleccione **Cerrar**. A estas alturas, la verificación debería haberse realizado correctamente.
9. Lo recomendable es introducir el número de teléfono móvil por si perdiera el acceso a la aplicación móvil. Especifique el país en la lista desplegable y escriba su número de teléfono móvil en la casilla junto al país. Seleccione **Siguiente**.
10. Llegados a este punto, ya ha configurado el método de contacto. Ahora es el momento de configurar contraseñas para aplicaciones que no son aplicaciones de explorador, como Outlook 2010 o anteriores. Si no utiliza estas aplicaciones, seleccione **Listo**. De lo contrario, continúe con el paso siguiente.
    
    ![Pantalla para crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)
11. Si está usando aplicaciones sin explorador, copie la contraseña de aplicación proporcionada y péguela en la aplicación sin explorador. Para información acerca de aplicaciones específicas como Outlook y Lync, consulte How to change the password in your email to the app password (Cómo cambiar la contraseña del correo electrónico por la de aplicación) y How to change the password in your application to the app password (Cómo cambiar la contraseña de la aplicación por la contraseña de aplicación).
12. Seleccione **Listo**.

## Adición de una cuenta manualmente
Para agregar una cuenta manualmente:

1. Seleccione el botón **Especificar cuenta manualmente**.
   
    ![Pantalla para escribir el código y la dirección URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)
   
    ![Pantalla para escribir el código y la dirección URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)
2. Si tiene una cuenta que ya dispone de Azure Multi-Factor Authentication, especifique el código y la dirección URL que se ofrecen en la misma página que le muestra el código de barras. Esta información aparece en los cuadros **Código** y **Dirección URL** en la aplicación móvil.
   
    ![Configuración](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)
3. Cuando termine la activación, seleccione **Contact me** (Contacto). Con este paso se envía una notificación o un código de verificación al teléfono. Seleccione **Comprobar**.

<!---HONumber=AcomDC_0921_2016-->