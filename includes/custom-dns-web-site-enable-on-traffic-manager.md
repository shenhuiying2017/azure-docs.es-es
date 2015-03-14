Después de que los registros de su nombre de dominio se hayan propagado, debería poder usar el explorador para comprobar que el nombre de dominio personalizado se puede utilizar para tener acceso al sitio web.

> [AZURE.NOTE] El CNAME puede tardar un tiempo en propagarse por el sistema DNS. Puede usar un servicio como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para comprobar que el CNAME está disponible.

Si aún no lo ha agregado al sitio web como extremo del Administrador de tráfico, debe hacerlo para que funcione la resolución de nombres, debido a que el nombre de dominio personalizado se enruta al Administrador de tráfico. Posteriormente, el Administrador de tráfico enruta al sitio web. Use la información de [Adición o eliminación de extremos](http://msdn.microsoft.com/library/windowsazure/hh744839.aspx) para agregar su sitio web como un extremo en el perfil del Administrador de tráfico.

> [AZURE.NOTE] Si su sitio web no aparece en la lista al agregar un extremo, compruebe que está configurado en modo estándar. Si desea trabajar con el Administrador de tráfico, debe utilizar el modo estándar para su sitio web.
\<!--HONumber=42-->
