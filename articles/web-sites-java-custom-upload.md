<properties linkid="develop-java-tutorials-web-site-custom_upload" urlDisplayName="Upload custom Java web site" pageTitle="Upload a custom Java web site to Azure" metaKeywords="" description="This tutorial shows you how to upload a custom Java web site to Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Upload a custom Java web site to Azure" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Carga de un sitio web de Java personalizado en Azure
====================================================

En este tema se explica cómo cargar un sitio web de Java personalizado en Azure. Se incluye información que se aplica a cualquier sitio web de Java, así como algunos ejemplos de aplicaciones específicas.

Tenga en cuenta que Azure proporciona un medio para la creación de sitios web de Java con la interfaz de usuario de configuración del portal de Azure y la galería de aplicaciones de Azure, como se documenta en [Introducción a los Sitios web Azure y Java](../web-sites-java-get-started). Este tutorial se destina a situaciones en las que no desea utilizar la interfaz de usuario de configuración de Azure ni la galería de aplicaciones de Azure.

Directrices de configuración
============================

A continuación se describe la configuración esperada para los sitios web de Java personalizados en Azure.

-   El puerto HTTP que utiliza el proceso de Java se asigna de manera dinámica. El proceso debe utilizar el puerto de la variable de entorno `HTTP_PLATFORM_PORT`.
-   Se deben deshabilitar todos los puertos de escucha aparte del agente de escucha simple HTTP. En Tomcat, ello incluye los puertos de apagado, HTTPS y AJP.
-   El contenedor debe estar configurado solo para el tráfico IPv4.
-   En la configuración se debe establecer el comando **startup** para la aplicación.
-   Las aplicaciones que requieren directorios con permiso de escritura tienen que estar ubicadas en el directorio de contenido del sitio web de Azure, que es **D:\\home**.

Puede establecer las variables de entorno como se requiere en el archivo web.config.

Configuración de web.config httpPlatform
========================================

La siguiente información describe el formato **httpPlatform** dentro de web.config.

**arguments** (Default=""). Argumentos para el ejecutable o script especificado en la configuración de **processPath**.

Ejemplos (que se muestran con **processPath** incluido):

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"

    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base="d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115" -jar "d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar""

**processPath** - Ruta de acceso al ejecutable o script que pondrá en marcha un proceso de escucha de las solicitudes HTTP.

Ejemplos:

    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\catalina.bat"

**rapidFailsPerMinute** (Valor predeterminado=10). Número de veces que se permite que el proceso especificado en **processPath** se bloquee por minuto. Si se supera este límite, **HttpPlatformHandler** detendrá el inicio del proceso durante el resto del minuto.

**requestTimeout** (Valor predeterminado="00:02:00".) Tiempo durante el cual **HttpPlatformHandler** esperará una respuesta del proceso de escucha en `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (Valor predeterminado=10). Número de veces que **HttpPlatformHandler** intentará iniciar el proceso especificado en **processPath**. Consulte **startupTimeLimit** para obtener más detalles.

**startupTimeLimit** (Valor predeterminado=10 segundos). Tiempo durante el cual **HttpPlatformHandler** esperará a que el ejecutable/script inicie un proceso que escucha en el puerto. Si se supera este límite de tiempo, **HttpPlatformHandler** cerrará el proceso e intentará volver a iniciarlo **startupRetryCount** veces.

**stdoutLogEnabled** (Valor predeterminado="true".) Si es true, **stdout** y **stderr** para el proceso especificado en la configuración **processPath** serán redirigidos al archivo especificado en **stdoutLogFile** (consulte la sección **stdoutLogFile**).

**stdoutLogFile** (Valor predeterminado="d:\\home\\LogFiles\\httpPlatformStdout.log"). Ruta de acceso absoluta del archivo por el cual se registrarán **stdout** y **stderr** desde el proceso especificado en **processPath**.

> [WACOM.NOTE] `%HTTP_PLATFORM_PORT%` es un marcador de posición especial que se debe especificar como parte de los **argumentos** o como parte de la lista **environmentVariables** de **httpPlatform**. Esto se reemplazará por un puerto generado internamente mediante **HttpPlatformHandler** de modo que el proceso que se especifica en **processPath** puede escuchar en este puerto.

Implementación
==============

Los sitios web basados ??en Java se pueden implementar fácilmente a través de la mayoría de los mismos medios que se utilizan con las aplicaciones web basadas en Internet Information Services (IIS). FTP, Git y Kudu son todas compatibles como mecanismos de implementación, así como la funcionalidad de SCM integrada para los sitios web. WebDeploy funciona como un protocolo; sin embargo, debido a que Java no está desarrollado en Visual Studio, WebDeploy no se adapta a los casos de uso de la implementación de un sitio web de Java.

Ejemplos de configuración de aplicaciones
=========================================

Para las siguientes aplicaciones, se proporciona un archivo web.config y la configuración de la aplicación como ejemplos para mostrar cómo habilitar la aplicación Java en sitios web de Azure.

Tomcat
------

Si bien hay dos variaciones en Tomcat que se suministran con Sitios web Azure, todavía es posible cargar las instancias específicas de los clientes. A continuación se muestra un ejemplo de una instalación de Tomcat con una JVM diferente.

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="d:\home\site\wwwroot\bin\tomcat\bin\startup.bat" 
            arguments="">
          <environmentVariables>
            <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
            <environmentVariable name="CATALINA_HOME" value="d:\home\site\wwwroot\bin\tomcat" />
            <environmentVariable name="JRE_HOME" value="d:\home\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

Por el lado de Tomcat, es necesario realizar algunos cambios en la configuración. Se debe editar server.xml para establecer:

-   Puerto de apagado = -1
-   Puerto del conector HTTP = {port.http}
-   Dirección del conector HTTP = "127.0.0.1"
-   Convierta en comentarios los conectores HTTPS y AJP
-   La configuración de IPv4 también se puede configurar en el archivo catalina.properties donde se puede agregar `java.net.preferIPv4Stack = true`

Las llamadas Direct3D no se admiten en los sitios web de Azure. Para deshabilitarlas, agregue la siguiente opción de Java si la aplicación realiza llamadas de ese tipo: `-Dsun.java2d.d3d=false`

Jetty
-----

Como es el caso de Tomcat, los clientes pueden cargar sus propias instancias de Jetty. En el caso de ejecutar la instalación completa de Jetty, la configuración sería similar a la siguiente:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
             arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base="d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115" -jar "d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar""
            startupTimeLimit="20"
          startupRetryCount="10"
          stdoutLogEnabled="true">
        </httpPlatform>
      </system.webServer>
    </configuration>

La configuración de Jetty necesita cambiarse en el archivo start.ini para establecer `java.net.preferIPv4Stack=true`.

Hudson
------

Nuestra prueba utilizó war de Hudson 3.1.2 y la instancia predeterminada de Tomcat 7.0.50, pero sin utilizar la interfaz de usuario para realizar la configuración. Debido a que Hudson es una herramienta de compilación de software, se recomienda instalarla en instancias dedicadas donde la marca **AlwaysOn** se pueda configurar en el sitio.

1.  En la raíz del sitio web de Azure, es decir, **d:\\home\\site\\wwwroot**, cree un directorio de **webapps** (si no está ya presente) y coloque Hudson.war en **d:\\home\\site\\wwwroot\\webapps**.
2.  Descargue apache maven 3.0.5 (compatible con Hudson) y colóquelo en **d:\\home\\site\\wwwroot**.
3.  Cree web.config en **d:\\home\\site\\wwwroot** y pegue el siguiente contenido en él:

         <?xml version="1.0" encoding="UTF-8"?>
         <configuration>
           <system.webServer>
             <handlers>
               <add name="httppPlatformHandler" path="*" verb="*" 
         modules="httpPlatformHandler" resourceType="Unspecified" />
             </handlers>
             <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
         startupTimeLimit="20"
         startupRetryCount="10">
         <environmentVariables>
           <environmentVariable name="HUDSON_HOME" 
         value="d:\home\site\wwwroot\hudson_home" />
           <environmentVariable name="JAVA_OPTS" 
         value="-Djava.net.preferIPv4Stack=true -Duser.home=d:/home/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
         </environmentVariables>            
             </httpPlatform>
           </system.webServer>
         </configuration>

    En este punto, se puede reiniciar el sitio web para que los cambios surtan efecto. Conéctese con http://yoursite/hudson para iniciar Hudson.

4.  Cuando Hudson se haya configurado, debería ver la siguiente pantalla:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)

5.  Obtenga acceso a la página de configuración de Hudson: Haga clic en **Manage Hudson** y, a continuación, haga clic en **Configure System**.
6.  Configure el JDK como se muestra a continuación:

    ![Configuración de Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7.  Configure Maven como se muestra a continuación:

    ![Configuración de Maven](./media/web-sites-java-custom-upload/maven.png)

8.  Guarde la configuración Hudson debe estar ahora configurado y listo para su uso.

Para obtener información adicional sobre Hudson, consulte <http://hudson-ci.org>.

Liferay
-------

Liferay es compatible con los sitios web de Azure. Debido a que Liferay puede requerir una importante cantidad de memoria, el sitio necesita ejecutarse en un trabajo dedicado mediano o grande, que puede proporcionar suficiente memoria. Liferay también tarda varios minutos en iniciarse. Por esa razón, se recomienda que configure el sitio en **Always On**.

Con Liferay 6.1.2 Community Edition GA3 incluido con Tomcat, se editaron los siguientes archivos después de descargar Liferay:

**Server.xml**

-   Cambie el puerto de apagado a -1.
-   Cambie el conector HTTP a `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
-   Comente el conector AJP.

En la carpeta **liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes**, cree un archivo llamado **portal-ext.properties**. Este archivo debe contener una línea, como se muestra aquí:

    liferay.home=d:/home/site/wwwroot/liferay

En el mismo nivel de directorio que la carpeta tomcat-7.0.40, cree un archivo llamado **web.config** con el siguiente contenido:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
    <add name="httpPlatformHandler" path="*" verb="*"
         modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="d:\home\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
                      arguments="run" 
                      startupTimeLimit="10" 
                      requestTimeout="00:10:00" 
                      stdoutLogEnabled="true">
          <environmentVariables>
      <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
      <environmentVariable name="CATALINA_HOME" value="d:\home\site\wwwroot\tomcat-7.0.40" />
            <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

En el bloque **httpPlatform**, **requestTimeout** se establece en "00:10:00". Se puede reducir, pero entonces es probable se vean algunos errores de tiempo de espera mientras Liferay arranca. Si se cambia este valor, se debe modificar también **connectionTimeout** en el server.xml de tomcat.

Vale la pena señalar que la variable de entorno JRE\_HOME se especifica en web.config anterior para apuntar a la JDK de 64 bits. El valor predeterminado es 32 bits, pero debido a que Liferay puede requerir altos niveles de memoria, se recomienda utilizar el JDK de 64 bits.

Después de que realice estos cambios, reinicie el sitio web que ejecuta Liferay y, a continuación, abra http://yoursite. El portal de Liferay está disponible en la raíz del sitio web.

Para obtener más información sobre Liferay, visite <http://www.liferay.com>.

