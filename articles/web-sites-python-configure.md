<properties 
	pageTitle="Configuración de Python con Sitios web Azure" 
	description="En este tutorial se describen las opciones para crear y configurar una aplicación Python básica compatible con la Interfaz de puerta de enlace de servicio web (WSGI) en Sitios web Azure." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Configuración de Python con Sitios web Azure

En este tutorial se describen las opciones para crear y configurar una aplicación Python básica compatible con la Interfaz de puerta de enlace de servicio web (WSGI) en Sitios web Azure.

Describe características adicionales de implementación de Git, por ejemplo, el entorno virtual y la instalación de paquetes mediante requirements.txt.

+ [¿Bottle, Django o Flask?](#bottle-django-flask)
+ [Creación de sitios web en el portal](#website-creation-on-portal)
+ [Publicación Git](#git-publishing)
+ [Información general sobre aplicaciones](#application-overview)
+ [Controlador de WSGI](#wsgi-handler)
+ [Entorno virtual](#next-steps)
+ [Administración de paquetes](#next-steps)
+ [Versión de Python](#next-steps)
+ [Proxy del entorno virtual](#virtual-environment-proxy)
+ [Personalización de la implementación de Git](#customize-git-deployment)
+ [Solución de problemas: implementación](#troubleshooting-deployment)
+ [Solución de problemas: instalación de paquetes](#troubleshooting-package-installation)
+ [Solución de problemas: entorno virtual](#troubleshooting-virtual-environment)


<h2><a name="bottle-django-flask"></a>¿Bottle, Django o Flask?</h2>

La Galería de Azure contiene plantillas para los marcos de Bottle, Django y Flask.  Si está desarrollando su primer Sitio web Azure o no está familiarizado con Git, le recomendamos que siga uno de estos tutoriales, que incluyen instrucciones paso a paso para crear una aplicación funcional desde la galería, mediante implementación de Git desde Windows o Mac:

- [Creación de sitios web con Bottle][]
- [Creación de sitios web con Django][]
- [Creación de sitios web con Flask][]


<h2><a name="website-creation-on-portal"></a>Creación de sitios web en el Portal</h2>

En este tutorial se asume que existe una suscripción a Azure y que ya se tiene acceso al Portal de administración de Azure.

Si no tiene ya un sitio web, puede crearlo en el Portal de administración de Azure.  Haga clic en el botón Nuevo en la esquina inferior izquierda. Aparecerá una ventana. Haga clic en Proceso, Sitio web y, a continuación, en Quick Create.

![](./media/web-sites-python-configure/configure-python-create-website.png)


<h2><a name="git-publishing"></a>Publicación Git</h2>

Use las pestañas QUICK START o PANEL para configurar la publicación Git en el sitio web recién creado.  Este tutorial usa Git para crear, administrar y publicar nuestro sitio web Python en Sitios web Azure.

![](./media/web-sites-python-configure/configure-python-git.png)

Después de configurar la publicación Git, se creará un repositorio Git y se asociará al sitio web.  La URL del repositorio se mostrará y, en adelante, se podrá usar para enviar datos del entorno de desarrollo local a la nube. Para publicar aplicaciones a través de Git, asegúrese de que también se haya instalado un cliente Git y use las instrucciones facilitadas para enviar el contenido del sitio web a Sitios web Azure.


<h2><a name="application-overview"></a>Información general sobre aplicaciones</h2>

En las secciones siguientes, se crean los siguientes archivos.  Se deben colocar en la raíz del repositorio Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


<h2><a name="wsgi-handler"></a>Controlador de WSGI</h2>

WSGI es un estándar de Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) que define una interfaz entre el servidor web y Python. Ofrece una interfaz normalizada para escribir varios marcos de trabajo y aplicaciones web con Python.  Los marcos de trabajo web conocidos de Python usan WSGI hoy en día.  La plataforma Sitios web Azure admite tales marcos de trabajo; además, los usuarios avanzados incluso pueden crear los suyos propios siempre que el controlador personalizado siga las instrucciones de la especificación de WSGI.

Este es un ejemplo de un archivo `app.py` que define un controlador personalizado:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Puede ejecutar esta aplicación localmente con `python app.py` y, a continuación, ir a `http://localhost:5555` en el explorador web.


<h2><a name="virtual-environment"></a>Entorno virtual</h2>

Aunque la aplicación de ejemplo anterior no requiere ningún paquete externo, es probable que su aplicación necesite algunos.

Para ayudar a administrar las dependencias de paquetes externo, la implementación de Azure Git admite la creación de entornos virtuales.

Cuando Azure detecta un archivo requirements.txt en la raíz del repositorio, crea automáticamente un entorno virtual denominado `env`.  Esto solo sucede en la primera implementación, o durante cualquier implementación después de que Python en tiempo de ejecución haya cambiado.

Probablemente deseará crear un entorno virtual de forma local para el desarrollo, pero no lo incluya en su repositorio Git.


<h2><a name="package-management"></a>Administración de paquetes</h2>

Los paquetes que aparezcan en requirements.txt se instalarán automáticamente en el entorno virtual mediante pip.  Esto se realiza en cada implementación, pero pip omitirá la instalación si un paquete ya se encuentra instalado.

Ejemplo de `requirements.txt`:

    azure==0.8.4


<h2><a name="python-version"></a>Versión de Python</h2>

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

Ejemplo de `runtime.txt`:

    python-2.7


<h2><a name="web-config"></a>Web.config</h2>

Necesitará crear un archivo web.config para especificar la forma en que el servidor debe controlar las solicitudes.

Tenga en cuenta que si tiene un archivo web.x.y.config en el repositorio, donde x.y coincide con el tiempo de ejecución de Python seleccionado, Azure copiará automáticamente el archivo adecuado como web.config.

Los siguientes ejemplos de web.config se basan en un script de proxy del entorno virtual, que se describe en la sección siguiente.  Funcionan con el controlador WSGI utilizado en el ejemplo `app.py` anterior.

Ejemplo de `web.config` para Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Ejemplo de `web.config` para Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Los archivos estáticos los administrará el servidor web directamente, sin pasar por el código de Python, para mejorar el rendimiento.

En los ejemplos anteriores, la ubicación de los archivos estáticos en el disco debe coincidir con la ubicación en la dirección URL.  Esto significa que una solicitud de `http://pythonapp.azurewebsites.net/static/site.css` servirá el archivo en el disco en `\static\site.css`.

Es posible configurar la regla `Static Files` para servir archivos desde una ubicación del disco distinta de la ubicación en la dirección URL.  En la siguiente definición de regla, una solicitud de `http://pythonapp.azurewebsites.net/static/site.css` servirá el archivo en el disco en `\FlaskWebProject\static\site.css`, en lugar de en `\static\site.css`.

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` es donde se especifica el controlador de WSGI.  En los ejemplos anteriores, 3w `app.wsgi_app` porque el controlador es una función denominada `wsgi_app` en `app.py` en la carpeta raíz.

`PYTHONPATH` se puede personalizar, pero si instala todas las dependencias en el entorno virtual, mediante su especificación en requirements.txt, no es necesario cambiarlo.


<h2><a name="virtual-environment-proxy"></a>Proxy del entorno virtual</h2>

El siguiente script se utiliza para recuperar el controlador de WSGI, activar el entorno virtual y el registro de errores. Está diseñado para ser genérico y se usa sin modificaciones.

Contenido de `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_HANDLER env var must be set')
        
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None

        if handler is None:
            raise ValueError('"%s" could not be imported' % handler_name)

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
        
        site.main()
        
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


<h2><a name="customize-git-deployment"></a>Personalización de la implementación de Git</h2>

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-deployment.md)]


<h2><a name="troubleshooting-deployment"></a>Solución de problemas: implementación</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>Solución de problemas: instalación de paquetes</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>Solución de problemas: entorno virtual</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]



[Creación de sitios web con Bottle]: ../web-sites-python-create-deploy-bottle-app
[Creación de sitios web con Django]: ../web-sites-python-create-deploy-django-app
[Creación de sitios web con Flask]: ../web-sites-python-create-deploy-flask-app





<!--HONumber=42-->
