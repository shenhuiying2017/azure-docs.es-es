1.  En Visual Studio, ejecute la aplicación de cliente para tratar de autenticar con la cuenta de usuario que creamos con el nombre de David.

    ![][]

2.  David no pertenece al grupo de ventas. Por tanto, la comprobación de acceso basada en rol denegará el acceso a las operaciones de tabla. Cierre la aplicación de cliente.

    ![][1]

3.  En Visual Studio, ejecute de nuevo la aplicación de cliente. Esta vez autenticaremos con la cuenta de usuario que creamos con el nombre de Roberto.

    ![][2]

4.  Roberto sí pertenece al grupo de ventas. Por tanto, la comprobación de acceso basada en rol permitirá el acceso a las operaciones de tabla.

    ![][3]

  []: ./media/mobile-services-aad-rbac-test-app/dave-login.png
  [1]: ./media/mobile-services-aad-rbac-test-app/unauthorized.png
  [2]: ./media/mobile-services-aad-rbac-test-app/bob-login.png
  [3]: ./media/mobile-services-aad-rbac-test-app/success.png
