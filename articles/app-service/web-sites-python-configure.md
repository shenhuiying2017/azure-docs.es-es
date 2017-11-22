---
title: "Configuración de Python con Azure App Service Web Apps"
description: "En este tutorial se describen las opciones para crear y configurar una aplicación Python básica compatible con la Interfaz de puerta de enlace de servicio web (WSGI) en Azure App Service Web Apps."
services: app-service
documentationcenter: python
tags: python
author: huguesv
manager: erikre
editor: 
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
ms.openlocfilehash: 86e19d5bb942937779665eb60d9dc0654c16747d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Configuración de Python con Azure App Service Web Apps
Este tutorial describen las opciones para crear y configurar una aplicación básica de interfaz de puerta de enlace de servidor web (WSGI) compatible con Python en [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Describe características adicionales de implementación de Git como, por ejemplo, el entorno virtual y la instalación del paquete mediante requirements.txt.

## <a name="bottle-django-or-flask"></a>¿Bottle, Django o Flask?
Azure Marketplace contiene plantillas para los marcos de Bottle, Django y Flask. Si desarrolla la primera aplicación web en Azure App Service puede crear una rápidamente en Azure Portal:

* [Creación de Aplicaciones web con Bottle](https://portal.azure.com/#create/PTVS.Bottle)
* [Creación de Aplicaciones web con Django](https://portal.azure.com/#create/PTVS.Django)
* [Creación de Aplicaciones web con Flask](https://portal.azure.com/#create/PTVS.Flask)

## <a name="web-app-creation-on-azure-portal"></a>Creación de una aplicación web en Azure Portal
En este tutorial se asume que existe una suscripción a Azure y que ya se tiene acceso a Azure Portal.

Si no tiene ninguna aplicación web, puede crearla en [Azure Portal](https://portal.azure.com).  Haga clic en el botón NUEVO que aparece en la esquina superior izquierda y, luego, haga clic en**Web + móvil** > **Aplicación web**.

## <a name="git-publishing"></a>Publicación Git
Configure la publicación de Git para la aplicación web recién creada siguiendo las instrucciones que se describen en [Implementación de Git local en Azure App Service](app-service-deploy-local-git.md). En este tutorial se usa GIT para crear, administrar y publicar su aplicación web Python en Azure App Service.

Después de configurar la publicación de GIT, se crea un repositorio de GIT y se asocia con la aplicación web. Aparece la dirección URL del repositorio y se puede usar para enviar datos del entorno de desarrollo local a la nube. Para publicar aplicaciones a través de Git, asegúrese de que también se haya instalado un cliente Git y use las instrucciones facilitadas para enviar el contenido de la aplicación web a Azure App Service.

## <a name="application-overview"></a>Información general de la aplicación
En las secciones siguientes, se crean los siguientes archivos. Se deben colocar en la raíz del repositorio de Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Controlador de WSGI
WSGI es un estándar de Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) que define una interfaz entre el servidor web y Python. Ofrece una interfaz normalizada para escribir varios marcos de trabajo y aplicaciones web con Python. Los marcos de trabajo web conocidos de Python usan WSGI hoy en día. Azure App Service Web Apps admite tales marcos de trabajo; además, los usuarios avanzados incluso pueden crear los suyos propios siempre que el controlador personalizado siga las instrucciones de la especificación de WSGI.

A continuación se muestra un ejemplo de `app.py` que define un controlador personalizado:

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

Puede ejecutar esta aplicación localmente con `python app.py` e ir a `http://localhost:5555` en el explorador web.

## <a name="virtual-environment"></a>Entorno virtual
Aunque la aplicación de ejemplo anterior no requiere ningún paquete externo, es probable que la aplicación requiera alguno.

Para ayudar a administrar las dependencias de paquete externo, la implementación de Git de Azure admite la creación de entornos virtuales.

Cuando Azure detecta un archivo requirements.txt en la raíz del repositorio, crea automáticamente un entorno virtual denominado `env`. Esto solo ocurre en la primera implementación, o durante cualquier implementación que ocurra después de que cambie el tiempo de ejecución de Python seleccionado.

Puede que quiera crear un entorno virtual localmente para el desarrollo, pero no incluirlo en el repositorio de GIT.

## <a name="package-management"></a>Administración de paquetes
Los paquetes enumerados en requirements.txt se instalan automáticamente en el entorno virtual con PIP. Esto se realiza en cada implementación, pero PIP omite la instalación si un paquete ya está instalado.

Ejemplo `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>versión de Python
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Ejemplo `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config
Tiene que crear un archivo web.config para especificar la manera de controlar las solicitudes del servidor.

Si tiene un archivo web.x.y.config en el repositorio, donde x.y coincide con el entorno en tiempo de ejecución de Python seleccionado, Azure copia automáticamente el archivo correspondiente como web.config.

Los siguientes ejemplos de web.config se basan en un script de proxy del entorno virtual, que se describe en la sección siguiente.  Funcionan con el controlador WSGI que se usa en el ejemplo `app.py` anterior.

Ejemplo `web.config` para Python 2.7:

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
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
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
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Ejemplo `web.config` para Python 3.4:

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
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
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
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


El servidor web administra los archivos estáticos directamente, sin pasar por el código de Python, para obtener un rendimiento mejorado.

En los ejemplos anteriores, la ubicación de los archivos estáticos en disco debe coincidir con la ubicación de la dirección URL. Esto significa que una solicitud de `http://pythonapp.azurewebsites.net/static/site.css` servirá el archivo en disco en `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` es donde especifica el controlador WSGI. En los ejemplos anteriores, es `app.wsgi_app` porque el controlador es una función denominada `wsgi_app` en `app.py` en la carpeta raíz.

`PYTHONPATH` , pero si instala todas las dependencias en el entorno virtual especificándolas en requirements.txt, no es necesario cambiarlo.

## <a name="virtual-environment-proxy"></a>Proxy del entorno virtual
El siguiente script se utiliza para recuperar el controlador de WSGI, activar el entorno virtual y registrar errores. Está diseñado para ser genérico y se usa sin modificaciones.

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
    import traceback

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
            log('error importing ptvsd.\n')

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')

        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
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
                last_tb = ': ' + traceback.format_exc()

        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))

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


## <a name="customize-git-deployment"></a>Personalización de la implementación de Git
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>Solución de problemas - Instalación de un paquete
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Solución de problemas - Entorno virtual
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

> [!NOTE]
> Si desea empezar a trabajar con Azure App Service antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba de App Service](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en App Service. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 
