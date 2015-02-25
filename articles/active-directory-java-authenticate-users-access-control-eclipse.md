<properties urlDisplayName="Access Control" pageTitle="Uso de control de acceso (Java): guía de características de Azure" metaKeywords="" description="Obtenga información acerca de cómo desarrollar y usar el Control de acceso con Java en Azure." metaCanonical="" services="active-directory" documentationCenter="Java" title="How to Authenticate Web Users with Azure Access Control Service Using Eclipse" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse

Esta guía le mostrará cómo utilizar el servicio de control de acceso de Azure (ACS) dentro del complemento de Azure para Eclipse con Java (de Microsoft Open Technologies). Para obtener más información sobre ACS, consulte la sección [Pasos siguientes](#next_steps).

> [WACOM.NOTE]
> El filtro de control de los servicios de acceso de Azure (de Microsoft Open Technologies) es una Community Technology Preview. Como versión preliminar de software, no cuenta formalmente con el respaldo de Microsoft Open Technologies, Inc. ni de Microsoft.

## Tabla de contenido

-   [¿Qué es ACS?][]
-   [Conceptos][]
-   [Requisitos previos][]
-   [Creación de un espacio de nombres de ACS][]
-   [Incorporación de proveedores de identidades][]
-   [Incorporación de una aplicación de usuario de confianza][]
-   [Creación de reglas][]
-   [Carga de un certificado en su espacio de nombres de ACS][]
-   [Revisión de la página de integración de aplicaciones][]
-   [Creación de una aplicación web de Java][]
-   [Incorporación de la biblioteca de filtros de ACS a su aplicación][]
-   [Implementación del emulador de proceso][]
-   [Implementación en Azure][]
-   [Pasos siguientes][]

## <a name="what-is"></a>¿Qué es ACS?

La mayoría de los desarrolladores no son expertos en identidad y generalmente no querrán
dedicar tiempo a desarrollar los mecanismos de autenticación y autorización necesarios para
sus aplicaciones y servicios. ACS es un servicio de Azure que
proporciona una manera sencilla de autenticar a los usuarios que necesitan tener acceso a las aplicaciones y los servicios web
sin tener que incluir una compleja
lógica de autenticación en el código.

En ACS están disponibles las características siguientes:

-   Integración con Windows Identity Foundation (WIF).
-   Compatibilidad con los proveedores de identidades (IP) habituales, entre los que se incluyen Windows Live ID, Google, Yahoo! y Facebook.
-   Compatibilidad con los servicios de federación de Active Directory (AD FS) 2.0.
-   Un servicio de administración basado en un protocolo de datos abierto (OData) que proporciona acceso programático a la configuración de ACS.
-   Un portal de administración que concede acceso administrativo a la configuración de ACS.

Para obtener más información acerca de ACS, consulte [Servicio de control de acceso 2.0][].

## <a name="concepts"></a>Conceptos

ACS de Azure se basa en los principios de identidad basada en notificaciones:
un enfoque coherente para crear mecanismos de autenticación para
las aplicaciones que se ejecutan de forma local o en la nube. La identidad basada en notificaciones
proporciona a las aplicaciones y los servicios un método común para la adquisición de
la información de identidad que requieren acerca de los usuarios de la organización, de
otras organizaciones y de Internet.

Para completar las tareas de esta guía, deberá comprender los siguientes
conceptos:

**Cliente**: en el contexto de esta guía de procedimientos, se trata de un explorador que
está intentando obtener acceso a la aplicación web.

**Aplicación de usuario de confianza**: una aplicación de usuario de confianza es un sitio web o
servicio que externaliza la autenticación a una autoridad externa. En
la jerga de la identidad, se dice que los usuarios de confianza confían en dicha autoridad. En esta guía
se explica cómo configurar su aplicación para que confíe en ACS.

**Token**:un token es una colección de datos de seguridad que se suele
emitir tras la correcta autenticación de un usuario. Contiene un conjunto de *notificaciones*, atributos del usuario autenticado. Una notificación puede representar el
nombre de un usuario, un identificador para un rol al que pertenece un usuario, la edad de un usuario,
etc. Un token normalmente presenta una firma digital, lo que significa que siempre
se puede rastrear su emisor y que su contenido no se puede alterar
. Un usuario obtiene acceso a una aplicación de usuario de confianza presentando un token
válido emitido por una entidad en la que confía la aplicación de usuario de confianza.

**Proveedor de identidades (IP)**: un IP es una autoridad que autentica
identidades de usuario y emite tokens de seguridad. En realidad, el trabajo de emisión
de tokens se implementa a través de un servicio especial llamado servicio de token de seguridad
(STS). Entre los ejemplos típicos de IP se incluyen Windows Live ID,
Facebook, repositorios de usuario de negocio (como Active Directory), etc.
Cuando ACS esté configurado para confiar en un IP, el sistema aceptará y
validará tokens emitidos por dicho IP. ACS puede confiar en varios IP a la vez,
lo que significa que cuando su aplicación confía en ACS, puede
ofrecer instantáneamente la aplicación a todos los usuarios autenticados de todos los IP
en los que ACS confía en su nombre.

**Proveedor de federación (FP)**: los IP tienen conocimiento directo de los usuarios,
y los autentican usando sus credenciales y emiten notificaciones acerca de lo que
saben de ellos. Un proveedor de federación (FP) es un tipo de autoridad
diferente: en lugar de autenticar a los usuarios directamente, actúa como un
intermediario y negocia la autenticación entre un RP y uno o varios
IP. Tanto los IP como los FP emiten tokens de seguridad, por lo tanto, ambos usan
servicios de token de seguridad (STS). ACS es un FP.

**Motor de reglas de ACS**: la lógica utilizada para transformar los tokens entrantes de
IP de confianza a tokens que pueda consumir el RP se codifica en forma
de reglas de transformación de notificaciones sencillas. ACS presenta una regla de motor que
se ocupa de aplicar la lógica de transformación que especificó para
su RP.

**Espacio de nombres de ACS**: un espacio de nombres es una partición de nivel superior de ACS que
se utiliza para organizar su configuración. Un espacio de nombres contiene una lista de IP
de confianza, las aplicaciones de RP que desea atender, las reglas que espera
que utilice el motor de reglas para procesar los tokens entrantes, etc. Un espacio de nombres
expone varios extremos que se utilizarán por la aplicación y el
desarrollador para conseguir que ACS realice su función.

La siguiente ilustración muestra cómo funciona la autenticación de ACS con una aplicación
web:

![ACS flow diagram][acs_flow]

1.  El cliente (en este caso, un explorador) solicita una página del RP.
2.  Como la solicitud no se ha autenticado todavía, el RP redirige al usuario a la autoridad en la que confía, es decir, ACS. El ACS presenta al usuario la selección de IP especificados para este RP. El usuario selecciona el IP adecuado.
3.  El cliente examina la página de autenticación del IP y pide al usuario que inicie sesión.
4.  Cuando el cliente se haya autenticado (por ejemplo, cuando haya escrito las credenciales de identidad), el IP emitirá un token de seguridad.
5.  Después de haber emitido un token de seguridad, el IP redirige el cliente a ACS y el cliente envía a ACS el token de seguridad que emite el IP.
6.  ACS valida el token de seguridad emitido por el IP, introduce las notificaciones de identidad de este token en el motor de reglas de ACS, calcula las notificaciones de identidad resultantes y emite un nuevo token de seguridad que contenga estas notificaciones.
7.  ACS redirige el cliente al RP. El cliente envía el nuevo token de seguridad emitido por ACS al RP. El RP valida la firma del token de seguridad emitido por ACS, valida las notificaciones en este token y devuelve la página que solicitó originalmente.

## <a name="pre"></a>Requisitos previos

Necesitará lo siguiente para completar las tareas de esta guía:

- Un kit para desarrolladores de Java (JDK) v1.6 o superior.
- IDE de Eclipse para desarrolladores de Java EE, Indigo o superior. Esto se puede descargar en <http://www.eclipse.org/downloads/>. 
- Una distribución de un servidor de aplicaciones o servidor web basado en Java, como Apache Tomcat, GlassFish, JBoss Application Server o Jetty.
- Una suscripción de Azure, que se puede adquirir en <http://www.microsoft.com/windowsazure/offers/>.
- Complemento de Azure para Eclipse con Java (de Microsoft Open Technologies), versión de abril de 2014. Para obtener más información, consulte [Instalación del complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)](http://msdn.microsoft.com/es-es/library/windowsazure/hh690946.aspx).
- Un certificado X.509 para utilizar con la aplicación. Necesitará este certificado en formato de certificado público (.cer) e intercambio de información personal (.PFX). (Las opciones para crear este certificado se describirán más adelante en este tutorial).
- Estar familiarizado con el emulador de proceso de Azure y las técnicas de implementación analizadas en [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/es-es/library/windowsazure/hh690944.aspx).

## <a name="create-namespace"></a>Creación de un espacio de nombres de ACS

Para empezar a utilizar el servicio de control de acceso (ACS) de Azure, debe
crear un espacio de nombres de ACS. El espacio de nombres proporciona un ámbito único para
tratar los recursos de ACS desde dentro de la aplicación.

1. Inicie sesión en el [Portal de administración de Azure][].
2. Haga clic en **Active Directory**. 
3. Para crear un nuevo espacio de nombres de control de acceso, haga clic en **Nuevo**, **Servicios de aplicaciones**, **Control de acceso** y, a continuación, en **Creación rápida**. 
4. Escriba un nombre para el espacio de nombres. Azure verificará si el nombre es único.
5. Seleccione la región en la que se usa el espacio de nombres. Para obtener el máximo rendimiento, utilice la región en la que va a implementar su aplicación.
6. Si tiene varias suscripciones, seleccione la que desea utilizar para el espacio de nombres de ACS.
7. Haga clic en **Crear**.

Azure crea y activa el espacio de nombres. Espere hasta que el estado del nuevo espacio de nombres sea **Activo** antes de continuar. 

## <a name="add-IP"></a>Incorporación de proveedores de identidades

En esta tarea, agregará IP para utilizarlos con su aplicación RP para
la autenticación. Para fines de demostración, esta tarea muestra cómo agregar
Windows Live como IP, pero podría utilizarse cualquiera de los IP que aparecen en el
Portal de administración de ACS.


1.  En el [Portal de administración de Azure][], haga clic en **Active Directory**, seleccione un espacio de nombres de control de acceso y, a continuación, haga clic en **Administrar**. Se abre el portal de administración de ACS.
2.  En el panel de navegación izquierdo del portal de administración de ACS, haga clic en **Proveedores de identidades**.
3.  Windows Live ID está habilitado de manera predeterminada y no se puede eliminar. Para los fines de este tutorial, solo se utiliza Windows Live ID. Sin embargo, en esta pantalla podría agregar otros IP haciendo clic en el botón **Agregar**.

Windows Live ID ahora está habilitado como un IP para su espacio de nombres de ACS. A continuación, especifique
la aplicación web de Java (que se va a crear más adelante) como un RP.

## <a name="add-RP"></a>Incorporación de una aplicación de usuario de confianza

En esta tarea, configurará ACS para reconocer su aplicación web de Java
como una aplicación RP válida.

1.  En el portal de administración de ACS, haga clic en **Aplicaciones de usuario de confianza**.
2.  En la página **Aplicaciones de usuario de confianza**, haga clic en **Agregar**.
3.  En la página **Agregar aplicación de usuario de confianza**, haga lo siguiente:
    1.  En **Nombre**, escriba el nombre del RP. Para los fines de este tutorial, escriba **Aplicación web
        de Azure**.
    2.  En **Modo**, seleccione **Escribir la configuración manualmente.**.
    3.  En **Dominio kerberos**, escriba el URI al que se aplica el token de seguridad que emitió ACS. Para esta tarea, escriba **http://localhost:8080/**.
        
        ![Relying party realm for use in compute emulator][relying_party_realm_emulator]
    4.  En **Dirección URL de retorno**, escriba la URL a la cual ACS devuelve el token de seguridad. Para esta tarea, escriba **http://localhost:8080/MyACSHelloWorld/index.jsp**
        
        ![Relying party return URL for use in compute emulator][relying_party_return_url_emulator]
    5.  Acepte los valores predeterminados en el resto de los campos.

4.  Haga clic en **Guardar**.

Se ha configurado correctamente la aplicación web de Java cuando se ejecuta en el emulador de proceso de Azure (en
http://localhost: 8080 /) para que sea un RP en su espacio de nombres de ACS. A continuación, cree
las reglas que ACS utiliza para procesar notificaciones para el RP.

## <a name="create-rules"></a>Creación de reglas

En esta tarea, definirá las reglas que controlan cómo se pasan notificaciones
desde los IP a los RP. Para los fines de esta guía, simplemente configuraremos
ACS para que copie los tipos y valores de notificaciones de entrada directamente en el token
de salida, sin filtrarlos o modificarlos.

1.  En la página principal del portal de administración de ACS, haga clic en **Grupos de reglas**.
2.  En la página **Grupos de reglas**, haga clic en **Grupo de reglas predeterminado para aplicación web de Azure**.
3.  En la página **Editar grupo de reglas**, haga clic en **Generar**.
4.  En la página **Generar reglas: Grupo de reglas predeterminado para Azure Web App**, asegúrese de que la opción Windows Lide ID esté activada y, a continuación, haga clic en **Generar**.	.
5.  En la página **Editar grupo de reglas**, haga clic en **Guardar**.

## <a name="upload-certificate"></a>Carga de un certificado en su espacio de nombres de ACS

En esta tarea, cargará un certificado .PFX que se utilizará para firmar solicitudes de token creadas por su espacio de nombres de ACS.

1.  En la página principal del portal de administración de ACS, haga clic en **Certificados y claves**.
2.  En la página **Certificados y claves**, haga clic en **Agregar** encima de **Firma de tokens**.
3.  En la página **Agregar certificado o clave de firma de tokens**:
    1. En la sección**Utilizado para**, haga clic en **Aplicación de usuario de confianza** y seleccione **Aplicación web de Azure** (que anteriormente definió como el nombre de su aplicación de usuario de confianza).
    2. En la sección **Tipo**, seleccione **Certificado X.509**.
    3. En la sección **Certificado**, haga clic en el botón de examinar y navegue al archivo del certificado X.509 que desea utilizar. Este será un archivo .PFX. Seleccione el archivo, haga clic en **Abrir**   y, a continuación, escriba la contraseña del certificado en el cuadro de texto **Contraseña**. Observe que, para fines de prueba, puede utilizar un certificado autofirmado. Para crear un certificado autofirmado, utilice el botón **Nuevo** que aparece en el cuadro de diálogo **Biblioteca de filtros de ACS** (que se describe más adelante); o bien, use la herramienta **encutil.exe** desde el [sitio web del proyecto][] del kit de inicio de Azure para Java (de Microsoft Open Technologies).
    4. Asegúrese de que la opción **Hacer principal** esté activada. La página **Agregar certificado o clave de firma de tokens** debería ser similar a la siguiente.

        ![Add token-signing certificate][add_token_signing_cert]
    5. Haga clic en **Guardar** para guardar la configuración y cierre la página **Agregar certificado o clave de firma de tokens**.

A continuación, revise la información de la página de integración de aplicaciones y
copie el URI que necesitará para configurar su aplicación web de Java
para utilizar ACS.

## <a name="review-app-int"></a>Revisión de la página de integración de aplicaciones

Puede encontrar toda la información y el código necesario para configurar
la aplicación web de Java (la aplicación de RP) para que funcione con ACS en
la página de integración de aplicaciones del portal de administración de ACS. Necesitará
esta información al configurar la aplicación web de Java para
la autenticación federada.

1.  En el portal de administración de ACS, haga clic en **Integración de aplicaciones**.  
2.  En la página **Integración de aplicaciones**, haga clic en **Páginas de inicio de sesión**.
3.  En la página **Integración de página de inicio de sesión**, haga clic en **Aplicación web de Azure**.

En la página **Integración de página de inicio de sesión: Azure Web App**, la dirección URL que aparece en **Option 1: Link to an ACS-hosted login page** se utilizará en su aplicación web de Java. Necesitará este valor cuando agregue la biblioteca de filtros de servicios de control de acceso de Azure a su aplicación de Java.

## <a name="create-java-app"></a>Creación de una aplicación web de Java
1. En el menú de Eclipse, haga clic en **Archivo**, **Nuevo** y, a continuación, en **Proyecto web dinámico**. (Si no ve **Proyecto web dinámico** como proyecto disponible después de hacer clic en **Archivo**, **Nuevo**, haga lo siguiente: haga clic en **Archivo**, haga clic en **Nuevo**, haga clic en **Proyecto**, expanda **Web**, haga clic en **Proyecto web dinámico** y, a continuación, haga clic en **Siguiente**.) Para los fines de este tutorial, asigne el nombre **MyACSHelloWorld** al proyecto. (Asegúrese de utilizar este nombre; los pasos posteriores de este historial esperan que su archivo WAR tenga el nombre MyACSHelloWorld). La pantalla se parecerá a la siguiente:

    ![Create a Hello World project for ACS exampple][create_acs_hello_world]

    Haga clic en **Finalizar**.
2. En la vista del explorador de proyectos de Eclipse, expanda **MyACSHelloWorld**. Haga clic con el botón derecho en **WebContent**, haga clic en **Nuevo** y luego en **Archivo JSP**.
3. En el cuadro de diálogo **Nuevo archivo JSP**, asigne al archivo el nombre **index.jsp**. Mantenga la carpeta principal como MyACSHelloWorld/WebContent, tal como aparece a continuación:

    ![Add a JSP file for ACS example][add_jsp_file_acs]

    Haga clic en **Siguiente**.

4. En el cuadro de diálogo **Seleccionar plantilla JSP**, seleccione **Nuevo archivo JSP (html)** y haga clic en **Finalizar**.
5. Cuando el archivo index.jsp se abra en Eclipse, agregue texto para mostrar **Hello ACS World!** dentro del elemento `<body>` existente. Su contenido de `<body>` actualizado debería aparecer de la siguiente manera:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Guarde el archivo index.jsp.
  
## <a name="add_acs_filter_library"></a>Incorporación de la biblioteca de filtros de ACS a su aplicación

1. En el explorador de proyectos de Eclipse, haga clic con el botón derecho en **MyACSHelloWorld** y, a continuación, haga clic en **Ruta de acceso de compilación** y luego en **Configurar ruta de acceso de compilación**.
2. En el cuadro de diálogo **Ruta de acceso de compilación de Java**, haga clic en la pestaña **Bibliotecas**.
3. Haga clic en **Agregar biblioteca**.
4. Haga clic en **Filtro de servicios de control de acceso de Azure (de Microsoft Open Technologies)** y luego en **Siguiente**. Se muestra el cuadro de diálogo del **Filtro de servicios de control de acceso de Azure**.  (El campo **Ubicación** puede tener una ruta de acceso diferente, dependiendo de dónde instalara Eclipse, y el número de versión podría ser diferente, dependiendo de las actualizaciones de software).

    ![Add ACS Filter library][add_acs_filter_lib]

5. En el explorador, abra la página **Integración de página de inicio de sesión** del portal de administración, copie la dirección URL que aparece en el campo **Opción 1: Vincular a una página de inicio de sesión basada en ACS** y péguela en el campo **Extremo de autenticación de ACS** del cuadro de diálogo de Eclipse.
6. En el explorador, abra la página **Editar aplicación de usuario de confianza** del portal de administración, copie la dirección URL que aparece en el campo **Dominio kerberos** y péguela en el campo **Dominio kerberos del usuario de confianza** del cuadro de diálogo de Eclipse.
7. En la sección **Seguridad** del cuadro de diálogo de Eclipse, si desea utilizar un certificado existente, haga clic en **Examinar**, navegue al certificado que desea utilizar, selecciónelo y haga clic en **Abrir**. O bien, si desea crear un certificado nuevo, haga clic en **Nuevo** para mostrar el cuadro de diálogo **Nuevo certificado** y, a continuación, especifique la contraseña, el nombre del certificado .cer y el nombre del archivo .pfx para el certificado nuevo.
8. Active **Insertar el certificado en el archivo WAR**. Al insertar el certificado de esta manera se incluye en su implementación sin que deba agregarlo manualmente como componente. (Si, por el contrario, debe almacenar el certificado de manera externa desde el archivo WAR, podría agregar el certificado como un componente de rol y desactivar la opción **Insertar el certificado en el archivo WAR**).
9. [Opcional] Mantenga activada la opción **Requerir conexiones HTTPS**. Si define esta opción, deberá tener acceso a su aplicación mediante el protocolo HTTPS. Si no desea requerir conexiones HTTPS, desactive esta opción.
10. En una implementación en el emulador de proceso, su configuración de **Filtro de ACS de Azure** será como la siguiente.

    ![Azure ACS Filter settings for a deployment to the compute emulator][add_acs_filter_lib_emulator]

11. Haga clic en **Finalizar**.
12. Haga clic en **Sí** cuando aparezca un cuadro de diálogo que indique que se creará un archivo web.xml.
13. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Ruta de compilación de Java**.

## <a name="deploy_compute_emulator"></a>Implementación del emulador de proceso

1. En el explorador de proyectos de Eclipse, haga clic con el botón derecho en **MyACSHelloWorld** y, a continuación, haga clic en **Azure** y luego en **Paquete para Azure**.
2. En **Nombre de proyecto**, escriba **MyAzureACSProject** y haga clic en **Siguiente**.
3. Seleccione un JDK y un servidor de aplicaciones. (Estos pasos aparecen detallados en el tutorial [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/es-es/library/windowsazure/hh690944.aspx)).
4. Haga clic en **Finalizar**.
5. Haga clic en el botón **Ejecutar en emulador de Azure**.
6. Una vez que la aplicación web de Java se inicie en el emulador de proceso, cierre todas las instancias del explorador (para que ninguna sesión actual del explorador interfiera con la prueba de inicio de sesión en ACS).
7. Para ejecutar la aplicación, abra <http://localhost:8080/MyACSHelloWorld/> en el explorador (o <https://localhost:8080/MyACSHelloWorld/> si ha activado **Requerir conexiones HTTPS**). Se le solicitará un inicio de sesión de Windows Live ID y, a continuación, debería ir a la URL de retorno que se especificó para su aplicación de usuario de confianza.
99.  Cuando haya terminado de visualizar la aplicación, haga clic en el botón **Restablecer emulador de Azure**.

## <a name="deploy_azure"></a>Implementación en Azure

Para implementar en Azure, deberá cambiar el dominio de usuario de confianza y la URL de retorno para su espacio de nombres de ACS.

1. Dentro del Portal de administración de Azure, en la página **Editar aplicación de usuario de confianza**, modifique **Dominio kerberos** para que sea la URL del sitio implementado. Reemplace **ejemplo** por el nombre DNS que especificó para la implementación.

    ![Relying party realm for use in production][relying_party_realm_production]

2. Modifique la **URL de retorno** para que sea la dirección URL de la aplicación. Reemplace **ejemplo** por el nombre DNS que especificó para la implementación.

    ![Relying party return URL for use in production][relying_party_return_url_production]

3. Haga clic en **Guardar** para guardar los cambios actualizados en la URL de retorno y el dominio de usuario de confianza.
4. Mantenga abierta la página **Integración de página de inicio de sesión** en el explorador, porque deberá copiar información desde ella.
5. En el explorador de proyectos de Eclipse, haga clic con el botón derecho en **MyACSHelloWorld** y, a continuación, haga clic en **Ruta de acceso de compilación** y luego en **Configurar ruta de acceso de compilación**.
6. Haga clic en la pestaña **Bibliotecas**, haga clic en **Filtro de servicios de control de acceso de Azure** y, a continuación, en **Editar**.
7. En el explorador, abra la página **Integración de página de inicio de sesión** del portal de administración, copie la dirección URL que aparece en el campo **Opción 1: Vincular a una página de inicio de sesión basada en ACS** y péguela en el campo **Extremo de autenticación de ACS** del cuadro de diálogo de Eclipse.
8. En el explorador, abra la página **Editar aplicación de usuario de confianza** del portal de administración, copie la dirección URL que aparece en el campo **Dominio kerberos** y péguela en el campo **Dominio kerberos del usuario de confianza** del cuadro de diálogo de Eclipse.
9. En la sección **Seguridad** del cuadro de diálogo de Eclipse, si desea utilizar un certificado existente, haga clic en **Examinar**, navegue al certificado que desea utilizar, selecciónelo y haga clic en **Abrir**. O bien, si desea crear un certificado nuevo, haga clic en **Nuevo** para mostrar el cuadro de diálogo **Nuevo certificado** y, a continuación, especifique la contraseña, el nombre del certificado .cer y el nombre del archivo .pfx para el certificado nuevo.
10. Mantenga activada la opción **Insertar el certificado en el archivo WAR**, siempre y cuando desee insertar el certificado en el archivo WAR.
11. [Opcional] Mantenga activada la opción **Requerir conexiones HTTPS**. Si define esta opción, deberá tener acceso a su aplicación mediante el protocolo HTTPS. Si no desea requerir conexiones HTTPS, desactive esta opción.
12. En una implementación en Azure, la configuración del filtro de ACS de Azure será como la siguiente.

    ![Azure ACS Filter settings for a production deployment][add_acs_filter_lib_production]

13. Haga clic en **Finalizar** para cerrar el cuadro de diálogo **Editar la biblioteca**.
14. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de MyACSHelloWorld**.
15. En Eclipse, haga clic en el botón **Publicar en la nube de Azure**. Siga las indicaciones, tal como lo hizo en la sección **Implementación de su aplicación en Azure** del tema [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/es-es/library/windowsazure/hh690944.aspx). 

Una vez que se ha implementado la aplicación web, cierre las sesiones abiertas del explorador, ejecute la aplicación web y se le solicitará iniciar sesión con credenciales de Windows Live ID y, a continuación, irá a la URL de retorno de su aplicación de usuario de confianza.

Cuando haya terminado de utilizar la aplicación Hello World de ACS, recuerde eliminar la implementación (aprenda a eliminar una implementación en el tema [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/es-es/library/windowsazure/hh690944.aspx)).


## <a name="next_steps"></a>Pasos siguientes

Para analizar el lenguaje de marcado de aserción de seguridad (SAML) que devuelve ACS a su aplicación, consulte [Visualización del SAML que devuelve el servicio de control de acceso de Azure][]. Para seguir explorando la funcionalidad de ACS y experimentar con escenarios más sofisticados, consulte [Servicio de control de acceso 2.0][].

Además, en este ejemplo se utilizó la opción **Insertar el certificado en el archivo WAR**. Esta opción facilita la implementación del certificado. Si, por el contrario, desea mantener el certificado de firma independiente del archivo WAR, puede utilizar la siguiente técnica:

1. En la sección **Seguridad** del cuadro de diálogo **Filtro de servicios de control de acceso de Azure**, escriba **${env.JAVA_HOME}/mycert.cer** y desactive la opción **Insertar el certificado en el archivo WAR**. (Ajuste mycert.cer si el nombre del archivo de certificado es distinto). Haga clic en **Finalizar** para cerrar el cuadro de diálogo.
2. Copie el certificado como componente de su implementación: En el explorador de proyectos de Eclipse, expanda **MyAzureACSProject**, haga clic con el botón derecho en **WorkerRole1**, haga clic en **Propiedades**, expanda **Rol de Azure** y, a continuación, haga clic en **Componentes**.
3. Haga clic en **Agregar**.
4. En el cuadro de diálogo **Agregar componente**:
    1. En la sección **Importar**:
        1. Utilice el botón **Archivo** para navegar al certificado que desea usar. 
        2. Para **Método**, seleccione **copiar**.
    2. En **Como nombre**, haga clic en el cuadro de texto y acepte el nombre predeterminado.
    3. En la sección **Implementar**:
        1. Para **Método**, seleccione **copiar**.
        2. En **A directorio**, escriba **%JAVA_HOME%**.
    4. El cuadro de diálogo **Agregar componente** debe parecerse al siguiente.

        ![Add certificate component][add_cert_component]

    5. Haga clic en **Aceptar**.

En este punto, su certificado se incluiría en la implementación. Observe que, independientemente de si insertó el certificado en el archivo WAR o si lo agregó como componente a la implementación, necesita cargar el certificado en su espacio de nombres, tal como se describe en la sección [Carga de un certificado en su espacio de nombres de ACS][].

[¿Qué es ACS?]: #what-is
[Conceptos]: #concepts
[Requisitos previos]: #pre
[Creación de una aplicación web de Java]: #create-java-app
[Creación de un espacio de nombres de ACS]: #create-namespace
[Incorporación de proveedores de identidades]: #add-IP
[Incorporación de una aplicación de usuario de confianza]: #add-RP
[Creación de reglas]: #create-rules
[Carga de un certificado en su espacio de nombres de ACS]: #upload-certificate
[Revisión de la página de integración de aplicaciones]: #review-app-int
[Configuración de la confianza entre ACS y la aplicación web ASP.NET]: #config-trust
[Incorporación de la biblioteca de filtros de ACS a su aplicación]: #add_acs_filter_library
[Implementación del emulador de proceso]: #deploy_compute_emulator
[Implementación en Azure]: #deploy_azure
[Pasos siguientes]: #next_steps
[sitio web de proyecto]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Visualización deL SAML que devuelve el servicio de control de acceso de Azure]: /es-es/develop/java/how-to-guides/view-saml-returned-by-acs/
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[SDK de Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portal de administración de Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
