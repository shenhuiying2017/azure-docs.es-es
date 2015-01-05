<properties urlDisplayName="Configuring Python with Azure Websites" pageTitle="Configuración de Python con Sitios web Azure" metaKeywords="" description="This tutorial describes options for authoring and configuring a basic Web server Gateway Interface (WSGI) compliant Python application on Azure Websites." metaCanonical="" services="web-sites" documentationCenter="Python" title="Configuring Python with Azure Websites" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="08/01/2014" ms.author="huvalo" />




# Configuración de Python con Sitios web Azure #

En este tutorial se describen las opciones para crear y configurar una aplicación Python básica compatible con la Interfaz de puerta de enlace de servicio web (WSGI) en Sitios web Azure. Empezar a utilizar Sitios web Azure es fácil y la aplicación Python tendrá margen para escalar y ampliar a otros servicios de Azure. La plataforma Sitios web Azure incluye Python (su elección entre 2.7.3 o 3.4.0) y el controlador genérico FastCGI wfastcgi.py para Python. Todo lo que tiene que hacer es configurar el sitio web para usar el controlador de Python. 

> [WACOM.NOTE] Puede seleccionar la versión de Python que desee usar en el portal Sitios web de Azure. Para ello, abra la pestaña Configurar de su sitio web y cambie la opción **Versión de Python**.

Para ver un ejemplo más completo de configuración del marco de trabajo Django en Sitios web Azure, consulte el tutorial siguiente: 
[http://www.windowsazure.com/es-es/develop/python/tutorials/web-sites-with-django](http://www.windowsazure.com/en-us/develop/python/tutorials/web-sites-with-django).  

## Compatibilidad para WSGI

WSGI es un estándar de Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) que define una interfaz entre el servidor web y Python. Ofrece una interfaz normalizada para escribir varios marcos de trabajo y aplicaciones web con Python.  Los marcos de trabajo web conocidos de Python usan WSGI hoy en día.  La plataforma Sitios web Azure admite tales marcos de trabajo; además, los usuarios avanzados incluso pueden crear los suyos propios siempre que el controlador personalizado siga las instrucciones de la especificación de WSGI.

## Creación de sitios web

En este tutorial se asume que existe una suscripción a Azure y que ya se tiene acceso al Portal de administración de Azure. Para obtener instrucciones detalladas acerca de cómo crear un sitio web, consulte [http://www.windowsazure.com/es-es/manage/services/web-sites/how-to-create-websites](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-create-websites).
 
En resumen, si no tiene ya un sitio web, puede crearlo en el Portal de administración de Azure. Seleccione la característica SITIOS WEB y use la opción QUICK CREATE, donde debe especificar una URL para el sitio web.

![](./media/web-sites-python-configure/configure-python-create-website.png)

## Publicación Git

Use las pestañas QUICK START o PANEL para configurar la publicación Git en el sitio web recién creado.  Este tutorial usa Git para crear, administrar y publicar nuestro sitio web Python en Sitios web Azure. 

![](./media/web-sites-python-configure/configure-python-git.png)

Después de configurar la publicación Git, se creará un repositorio Git y se asociará al sitio web.  La URL del repositorio se mostrará y, en adelante, se podrá usar para enviar datos del entorno de desarrollo local a la nube. Para publicar aplicaciones a través de Git, asegúrese de que también se haya instalado un cliente Git y use las instrucciones facilitadas para enviar el contenido del sitio web a Sitios web Azure.

## Contenido de sitios web

Como ejemplo, usamos una aplicación Python básica con un controlador de WSGI básico que ilustra el trabajo mínimo necesario para beneficiarse de la compatibilidad de Python en Sitios web Azure.  Esta aplicación Python de base se puede usar para comenzar a crear diferentes soluciones, con una complejidad que abarca desde el ejemplo siguiente hasta un marco de trabajo web completo.  

A continuación se muestra el código del controlador de WSGI básico. Es similar al que sugiere la especificación [PEP 3333](http://www.python.org/dev/peps/pep-3333/) como un punto de partida para una aplicación que cumple el estándar WSGI. Hemos guardado este contenido en un archivo denominado ConfigurePython.py creado en una carpeta ConfigurePython en la raíz del sitio web:

	def application(environ, start_response):
	    status = '200 OK'
	    response_headers = [('Content-type', 'text/plain')]
	    start_response(status, response_headers)
	    yield 'Hello from Azure Websites\n'

*application* es un invocable de Python, que actuará como el punto de entrada llamado por un servidor compatible con WSGI. Este objeto invocable acepta dos argumentos posicionales: 

* *environ*: un diccionario con varias variables de entorno.
* *start_response*: un invocable proporcionado por el servidor web para la transferencia del estado HTTP y el encabezado de respuesta.

Este controlador devolverá el texto sin formato "Hello from Azure Websites" para cada solicitud que se le realice.

## Opciones de configuración

Hay 2 opciones diferentes para configurar la aplicación Python con Sitios web Azure.

<h3 id="option1">Opción 1: Portal</h3>

1,1. Registre el controlador FastCGI mediante la pestaña CONFIGURE del portal.
Para este ejemplo, usamos el controlador FastCGI para Python incluido en Sitios web Azure. Para hacer lo mismo, use las siguientes rutas de acceso para el argumento del procesador de scripts y del controlador FastCGI:

* Ruta de acceso al procesador de scripts de Python: D:\python27\python.exe
* Ruta de acceso al controlador FastCGI de Python: D:\python27\scripts\wfastcgi.py

![](./media/web-sites-python-configure/configure-python-handler-mapping.png)

1,2. Configure la aplicación en la misma pestaña CONFIGURE del portal.
Las opciones de configuración de la aplicación se convierten en variables de entorno. Se trata de un mecanismo que puede usar para los valores de configuración que la aplicación Python requiere. Para esta aplicación de ejemplo básica, hemos configurado lo siguiente:

* PYTHONPATH informa a Python del directorio donde buscar los módulos. Sitios web Azure ofrece D:\home\site\wwwroot como código sintáctico que remite a la raíz del sitio web. 
* WSGI\_HANDLER registra el nombre de un módulo o paquete y el atributo que se usará.

![](./media/web-sites-python-configure/configure-python-app-settings.png)

<h3 id="option2">Opción 2: web.config</h3>
La alternativa de configuración es usar un archivo web.config en la raíz del sitio web para las acciones descritas a continuación. El uso de la opción web.config ofrece mayor potencial de transferencia para una aplicación web. Hay dos enfoques disponibles para dirigir las solicitudes a la aplicación web: definir un controlador que controle la ruta de acceso *, que ordena a IIS que redirija todas las solicitudes entrantes a través de Python, o bien definir una ruta de acceso específica que Python controlará y, posteriormente, emplear la reconfiguración de URL para redirigir varias URL a nuestra ruta de acceso seleccionada.  De hecho, recomendamos el último enfoque para obtener mayor rendimiento, que consiste en usar un archivo de controlador vacío en la raíz del sitio web para que actúe como destino de la solicitud (handler.fcgi en nuestro ejemplo). En el escenario anterior, todas las solicitudes, incluidas las de contenido estático (por ejemplo, los archivos de imagen y hojas de estilo), tendrán que pasar por Python, resolviendo las optimizaciones que el servidor web ofrece para obtener acceso a los archivos estáticos.  El uso del último enfoque permite ofrecer contenido estático con eficacia e invocar a Python solo cuando resulta necesario.

2,1. Especifique la variable PYTHONPATH. 
> Esto informará a Python de dónde buscar el código de aplicación. D:\home\site\wwwroot también se usa aquí como ruta de acceso absoluta al sitio web.

2.2. Establecer la variable WSGI\_HANDLER.
> La plataforma Sitios web Azure usa este valor para ordenar a Python que llame a nuestro controlador de WSGI.  El valor de esta variable es una expresión de Python que, cuando se ejecuta, debe devolver un invocable que representa a un controlador de WSGI. 

2.3. Agregue un controlador para Python.
> De esta forma se informará a Sitios web Azure de que Python debe controlar las solicitudes realizadas a la ruta de acceso de handler.fcgi. Es importante que la sintaxis del controlador sea exactamente igual a la que se encuentra en la etiqueta &lt;handlers&gt; en el ejemplo siguiente, a menos que proporcione su propio controlador FastCGI o la pila de desarrollo de Python.

2.4. Reconfigure las URL en handler.fcgi.
> Realizar solicitudes de handler.fcgi constantemente tal vez no sea la mejor opción. Para seleccionar la ruta de acceso de los archivos que controlará el controlador de Python, hemos usado la reconfiguración de URL para que el controlador de Python pueda controlar todas las URL.

	<configuration>
  		<appSettings>
    		<add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
    		<add key="WSGI_HANDLER" value="ConfigurePython.application" />
  		</appSettings>
  		<system.webServer>
    		<handlers>
      			<add name="PythonHandler" 
           		verb="*" path="handler.fcgi" 
           		modules="FastCgiModule" 
           		scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
           		resourceType="Either" />
   			</handlers>
			<rewrite>
	    		<rules>
					<rule name="Configure Python" stopProcessing="true">
		    			<match url="(.*)" ignoreCase="false" />
		    			<conditions>
							<add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
		    			</conditions>
		    			<action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
					</rule>
	    		</rules>
			</rewrite>
  		</system.webServer>
	</configuration> 

La estructura de carpeta para el ejemplo en la raíz del sitio web es la siguiente (el uso de mayúsculas y minúsculas de la carpeta de Python y los nombres de archivo es importante y se refleja en web.config):

* ConfigurePython\ConfigurePython.py
* web.config
* handler.fcgi

Habida cuenta de que estamos reconfigurando todas las URL para handler.fcgi y controlando dicha ruta de acceso a través de FastCGI para Python, necesitamos crear un archivo de marcador de posición con el mismo nombre, a fin de que IIS no devuelva un error HTTP 404. Esto se debe al comportamiento interno del módulo FastCGI de IIS, que impone la existencia del archivo solicitado antes de transferirlo a la aplicación del procesador de scripts especificada.

Vaya al sitio web para probar la configuración correcta. Para este ejemplo, aparece el mensaje "Hello from Azure Websites" al obtener acceso.

![](./media/web-sites-python-configure/configure-python-result.png)


<!--HONumber=35.1-->
