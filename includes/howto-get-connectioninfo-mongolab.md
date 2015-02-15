Cuando realiza el aprovisionamiento en una base de datos MongoLab, MongoLab transmite un URI de conexión a Azure en el formato de cadena de conexión estándar de MongoDB. Este valor se usa para iniciar una conexión de MongoDB mediante su elección del controlador de MongoDB. Para obtener más información sobre las cadenas de conexión, consulte [Conexiones](http://www.mongodb.org/display/DOCS/Connections) en mongodb.org.

**El URI contiene el nombre del usuario de la base de datos y la contraseña.  Déle el mismo tratamiento que a la información confidencial y no lo comparta.**

Realice los siguientes pasos para recuperar el URI en el Portal de Azure:

1. Seleccione **Complementos**.  
![AddonsButton][button-addons]
1. Busque el servicio de MongoLab en la lista de complementos.  
![EntradaMongolab][entry-mongolabaddon]
1. Haga clic en el nombre del complemento para ir a la página de complementos.
1. Haga clic en **Información de la conexión**.  
![BotónInformaciónConexión][button-connectioninfo]  
Se mostrará el URI de MongoLab URI:  
![PantallaInformaciónConexión][screen-connectioninfo]  
1.  Haga clic en el botón del portapapeles a la derecha del valor MONGOLAB_URI para copiar el valor completo al portapapeles.

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png

<!--HONumber=42-->
