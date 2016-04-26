<properties
	pageTitle="Introducción a Azure Active Directory Premium"
	description="Un tema que explica cómo suscribirse a la edición Azure Active Directory Premium."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo" 
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="markvi"/>

# Introducción a Azure Active Directory Premium

Azure Active Directory viene en tres ediciones: Premium, Básico y Gratis. La edición Gratis se incluye con una suscripción de Azure u Office 365. Las ediciones Básico y Premium están disponibles a través de un [contrato Enterprise de Microsoft](https://www.microsoft.com/es-ES/licensing/licensing-programs/enterprise.aspx) o el programa [Licencias por volumen abiertas](https://www.microsoft.com/es-ES/licensing/licensing-programs/open-license.aspx). Los suscriptores de Azure y Office 365 también pueden comprar Active Directory Premium en línea. [Inicie sesión aquí](https://portal.office.com/Commerce/Catalog.aspx) para comprarlo.

> [AZURE.NOTE]
Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Azure Active Directory Premium también se incluye en **Enterprise Mobility Suite**. Enterprise Mobility Suite constituye una manera rentable de que las organizaciones usen los siguientes servicios juntos en un mismo plan de licencias:

- Active Directory Premium 
- Azure Rights Management
- Microsoft Intune


Para obtener más información, consulte el sitio web de [Enterprise Mobility Suite](https://www.microsoft.com/es-ES/server-cloud/enterprise-mobility/overview.aspx).

Para empezar a usar las características de Azure Active Directory Premium ya, siga los pasos descritos en las siguientes secciones. Se aplican los mismos pasos a la edición básica de Azure Active Directory.

## Paso 1: Suscripción de Azure Active Directory Premium y Básico

Para suscribirse, consulte el sitio web de [Licencias por volumen](http://www.microsoft.com/es-ES/licensing/how-to-buy/how-to-buy.aspx).

## Paso 2: Activación del plan de licencias

Cuando termine la primera compra del plan de licencias a través del programa de licencias por volumen de Enterprise de Microsoft, recibirá un correo electrónico de confirmación. Necesita este correo electrónico para activar el primer plan de licencias. En las compras posteriores para este directorio, las licencias se activarán automáticamente en el mismo directorio.

Para iniciar la activación, haga clic en **Inicio de sesión** o en **Suscribirse**.


![][1]

Si tiene un inquilino existente, haga clic en **Inicio de sesión** para iniciar sesión con la cuenta de administrador existente. Es importante que inicie sesión con las credenciales de administrador global desde el directorio donde se deben activar las licencias.

Si desea crear un nuevo inquilino de Azure Active Directory para usar con el plan de licencias, haga clic en **Suscribirse** para abrir el cuadro de diálogo **Crear perfil de cuenta**.

![][2]

Cuando haya terminado, aparecerá el cuadro de diálogo siguiente como confirmación para la activación del plan de licencias para el inquilino.

![][3]

## Paso 3: Activación de acceso de Azure Active Directory

Cuando las licencias se aprovisionan a su directorio, se le envía un **correo electrónico de bienvenida**. El correo electrónico confirma que puede empezar a administrar las licencias y características de Azure Active Directory Premium o Enterprise Mobility Suite.

Si ha usado Microsoft Azure antes, puede ir a [http://manage.windowsazure.com](http://manage.windowsazure.com) para asignar las licencias nuevas (consulte el [Paso 4](#step-4-assign-license-to-user-accounts) para más información).

Si no está familiarizado con Microsoft Azure, puede hacer clic en **Inicio de sesión** en el correo electrónico o ir a la [página de activación de Acceso a Azure Active Directory](https://account.windowsazure.com/signup?offer=MS-AZR-0110P). Ambos métodos le llevarán a través de una serie de pasos que le ayudarán a acceder a su directorio a través del Portal de Azure clásico.

![][4]

Una vez que haya iniciado sesión correctamente, será necesario completar una pantalla de autenticación de segundo factor proporcionando un número de teléfono móvil y validándolo. Cuando haya completado la comprobación de dispositivos móviles, podrá activar el Acceso a Azure Active Directory haciendo clic en **Suscribirse**.

![][5]

La activación puede tardar unos minutos. Una vez que el acceso esté activo, la barra marrón desaparecerá y podrá hacer clic en **Portal**.

![][6]

En este caso, el acceso a Azure se limita a Azure Active Directory.

![][7]

Es posible que ya haya obtenido acceso a Azure previamente. Además, puede actualizar su acceso a Azure Active Directory a acceso de Azure completo mediante la activación de suscripciones de Azure adicionales. En estos casos, el Portal de Azure clásico tiene más funcionalidades.

![][8]

Si intenta activar el acceso a Azure Active Directory antes de recibir el correo electrónico de bienvenida, puede experimentar el siguiente mensaje de error. Vuelva a intentarlo en unos minutos una vez haya recibido el correo electrónico.

![][9]

Los nuevos administradores de su suscripción también pueden activar su acceso al Portal de Azure clásico a través de este vínculo.

## Paso 4: Asignación de licencias a cuentas de usuario

Antes de empezar a utilizar el plan que compró, debe asignar manualmente licencias a cuentas de usuario dentro de su organización para que puedan usar las características enriquecidas que se proporcionan con Premium. Utilice los pasos siguientes para asignar licencias a los usuarios para que puedan usar las características de Azure Active Directory Premium.

Para asignar licencias a los usuarios:

1. Inicie sesión en el Portal de Azure clásico como el administrador global del directorio que desea personalizar.
2. Haga clic en **Active Directory** y, a continuación, seleccione el directorio donde desee asignar licencias.
3. Seleccione la pestaña **Licencias**, seleccione **Active Directory Premium** o **Enterprise Mobility Suite** y, a continuación, haga clic en **Asignar**.

    ![][10]

4. En el cuadro de diálogo, seleccione los usuarios a los que desee asignar las licencias y, a continuación, haga clic en el icono de marca de verificación para guardar los cambios.

    ![][11]

## Restricciones de licencia

Algunos planes de licencias son subconjuntos o superconjuntos de otros planes de licencias. Normalmente, un usuario no se puede asignar a un plan de licencias ya asignado a ellos. Si tiene pensado asignar un plan de licencias que es un superconjunto, deberá quitar primero el plan de licencias de subconjunto.

## Requisitos de licencia

Al asignar una licencia a un usuario, puede especificar una ubicación de uso principal en las propiedades de su cuenta. Si no se especifica una ubicación de uso, la ubicación del inquilino se asigna automáticamente al usuario.

![][12]

La disponibilidad de servicios y características de un servicio en la nube de Microsoft varía según el país o la región. Un servicio, como Voz sobre IP (VoIP), puede estar disponible en un país o región y no disponible en otro. Las características dentro de un servicio pueden estar restringidas por motivos legales en determinados países o regiones. Para ver si un servicio o una característica está disponible con o sin restricciones, busque su país o región en el sitio de restricciones de licencia de un servicio.

## Pasos siguientes

- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
- [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

<!---HONumber=AcomDC_0420_2016-->