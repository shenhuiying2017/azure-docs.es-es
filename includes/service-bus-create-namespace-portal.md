1. Inicie sesión en el Portal de Azure clásico.

2. En el panel de navegación izquierdo del Portal, haga clic en **Bus de servicio**.

3. En el panel inferior del Portal, haga clic en **Crear**.

    ![Seleccionar Crear][select-create]
   
4. En el cuadro de diálogo **Agregar un nuevo espacio de nombres**, escriba un nombre de espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.

    ![Nombre del espacio de nombres][namespace-name]
  
5. Después de comprobar que el nombre del espacio de nombres esté disponible, elija el país o región donde debería hospedarse.

6. Deje los demás campos del cuadro de diálogo con los valores predeterminados (**Mensajería** y **Nivel estándar**) y, a continuación, haga clic en la marca de verificación Aceptar. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.
 
    ![Creado correctamente][created-successfully]

###Obtención de las credenciales
1. En el panel de navegación izquierdo, haga clic en el nodo **Bus de servicio** para ver la lista de espacios de nombres disponibles:
 
    ![Seleccionar el Bus de servicio][select-service-bus]
  
2. Seleccione el espacio de nombres que acaba de crear en la lista desplegable:
 
    ![Seleccionar el espacio de nombres][select-namespace]
 
3. Haga clic en **Información de conexión**.

    ![Información de conexión][connection-information]
  
4. En el panel **Información de conexión de acceso**, encuentre la cadena de conexión que contiene la clave SAS y el nombre de la clave.

    ![Información de conexión de acceso][access-connection-information]
  
5. Anote la clave o cópiela en el Portapapeles.

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0615_2016-->