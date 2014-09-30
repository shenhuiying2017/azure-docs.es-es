-   Inicie sesión en la cuenta de Azure con sus credenciales.

    Este método es más rápido y sencillo pero, si lo utiliza, no podrá ver la Base de datos SQL ni los Servicios móviles de Azure en la ventana **Server Explorer**.

    En **Server Explorer**, haga clic en el botón **Connect to Azure**. También puede hacer clic con el botón secundario en el nodo **Azure** y, a continuación, hacer clic en **Connect to Azure** en el menú contextual.

-   Instale un certificado de administración que permita el acceso a su cuenta.

    En **Server Explorer**, haga clic con el botón secundario en el nodo **Azure** y, a continuación, haga clic en **Manage Subscriptions** en el menú contextual. En el cuadro de diálogo **Manage Azure Subscriptions**, haga clic en la pestaña **Certificados** y, a continuación, en **Import**. Siga las instrucciones para descargar y luego importar un archivo de suscripción (también conocido como archivo *.publishsettings*) para su cuenta de Azure.

    <div class="dev-callout"><strong>Nota de seguridad:</strong><br /> <p>Descargue el archivo de suscripci&oacute;n en una carpeta ajena a los directorios de c&oacute;digo fuente (por ejemplo, en la carpeta Descargas) y elim&iacute;nelo una vez que finalice la importaci&oacute;n. Si un usuario malintencionado obtuviera acceso al archivo de suscripci&oacute;n, podr&iacute;a editar, crear y eliminar servicios de Azure.</p></div>


    Para obtener más información, consulte [Administrar cuentas, suscripciones y roles administrativos][].

  [Administrar cuentas, suscripciones y roles administrativos]: http://go.microsoft.com/fwlink/?LinkId=324796
