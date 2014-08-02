Después de que los registros de su nombre de dominio se han propagado, debería poder utilizar el explorador para comprobar que el nombre de dominio personalizado se puede utilizar para tener acceso al sitio web.

> [WACOM.NOTE] El CNAME tardará cierto tiempo en propagarse por el sistema DNS. Puede utilizar un servicio como <http://www.digwebinterface.com/> para comprobar que el CNAME está disponible.

Si aún no lo ha agregado al sitio web como extremo del Administrador de tráfico, debe hacerlo para que funcione la resolución de nombres, debido a que el nombre de dominio personalizado se enruta a Traffic Manager. Posteriormente, el Administrador de tráfico enruta al sitio web. Utilice la información de [Adición o eliminación de extremos](http://msdn.microsoft.com/en-us/library/windowsazure/hh744839.aspx) para agregar su sitio web como un extremo en su perfil del Administrador de tráfico.

> [WACOM.NOTE] Si su sitio web no aparece en la lista al agregar un extremo, compruebe que está configurado en modo estándar. Si desea trabajar con el Administrador de tráfico, debe utilizar el modo estándar para su sitio web.

