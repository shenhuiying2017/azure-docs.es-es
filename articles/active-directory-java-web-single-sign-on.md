<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin" />

# Inicio de sesión web único con Java y Azure Active Directory

## <a name="introduction"></a>Introducción

Este tutorial mostrará a los desarrolladores de Java cómo utilizar Azure Active Directory para permitir el inicio de sesión único para usuarios de los clientes de Office 365. Aprenderá a:

-   Aprovisionar la aplicación web en el inquilino de un cliente
-   Proteger la aplicación con WS-Federation

### Requisitos previos

Este tutorial utiliza un servidor de aplicaciones específico, pero si tiene experiencia como desarrollador de Java, el proceso que se describe a continuación también se puede aplicar a otros entornos. En este tutorial se necesitan los siguientes requisitos previos para el entorno de desarrollo:

-   [Código de ejemplo de Java para Azure Active Directory][Código de ejemplo de Java para Azure Active Directory]
-   [Java Runtime Environment 1.6][Java Runtime Environment 1.6]
-   [JBoss Application Server versión 7.1.1.Final][JBoss Application Server versión 7.1.1.Final]
-   [IDE de JBoss Developer Studio][IDE de JBoss Developer Studio]
-   Internet Information Services (IIS) 7.5 con SSL habilitado
-   Windows PowerShell
-   [Instalar y configurar Windows PowerShell para el inicio de sesión único][Instalar y configurar Windows PowerShell para el inicio de sesión único]

### Tabla de contenido

-   [Introducción][Introducción]
-   [Paso 1: Crear una aplicación de Java][Paso 1: Crear una aplicación de Java]
-   [Paso 2: Aprovisionar la aplicación en el inquilino de Active Directory de la empresa][Paso 2: Aprovisionar la aplicación en el inquilino de Active Directory de la empresa]
-   [Paso 3: Proteger la aplicación con WS-Federation para el inicio de sesión de empleado][Paso 3: Proteger la aplicación con WS-Federation para el inicio de sesión de empleado]
-   [Resumen][Resumen]

## <a name="createapp"></a>Paso 1: Creación de una aplicación Java

Este paso describe cómo crear una aplicación Java simple que representará un recurso protegido. El acceso a este recurso se otorgará a través de la autenticación federada administrada por el STS de la empresa, que se describe más adelante en este tutorial.

1.  Abra una nueva instancia de JBoss Developer Studio.
2.  En el menú **File**, haga clic en **New** y, a continuación, en **Project...**.
3.  En el cuadro de diálogo **New Project**, expanda la carpeta **Maven**, haga clic en **Maven Project** y, a continuación, en **Next**.
4.  En el cuadro de diálogo **New Maven Project**, active **Create a simple project (skip archetype selection)** y, a continuación, haga clic en **Next**.
5.  En la página siguiente del cuadro de diálogo **New Maven Project**, escriba *sample* en los cuadros de texto **Group Id** y **Artifact Id**. A continuación, seleccione el valor **war** en el menú desplegable **Packaging** y, a continuación, haga clic en **Finish**.
6.  Se creará el proyecto Maven. En el menú **Explorador de proyectos** de la izquierda, expanda el proyecto **sample**, haga clic con el botón secundario en el archivo **pom.xml** y, a continuación, haga clic en **Open with** y luego en **Editor de texto**.
7.  En el archivo **pom.xml**, agregue el siguiente XML dentro de la sección *\<project\>*:

        <repositories>
            <repository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.0.2</version>
                    <configuration>
                        <source>1.6</source>
                        <target>1.6</target>
                    </configuration>
                </plugin>
            </plugins>
        </build> 

    Una vez que haya especificado este XML, guarde el archivo **pom.xml**.

8.  Haga clic con el botón secundario en el proyecto **sample** y, a continuación, haga clic en **Maven** y luego en **Update Maven Project**. En el cuadro de diálogo **Update Maven Project**, haga clic en **OK**. Este paso actualizará el proyecto con los cambios en **pom.xml**.

9.  Haga clic con el botón secundario en el proyecto **sample** y, a continuación, haga clic en **New** y luego en **JSP File**.

10. En el cuadro de diálogo **New JSP File**, cambie la ruta de acceso del archivo nuevo por *sample/src/main/webapp*. Luego, asigne al archivo el nombre **index.jsp** y haga clic en **Finish**.

11. El nuevo archivo **index.jsp** se abrirá automáticamente. Reemplace el código generado automáticamente por el siguiente código y luego guarde el archivo:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. Haga clic con el botón secundario en el proyecto **sample** y, a continuación, haga clic en **Run As** y luego en **Run on Server**.

13. En el cuadro de diálogo **Run On Server**, asegúrese de que esté activada la opción **JBoss Enterprise Application Platform 6.x** y, a continuación, seleccione **Finished**.

## <a name="provisionapp"></a>Paso 2: Aprovisionar la aplicación en el inquilino de Active Directory de la empresa

Este paso describe cómo un administrador de un cliente de Azure Active Directory aprovisiona la aplicación Java en su inquilino y configura el inicio de sesión único. Una vez que se completa este paso, los empleados de la empresa se pueden autenticar en la aplicación web mediante sus cuentas de Office 365.

El proceso de aprovisionamiento comienza con la creación de una nueva entidad de servicio para la aplicación. Azure Active Directory utiliza las entidades de servicio para registrar y autenticar las aplicaciones en el directorio.

1.  Si todavía no lo ha hecho, descargue e instale los commandlets de PowerShell para Office 365.
2.  En el menú **Inicio**, ejecute la consola **Módulo Microsoft Online Services para Windows PowerShell**. Esta consola proporciona un entorno de línea de comandos para configurar atributos acerca de su inquilino de Office 365, tales como la creación y la modificación de entidades de servicio.
3.  Para importar el módulo **MSOnlineExtended** requerido, escriba el siguiente comando y presione Entrar:

        Import-Module MSOnlineExtended -Force

4.  Para conectarse a su directorio de Office 365, necesitará proporcionar las credenciales de administrador de la empresa. Escriba el siguiente comando y presione Entrar y, a continuación, escriba las credenciales en el símbolo del sistema:

        Connect-MsolService

5.  Ahora creará una nueva entidad de servicio para la aplicación. Escriba el siguiente comando y presione Entrar:

        New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Este paso dará como resultado información similar a la siguiente:

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Java Website
        ServicePrincipalNames : {javaSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    > [WACOM.NOTE]
    > Debe guardar esta salida, en especial la clave simétrica generada. La clave solo se revelará durante la creación de la entidad de servicio y no podrá recuperarla en el futuro. Los otros valores son necesarios para utilizar la API Graph para leer y escribir información en el directorio.

6.  El paso final define la URL de respuesta para su aplicación. La URL de respuesta es donde se envían las respuestas después de los intentos de autenticación. Escriba los siguientes comandos y presione Entrar:

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

La aplicación web ahora se aprovisionó en el directorio y los empleados de la empresa la pueden utilizar para el inicio de sesión web único.

## <a name="protectapp"></a>Paso 3: Proteger la aplicación con WS-Federation para el inicio de sesión de empleado

Este paso le mostrará cómo agregar compatibilidad para el inicio de sesión federado con Windows Identity Foundation (WIF) y la biblioteca **waad-federation** que descargó como parte del paquete de código de ejemplo en los requisitos previos. También agregará una página de inicio de sesión y configurará la confianza entre la aplicación y el inquilino de directorio.

1.  En JBoss Developer Studio, haga clic en **File** y luego en **Import**.

2.  En el cuadro de diálogo **Import**, expanda la carpeta **Maven**, haga clic en **Existing Maven Projects** y, a continuación, en **Next**.

3.  En el cuadro de diálogo **Import Maven Projects**, defina la ruta de acceso del **directorio raíz** donde descargó la biblioteca **waad-federation** en el código de ejemplo. A continuación, active la casilla junto al archivo **pom.xml** del proyecto **waad-federation** y haga clic en **Finish**.

4.  Expanda el proyecto **sample**, haga clic con el botón secundario en el archivo **pom.xml**, haga clic en **Abrir con** y, a continuación, en **Text Editor**.

5.  En el archivo **pom.xml**, agregue el siguiente XML dentro de la sección *\<project\>* y luego guarde el archivo:

        <dependencies>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>servlet-api</artifactId>
                <version>3.0-alpha-1</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.samples.waad.federation</groupId>
                <artifactId>waad-federation</artifactId>
                <version>0.0.1-SNAPSHOT</version>
            </dependency>
        </dependencies> 

6.  Haga clic con el botón secundario en el proyecto **sample** y, a continuación, haga clic en **Maven** y luego en **Update Project**. En el cuadro de diálogo **Update Maven Project**, haga clic en **OK**. Este paso actualizará el proyecto con los cambios en **pom.xml**.

7.  Haga clic con el botón secundario en el proyecto **sample** y, a continuación, haga clic en **New** y luego en **Filter**.

8.  En el cuadro de diálogo **Create Filter**, escriba *FederationFilter* para la entrada **Class name** y, a continuación, haga clic en **Finish**.

9.  Se abrirá el archivo **FederationFilter.java** que se creó automáticamente. Reemplace su código por el siguiente y guarde el archivo:

        import java.io.IOException;
        import javax.servlet.Filter;
        import javax.servlet.FilterChain;
        import javax.servlet.FilterConfig;
        import javax.servlet.ServletException;
        import javax.servlet.ServletRequest;
        import javax.servlet.ServletResponse;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import java.util.regex.*;
        import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

        public class FederationFilter implements Filter {
            private String loginPage;
            private String allowedRegex;
            public void init(FilterConfig config) throws ServletException {
                this.loginPage = config.getInitParameter("login-page-url");
                this.allowedRegex = config.getInitParameter("allowed-regex");
            }
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                    if (request instanceof HttpServletRequest) { 
                        HttpServletRequest httpRequest = (HttpServletRequest) request;
                        if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                            FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                            boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                            if (!allowedUrl && !loginManager.isAuthenticated()) {
                                HttpServletResponse httpResponse = (HttpServletResponse) response;
                                String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                httpResponse.setHeader("Location", this.loginPage + "?returnUrl=" + encodedReturnUrl);
                                httpResponse.setStatus(302);
                                return;
                            }
                        }
                    }
                chain.doFilter(request, response);
            }
            public void destroy() {
            }
        } 

10. En el **Explorador de proyectos**, expanda las carpetas **src**, **main** y luego **webapp**. Haga clic con el botón secundario en el archivo **web.xml** y luego haga clic en **Open With** y **Text Editor**.

11. En el archivo **web.xml**, agregará un filtro para gestionar páginas protegidas y no protegidas y también se redirigirá a los usuarios no autenticados a la página de inicio de sesión (especificada según el parámetro de filtro **login-page-url**). Sin embargo, no se filtrará una URL si coincide con el regex especificado en el parámetro de filtro **allowed-regex**. Agregue el siguiente XML dentro de la sección *\<web-app\>* y, a continuación, guarde el archivo **web.xml**.

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. Para crear una página de inicio de sesión, haga clic con el botón secundario en el proyecto **sample** y, a continuación, haga clic en **New** y en **JSP File**.

13. En el cuadro de diálogo **New JSP File**, cambie la ruta de acceso del archivo nuevo por *sample/src/main/webapp*. Luego, asigne al archivo el nombre **login.jsp** y haga clic en **Finish**.

14. El nuevo archivo **login.jsp** se abrirá automáticamente. Reemplace el código generado automáticamente por el siguiente código y luego guarde el archivo:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. En el **Explorador de proyectos**, expanda la carpeta **/src/main** del proyecto **sample**. Haga clic con el botón secundario en la carpeta **resources** y, a continuación, haga clic en **New** y luego en **Other**.

16. En el cuadro de diálogo **New**, expanda la carpeta **JBoss Tools Web**, haga clic en **Properties File** y, a continuación, en **Next**.

17. En el cuadro de diálogo **New File Properties**, asigne al archivo el nombre **federation** y haga clic en **Finish**.

18. En el **Explorador de proyectos**, expanda la carpeta **src/main/resources** del proyecto **sample**. Haga clic con el botón secundario en el archivo **federation.properties**, haga clic en **Open With** y **Text Editor**.

19. En el archivo **federation.properties**, incluya las siguientes entradas de configuración y luego guarde el archivo:

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE]
    > Los valores **audienceuris** y **realm** deben ir precedidos por "spn:".

20. Ahora debe crear un servlet. Haga clic con el botón secundario en el proyecto **sample** y, a continuación, haga clic en **New**, **Other** y, finalmente, en **Servlet**.

21. En el cuadro de diálogo **Create Servlet**, proporcione un **nombre de clase** de *FederationServlet* y haga clic en **Finish**.

22. El archivo **FederationServlet.java** se carga automáticamente. Reemplace su contenido por el siguiente código y guarde el archivo:

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;

        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. En el **Explorador de proyectos**, expanda la carpeta **src/main/webapp/WEB-INF**. Haga clic con el botón secundario en el archivo **web.xml** y luego haga clic en **Open With** y **Text Editor**.

24. En el archivo **web.xml**, reemplace la configuración **/FederationServlet** de la sección *\<url-pattern\>* por **/ws-saml**. Por ejemplo:

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. En el **Explorador de proyectos**, expanda la carpeta **src/main/webapp**. Haga clic con el botón secundario en el archivo **index.jsp** y luego haga clic en **Open With** y **Text Editor**.

26. En el archivo **index.jsp**, reemplace el código existente por el siguiente y, a continuación, guarde el archivo:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. En el **Explorador de proyectos**, expanda la carpeta **src/main/webapp/WEB-INF**. Haga clic con el botón secundario en el archivo **web.xml** y luego haga clic en **Open With** y **Text Editor**.

28. Ahora habilitaremos SSL para la aplicación. En el archivo **web.xml**, inserte la siguiente sección *\<security-constraint\>* dentro de la sección *\<web-app\>* y, a continuación, guarde el archivo:

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE]
    > Antes de continuar, asegúrese de que JBoss Server ya esté configurado para admitir SSL.

29. Ahora estamos preparados para ejecutar la aplicación de un extremo a otro. Haga clic con el botón secundario en el proyecto **sample** y, a continuación, haga clic en **Run As** y luego en **Run On Server**. Acepte los valores que especificó antes y haga clic en **Finish**.

30. El explorador de JBoss abrirá la página de inicio de sesión. Si hace clic en el vínculo **Awesome Computers**, se le redirigirá a la página del proveedor de identidades de Office 365, donde puede iniciar sesión con sus credenciales de inquilino de directorio. Por ejemplo, *john.doe@fabrikam.onmicrosoft.com*.

31. Una vez que haya iniciado sesión, se le volverá a redirigir a la página protegida (**sample/index.jsp**) como usuario autenticado.

## <a name="summary"></a>Resumen

Este tutorial le ha mostrado cómo crear y configurar una aplicación Java de un solo inquilino que utiliza las capacidades de inicio de sesión único de Azure Active Directory.

  [Código de ejemplo de Java para Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA
  [Java Runtime Environment 1.6]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [JBoss Application Server versión 7.1.1.Final]: http://www.jboss.org/jbossas/downloads/
  [IDE de JBoss Developer Studio]: https://devstudio.jboss.com/earlyaccess/
  [Instalar y configurar Windows PowerShell para el inicio de sesión único]: http://onlinehelp.microsoft.com/es-es/office365-enterprises/ff652560.aspx
  [Introducción]: #introduction
  [Paso 1: Crear una aplicación de Java]: #createapp
  [Paso 2: Aprovisionar la aplicación en el inquilino de Active Directory de la empresa]: #provisionapp
  [Paso 3: Proteger la aplicación con WS-Federation para el inicio de sesión de empleado]: #protectapp
  [Resumen]: #summary
