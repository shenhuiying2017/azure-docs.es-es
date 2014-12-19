#Guía de seguridad de Azure

##Descripción breve

Al desarrollar aplicaciones para Azure, la identidad y
y el acceso son los principales problemas de seguridad que se deben tener en cuenta.
En este tema se explican los principales problemas de seguridad relacionados con la identidad y el acceso en la nube y cómo se
pueden proteger mejor sus aplicaciones de la nube.

##Información general

La seguridad de una aplicación depende directamente de su superficie. Cuanta más superficie
expone la aplicación, más problemas de seguridad habrá. Por
ejemplo, una aplicación que se ejecuta como un proceso por lotes desatendido expone
menos, desde una perspectiva de seguridad, que un sitio web disponible públicamente.

Al pasar a la nube, tendrá tranquilidad con la
infraestructura y las redes, ya que éstas se administran en centros de datos
con las prácticas de seguridad, herramientas y tecnología de primer nivel. Por otra parte
, la nube expone intrínsecamente más superficie de su
aplicación de la que los atacantes pueden aprovechar potencialmente. Esto se debe a que
muchas de las tecnologías y los servicios de la nube se exponen como extremos
frente a componentes en memoria. Almacenamiento de Azure, Bus de servicio, Base de datos SQL
(anteriormente SQL Azure) y muchos otros servicios son accesibles
a través de sus extremos mediante conexión.

En aplicaciones de la nube, recae más responsabilidad en los
desarrolladores de aplicaciones para diseñar, desarrollar y mantener las aplicaciones
de la nube conforme a altos estándares de seguridad para mantener a raya a los atacantes.
Considere el siguiente diagrama (extraído de [Azure Security
Notes PDF](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx) de J.D. Meier): observe cómo aborda la parte de la infraestructura el
proveedor de la nube, en nuestro caso, Azure, dejando más trabajo de seguridad
para los desarrolladores de aplicaciones:

![Securing the application][01]

La buena noticia es que todas las prácticas de desarrollo,
principios y técnicas de seguridad que ya conoce se siguen aplicando al desarrollar
aplicaciones de la nube. Considere las áreas clave siguientes que deben
abordarse:

-   Todas las entradas se validan por tipo, longitud, rango y patrones de cadena
    para evitar los ataques de inyección y todos los datos que la aplicación refleja
    están correctamente saneados.
-   Deben protegerse los datos confidenciales inactivos y en circulación
    para mitigar la divulgación de información y las amenazas de manipulación de datos.
-   La auditoría y el registro deben implementarse correctamente para mitigar las amenazas
    sin repudio.
-   La autenticación y autorización deben implementarse mediante los
    mecanismos probados que la plataforma ofrece para evitar la suplantación de identidad y
    la elevación de amenazas de privilegios.

Para obtener una lista completa de amenazas, ataques, vulnerabilidades y
contramedidas, consulte los patrones y las prácticas de [Cheat Sheet: Web
Application Security Frame](http://msdn.microsoft.com/es-es/library/ff649461.aspx) y [Security Guidance for Applications Index](http://msdn.microsoft.com/es-es/library/ff650760.aspx).

En la nube, los mecanismos de control de acceso y autenticación son muy
diferentes a los que están disponibles para las aplicaciones locales. Se ofrecen
muchas más opciones de autenticación y acceso para las aplicaciones
de la nube que pueden llevar a confusión y, como resultado, a
implementaciones defectuosas. Sin contar con que se introduce más confusión al definir lo que significa una aplicación en
la nube. Por ejemplo, la aplicación podría implementarse en la nube aún
si Active Directory puede proporcionar su mecanismo de autenticación. Por
otra parte, la aplicación podría implementarse de forma local, pero con
mecanismos de autenticación en la nube, por ejemplo, mediante el control
de acceso de Active Directory para Azure (anteriormente conocido como servicio de control
de acceso o ACS).

##Amenazas, vulnerabilidades y ataques

Una amenaza es un posible resultado no deseado que se desea evitar, como la
divulgación de información confidencial o que un servicio deje de estar disponible.
Es una práctica habitual clasificar las amenazas con el acrónimo "STRIDE", por sus iniciales en inglés.

-   **S**uplantación o robo de identidad
-   **M**anipulación de datos
-   **R**epudio de acciones
-   **D**ivulgación de información
-   **D**enegación de servicio
-   **E**levación de privilegios

Las vulnerabilidades son errores que nosotros, como desarrolladores, introducimos en el código
que hacen que los atacantes puedan explotar una aplicación. Por ejemplo,
el envío de datos confidenciales con texto sin cifrar hace que sea posible lanzar una
amenaza de divulgación de información mediante un ataque de rastreo de tráfico.

El objetivo de los ataques es aprovechar las vulnerabilidades para causar daño
a una aplicación. Por ejemplo, un filtro de scripts de sitios, o XSS, es un
ataque que explota una salida no saneada. Otro ejemplo es
la interceptación de la red para capturar las credenciales que se envían sin cifrar. Estos
ataques pueden conducir a una amenaza de suplantación de identidad. Para que sea
sencillo, considere las amenazas, las vulnerabilidades y los ataques como algo perjudicial.
Tenga en cuenta los siguientes diagramas como una vista panorámica de los aspectos negativos
relacionados con una aplicación web implementada en Azure
[Azure Security Notes PDF](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx), de J.D. Meier):

![Threats Vulnerabilities and Attacks][02]

Usted, como desarrollador, tiene control sobre las vulnerabilidades. Mientras
menos vulnerabilidades se introduzcan, menos opciones tendrán los atacantes
para actuar.

Las vulnerabilidades relacionadas con la identidad y el acceso lo exponen a todas las
amenazas en el modelo STRIDE. Por ejemplo, un mecanismo de autenticación incorrectamente implementado
puede dar lugar a una suplantación de identidad y, como resultado,
a divulgación de información, manipulación de datos, operaciones de privilegios elevados
o incluso a apagar el servicio por completo. Tenga en cuenta las siguientes
preguntas que pueden indicar posibles vulnerabilidades en la implementación
de la identidad y el acceso en la aplicación de la nube:

-   ¿Está enviando credenciales sin cifrar por Internet a sus
    servicios de Azure?
-   ¿Almacena las credenciales de los servicios de Azure sin cifrar?
-   ¿Sus credenciales de los servicios de Azure siguen directivas de contraseñas
    seguras?
-   ¿Confía en Azure para verificar las credenciales frente al
    uso de mecanismos de verificación personalizados?
-   ¿Se limitan las sesiones de autenticación de los servicios de Azure o la
    vigencia del token a una ventana de tiempo razonable?
-   ¿Verifica los permisos para cada punto de entrada de Azure de su
    aplicación distribuida en la nube?
-   ¿El mecanismo de autorización presenta errores de forma segura sin exponer
    información confidencial, o sin permitir el acceso ilimitado?

##Contramedidas

La mejor contramedida frente a un ataque consiste en utilizar mecanismos de identidad y
acceso ofrecidos por la plataforma en lugar de implementar el suyo
propio. Considere las siguientes tecnologías de identidades y acceso importantes:

**Windows Identity Foundation (WIF).** WIF es una biblioteca de tiempo de ejecución de .NET
incluida con .NET Framework 4.5 (también está disponible como una descarga
independiente para .NET 3.5/4.0). WIF hace el trabajo pesado para el control
de protocolos como WS-Federation y WS-Trust y como control de tokens como el
lenguaje de marcado de aserción de seguridad (SAML), para que no tenga que escribir
código muy complejo relacionado con la seguridad en la aplicación. Los siguientes
recursos proporcionan información detallada sobre WIF:

-   [Muestras de Windows Identity Foundation 4.5](http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication) en la Galería de código de MSDN.
-   [Herramientas Windows Identity Foundation 4.5 para Visual Studio 11 Beta](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e) en
    la Galería de código de MSDN.
-   [Tiempo de ejecución de Windows Identity Foundation (.Net 3.5/4.0)](http://www.microsoft.com/es-es/download/details.aspx?id=17331) en MSDN.
-   [Ejemplos de Windows Identity Foundation 3.5/4.0 y plantillas de Visual Studio
    2008/2010](http://www.microsoft.com/es-es/download/details.aspx?displaylang=en&id=4451) en MSDN.

**Servicio de control de acceso de Azure AD (anteriormente conocido como ACS)**. 
Control de acceso de Azure AD es un servicio en la nube que proporciona el servicio de token
de seguridad (STS) y permite la federación con distintos proveedores de identidad
(IdP), como un proveedor de identidad de Active Directory corporativo o un proveedor de identidad de Internet, como
Windows Live ID / Cuenta de Microsoft, Facebook, Google, Yahoo! y Open
ID 2.0. Los siguientes recursos proporcionan información
detallada sobre el Control de acceso de Azure AD:

-   [Servicio de control de acceso 2.0](http://msdn.microsoft.com/library/gg429786.aspx) 
-   [Scenarios and Solutions Using ACS](http://msdn.microsoft.com/es-es/library/gg185920.aspx)
-   [Procedimientos de ACS](http://msdn.microsoft.com/es-es/library/windowsazure/gg185939.aspx)
-   [A Guide to Claims-Based Identity and Access Control](http://msdn.microsoft.com/es-es/library/ff423674.aspx)
-   [Identity Developer Training Kit](http://www.microsoft.com/es-es/download/details.aspx?id=14347)
-   [MSDN-hosted Identity Developer Training Course](http://msdn.microsoft.com/es-es/IdentityTrainingCourse)

**Servicios de federación de Active Directory (ADFS).**Servicios de federación
de Active Directory (ADFS) 2.0 proporciona compatibilidad para soluciones de identidades para
notificaciones que engloban la tecnología de Windows Server y
Windows Server. AD FS 2.0 admite los protocolos WS-Trust, WS-Federation y SAML
. Los recursos siguientes ofrecen información detallada sobre AD
FS:

-   [AD FS 2.0 Content Map](http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx)
-   [Diseño Web SSO][Diseño Web SSO]
-   [Diseño Web SSO federado][Diseño Web SSO federado]

**Firmas de acceso compartido de Azure.** Las firmas de acceso compartido
le permiten ajustar el acceso a un recurso de contenedor o blob. Los
recursos siguientes proporcionan información detallada sobre las firmas de acceso
compartido.

-   [Administrar el acceso a los recursos de almacenamiento de Azure](http://msdn.microsoft.com/es-es/library/ee393343.aspx)
-   [New Storage Feature: Shared Access Signatures](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [Shared Access Signatures Are Easy These Days](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

##Mapa de escenarios

En esta sección se describen brevemente los principales escenarios que se tratan en este tema.
Se utiliza como un mapa para determinar la solución de identidades correcta para la
aplicación.

-   **Aplicación de formularios Web Forms ASP.NET con autenticación federada.** En el
    escenario controlará el acceso a la aplicación ASP.NET Web Forms con la utilización de
    la identidad de Internet como Live ID / Cuenta de Microsoft o
    la identidad corporativa administrada en Windows Server Active Directory.
-   **Servicio WCF (SOAP) con autenticación federada.**En este
    escenario controla el acceso a un servicio WCF (SOAP) mediante identidades
    de servicio administradas por el Control de acceso de Azure AD.
-   **Servicio WCF (SOAP) con autenticación federada, identidades en
    Active Directory.** En este escenario controla el acceso a su servicio web WCF
    (SOAP) mediante identidades administradas por Active Directory
    de Windows Server corporativo.
-   **Servicio WCF (REST) con autenticación federada.**En este
    escenario controla el acceso a un servicio WCF (REST) mediante identidades
    de servicio administradas por el Control de acceso de Azure AD.
-   **Servicio WCF (REST) con Live ID / Cuenta de Microsoft, Facebook,
    Google, Yahoo!, Open ID.** En este escenario, controla el acceso al
    servicio WCF (REST) mediante la identidad de Internet como Live ID /
    Cuenta Microsoft.
-   **Aplicación web ASP.NET para el servicio REST WCF mediante el token SWT compartido.** En el
    escenario tiene una aplicación distribuida con una aplicación web de ASP.NET
    frontend y un servicio REST auxiliar y desea distribuir el contexto
    del usuario a través de niveles físicos.
-   **Autorización de control de acceso basado en roles (RBAC) en aplicaciones y servicios
    para notificaciones.** En este escenario, desea implementar
    lógica de autorización en su aplicación basada en roles.
-   **Autorización basada en notificaciones en aplicaciones y servicios
    para notificaciones.** En este escenario, desea implementar lógica
    de autorización en la aplicación basada en reglas de autorización complejas.
-   **Escenarios de identidades y acceso del servicio de almacenamiento de Azure.**En
    este escenario, necesita compartir de forma segura el acceso a contenedores
    y blobs de almacenamiento de Azure.
-   **Escenarios de identidades y acceso de Base de datos SQL de Azure.**Base de datos
    SQL admite la autenticación de SQL Server. No se admite
    la autenticación de Windows (seguridad integrada). Los usuarios deben
    proporcionar credenciales (inicio de sesión y contraseña) cada vez que se conectan a
    Base de datos SQL.
-   **Escenarios de identidades y acceso del bus de servicio de Azure.**En este
    escenario necesita obtener acceso seguro a las colas del Bus de servicio de Azure.
-   **Escenarios de identidades y acceso en la memoria caché.**En este escenario
    debe obtener acceso seguro a datos administrados por la memoria caché.
-   **Escenarios de identidades y acceso de Azure Marketplace.**En este
    escenario, debe tener acceso seguro a conjuntos de datos de
    Azure Marketplace.

##Escenarios de identidades y acceso de Azure

En esta sección se describen los escenarios comunes de identidades y acceso para las distintas
arquitecturas de aplicaciones. Utilice el mapa de escenarios para una rápida
orientación.

###Aplicación de formularios Web Forms ASP.NET con autenticación federada

En este escenario de arquitecturas de aplicaciones, la aplicación web puede
implementarse en Azure o a nivel local. La aplicación requiere
que sus usuarios se autentiquen mediante proveedores de Active Directory corporativo o
identidades de Internet.

Para resolver este escenario, utilice el servicio de control de acceso de Azure AD y Windows
Identity Foundation.

![Azure Active Directory Access control][03]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Crear mi primera aplicación ASP.NET para notificaciones mediante ACS](http://msdn.microsoft.com/es-es/library/gg429779.aspx)
-   [Eliminación de Hospedar páginas de inicio de sesión en su aplicación web de ASP.NET](http://msdn.microsoft.com/es-es/library/gg185926.aspx)
-   [Eliminación de Implementar la autorización de notificaciones en una aplicación ASP.NET para notificaciones mediante WIF y ACS](http://msdn.microsoft.com/es-es/library/gg185907.aspx)    
-   [Eliminación de Implementación de control de acceso basado en roles (RBAC) en una aplicación
    ASP.NET para notificaciones mediante WIF y ACS](http://msdn.microsoft.com/es-es/library/gg185914.aspx)
-   [Eliminación de Configuración de la confianza entre aplicaciones web de ACS y ASP.NET
    Uso de certificados X.509](http://msdn.microsoft.com/es-es/library/gg185947.aspx)
-   [Code Sample: Formularios simples ASP.NET](http://msdn.microsoft.com/es-es/library/gg185938.aspx)

###Servicio WCF (SOAP) con identidad de servicio

En este escenario de arquitectura de aplicaciones, su servicio WCF (SOAP)
puede implementarse en Azure o a nivel local. Se tiene acceso al servicio
como servicio auxiliar mediante una una aplicación web o incluso otro servicio
web. Es preciso controlar el acceso a este servicio mediante la identidad específica
de la aplicación. Piense en el tipo de cuenta del grupo de aplicaciones que ha usado en
IIS; aunque la tecnología es diferente, los enfoques son similares
por el hecho de que al servicio se tiene acceso mediante una cuenta de ámbito
de aplicación frente a una cuenta de usuario final.

Utilice la característica de identidad de servicio en el servicio de control de acceso de Azure AD.
Esto es similar a la cuenta del grupo de aplicaciones de IIS que estaba utilizando cuando
implementó las aplicaciones en Windows Server e IIS. Configure el Control de acceso
AD para enviar tokens SAML que WIF administrará en el servicio
servicio WCF (SOAP).

![WCF (SOAP) Service][04]


Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Incorporación de identidades de servicio con un certificado X.509, contraseña
    o clave simétrica](http://msdn.microsoft.com/es-es/library/gg185924.aspx)
-   [Eliminación de Autenticación con un certificado de cliente para un servicio WCF
    protegido con ACS](http://msdn.microsoft.com/es-es/library/hh289316.aspx)
-   [Eliminación de Autenticación con un nombre de usuario y una contraseña para un servicio WCF
    protegido con ACS](http://msdn.microsoft.com/es-es/library/gg185954.aspx)
-   [Code Sample: Autenticación de certificados WCF](http://msdn.microsoft.com/es-es/library/gg185952.aspx)
-   [Code Sample: Autenticación de nombre de usuario de WCF](http://msdn.microsoft.com/es-es/library/gg185927.aspx)

###Servicio WCF (SOAP) con autenticación federada, identidades en Active Directory

En este escenario de arquitectura de aplicaciones, su servicio WCF (SOAP)
puede implementarse en Azure o a nivel local. Tendrá que controlar el acceso
mediante una identidad administrada por un servidor corporativo de Active Directory
de Windows Server.

Use el Control de acceso de Azure AD configurado para la federación con
Windows Server AD FS. En este caso no es necesario configurar la identidad del
servicio con el control de acceso de Azure AD. El agente que necesita
obtener acceso al servicio WCF (SOAP) proporciona credenciales a AD FS y, si la
autenticación se realiza correctamente, AD FS emite el token. El token se
se envía al Control de acceso de Azure AD y se vuelve a emitir en el
agente. El agente utiliza el token para enviar la solicitud al servicio WCF (SOAP)
.

![WCF (SOAP) Service With AD][05]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Incorporación de identidades de servicio con un certificado X.509, contraseña
    o clave simétrica](http://msdn.microsoft.com/es-es/library/gg185924.aspx)
-   [Eliminación de Configurar AD FS 2.0 como proveedor de identidades](http://msdn.microsoft.com/es-es/library/gg185961.aspx)
-   [Eliminación de Uso del servicio de administración para configurar AD FS 2.0 como un
    proveedor de identidades empresariales](http://msdn.microsoft.com/es-es/library/gg185905.aspx)
-   [Code Sample: Autenticación federada WCF con AD FS 2.0
](http://msdn.microsoft.com/es-es/library/hh127796.aspx)

###Servicio WCF (REST) con identidades de servicio

En este escenario se puede implementar el servicio WCF (REST) 
en Azure o a nivel local. Se tiene acceso al servicio como un servicio auxiliar mediante una
aplicación web u otro servicio web. Tendrá que controlar el acceso
mediante una identidad específica de la aplicación. Considérelo en términos del
tipo de cuenta del grupo de aplicación que se utilizó en IIS; aunque la tecnología
es diferente, los métodos son similares por el hecho de que se tiene acceso al servicio
mediante una cuenta de ámbito de aplicación frente a la cuenta de usuario final.

Utilice la característica de identidad de servicio en el servicio de control de acceso de Azure AD.
Configure el servicio de control de acceso de Azure AD para enviar tokens de web
simples (SWT). Para administrar el token SWT en el servicio REST, puede
implementar un controlador de token personalizado y conectarlo al canal WIF
, o bien puede analizarlo "manualmente" sin utilizar la infraestructura de
WIF.

Considere el diagrama siguiente (WIF es opcional):

![REST Service][06]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Configurar la confianza entre el servicio WCF y ACS mediante claves
    simétricas](http://msdn.microsoft.com/es-es/library/gg185958.aspx)
-   [Eliminación de Autenticación de un servicio WCF de REST implementado en Azure
    con ACS](http://msdn.microsoft.com/es-es/library/hh289317.aspx)
-   [Code Sample: ASP.NET Web Service](http://msdn.microsoft.com/es-es/library/gg983271.aspx)
-   [Code Sample: Windows Phone 7 Application](http://msdn.microsoft.com/es-es/library/gg983271.aspx)
-   [WCF de REST con el token de SWT emitido por el servicio de control de acceso de Azure
    (ACS)](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

###Servicio WCF (REST) con Live ID/cuenta de Microsoft, Facebook, Google, Yahoo!, Open ID

En este escenario se puede implementar el servicio WCF (REST) 
en Azure o a nivel local. Es preciso controlar el acceso a este servicio mediante una identidad de Internet
pública, como Live ID / Cuenta de Microsoft o Facebook.

Utilice el servicio de control de acceso de Azure AD para enviar tokens de SWT. Para controlar el
token SWT en el lado del servicio de REST, puede implementar un controlador de token personalizado
y conectarlo a una canalización WIF, o bien puede analizarlo "manualmente"
sin utilizar la infraestructura WIF.

Es importante tener en cuenta que para implementar este escenario, la
aplicación necesita utilizar el control del explorador web para recopilar las credenciales
del usuario final. No resulta conveniente para escenarios en los que al servicio de REST
se tiene acceso desde una aplicación web ASP.NET. Solo es conveniente para
escenarios en los que al servicio de REST se obtiene acceso mediante la aplicación de cliente
del usuario, como una aplicación Windows Phone 7 o un cliente de escritorio
enriquecido. El motivo principal para asumir el control del explorador web es que
las identidades de Internet no admiten de forma nativa los escenarios de perfiles activos
(escenario de servicios web). Las identidades de Internet admiten principalmente escenarios de perfil pasivo
(aplicaciones web) que se basan en las redirecciones del explorador: aquí es donde el control del
explorador web resulta práctico.

Considere el diagrama siguiente (WIF es opcional y, por tanto, no se muestra):

![WIF is optional][07]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Authenticate to a REST WCF Service Deployed to Azure Using ACS](http://msdn.microsoft.com/es-es/library/hh289317.aspx)
-   [Eliminación de Configurar Google como proveedor de identidades](http://msdn.microsoft.com/es-es/library/gg185976.aspx)
-   [Eliminación de Configurar Facebook como proveedor de identidades](http://msdn.microsoft.com/es-es/library/gg185919.aspx)
-   [Eliminación de Configurar Yahoo! como proveedor de identidades](http://msdn.microsoft.com/es-es/library/gg185977.aspx)
-  [Code Sample: Windows Phone 7 Application](http://msdn.microsoft.com/es-es/library/gg983271.aspx)
-   [WCF de REST con el token de SWT emitido por el servicio de control de acceso de Azure
    (ACS)](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)


###Aplicación web ASP.NET para el servicio REST WCF mediante el token SWT compartido

En este escenario tiene una aplicación distribuida con una aplicación web
ASP.NET front-end y un servicio REST auxiliar y desea mantener
el contexto del usuario final en niveles físicos. Esto a veces es necesario
para implementar la lógica de autorización o los registros según la identidad
del usuario final en el servicio de REST auxiliar.

Configure el servicio de control de acceso de Azure AD para enviar un token de SWT. El token
SWT se emite para la aplicación web ASP.NET front-end y, a continuación, se comparte con
el servicio REST auxiliar. En este caso, solo hay una entidad de confianza
configurada en el Control de acceso de Azure AD. Sin embargo, hay
varias advertencias:

-   Dado que WIF no proporciona un controlador de token SWT de fábrica,
    necesita implementar un controlador de tokens personalizado para su uso con la aplicación web
    de ASP.NET. Debe basarse en el trabajo pesado que WIF hace para
    admitir el protocolo WS-Federation que depende de redirecciones del explorador
    frente a lo que supone la implementación por parte del usuario.
-   Al implementar un controlador de token personalizado de SWT, asegúrese de que el
    token de arranque se aborda para garantizar su conservación.
    En caso contrario, no podrá compartirlo y enviarlo al
    servicio de REST auxiliar.
-   No tiene que utilizar WIF en un servicio de REST; en su lugar, puede analizar el
    el token "manualmente" ya que no es necesario controlar las redirecciones en
    en este caso.

![ASP.NET Web Application][08]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Configurar Google como proveedor de identidades](http://msdn.microsoft.com/es-es/library/gg185976.aspx)
-   [Eliminación de Configurar Facebook como proveedor de identidades](http://msdn.microsoft.com/es-es/library/gg185919.aspx)
-   [Eliminación de Configurar Yahoo! como proveedor de identidades](http://msdn.microsoft.com/es-es/library/gg185977.aspx)
-   [Aplicación web para ASP.NET para la delegación del servicio WCF de REST con el token de
    SWT](http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82)

###Control de acceso basado en roles (RBAC) en aplicaciones y servicios para notificaciones

En este escenario tendrá que implementar la autorización en la aplicación
web o servicio basado en roles de usuario: el usuario con roles necesarios obtiene
acceso y se rechazan aquellos que no tienen los roles necesarios. En pocas palabras,
la aplicación tiene que responder a esta simple pregunta: ¿tiene el usuario el rol
X?

Hay varias formas de resolver este escenario. Puede usar el
Control de acceso AD Azure, el Administrador de autenticación de notificaciones WIF,
la asignación samlSecurityTokenRequirement o el administrador de roles de cliente.

WIF se usa en todos los casos. WIF admite el método IPrincipal.IsInRole("MyRole")
. En la mayoría de los casos, la clave es asegurarse de que hay una notificación del tipo de
rol con la URI de
http://schemas.microsoft.com/ws/2008/06/identity/claims/role en el token,
de tal manera que WIF pueda verificar correctamente la pertenencia al rol cuando se llama al
método IsInRole.

**Servicio de control de acceso de Azure AD**. En esta implementación,
se utiliza el motor de reglas de transformación de notificaciones del Control de acceso AD Azure. Con la utilización
de las reglas del motor de reglas de transformación de notificaciones, puede transformar
cualquier notificación entrante en una notificación de tipo de rol, de tal manera que cuando
el token llega a la aplicación o a un servicio, WIF puede analizar esta notificación de rol
para asegurarse de que la llamada al método IsInRole se realiza correctamente.

![][09]

**ClaimsAuthenticationManager de WIF**. En esta implementación, use
ClaimsAuthenticationManager como punto de extensibilidad de WIF. Con este
enfoque, puede transformar cualquier notificación entrante arbitraria en una notificación de
rol en la aplicación. La complejidad de la transformación solo está
limitada por el código que escriba.

![][10]

**Asignación de samlSecurityTokenRequriement**. En esta implementación, use
la configuración de samlSecurityTokenRequirement en el archivo web.config para indicar a WIF
qué tipos de notificación se comportan como si fueran tipos de notificaciones de roles. Por ejemplo, .
Si el token lleva una notificación de tipo de grupo, puede asignarla a un tipo de
notificación de rol. Con este método solo pueden conseguir asignaciones simples.

![][11]

**RoleManager personalizado.** En esta implementación implementa un RoleManager
personalizado. WIF se utiliza para inspeccionar las notificaciones entrantes al implementar
métodos de interfaz de RoleManager personalizados, como GetAllRoles().

![][12]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Implementación de control de acceso basado en roles (RBAC) en una aplicación
    ASP.NET para notificaciones mediante WIF y ACS](http://msdn.microsoft.com/es-es/library/gg185914.aspx)
-   [Eliminación de Implementar la lógica de transformación de tokens mediante reglas](http://msdn.microsoft.com/es-es/library/gg185955.aspx)
-   [Autorización con RoleManager para aplicaciones web ASP.NET (WIF) de
    notificaciones](http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx)
-   Code Sample: Uso de notificaciones en IsInRole en [Windows Identity Foundation
    SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)

###Autorización basada en notificaciones en aplicaciones y servicios para notificaciones

En este escenario tendrá que implementar lógica de autorización compleja en
la aplicación web o servicio y el método IsInRole() no es
adecuado para satisfacer sus necesidades de autorización. Si su enfoque de autorización
se basa en roles, a continuación, considere la posibilidad de implementar el acceso basado en
roles que se ha descrito en la sección anterior.

Utilice ClaimsAuthorizationManager como punto de extensibilidad de WIF.
ClaimsAuthorizationManager permite las llamadas de comprobación de acceso externo para que
el código de la aplicación parezca más limpio y más fácil de mantener que cuando se
implementan las comprobaciones de acceso en el código de la aplicación.

![][13]

Consulte los recursos siguientes para implementar este escenario:

-   [Eliminación de Implementar la lógica de transformación de tokens mediante reglas](http://msdn.microsoft.com/es-es/library/gg185955.aspx)
-   [Eliminación de Implementación de autorización de notificaciones en una aplicación de ASP.NET
    para notificaciones con WIF y ACS](http://msdn.microsoft.com/es-es/library/gg185907.aspx)
-   Code Sample: Autorización basada en notificaciones en [Windows Identity
    Foundation SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)


##Escenarios de identidades y acceso del servicio de almacenamiento de Azure

Este escenario, necesita compartir de forma segura el acceso a contenedores y blobs
de almacenamiento de Azure.

Utilice las firmas de acceso compartido. Para obtener acceso a la cuenta de servicio de almacenamiento
desde su propia aplicación, utilice el hash compartido que está disponible a través del
Portal de Azure al configurar y administrar las cuentas del
servicio de almacenamiento. Cuando desee dar a otra persona acceso a los blobs y
contenedores en la cuenta de servicio de almacenamiento, use la URL de
firmas de acceso compartido.

Preste especial atención a la administración segura de la información para evitar su
exposición; además, preste especial atención a la vigencia de las firmas
de acceso compartido.

![][14]

Consulte los recursos siguientes para resolver este escenario:

-   [Administrar el acceso a los recursos de almacenamiento de Azure](http://msdn.microsoft.com/es-es/library/ee393343.aspx)
-   [New Storage Feature: Shared Access Signatures](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [Shared Access Signatures Are Easy These Days](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)


##Escenarios de identidades y acceso de Base de datos SQL de Azure

Base de datos SQL solo admite la autenticación de SQL Server. No se admite
la autenticación de Windows (seguridad integrada). Los usuarios deben
proporcionar credenciales (inicio de sesión y contraseña) cada vez que se conectan a
Base de datos SQL. Preste especial atención al administrar su nombre de usuario y
contraseña para evitar la divulgación de información.

![][15]

Consulte los recursos siguientes para resolver este escenario:

-   [Instrucciones y limitaciones de seguridad (Base de datos SQL de Azure)](http://msdn.microsoft.com/es-es/library/windowsazure/ff394108.aspx#authentication)
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante sqlcmd](http://msdn.microsoft.com/es-es/library/windowsazure/ee336280.aspx)
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ADO.NET](http://msdn.microsoft.com/es-es/library/windowsazure/ee336243.aspx)
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ASP.NET](http://msdn.microsoft.com/es-es/library/windowsazure/ee621781.aspx)
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante WCF Data Services](http://msdn.microsoft.com/es-es/library/windowsazure/ee621789.aspx)
-  [Direccionamiento del Conectar con Base de datos SQL de Azure mediante PHP](http://msdn.microsoft.com/es-es/library/windowsazure/ff394110.aspx)
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante JDBC](http://msdn.microsoft.com/es-es/library/windowsazure/gg715284.aspx)
-   [Direccionamiento del Conectar con Base de datos SQL de Azure mediante ADO.NET Entity Framework](http://msdn.microsoft.com/es-es/library/windowsazure/ff951633.aspx)

##Escenarios de identidades y acceso del bus de servicio de Azure

El Bus de servicio y el Control de acceso de Azure AD tienen una relación especial,
ya que cada espacio de nombres del Bus de servicio se enlaza con un espacio de nombres
del servicio de control de acceso del mismo nombre, con el sufijo
"-sb". El motivo por el que existe esta relación especial es la forma en que
el Bus de servicio y el Control de acceso administran su relación de confianza mutua
y los secretos criptográficos asociados. Consulte los
recursos siguientes para obtener más detalles.

![][16]

Consulte los recursos siguientes para resolver este escenario:

-   [Securing Service Bus with ACS](http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS) (Video)
-   [Securing Service Bus with ACS](https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849) (Diapositivas)
-   [Autenticación y autorización del Service Bus con el servicio de control
    de acceso](http://msdn.microsoft.com/es-es/library/hh403962.aspx)

##Escenarios de identidades y acceso de la caché en memoria

La caché en memoria (anteriormente conocida como caché de Azure) se basa en
el Control de acceso de Azure AD para autenticación. Utiliza las claves compartidas
disponibles a través del portal de administración. Utilice las claves del código o
los archivos de configuración al obtener acceso a la memoria caché. Asegúrese de almacenar las claves
de forma segura para evitar la divulgación de información.

![][17]


Consulte los recursos siguientes para resolver este escenario:

-   [Direccionamiento del Configuración de un cliente de caché mediante programación para Azure
    Almacenamiento en caché](http://msdn.microsoft.com/es-es/library/windowsazure/gg618003.aspx)
-   [Direccionamiento del Configuración de un cliente de caché usando el archivo de configuración de la aplicación
    para almacenamiento en caché de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/gg278346.aspx)
-   [Ejemplos de almacenamiento en caché y bus de servicio de Azure](http://msdn.microsoft.com/es-es/library/ee706741.aspx) sección (ejemplos de almacenamiento en
    caché)

##Escenarios de identidades y acceso de Azure Marketplace

Todo el acceso a un conjunto de datos de Azure Marketplace, ya sea gratuito o de pago,
debe autenticar al usuario antes de conceder el acceso. Cuando se
crea una aplicación, el proceso de autenticación debe incluirse en el
código. Considere los siguientes escenarios comunes:

###Yo obtengo acceso a mi conjunto de datos

En este escenario, se crea una aplicación que consume los conjuntos de datos
en su suscripción a Marketplace. Usted es el usuario de la aplicación.
La aplicación se puede implementar en Azure, en local o en Marketplace.

Utilice la clave compartida disponible a través de la suscripción
a Marketplace. Obtiene la clave compartida en el portal de Marketplace.

![][18]

Consulte los recursos siguientes para resolver este escenario:

-   [Implementar la autenticación básica HTTP en la aplicación de Marketplace](http://msdn.microsoft.com/es-es/library/gg193417.aspx)

###Los usuarios obtienen acceso a mis conjuntos de datos

En este escenario, se crea una aplicación que permite a los usuarios
obtener acceso a un conjunto de datos. La aplicación se puede implementar en Azure,
de forma local o en Marketplace.

Para resolver este escenario, utilice la delegación OAuth. Se solicitará a los usuarios
que proporcionen las credenciales de Live ID / Cuenta de Microsoft y, a continuación,
se someterán al proceso de consentimiento.

![][19]

Consulte los recursos siguientes para resolver este escenario:

-   [OAuth Web Client Example](http://go.microsoft.com/fwlink/?LinkId=219162)
-   [OAuth Rich Client Example](http://go.microsoft.com/fwlink/?LinkId=219163)

###La aplicación obtiene acceso a la API de Marketplace

En este escenario, se crea una aplicación que obtiene acceso a la API
de Marketplace. La API de Marketplace requiere autenticación para
realizar llamadas correctamente. La aplicación se implementa en
Azure Marketplace.

![][20]

Consulte el kit de publicación de Marketplace para saber sobre cómo
implementar la autenticación.

Consulte los recursos siguientes para resolver este escenario:

-   [Descargar el App Publishing Kit](http://go.microsoft.com/fwlink/?LinkId=221323)
-   [Introducción a Azure Marketplace para aplicaciones](https://datamarket.azure.com/)

##Mecanismos de seguridad

En esta sección se describen los mecanismos de seguridad para Windows Identity Foundation y
Control de acceso de Azure AD. Úselos como una lista de comprobación de seguridad básica
para estas tecnologías al diseñar e implementar la aplicación.

###Windows Identity Foundation

A continuación se indican los principales mecanismos de seguridad de WIF. La información siguiente es un
resumen de las [consideraciones de diseño de WIF](http://msdn.microsoft.com/es-es/library/ee517298.aspx) y [Windows Identity Foundation
(WIF) Seguridad para aplicaciones web de ASP.NET - Amenazas y contramedidas](http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx)
.

-   **IssuerNameRegistry**. Especifica los servicios de token de seguridad de confianza
    (STS). Asegúrese de que solo se muestran los STS de confianza.
-   **cookieHandler requireSsl="true"**. Especifica si las cookies de la sesión
    se transfieren a través del protocolo SSL.
-   **wsFederation's requireHttps="true"**. Especifica si la
    la comunicación del protocolo de federación con un proveedor de identidades se ha realizado
    a través del protocolo SSL.
-   **tokenReplayDetection enabled="true"**. Especifica si está habilitada la característica
    de detección de reproducción del token. Tenga en cuenta que esta función
    crea una afinidad de servidor ya que administra las copias locales de los tokens usados.
-   **audienceUris**. Especifica la audiencia prevista del token. Si su
    aplicación recibe un token que no estaba diseñado para su aplicación,
    WIF lo rechazará.
-   **requestValidation** y **httpRuntime requestValidationType**.
    Habilita/deshabilita la característica de validación ASP.NET. Consulte las instrucciones
    descritas en [Windows Identity Foundation (WIF): A Potentially
    Dangerous Request.Form Value Was Detected from the Client](http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx)

###Control de acceso de Azure AD

Considere los siguientes mecanismos de seguridad al implementar el servicio de control de
acceso de Azure AD. La información siguiente es un resumen de [Directrices de seguridad
de ACS](http://msdn.microsoft.com/es-es/library/gg185962.aspx) y [Directrices de administración de certificados y claves](http://msdn.microsoft.com/es-es/library/hh204521.aspx).

-   **Expiración de tokens de STS**. Use el portal de administración de
    control de acceso de Azure AD para definir la expiración del token agresivo.
-   **Validación de datos al utilizar la característica URL del error**. Azure
    La característica de dirección URL del error de control de acceso AD requiere el acceso anónimo a la
    página de la aplicación cuando envía mensajes de error. Suponga que todos los datos
    que llegan a esta página son peligrosos si proceden de fuentes que no son de confianza.
-   **Cifrado de tokens para escenarios extremadamente confidenciales**. Para mitigar
    la amenaza de divulgación de información que está disponible en el token,
    considere la posibilidad de cifrar los tokens.
-   **Cifrado de cookies mediante RSA al implementarlos en Azure**.
    De manera predeterminada WIF cifra las cookies mediante DPAPI. Crea la afinidad
    del servidor y puede producir excepciones cuando se implemente en la granja de servidores
    y en entornos de Azure. Use RSA en lugar de la granja web y 
    escenarios de Azure.
-   **Certificados de firma de tokens**. Renueve los certificados de firmas de tokens
    periódicamente para evitar la denegación de servicio. El control de acceso de Azure AD
    firma todos los tokens de seguridad que emite. Los certificados X.509
    se usan para firmar al compilar una aplicación que consume tokens de SAML
    emitidos por ACS. Cuando expiren los certificados de firma podrá
    recibe errores cuando intente solicitar un token.
-   **Claves de firma de tokens**. Renueve periódicamente las claves de firma de tokens para
    evitar la denegación de servicio. Control de acceso de Azure AD firma todos
    los tokens de seguridad que emite. Se utilizan las claves de firma simétricas de 256 bits
    cuando se crea una aplicación que consume tokens de SWT enviados por
    ACS. Cuando expiren las claves de firma, recibirá errores al intentar
    solicitar un token.
-   **Certificados de cifrado de tokens**. Renueve los certificados de cifrado de tokens
    para evitar la denegación de servicio. Se necesita el cifrado de tokens
    si una aplicación de usuario de confianza es un servicio web
    que usa tokens de prueba de posesión a través del protocolo WS-Trust,
    en otros casos, el cifrado de tokens es opcional. Cuando los certificados de cifrado
    expiran, recibirá errores al intentar realizar una solicitud de un
    token.
-   **Certificados de descifrado de tokens**. Renueve los certificados de cifrado de tokens
    periódicamente para evitar la denegación de servicio. Control de
    de acceso Azure AD puede aceptar tokens cifrados de los proveedores de identidades de
    WS-Federation (por ejemplo, AD FS 2.0). Se usa un certificado X.509
    hospedado en Control de acceso de Azure AD para el descifrado.
    Cuando expiren los certificados de descifrado, recibirá errores cuando
    intente solicitar un token.
-   **Credenciales de identidad de servicio**. Renueve las credenciales de identidad de servicio
    periódicamente para evitar la denegación de servicio. Las identidades de servicio usan
    credenciales configuradas globalmente para el espacio de nombres de Control de acceso de
    Azure AD que permiten que las aplicaciones o los clientes se
    autentiquen directamente con Control de acceso de Azure AD y
    reciban un token. Hay tres tipos de credenciales con las que la identidad
    del servicio Control de acceso de Azure AD se puede asociar: clave simétrica
    , contraseña y certificado X.509. Empezará a recibir
    excepción cuando expiren las credenciales.
-   **Credenciales de la cuenta del servicio de administración del control de acceso
     de Azure AD.**. Renueve las credenciales del servicio de administración periódicamente para
    evitar la denegación de servicio. El servicio de administración del
    Control de acceso de Azure AD es un componente clave que le permite
    administrar y configurar mediante programación el 
    espacio de nombres del Control de acceso de Azure AD. Hay tres tipos de credenciales con las que el
    la cuenta del servicio de administración se puede asociar. Son
    la clave simétrica, la contraseña y un certificado X.509. Empezará a
    recibir una excepción cuando las credenciales caduquen.
-   **Certificados de cifrado y de firma del proveedor de identidades
    de WS-Federation**. Consulte la validez del certificado del proveedor de identidades de WS-Federation
    para evitar la denegación de servicio. El certificado del proveedor de identidades de WS-Federation
    está disponible a través de sus metadatos.
    Al configurar el proveedor de identidades de WS-Federation, como AD FS, el certificado
    de firma de WS-Federation se configura a través de los metadatos
    de WS-Federation disponibles a través de una dirección URL o como un archivo. Después de haber configurado el
    proveedor de identidades de WS-Federation, use el servicio de administración del
    control de acceso de Azure AD para consultar la validez de sus
    certificados. Cuando expire el certificado, comenzará a recibir
    excepciones.

##Hospedaje compartido usando Sitios web Azure

Todos los escenarios y soluciones descritos en este tema son válidos cuando la
aplicación está hospedada en Sitios web de Azure.

##Máquinas virtuales de Azure

Todos los escenarios y soluciones descritos en este tema son válidos cuando la
aplicación se hospeda en Máquinas virtuales de Azure.

##Recursos

-   [Identity Developer Training Kit](http://go.microsoft.com/fwlink/?LinkId=214555)
-   [MSDN-hosted Identity Developer Training Course](http://go.microsoft.com/fwlink/?LinkId=214561)
-   [A Guide to Claims-based Identity and Access Control](http://go.microsoft.com/fwlink/?LinkId=214562)
-   [Access Control Service](http://msdn.microsoft.com/es-es/library/windowsazure/gg429786.aspx)
-   [Procedimientos de ACS](http://msdn.microsoft.com/es-es/library/windowsazure/gg185939.aspx)
-   [Secure Azure Web Role ASP.NET Web Application Using Access Control Service v2.0](http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx)
-   [Azure AD Access Control Service (ACS) Academy Videos](http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx)
-   [Microsoft Security Development Lifecycle](http://www.microsoft.com/security/sdl/default.aspx)
-   [SDL Threat Modeling Tool 3.1.8](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955)
-   [Security and Privacy Blogs](http://www.microsoft.com/about/twc/en/us/blogs.aspx)
-   [Security Response Center](http://www.microsoft.com/security/msrc/default.aspx)
-   [Security Intelligence Report](http://www.microsoft.com/security/sir/)
-   [Security Development Lifecycle](http://www.microsoft.com/security/sdl/default.aspx)
-   [Security Developer Center (MSDN)](http://msdn.microsoft.com/security/)


[01]:./media/SecurityRX/01_SecuringTheApplication.gif
[02]:./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
[03]:./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
[04]:./media/SecurityRX/04_WCF(SOAP)Service.gif
[05]:./media/SecurityRX/05_AzureADAccessControl.gif
[06]:./media/SecurityRX/06_RESTService.gif
[07]:./media/SecurityRX/07_WIFisOptional.gif
[08]:./media/SecurityRX/08_ASPNETWebApptoREST.gif
[09]:./media/SecurityRX/09_RBAC.gif
[10]:./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
[11]:./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
[12]:./media/SecurityRX/12_CustomRoleManager.gif
[13]:./media/SecurityRX/13_ClaimsAuthorizationManager.gif
[14]:./media/SecurityRX/14_WindowsAzurestorage.gif
[15]:./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
[16]:./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
[17]:./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
[18]:./media/SecurityRX/18_IAccessMyDataset.gif
[19]:./media/SecurityRX/19_UsersAccessMyDatasets.gif
[20]:./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif

[Web SSO Design]: http://technet.microsoft.com/es-es/library/dd807033(WS.10).aspx
[Federated Web SSO Design]: http://technet.microsoft.com/es-es/library/dd807050(WS.10).aspx
