En esta sección agregará dos nuevos usuarios a su directorio junto con el nuevo grupo de ventas. Uno de los usuarios se incluirá en el grupo de ventas y el otro no.

### Creación de usuarios

1.  En el [Portal de administración de Azure], vaya al directorio que configuró previamente para autenticación cuando realizó el tutorial [Introducción a la autenticación].
2.  Haga clic en **Usuarios** en la parte superior de la página. A continuación, haga clic en el botón **Agregar usuario** de la parte inferior.
3.  Complete los cuadros de diálogo de nuevo usuario para crear un usuario llamado **Roberto**. Anote la contraseña temporal de ese usuario.
4.  Cree otro usuario llamado **David**. Anote la contraseña temporal de ese usuario.
5.  Los nuevos usuarios tendrán un aspecto similar al que se muestra a continuación.

    ![][]

### Creación del grupo de ventas

1.  En la página de directorios, haga clic en **Grupos** en la parte superior de la página. A continuación, haga clic en el botón **Agregar grupo** de la parte inferior.
2.  Escriba **Ventas** como nombre del grupo y presione el botón de finalizar en el cuadro de diálogo para crear el grupo.

    ![][1]

### Incorporación de usuarios al grupo de ventas

1.  Haga clic en **Grupos** en la parte superior de la página de directorios. A continuación, haga clic en el grupo **Ventas** para ir a la página del grupo de ventas.
2.  En la página del grupo de ventas, haga clic en **Agregar miembros**. Agregue el usuario llamado **Roberto** al grupo de ventas. El usuario llamado **David** no será miembro del grupo.

    ![][2]

3.  En la página del grupo de ventas, haga clic en **Configurar** y, a continuación, fíjese en el **Id. de objeto** del grupo de ventas. En realidad, este tutorial se fija en el identificador del objeto del grupo utilizando las API de Graph. Así pues, no necesitará el identificador. Sin embargo, en algunos casos es mejor no utilizar el nombre del grupo porque podría cambiar (mientras que el identificador permanece igual). Si desea almacenar el identificador de grupo como una configuración de aplicación en el servicio móvil, o codificarlo de forma rígida en su código, es aquí donde lo encontrará.

    ![][3]

  []: ./media/mobile-services-aad-rbac-create-sales-group/users.png
  [1]: ./media/mobile-services-aad-rbac-create-sales-group/sales-group.png
  [2]: ./media/mobile-services-aad-rbac-create-sales-group/group-membership.png
  [3]: ./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png
