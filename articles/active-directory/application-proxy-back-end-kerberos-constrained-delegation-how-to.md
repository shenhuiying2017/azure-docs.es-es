---
title: "Configuración de una aplicación de proxy de aplicación para que use la delegación limitada de Kerberos | Microsoft Docs"
description: "Cómo configurar la delegación limitada de Kerberos para una aplicación de proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3a768c30cb874d42d7b4fbd2eeaa6c0e23904e10
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-configure-an-application-proxy-application-to-use-kerberos-constrained-delegation"></a>Configuración de una aplicación de proxy de aplicación para que use la delegación limitada de Kerberos

Los métodos disponibles para lograr el SSO en las aplicaciones publicadas puede variar ligeramente de una aplicación a otra y una de las opciones que el proxy de aplicación de Azure ofrece desde el principio es la delegación limitada de Kerberos (KCD). Esta consiste en un host de conector que está configurado para realizar la autenticación limitada de Kerberos en aplicaciones back-end en nombre de los usuarios.

El procedimiento para habilitar KCD es relativamente sencillo y no suele requerir más que una comprensión general de los diferentes componentes y del flujo de autenticación que hace posible el SSO. Escasean las buenas fuentes de información para ayudar a solucionar problemas en los que el SSO de KCD no funcione según lo previsto.

Por lo tanto, este artículo intenta proporcionar un único punto de referencia que debería ayudar a solucionar problemas y corregir algunos de los problemas más habituales. Al mismo tiempo, se ofrecen más instrucciones para diagnosticar las implementaciones más complejas y con mayores problemas.

Tenga en cuenta que en este artículo se da por supuesto lo siguiente:

-   Se ha implementado el proxy de aplicación de Azure según nuestra [documentación](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) y el acceso general a aplicaciones sin KCD funciona según lo previsto.

-   La aplicación de destino publicada se basa en IIS y la implementación de Kerberos de Microsoft.

-   Los hosts de servidor y aplicación residen en un único dominio de Active Directory. Encontrará información detallada sobre los escenarios de bosque y entre dominios en nuestras [notas del producto para KCD](http://aka.ms/KCDPaper).

-   Se publica la aplicación en cuestión en un inquilino de Azure con la autenticación previa habilitada y se espera que los usuarios se autentiquen en Azure mediante la autenticación basada en formularios. No se tratan los escenarios de autenticación de cliente compleja en este artículo, pero se agregarán en el futuro.

## <a name="prerequisites"></a>Requisitos previos

El proxy de aplicación de Azure se puede implementar en prácticamente cualquier tipo de entorno o infraestructura y, seguramente, las arquitecturas varían de una organización a otra. Una de las causas más habituales de los problemas relacionados con KCD no son los entornos en sí, sino simples errores de configuración o descuidos en general.

Por esta razón, se aconseja empezar siempre comprobando que se cumplan todos los requisitos previos explicados en el artículo principal [sobre el uso del SSO de KCD con el proxy de aplicación](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) antes de empezar a intentar solucionar los problemas.

En particular, la sección sobre la configuración de KCD en 2012R2, ya que emplea un enfoque esencialmente diferente a la configuración de KCD en versiones anteriores de Windows, sin dejar de tener en cuenta otras consideraciones:

-   No es raro que un servidor miembro del dominio abra un cuadro de diálogo de canal seguro con un controlador de dominio específico y más adelante cambie a otro en un momento dado. Por tanto, en general, los hosts de conector no deberían restringirse a la comunicación exclusiva con controladores de dominio de sitio locales específicos.

-   De forma semejante al punto anterior, los escenarios entre dominios se basan en referencias que dirigen a un host de conector a controladores de dominio que pueden residir fuera del perímetro de la red local. En este escenario, es igualmente importante asegurarse de permitir también al tráfico hacia controladores de dominio que representen otros dominios para evitar que la delegación genere un error.

-   Siempre que sea posible, debe evitar colocar dispositivos IPS/IDS activos entre los hosts de conector y los controladores de dominio, ya que en ocasiones son demasiado intrusivos e interfieren con el tráfico RPC central.

Aunque lo ideal sería solucionar los problemas de forma rápida y eficiente, esto puede llevar tiempo, así que, en la medida de lo posible, debería probar la delegación en los escenarios más sencillos. Cuantas más variables introduzca, con más tendrá que trabajar. Por ejemplo, si limita las pruebas a un solo conector, puede ahorrar valioso tiempo, y puede agregar más conectores una vez resueltos los problemas.

Algunos factores del entorno también podrían estar contribuyendo a un problema, así que intente reducir la arquitectura al mínimo para las pruebas. Por ejemplo, las ACL de firewall interno mal configuradas son habituales, así que, si es posible, permita que todo el tráfico procedente de un conector vaya directamente a los controladores de dominio y la aplicación back-end. 

En realidad, el mejor lugar donde colocar conectores es lo más cerca posible de sus destinos. Tener un firewall en línea durante las pruebas simplemente agrega complejidad innecesaria y podría prolongarlas.

De todos modos, ¿qué constituye un problema de KCD? Hay varias indicaciones clásicas de que existen problemas con el SSO de KCD y los primeros signos normalmente se manifiestan en el explorador.

   ![Error de configuración de KCD incorrecta](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Error de autorización por falta de permisos](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Todos ellos manifiestan el mismo síntoma: no se puede llevar a cabo el SSO y, por consiguiente, se deniega el acceso del usuario a la aplicación.

## <a name="troubleshooting"></a>Solución de problemas

Cómo solucione cada problema depende del problema y los síntomas observados. Antes de continuar, se recomienda seguir estos vínculos, ya que contienen información útil que puede que desconozca:

-   [Solución de problemas y mensajes de error de Proxy de aplicación](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Errores y síntomas de Kerberos](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [Trabajar con SSO cuando las identidades locales y en la nube no son idénticas](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

Si ha llegado hasta aquí, el principal problema definitivamente existe. Debe investigar un poco más; para ello, empiece separando el flujo en tres fases distintas cuyos problemas pueda solucionar.

**Autenticación previa del cliente**: el usuario externo se autentica en Azure mediante un explorador.

Es fundamental poder realizar la autenticación previa en Azure para que el SSO de KCD funcione. Esto es lo primero que se debe probar y solucionar, si hay algún problema. Tenga en cuenta que la fase de autenticación previa no guarda relación con KCD ni con la aplicación publicada. Debería ser bastante fácil corregir cualquier discrepancia con una comprobación de que la cuenta en cuestión exista en Azure y de que no esté deshabilitada ni bloqueada. La respuesta de error en el explorador suele ser lo bastante descriptiva como para entender la causa. También puede leer la documentación sobre solución de problemas como referencia si no está seguro.

**Servicio de delegación**: el conector del proxy de Azure obtiene un vale de servicio de Kerberos de un KDC (Centro de distribución de claves Kerberos), en nombre de los usuarios.

Las comunicaciones externas entre el cliente y el front-end de Azure no deberían guardar ninguna relación con KCD, aparte de asegurarse de que funcione. Esto es para que se pueda proporcionar al servicio de proxy de Azure un identificador de usuario válido que se usará para obtener un vale de Kerberos. Sin este KCD, no es posible y se produciría un error.

Como se mencionó antes, los mensajes de error del explorador suelen proporcionan buenos indicios de por qué se producen errores. Asegúrese de anotar el identificador de actividad y la marca de tiempo en la respuesta, ya que esto permite correlacionar el comportamiento con eventos reales en el registro de eventos del proxy de Azure.

   ![Error de configuración de KCD incorrecta](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Las entradas correspondientes en el registro de eventos se verían como los eventos 13019 o 12027. Puede encontrar los registros de eventos del conector en **Registros de aplicaciones y servicios** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Conector**&gt;**Administración**.

   ![Evento 13019 del registro de eventos del proxy de aplicación](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Evento 12027 del registro de eventos del proxy de aplicación](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Use un registro D en su servicio DNS interno para la dirección de la aplicación, en lugar de un registro CName.

-   Vuelva a confirmar que se han concedido al host de conector los derechos para delegar en el SPN de la cuenta de destino designada y que se ha seleccionado **Usar cualquier protocolo de autenticación**. Esto se trata en el artículo principal [sobre la configuración del SSO](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd).

-   Compruebe que solo haya una instancia del SPN en AD; para ello, emita **setspn - x** desde un símbolo del sistema en cualquier host miembro de dominio.

-   Compruebe si se está aplicando una directiva de dominio para limitar el [tamaño máximo de tokens de Kerberos emitidos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/), ya que esto impide que el conector obtenga un token si se considera excesivo.

El siguiente paso que se recomienda para más información de bajo nivel sobre los problemas es un seguimiento de red que capture los intercambios entre el host de conector y un KDC de dominio. Puede encontrarlo en el [artículo que profundiza en la solución de problemas](https://aka.ms/proxytshootpaper).

Si la generación de vales no presenta problemas, debería ver un evento en los registros que indique que se produjo un error en la autenticación porque la aplicación devolvió un error 401. Esto suele indicar que la aplicación de destino rechaza el vale; por tanto, continúe con la siguiente fase.

**Aplicación de destino**: el consumidor del vale de Kerberos proporcionado por el conector.

En esta fase se espera que el conector haya enviado un vale de servicio de Kerberos al back-end, como encabezado de la primera solicitud de aplicación.

-   Utilizando la dirección URL interna de la aplicación definida en el portal, valide que la aplicación sea accesible directamente desde el explorador en el host de conector. Entonces, puede iniciar sesión correctamente. Encontrará detalles sobre cómo hacerlo en la página de solución de problemas del conector.

-   Todavía en el host de conector, confirme que la autenticación entre el explorador y la aplicación esté utilizando Kerberos, mediante una de las siguientes acciones:

1.  Ejecute Herramientas de desarrollo (**F12**) en Internet Explorer o utilice [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) desde el host de conector. Vaya a la aplicación con la dirección URL interna e inspeccione los encabezados de autorización de World Wide Web ofrecidos que se devuelven en la respuesta de la aplicación, para asegurarse de que Negociar o Kerberos esté presente. Un blob de Kerberos posterior, devuelto en la respuesta del explorador a la aplicación, suele empezar por **YII**, así que esto es una buena indicación de la implicación de Kerberos. Por otra parte, NTLM siempre empieza por **TlRMTVNTUAAB**, que se convierte en NTLMSSP cuando se descodifica desde Base64. Si ve **TlRMTVNTUAAB** al principio del blob, significa que Kerberos **no** está disponible. Si no lo ve, es probable que Kerberos esté disponible.

  * Tenga en cuenta que, si usa Fiddler, este método requiere deshabilitar temporalmente la protección ampliada en la configuración de la aplicación en IIS.

     ![Ventana de inspección de red del explorador](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Ilustración:* como no empieza por TIRMTVNTUAAB, en este ejemplo, Kerberos está disponible. Se trata de un ejemplo de blob de Kerberos que no empieza por YII.

2.  Quite NTLM temporalmente de la lista de proveedores en el sitio de IIS y acceda a la aplicación directamente desde Internet Explorer en el host de conector. Con NTLM eliminado de la lista de proveedores, debería tener acceso a la aplicación solo mediante Kerberos. Si se produce un error, esto indica que hay un problema con la configuración de la aplicación y que la autenticación Kerberos no funciona.

Si Kerberos no está disponible, compruebe la configuración de autenticación de la aplicación en IIS para asegurarse de que Negociar aparezca en primer lugar, con NTLM justo después. (No Negociar:Kerberos o Negociar:PKU2U). Continúe solamente si Kerberos es funcional.

   ![Proveedores de autenticación de Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Con Kerberos y NTLM listos, ahora deshabilite temporalmente la autenticación previa de la aplicación en el portal. Vea si puede acceder a ella desde Internet mediante la dirección URL externa. Se le debería pedir que se autenticara y debería poder hacerlo con la misma cuenta que usó en el paso anterior. De lo contrario, esto indica un problema con la aplicación back-end y no con KCD.

-   Ahora vuelva a habilitar la autenticación previa en el portal y autentíquese por medio de Azure; intente conectarse a la aplicación mediante su dirección URL externa. Si se produce un error de SSO, verá un mensaje de error Prohibido en el explorador, además del evento 13022 en el registro:

    *El conector de proxy de aplicación de Microsoft AAD no puede autenticar al usuario porque el servidor back-end responde a los intentos de autenticación Kerberos con un error HTTP 401.*

    ![Error HTTTP 401: Prohibido](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Compruebe la aplicación IIS para asegurarse de que el grupo de aplicaciones esté configurado para usar la misma cuenta con la que se ha configurado el SPN en AD. Para ello, vaya a IIS, como se muestra a continuación.

    ![Ventana de configuración de la aplicación IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Una vez que conozca la identidad, emita el siguiente comando desde un símbolo del sistema para asegurarse de que esta cuenta esté definitivamente configurada con el SPN en cuestión. Por ejemplo, **setspn – q http/spn.wacketywack.com**

    ![Ventana de comando SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Compruebe que el SPN definido en la configuración de la aplicación en el portal sea el mismo que está configurado en la cuenta de AD de destino utilizada por el grupo de aplicaciones de la aplicación.

   ![Configuración del SPN en Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Vaya a IIS y seleccione la opción **Editor de configuración** para la aplicación; vaya a **system.webServer/security/authentication/windowsAuthentication** para asegurarse de que **UseAppPoolCredentials** esté establecido en Verdadero.

   ![Opción de credencial de grupos de aplicaciones en la configuración de IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Aunque resulta útil para mejorar el rendimiento de las operaciones de Kerberos, dejar el modo kernel habilitado también hace que el vale para el servicio solicitado se descifre mediante la cuenta de equipo. Esto también se denomina sistema local, y tenerlo establecido en Verdadero interrumpe KCD cuando la aplicación está hospedada en varios servidores en una granja.

-   Como comprobación adicional, también puede deshabilitar la protección **extendida**. Se han encontrado escenarios donde se ha demostrado que esto interrumpe KCD cuando está habilitada en configuraciones muy específicas, en las que una aplicación se publica como subcarpeta del sitio web predeterminado. En sí está configurada para la autenticación anónima únicamente y deja cuadros de diálogo enteros atenuados, lo que indica que los objetos secundarios no heredarían ninguna configuración activa. Sin embargo, si es posible, se recomienda tenerla siempre habilitada. Desde luego, puede hacer pruebas, pero no se olvide de volver a habilitarla.

Estas comprobaciones adicionales deberían haberle encaminado a empezar a usar la aplicación publicada. Puede proceder a agregar más conectores que también estén configurados para la delegación pero, a estas alturas, se recomienda leer este tutorial técnico más detallado [La guía completa para solucionar problemas del proxy de aplicación de Azure AD](https://aka.ms/proxytshootpaper).

Si todavía no puede solucionar el problema, el equipo de soporte técnico estará encantado de ayudarle a partir de aquí. Cree un vale de soporte directamente en el portal y nuestros ingenieros se pondrán en contacto.

## <a name="other-scenarios"></a>Otros escenarios

-   El proxy de aplicación de Azure solicita un vale de Kerberos antes de enviar su solicitud a una aplicación. Algunas aplicaciones de terceros, como Tableau Server, no aceptan este método de autenticación, sino que esperan que se lleven a cabo negociaciones más convencionales. La primera solicitud es anónima, lo que permite que la aplicación responda con los tipos de autenticación que admite mediante un error 401.

-   Autenticación de dos saltos: se utiliza normalmente en escenarios con una aplicación en niveles, con un back-end y un front-end, ambos de los cuales requieren autenticación, como SQL Reporting Services.

## <a name="next-steps"></a>Pasos siguientes
[Configuración de la delegación limitada de Kerberos (KCD) en un dominio administrado](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
