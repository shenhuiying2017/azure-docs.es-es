<properties
	pageTitle="¿Qué es la suscripción de autoservicio de Azure? | Microsoft Azure"
	description="Información general de la suscripción de autoservicio de Azure: cómo administrar el proceso de suscripción y adquirir un nombre de dominio DNS."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/23/2016"
	ms.author="curtand"/>


# ¿Qué es la suscripción de autoservicio de Azure?

En este tema se explica el proceso de registro de autoservicio y el modo de adquirir un nombre de dominio DNS.

## Razones para usar la suscripción de autoservicio

- Permita que los usuarios tengan acceso a los servicios que desean más rápido.
- Cree ofertas basadas en correo electrónico para un servicio.
- Cree flujos de suscripción basados en correo electrónico que permitan a los usuarios crear identidades rápidamente con sus alias de correo electrónico del trabajo fáciles de recordar.
- Los directorios de Azure no administrados se pueden convertir en directorios administrados más adelante y reutilizarse para otros servicios.

## Términos y definiciones

+ **Registro de autoservicio**: método por el que un usuario se registra en un servicio en la nube y por el que se le crea automáticamente una identidad en Azure Active Directory (AD) basada en su dominio de correo electrónico.
+ **Directorio de Azure no administrado**: directorio donde se crea la identidad. Un directorio no administrado es un directorio que no tiene administrador global.
+ **Usuario comprobado por correo electrónico**: tipo de cuenta de usuario en Azure AD. Un usuario que tiene una identidad que se crean automáticamente después de suscribirse a una oferta de autoservicio se conoce como usuario comprobado por correo electrónico. Un usuario comprobado por correo electrónico es un miembro regular de un directorio etiquetado con creationmethod = EmailVerified.

## Experiencia del usuario

Por ejemplo, supongamos que un usuario cuyo correo electrónico es Dan@BellowsCollege.com recibe archivos confidenciales por correo electrónico. Los archivos se han protegido con Azure Rights Management (Azure RMS). Pero la organización de Dan, Bellows College, no se ha suscrito a Azure RMS ni ha implementado Active Directory RMS. En este caso, Dan puede obtener una suscripción gratuita de RMS para usuarios a fin de leer los archivos protegidos.

Si Dan es el primer usuario con una dirección de correo electrónico de BellowsCollege.com en registrarse en esta oferta de autoservicio, se creará un directorio no administrado para BellowsCollege.com en Azure AD. Si otros usuarios del dominio BellowsCollege.com se registran en esta oferta o una oferta de autoservicio similar, también se crearán para ellos cuentas de usuario verificadas por correo electrónico en el mismo directorio no administrado en Azure.

## Experiencia del administrador

Un administrador que posee el nombre de dominio DNS de un directorio de Azure no administrado puede adquirir o fusionar el directorio después de demostrar la propiedad. En las secciones siguientes se explica con más detalle la experiencia del administrador, pero a continuación se incluye un resumen:

- Al adquirir un directorio de Azure no administrado, se convierte en administrador global del directorio no administrado. Esto se denomina a veces adquisición interna.
- Al fusionar un directorio de Azure no administrado, agregue el nombre de dominio DNS del directorio no administrado a su directorio de Azure administrado; se creará una asignación de usuarios a recursos para que los usuarios puedan seguir teniendo acceso a los servicios sin interrupción. Esto se denomina a veces adquisición externa.

## ¿Qué se crea en Azure Active Directory?

#### Directorio

- Se crea un directorio de Azure Active Directory para el dominio, un directorio por dominio.
- El directorio de Azure AD no tiene ningún administrador global.

#### Usuarios

- Para cada usuario que se registra, se crea un objeto de usuario en el directorio de Azure AD.
- Cada objeto de usuario se marca como externo.
- Cada usuario tiene acceso al servicio en el que se ha registrado.

### ¿Cómo puedo reclamar un directorio de Azure AD de autoservicio para un dominio de mi propiedad?

Puede reclamar un directorio de Azure AD de autoservicio realizando la validación del dominio. La validación del dominio demuestra que es el propietario del dominio mediante la creación de registros DNS.

Hay dos formas de realizar una adquisición de DNS de un directorio de Azure AD:

- Adquisición interna (el administrador detecta un directorio de Azure no administrado y desea convertirlo en un directorio administrado).
- Adquisición externa (el administrador intenta agregar un nuevo dominio a su directorio de Azure administrado).

Puede que le interese validar que posee un dominio, porque va a adquirir un directorio no administrado después de que un usuario realice un registro de autoservicio, o bien puede que quiera agregar un dominio nuevo a un directorio administrado existente. Por ejemplo, tiene un dominio denominado contoso.com y desea agregar un nuevo dominio denominado contoso.co.uk o contoso.uk.

## ¿Qué es la adquisición de un dominio?  

En esta sección se describe cómo validar la propiedad de su dominio.

### ¿Qué es la validación de dominios y por qué se usa?

Para realizar operaciones en un directorio, Azure AD necesita que valide la propiedad del dominio DNS. La validación del dominio permite reclamar el directorio y promover el directorio de autoservicio a un directorio administrado o fusionar el directorio de autoservicio en un directorio administrado existente.

## Ejemplos de validación de dominio

Hay dos formas de realizar una adquisición de DNS de un directorio:

+ Adquisición interna (por ejemplo, un administrador detecta un directorio no administrado de autoservicio y desea convertirlo en un directorio administrado).
+ Adquisición externa (por ejemplo, un administrador intenta agregar un nuevo dominio a un directorio administrado).

### Adquisición interna: promueva un directorio no administrado de autoservicio a un directorio administrado.

Al realizar una adquisición interna, el directorio pasa de ser un directorio no administrado a un directorio administrado. Debe completar la validación del nombre de dominio DNS, donde crea un registro MX o un registro TXT en la zona DNS. Esa acción:

+ Valida la propiedad de su dominio
+ Hace que el directorio sea administrado
+ Le convierte en administrador global del directorio

Supongamos que un administrador de TI de Bellows College detecta que los usuarios de la escuela se han suscrito a ofertas de autoservicio. Como propietario registrado del nombre DNS BellowsCollege.com, el administrador de TI puede validar la propiedad del nombre DNS en Azure y adquirir el directorio no administrado. A continuación, el directorio se convierte en un directorio administrado y al administrador de TI se le asigna el rol de administrador global para el directorio BellowsCollege.com.

### Adquisición externa: fusión de un directorio de autoservicio en un directorio administrado existente

En una adquisición externa, ya tiene un directorio administrado y desea que todos los usuarios y grupos de un directorio no administrado se unan a ese directorio administrado en lugar de poseer dos directorios independientes.

Como administrador de un directorio administrado, se agrega un dominio que tiene asociado un directorio no administrado.

Por ejemplo, suponga que es administrador de TI y ya tiene un directorio administrado para Contoso.com, un nombre de dominio registrado para su organización. Descubre que los usuarios de su organización han realizado una suscripción de autoservicio a una oferta con un nombre de dominio de correo electrónico user@contoso.co.uk, que es otro nombre de dominio que posee su organización. Actualmente, esos usuarios tienen cuentas en un directorio no administrado para contoso.co.uk.

Usted no desea administrar dos directorios independientes, por lo que fusiona el directorio no administrado para contoso.co.uk en su directorio administrado de TI existente para contoso.com.

La adquisición externa sigue el mismo proceso de validación de DNS que la adquisición interna. La diferencia es que se reasignan los usuarios y servicios al directorio administrado de TI.

#### ¿Cuál es el impacto de realizar una adquisición externa?

Con una adquisición externa, se crea una asignación de usuarios a recursos para que los usuarios sigan teniendo acceso a los servicios sin interrupción. Muchas aplicaciones, como RMS para usuarios, administran la asignación de usuarios a los recursos bien y los usuarios pueden seguir teniendo acceso a los servicios sin cambios. Si una aplicación no administra la asignación de usuarios a recursos de forma eficaz, es posible que se bloquee la adquisición externa explícitamente para evitar una experiencia deficiente de los usuarios.

#### Admisión de la adquisición del directorio por servicio

Actualmente, los servicios siguientes admiten la adquisición:

- RMS


Los siguientes servicios admitirán la adquisición próximamente:

- PowerBI

Los siguientes servicios no la admiten y requieren una acción adicional del administrador para migrar datos de usuario después de una adquisición externa.

- SharePoint/OneDrive


## Realización de una adquisición de nombre de dominio DNS

Tiene unas cuantas opciones para realizar una validación de dominio (y realizar una adquisición si lo desea):

1.  Portal de administración de Azure

	Una adquisición se desencadena agregando un dominio. Si ya existe un directorio para el dominio, tendrá la opción de realizar una adquisición externa.

	Inicio de sesión en el portal de Azure con credenciales Vaya al directorio existente y, después, a **Agregar dominio**.

2.  Office 365

	Puede usar las opciones de la [Administrar dominios](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) en Office 365 para trabajar con sus dominios y registros DNS. Consulte [Comprobar el dominio en Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

	Los pasos siguientes son necesarios para realizar una validación con Windows PowerShell.

	Paso |	Cmdlet que se debe usar
	-------	| -------------
	Crear un objeto de credencial | Get-Credential
	Conectarse a Azure | Connect-MsolService
	Obtener una lista de dominios | Get-MsolDomain
	Crear un desafío | Get-MsolDomainVerificationDns
	Crear un registro DNS | Realice esta acción en su servidor de DNS
	Comprobar el desafío | Confirm-MsolEmailVerifiedDomain

Por ejemplo:

1. Conéctese a Azure AD con las credenciales que se usaron para responder a la oferta de autoservicio: import-module MSOnline $msolcred = get-credential connect-msolservice -credential $msolcred

2. Obtenga una lista de dominios:

	Get-MsolDomain

3. A continuación, ejecute el cmdlet Get-MsolDomainVerificationDns para crear un desafío:

	Get-MsolDomainVerificationDns –DomainName *nombre\_de\_dominio* –Mode DnsTxtRecord

	Por ejemplo:

	Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord

4. Copie el valor (el desafío) que se devuelve desde este comando.

	Por ejemplo:

	MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. En el espacio de nombres DNS público, cree un registro txt de DNS que contenga el valor que copió en el paso anterior.

	El nombre de este registro es el nombre del dominio principal, por lo que si crea este registro de recursos con el rol DNS desde Windows Server, deje el nombre del registro en blanco y pegue el valor en el cuadro de texto.

6. Ejecute el cmdlet Confirm-MsolDomain para comprobar el desafío:

	Confirm-MsolEmailVerifiedDomain -DomainName *nombre\_de\_dominio*

	Por ejemplo:

	Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

Un desafío correcto le devuelve el mensaje sin errores.

## ¿Cómo controlo la configuración de autoservicio?

Actualmente, los administradores tienen dos controles de autoservicio . Pueden controlar:

- Si los usuarios pueden unirse o no al directorio mediante el correo electrónico.
- Si los usuarios pueden concederse o no licencias para aplicaciones y servicios.


### ¿Cómo puedo controlar estas capacidades?

Un administrador puede configurar estas capacidades con estos parámetros Set-MsolCompanySettings de cmdlet de Azure AD:

+ **AllowEmailVerifiedUsers** controla si un usuario puede crear o unirse a un directorio no administrado. Si establece este parámetro en $false, ningún usuario comprobado por correo electrónico se puede unir al directorio.
+ **AllowAdHocSubscriptions** controla la capacidad de los usuarios de realizar suscripciones de autoservicio. Si establece el parámetro en $false, ningún usuario puede realizar suscripciones de autoservicio.


### ¿Cómo funciona los controles conjuntamente?

Estos dos parámetros se pueden usar juntos para definir un control más preciso de la suscripción de autoservicio. Por ejemplo, el comando siguiente permitirá a los usuarios realizar suscripciones de autoservicio pero solo si estos usuarios ya tienen una cuenta en Azure AD (en otras palabras, los usuarios que necesitan crear una cuenta comprobada por correo electrónico no pueden realizar suscripciones de autoservicio):

	Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

En el siguiente diagrama se explican las distintas combinaciones de estos parámetros y las condiciones resultantes para el directorio y el registro de autoservicio.

![][1]

Para obtener más información y ejemplos de cómo usar estos parámetros, consulte [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## Otras referencias

-  [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

<!---HONumber=AcomDC_0824_2016-->