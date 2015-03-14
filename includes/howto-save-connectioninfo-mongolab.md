Aunque es posible pegar un URI de MongoLAB en el código, recomendamos configurarlo en el entorno para obtener una mayor facilidad en la administración. De esta forma, si el URI cambia, puede actualizarlo a través del Portal de Azure sin dirigirse al código.


1. En el Portal de Azure, seleccione **Sitios web**.
1. Haga clic en el nombre del sitio web de la lista de sitios web.  
![WebSiteEntry][entry-website]  
Aparecerá el panel del sitio web.

1. Haga clic en **Configurar** en la barra de menús.  
![ConfigurarPanelSitioWeb][focus-mongolab-websitedashboard-config]

1. Desplácese hacia abajo hasta la sección Cadenas de conexión.  
![CadenasConexiónSitioWeb][focus-mongolab-websiteconnectionstring]

1. Para **Nombre**, escriba MONGOLAB_URI.
1. Para **Value**, pegue la cadena de conexión que se obtuvo en la sección anterior.
1. Seleccione **Personalizado** en la lista desplegable **Tipo** en lugar del valor predeterminado **SQLAzure**).
1. Haga clic en **Guardar** en la barra de herramientas.  
![GuardarSitioWeb][button-website-save]

**Nota:** Azure agrega el prefijo **CUSTOMCONNSTR\_** a esta variable, por lo cual el código anterior hace referencia a **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
<!--HONumber=42-->
