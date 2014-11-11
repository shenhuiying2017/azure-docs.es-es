<properties linkid="develop-java-how-to-guides-access-control" urlDisplayName="Access Control" pageTitle="How to use Access Control (Java) - Azure feature guide" metaKeywords="" description="Learn how to develop and use Access Control with Java in Azure." metaCanonical="" services="active-directory" documentationCenter="Java" title="How to Authenticate Web Users with Azure Access Control Service Using Eclipse" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse

Esta guía le mostrará cómo utilizar el servicio de control de acceso de Azure (ACS) dentro del complemento de Azure para Eclipse con Java (de Microsoft Open Technologies). Para obtener más información sobre ACS, consulte la sección [Pasos siguientes][Pasos siguientes].

> [WACOM.NOTE]
> El filtro de control de los servicios de acceso de Azure (de Microsoft Open Technologies) es una Community Technology Preview. Como versión preliminar de software, no cuenta formalmente con el respaldo de Microsoft Open Technologies, Inc. ni de Microsoft.

## Tabla de contenido

-   [¿Qué es ACS?][¿Qué es ACS?]
-   [Conceptos][Conceptos]
-   [Requisitos previos][Requisitos previos]
-   [Creación de un espacio de nombres de ACS][Creación de un espacio de nombres de ACS]
-   [Incorporación de proveedores de identidades][Incorporación de proveedores de identidades]
-   [Incorporación de una aplicación de usuario de confianza][Incorporación de una aplicación de usuario de confianza]
-   [Creación de reglas][Creación de reglas]
-   [Carga de un certificado en su espacio de nombres de ACS][Carga de un certificado en su espacio de nombres de ACS]
-   [Revisión de la página de integración de aplicaciones][Revisión de la página de integración de aplicaciones]
-   [Creación de una aplicación web de Java][Creación de una aplicación web de Java]
-   [Incorporación de la biblioteca de filtros de ACS a su aplicación][Incorporación de la biblioteca de filtros de ACS a su aplicación]
-   [Implementación del emulador de proceso][Implementación del emulador de proceso]
-   [Implementación en Azure][Implementación en Azure]
-   [Pasos siguientes][Pasos siguientes]

## <a name="what-is"></a>¿Qué es ACS?

La mayoría de los desarrolladores ni son expertos en identidad ni
generalmente quieren dedicar demasiado tiempo a desarrollar mecanismos de autenticación y autorización para sus
aplicaciones y servicios. ACS es un servicio de Azure que
ofrece un sencillo método de autenticación de usuarios que necesitan tener acceso a sus servicios y aplicaciones web sin tener que
tomar en consideración una lógica de
autenticación compleja en su código.

En ACS están disponibles las características siguientes:

-   Integración con Windows Identity Foundation (WIF).
-   Compatibilidad con los proveedores de identidades (IP) habituales, entre los que se incluyen Windows Live ID, Google, Yahoo! y Facebook.
-   Compatibilidad con los servicios de federación de Active Directory (AD FS) 2.0.
-   Un servicio de administración basado en un protocolo de datos abierto (OData) que proporciona acceso programático a la configuración de ACS.
-   Un portal de administración que concede acceso administrativo a la configuración de ACS.

Para obtener más información acerca de ACS, consulte [Access Control Service 2.0][Access Control Service 2.0].

## <a name="concepts"></a>Conceptos

Azure ACS se ha creado sobre los principios de la identidad basada en notificaciones,
un enfoque coherente para crear mecanismos de autenticación para las
aplicaciones que se ejecutan en el entorno local o en la nube. La identidad basada en notificaciones
proporciona un método común para que las aplicaciones y los servicios obtengan la
información necesaria acerca de la identidad de los usuarios
de la organización, de otras organizaciones y en Internet.

Para completar las tareas de esta guía, deberá comprender los siguientes
conceptos:

**Cliente**: en el contexto de esta guía de procedimientos, es un explorador que
intenta obtener acceso a su aplicación web.

**Aplicación de usuarios de confianza (RP)**: una aplicación RP es un
servicio o sitio web que externaliza la autenticación a una autoridad externa. En la jerga de la
identidad, se dice que los RP confían en dicha autoridad. En esta guía se
explica cómo configurar su aplicación para que confíe en ACS.

**Token**: un token es una colección de datos de seguridad que normalmente se
emite después de la autenticación correcta de un usuario. Contiene un conjunto de *notificaciones*, atributos del usuario autenticado. Una notificación puede representar el nombre de un
usuario, un identificador para el rol al que pertenece un usuario, la edad del usuario,
etc. Normalmente se firma un token de manera digital, lo que significa que
siempre se podrá rastrear a quien lo emite y no se podrá
cambiar su contenido. Un usuario tiene acceso a una aplicación RP presentando un
token válido emitido por una autoridad en la que confía la aplicación RP.

**Proveedor de identidades (IP)**: un proveedor de identidades es una autoridad que autentica las
identidades de los usuarios y emite los tokens de seguridad. El trabajo real de emitir
tokens se implementa a través de un servicio especial llamado servicio de token
de seguridad (STS). Ejemplos típicos de proveedores de identidades incluyen Windows Live ID;
Facebook, repositorios de usuarios profesionales (como Active Directory), etc.
Cuando ACS se ha configurado para confiar en un IP, el sistema aceptará y
validará los tokens que emite dicho IP. ACS puede confiar en varios IP a la vez, lo que significa que
cuando su aplicación confía en ACS, puede
ofrecer de manera inmediata su aplicación a todos los usuarios autenticados por
todos los IP en los que ACS confía en su nombre.

**Proveedor de federación (FP)**: los IP conocen directamente a los usuarios,
los autentican usando sus credenciales y emiten notificaciones
sobre lo que saben de ellos. Un proveedor de federación (FP) es un tipo de
autoridad diferente: en lugar de autenticar directamente a los usuarios, actúa como
intermediario y negocia la autenticación entre un RP y una o varias
IP. Tanto los IP como los FP emiten tokens de seguridad, por lo tanto, ambos utilizan los
servicios de token de seguridad (STS). ACS es un FP.

**Motor de reglas de ACS**: la lógica que se utiliza para
transformar los tokens entrantes procedentes de IP de confianza en tokens que pueda consumir el RP
se codifica en forma de simples reglas de transformación de notificaciones. ACS incluye un motor de reglas que se
ocupa de aplicar cualquier lógica de transformación que se haya especificado para su
RP.

**Espacio de nombres de ACS**: un espacio de nombres es una partición de nivel superior de ACS que se
utiliza para organizar su configuración. Un espacio de nombres contiene una lista de los IP en los que
confía, las aplicaciones de RP a las que desea servir,
las reglas con las que espera que el motor de reglas procese los tokens entrantes, etc. Un espacio de nombres
expone varios extremos que la aplicación y el
desarrollador utilizarán para hacer que ACS realice su función.

La siguiente ilustración muestra cómo funciona la autenticación de ACS con una
aplicación web:

![Diagrama de flujo de ACS][Diagrama de flujo de ACS]

1.  El cliente (en este caso, un explorador) solicita una página del RP.
2.  Como la solicitud no se ha autenticado todavía, el RP redirige al usuario a la autoridad en la que confía, es decir, ACS. El ACS presenta al usuario la selección de IP especificados para este RP. El usuario selecciona el IP adecuado.
3.  El cliente examina la página de autenticación del IP y pide al usuario que inicie sesión.
4.  Cuando el cliente se haya autenticado (por ejemplo, cuando haya escrito las credenciales de identidad), el IP emitirá un token de seguridad.
5.  Después de haber emitido un token de seguridad, el IP redirige el cliente a ACS y el cliente envía a ACS el token de seguridad que emite el IP.
6.  ACS valida el token de seguridad emitido por el IP, introduce las notificaciones de identidad de este token en el motor de reglas de ACS, calcula las notificaciones de identidad resultantes y emite un nuevo token de seguridad que contenga estas notificaciones.
7.  ACS redirige el cliente al RP. El cliente envía el nuevo token de seguridad emitido por ACS al RP. El RP valida la firma del token de seguridad emitido por ACS, valida las notificaciones en este token y devuelve la página que solicitó originalmente.

## <a name="pre"></a>Requisitos previos

Necesitará lo siguiente para completar las tareas de esta guía:

-   Un kit para desarrolladores de Java (JDK) v1.6 o superior.
-   Eclipse IDE para Java EE Developers, Indigo o superior. Esto se puede descargar en <http://www.eclipse.org/downloads/>.
-   Una distribución de un servidor de aplicaciones o servidor web basado en Java, como Apache Tomcat, GlassFish, JBoss Application Server o Jetty.
-   una suscripción de Azure, que se puede adquirir en <http://www.microsoft.com/windowsazure/offers/>.
-   Complemento de Azure para Eclipse con Java (de Microsoft Open Technologies), versión de april de 2014. Para obtener más información, consulte [Instalación del complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)][Instalación del complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)].
-   Un certificado X.509 para utilizar con la aplicación. Necesitará este certificado en formato de certificado público (.cer) e intercambio de información personal (.PFX). (Las opciones para crear este certificado se describirán más adelante en este tutorial).
-   Estar familiarizado con el emulador de proceso de Azure y las técnicas de implementación analizadas en [Creación de una aplicación Hello World para Azure en Eclipse][Creación de una aplicación Hello World para Azure en Eclipse].

## <a name="create-namespace"></a>Creación de un espacio de nombres de ACS

Para comenzar a utilizar el servicio de control de acceso (ACS) en Azure, debe
crear un espacio de nombres de ACS. El espacio de nombres proporciona un ámbito único para
dirigir los recursos de ACS desde dentro de su aplicación.

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en **Active Directory**.
3.  Para crear un nuevo espacio de nombres de control de acceso, haga clic en **New**, **Servicios de aplicaciones**, **Control de acceso** y, a continuación, en **Quick Create**.
4.  Escriba un nombre para el espacio de nombres. Azure verificará si el nombre es único.
5.  Seleccione la región en la que se usa el espacio de nombres. Para obtener el máximo rendimiento, utilice la región en la que va a implementar su aplicación.
6.  Si tiene varias suscripciones, seleccione la que desea utilizar para el espacio de nombres de ACS.
7.  Haga clic en **Crear**.

Azure crea y activa el espacio de nombres. Espere hasta que el estado del nuevo espacio de nombres sea **Active** antes de continuar.

## <a name="add-IP"></a>Incorporación de proveedores de identidades

En esta tarea, agregará IP para utilizarlos con su aplicación RP para la
autenticación. Para los fines de esta demostración, esta tarea muestra cómo
agregar Windows Live como un IP, pero podría utilizarse cualquiera de los IP
que aparecen en el portal de administración de ACS.

1.  En el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Active Directory**, seleccione un espacio de nombres de control de acceso y, a continuación, haga clic en **Manage**. Se abre el portal de administración de ACS.
2.  En el panel de navegación izquierdo del portal de administración de ACS, haga clic en **Identity providers**.
3.  Windows Live ID está habilitado de manera predeterminada y no se puede eliminar. Para los fines de este tutorial, solo se utiliza Windows Live ID. Sin embargo, en esta pantalla podría agregar otros IP haciendo clic en el botón **Add**.

Windows Live ID ahora está habilitado como un IP para su espacio de nombres de ACS. A continuación,
especifique la aplicación web de Java (que se va a crear más adelante) como un RP.

## <a name="add-RP"></a>Incorporación de una aplicación de usuario de confianza

En esta tarea, configurará ACS para reconocer la
aplicación web de Java como una aplicación RP válida.

1.  En el portal de administración de ACS, haga clic en **Relying party applications**.
2.  En la página **Relying Party Applications**, haga clic en **Add**.
3.  En la página **Add Relying Party Application**, haga lo siguiente:

    1.  En **Name**, escriba el nombre del RP. Para los fines de este tutorial, escriba **Azure Web
        App**.
    2.  En **Mode**, seleccione **Enter settings manually**.
    3.  En **Realm**, escriba el URI al que se aplica el token de seguridad que emitió ACS. Para esta tarea, escriba **<http://localhost:8080/>**.
        ![El dominio de usuario de confianza para utilizar en el emulador de proceso][El dominio de usuario de confianza para utilizar en el emulador de proceso]
    4.  En **Return URL**, escriba la URL a la cual ACS devuelve el token de seguridad. Para esta tarea, escriba **<http://localhost:8080/MyACSHelloWorld/index.jsp>**
        ![URL de retorno de usuario de confianza para utilizar en el emulador de proceso][URL de retorno de usuario de confianza para utilizar en el emulador de proceso]
    5.  Acepte los valores predeterminados en el resto de los campos.

4.  Haga clic en **Save**.

Ha configurado correctamente su aplicación web de Java para que cuando se ejecute en el emulador de proceso de Azure (en
<http://localhost:8080/>) sea un RP en su espacio de nombres de ACS. A continuación, cree
las reglas que ACS utiliza para procesar notificaciones para el RP.

## <a name="create-rules"></a>Creación de reglas

En esta tarea, definirá las reglas que controlan la manera en que las notificaciones se transmiten desde los
IP a su RP. En lo que respecta a esta guía, simplemente configuraremos
ACS para que copie los valores y tipos de notificación de introducción directamente en el token de
salida, sin filtrarlos ni modificarlos.

1.  En la página principal del portal de administración de ACS, haga clic en **Rule groups**.
2.  En la página **Rule Groups**, haga clic en **Default Rule Group for Azure Web App**.
3.  En la página **Edit Rule Group**, haga clic en **Generate**.
4.  En la página **Generate Rules: Grupo de reglas predeterminado para Azure Web App**, asegúrese de que la opción Windows Lide ID esté activada y, a continuación, haga clic en **Generar**.
    .
5.  En la página **Edit Rule Group**, haga clic en **Save**.

## <a name="upload-certificate"></a>Carga de un certificado en su espacio de nombres de ACS

En esta tarea, cargará un certificado .PFX que se utilizará para firmar solicitudes de token creadas por su espacio de nombres de ACS.

1.  En la página principal del portal de administración de ACS, haga clic en **Certificates and keys**.
2.  En la página **Certificates and Keys**, haga clic en **Add** encima de **Token Signing**.
3.  En la página **Add Token-Signing Certificate or Key**:

    1.  En la sección **Used for**, haga clic en **Relying Party Application** y seleccione **Azure Web App** (que anteriormente definió como el nombre de su aplicación de usuario de confianza).
    2.  En la sección **Type**, seleccione **X.509 Certificate**.
    3.  En la sección **Certificate**, haga clic en el botón de examinar y navegue al archivo del certificado X.509 que desea utilizar. Este será un archivo .PFX. Seleccione el archivo, haga clic en **Open** y, a continuación, escriba la contraseña del certificado en el cuadro de texto **Password**. Observe que, para fines de prueba, puede utilizar un certificado autofirmado. Para crear un certificado autofirmado, utilice el botón **New** que aparece en el cuadro de diálogo **ACS Filter Library** (que se describe más adelante), o bien, puede usar la utilidad **encutil.exe** desde el [sitio web del proyecto][sitio web del proyecto] del kit de inicio de Azure para Java (de Microsoft Open Technologies).
    4.  Asegúrese de que la opción **Make Primary** esté activada. Su página **Add Token-Signing Certificate or Key** debe tener un aspecto similar al siguiente.
        ![Add token-signing certificate][Add token-signing certificate]
    5.  Haga clic en **Save** para guardar la configuración y cierre la página **Add Token-Signing Certificate or Key**.

A continuación, revise la información que se encuentra en la página de integración de aplicaciones y
copie el URI que necesitará para configurar su
aplicación web de Java para usar ACS.

## <a name="review-app-int"></a>Revisión de la página de integración de aplicaciones

Puede encontrar toda la información y el código que se necesita para configurar su aplicación
web de Java (la aplicación RP) para trabajar con ACS
en la página de integración de aplicaciones del portal de administración de ACS. Necesitará
esta información cuando configure la aplicación web de Java para la
autenticación federada.

1.  En el portal de administración de ACS, haga clic en **Integración de aplicaciones**.
2.  En la página **Application Integration**, haga clic en **Login Pages**.
3.  En la página **Login Page Integration**, haga clic en **Azure Web App**.

En la página **Login Page Integration: Azure Web App**, la dirección URL que aparece en **Option 1: Link to an ACS-hosted login page** se utilizará en su aplicación web de Java. Necesitará este valor cuando agregue la biblioteca de filtros de servicios de control de acceso de Azure a su aplicación Java.

## <a name="create-java-app"></a>Creación de una aplicación web de Java

1.  En el menú de Eclipse, haga clic en **File**, **New** y, a continuación, en **Dynamic Web Project**. (Si no ve **Dynamic Web Project** como proyecto disponible después de hacer clic en **File**, **New**, haga lo siguiente: haga clic en **File**, **New**, **Project**, expanda **Web**, haga clic en **Dynamic Web Project** y, a continuación, en **Next**). Para los fines de este tutorial, asigne el nombre **MyACSHelloWorld** al proyecto. (Asegúrese de utilizar este nombre; los pasos posteriores de este historial esperan que su archivo WAR tenga el nombre MyACSHelloWorld). La pantalla se parecerá a la siguiente:

    ![Ejemplo: Crear un proyecto Hello World para ACS][Ejemplo: Crear un proyecto Hello World para ACS]

    Haga clic en **Finish**.

2.  En la vista del explorador de proyectos de Eclipse, expanda **MyACSHelloWorld**. Haga clic con el botón secundario en **WebContent**, luego haga clic en **New** y, a continuación, en **JSP File**.
3.  En el cuadro de diálogo **New JSP File**, asigne al archivo el nombre **index.jsp**. Mantenga la carpeta principal como MyACSHelloWorld/WebContent, tal como aparece a continuación:

    ![Ejemplo: Agregar un archivo JSP para ACS][Ejemplo: Agregar un archivo JSP para ACS]

    Haga clic en **Next**.

4.  En el cuadro de diálogo **Select JSP Template**, seleccione **New JSP File (html)** y, a continuación, haga clic en **Finish**.
5.  Cuando se abre el archivo index.jsp en Eclipse, agregue texto para que muestre **Hello ACS World!** dentro del elemento `<body>` existente. Su contenido de `<body>` actualizado debería aparecer de la siguiente manera:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>

    Guarde el archivo index.jsp.

## <a name="add_acs_filter_library"></a>Incorporación de la biblioteca de filtros de ACS a su aplicación

1.  En el explorador de proyectos de Eclipse, haga clic con el botón secundario en **MyACSHelloWorld** y, a continuación, haga clic en **Build Path** y luego en **Configure Build Path**.
2.  En el cuadro de diálogo **Java Build Path**, haga clic en la pestaña **Libraries**.
3.  Haga clic en **Add Library**.
4.  Haga clic en **Azure Access Control Services Filter (by MS Open Tech)** y, a continuación, en **Next**. Aparecerá el cuadro de diálogo **Azure Access Control Services Filter**. (El campo **Location** puede tener una ruta de acceso distinta, dependiendo de dónde instalara Eclipse; además, el número de versión podría ser distinto, dependiendo de las actualizaciones de software).

    ![Agregar biblioteca de filtros de ACS][Agregar biblioteca de filtros de ACS]

5.  En el explorador, abra la página **Login Page Integration** del portal de administración, copie la dirección URL que aparece en el campo **Option 1: Link to an ACS-hosted login page** y péguela en el campo **ACS Authentication Endpoint** del cuadro de diálogo de Eclipse.
6.  En el explorador, abra la página **Edit Relying Party Application** del portal de administración, copie la dirección URL que aparece en el campo **Realm** y péguela en el campo **Relying Party Realm** del cuadro de diálogo de Eclipse.
7.  En la sección **Security** del cuadro de diálogo de Eclipse, si desea utilizar un certificado existente, haga clic en **Browse**, navegue al certificado que desea utilizar, selecciónelo y haga clic en **Open**. O bien, si desea crear un certificado nuevo, haga clic en **New** para mostrar el cuadro de diálogo **New Certificate** y, a continuación, especifique la contraseña, el nombre del certificado .cer y el nombre del archivo .pfx para el certificado nuevo.
8.  Haga clic en **Embed the certificate in the WAR file**. Al insertar el certificado de esta manera se incluye en su implementación sin que deba agregarlo manualmente como componente. (Si, por el contrario, debe almacenar el certificado de manera externa desde el archivo WAR, podría agregar el certificado como un componente de rol y desactivar la opción **Embed the certificate in the WAR file**).
9.  [Opcional] Mantenga activada la opción **Require HTTPS connections**. Si define esta opción, deberá tener acceso a su aplicación mediante el protocolo HTTPS. Si no desea exigir conexiones HTTPS, desactive esta opción.
10. En una implementación en el emulador de proceso, su configuración de **Azure ACS Filter** será como la siguiente.

    ![Configuración del filtro de Azure ACS para una implementación en el emulador de proceso][Configuración del filtro de Azure ACS para una implementación en el emulador de proceso]

11. Haga clic en **Finish**.
12. Haga clic en **Sí** cuando aparezca un cuadro de diálogo que indique que se creará un archivo web.xml.
13. Haga clic en **OK** para cerrar el cuadro de diálogo **Java Build Path**.

## <a name="deploy_compute_emulator"></a>Implementación del emulador de proceso

1.  En el explorador de proyectos de Eclipse, haga clic con el botón secundario en **MyACSHelloWorld** y, a continuación, haga clic en **Azure** y luego en **Package for Azure**.
2.  En **Project name**, escriba **MyAzureACSProject** y haga clic en **Next**.
3.  Seleccione un JDK y un servidor de aplicaciones. (Estos pasos aparecen detallados en el tutorial [Creación de una aplicación Hello World para Azure en Eclipse][Creación de una aplicación Hello World para Azure en Eclipse]).
4.  Haga clic en **Finish**.
5.  Haga clic en el botón **Run in Azure Emulator**.
6.  Una vez que la aplicación web de Java se inicie en el emulador de proceso, cierre todas las instancias del explorador (para que ninguna sesión actual del explorador interfiera con la prueba de inicio de sesión en ACS).
7.  Para ejecutar la aplicación abra <http://localhost:8080/MyACSHelloWorld/> en el explorador (o <https://localhost:8080/MyACSHelloWorld/> si ha activado **Requerir conexiones HTTPS**). Se le solicitará un inicio de sesión de Windows Live ID y, a continuación, debería ir a la URL de retorno que se especificó para su aplicación de usuario de confianza.
8.  Cuando haya terminado de visualizar la aplicación, haga clic en el botón **Reset Azure Emulator**.

## <a name="deploy_azure"></a>Implementación en Azure

Para implementar en Azure, deberá cambiar el dominio de usuario de confianza y la URL de retorno para su espacio de nombres de ACS.

1.  Dentro del Portal de administración de Azure, en la página **Edit Relying Party Application**, modifique **Realm** para que sea la URL del sitio implementado. Reemplace **example** por el nombre DNS que especificó para la implementación.

    ![El dominio de usuario de confianza para utilizar en producción][El dominio de usuario de confianza para utilizar en producción]

2.  Modifique la **URL de retorno** para que sea la dirección URL de la aplicación. Reemplace **example** por el nombre DNS que especificó para la implementación.

    ![La URL de retorno de usuario de confianza para utilizar en producción][La URL de retorno de usuario de confianza para utilizar en producción]

3.  Haga clic en **Save** para guardar los cambios actualizados en la URL de retorno y el dominio de usuario de confianza.
4.  Mantenga abierta la página **Login Page Integration** en el explorador, porque deberá copiar información desde ella.
5.  En el explorador de proyectos de Eclipse, haga clic con el botón secundario en **MyACSHelloWorld** y, a continuación, haga clic en **Build Path** y luego en **Configure Build Path**.
6.  Haga clic en la pestaña **Libraries**, **Azure Access Control Services Filter** y, a continuación, en **Editar**.
7.  En el explorador, abra la página **Login Page Integration** del portal de administración, copie la dirección URL que aparece en el campo **Option 1: Link to an ACS-hosted login page** y péguela en el campo **ACS Authentication Endpoint** del cuadro de diálogo de Eclipse.
8.  En el explorador, abra la página **Edit Relying Party Application** del portal de administración, copie la dirección URL que aparece en el campo **Realm** y péguela en el campo **Relying Party Realm** del cuadro de diálogo de Eclipse.
9.  En la sección **Security** del cuadro de diálogo de Eclipse, si desea utilizar un certificado existente, haga clic en **Browse**, navegue al certificado que desea utilizar, selecciónelo y haga clic en **Open**. O bien, si desea crear un certificado nuevo, haga clic en **New** para mostrar el cuadro de diálogo **New Certificate** y, a continuación, especifique la contraseña, el nombre del certificado .cer y el nombre del archivo .pfx para el certificado nuevo.
10. Si desea insertar el certificado en el archivo WAR, mantenga activada la opción **Embed the certificate in the WAR file**.
11. [Opcional] Mantenga activada la opción **Require HTTPS connections**. Si define esta opción, deberá tener acceso a su aplicación mediante el protocolo HTTPS. Si no desea exigir conexiones HTTPS, desactive esta opción.
12. En una implementación en Azure, la configuración del filtro de ACS de Azure será como la siguiente.

    ![Configuración del filtro de ACS de Azure para una implementación en producción][Configuración del filtro de ACS de Azure para una implementación en producción]

13. Haga clic en **Finish** para cerrar el cuadro de diálogo **Edit Library**.
14. Haga clic en **OK** para cerrar el cuadro de diálogo **Properties for MyACSHelloWorld**.
15. En Eclipse, haga clic en el botón **Publish to Azure Cloud**. Siga las indicaciones, tal como lo hizo en la sección **Implementación de su aplicación en Azure** del tema [Creación de una aplicación Hello World para Azure en Eclipse][Creación de una aplicación Hello World para Azure en Eclipse].

Una vez que se ha implementado la aplicación web, cierre las sesiones abiertas del explorador, ejecute la aplicación web y se le solicitará iniciar sesión con credenciales de Windows Live ID y, a continuación, irá a la URL de retorno de su aplicación de usuario de confianza.

Cuando haya terminado de utilizar la aplicación Hello World de ACS, recuerde eliminar la implementación (aprenda a eliminar una implementación en el tema [Creación de una aplicación Hello World para Azure en Eclipse][Creación de una aplicación Hello World para Azure en Eclipse]).

## <a name="next_steps"></a>Pasos siguientes

Para analizar el lenguaje de marcado de aserción de seguridad (SAML) que devuelve ACS a su aplicación, consulte [Visualización del SAML que devuelve el servicio de control de acceso de Azure][Visualización del SAML que devuelve el servicio de control de acceso de Azure]. Para seguir explorando la funcionalidad de ACS y experimentar con escenarios más sofisticados, consulte [Access Control Service 2.0][Access Control Service 2.0].

Además, en este ejemplo se utilizó la opción **Embed the certificate in the WAR file**. Esta opción facilita la implementación del certificado. Si, por el contrario, desea mantener el certificado de firma independiente del archivo WAR, puede utilizar la siguiente técnica:

1.  En la sección **Security** del cuadro de diálogo **Azure Access Control Services Filter**, escriba **${env.JAVA\_HOME}/mycert.cer** y desactive la opción **Embed the certificate in the WAR file**. (Ajuste mycert.cer si el nombre del archivo de certificado es distinto). Haga clic en **Finish** para cerrar el cuadro de diálogo.
2.  Copie el certificado como componente de su implementación: En el explorador de proyectos de Eclipse, expanda **MyAzureACSProject**, haga clic con el botón secundario en **WorkerRole1**, haga clic en **Properties**, expanda **Azure Role** y, a continuación, haga clic en **Componentes**.
3.  Haga clic en **Add**.
4.  Dentro del cuadro de diálogo **Add Component**:

    1.  En la sección **Import**:
		1.  Utilice el botón **File** para navegar al certificado que desea usar.
        2.  En **Method**, seleccione **copiar**.

    2.  En **As Name**, haga clic en el cuadro de texto y acepte el nombre predeterminado.
    3.  En la sección **Deploy**:
        1.  En **Method**, seleccione **copiar**.
        2.  En **To directory**, escriba **%JAVA\_HOME%**.

    4.  El cuadro de diálogo **Add Component** debe parecerse al siguiente.

        ![Agregar componente de certificado][Agregar componente de certificado]

    5.  Haga clic en **OK**.

En este punto, su certificado se incluiría en la implementación. Observe que, independientemente de si insertó el certificado en el archivo WAR o si lo agregó como componente a la implementación, necesita cargar el certificado en su espacio de nombres, tal como se describe en la sección [Carga de un certificado en su espacio de nombres de ACS][Carga de un certificado en su espacio de nombres de ACS].

<!-- Eclipse-specific -->

  [Pasos siguientes]: #next_steps
  [Conceptos]: #concepts
  [Requisitos previos]: #pre
  [Creación de un espacio de nombres de ACS]: #create-namespace
  [Incorporación de proveedores de identidades]: #add-IP
  [Incorporación de una aplicación de usuario de confianza]: #add-RP
  [Creación de reglas]: #create-rules
  [Carga de un certificado en su espacio de nombres de ACS]: #upload-certificate
  [Revisión de la página de integración de aplicaciones]: #review-app-int
  [Creación de una aplicación web de Java]: #create-java-app
  [Incorporación de la biblioteca de filtros de ACS a su aplicación]: #add_acs_filter_library
  [Implementación del emulador de proceso]: #deploy_compute_emulator
  [Implementación en Azure]: #deploy_azure
  [Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
  [Diagrama de flujo de ACS]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png
  [Creación de una aplicación Hello World para Azure en Eclipse]: http://msdn.microsoft.com/es-es/library/windowsazure/hh690944.aspx
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [El dominio de usuario de confianza para utilizar en el emulador de proceso]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
  [URL de retorno de usuario de confianza para utilizar en el emulador de proceso]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
  [sitio web del proyecto]: http://wastarterkit4java.codeplex.com/releases/view/61026
  [Add token-signing certificate]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
  [Ejemplo: Crear un proyecto Hello World para ACS]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
  [Ejemplo: Agregar un archivo JSP para ACS]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
  [Agregar biblioteca de filtros de ACS]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
  [Configuración del filtro de Azure ACS para una implementación en el emulador de proceso]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
  [El dominio de usuario de confianza para utilizar en producción]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
  [La URL de retorno de usuario de confianza para utilizar en producción]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
  [Configuración del filtro de ACS de Azure para una implementación en producción]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png
  [Visualización del SAML que devuelve el servicio de control de acceso de Azure]: /es-es/develop/java/how-to-guides/view-saml-returned-by-acs/
  [Agregar componente de certificado]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
