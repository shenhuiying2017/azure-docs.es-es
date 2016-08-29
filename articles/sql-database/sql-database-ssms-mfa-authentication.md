<properties
   pageTitle="Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL | Microsoft Azure"
   description="Use Multi-Factor Authentication con SSMS para Base de datos SQL y Almacenamiento de datos SQL."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/15/2016"
   ms.author="rick.byham@microsoft.com"/>

# Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL

Base de datos SQL de Azure y Almacenamiento de datos SQL de Azure ahora admiten conexiones provenientes de SQL Server Management Studio (SSMS) con *Autenticación universal de Active Directory*. Autenticación universal de Active Directory es un flujo de trabajo interactivo que admite *Azure Multi-Factor Authentication* (MFA). Azure MFA ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura gracias a una gran variedad de opciones sencillas de verificación, como llamadas telefónicas, mensajes de texto, tarjetas inteligentes con PIN o notificaciones de aplicaciones móviles, lo que permite a los usuarios elegir el mecanismo que prefieran. Para una descripción de Multi-Factor Authentication, consulte [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).

SSMS ahora admite:

- MFA interactivo con Azure AD con el potencial de una validación en cuadros de diálogo emergentes.
- Los métodos no interactivos Contraseña de Active Directory y Autenticación integrada de Active Directory que se pueden usar en muchas aplicaciones distintas (ADO.NET, JDBC, ODBC, etc.). Estos dos métodos nunca generan cuadros de diálogo emergentes.

Cuando la cuenta de usuario está configurada para MFA, el flujo de trabajo de autenticación interactivo requiere la interacción adicional del usuario a través de cuadros de diálogo emergentes, el uso de tarjetas inteligentes, etc. Cuando la cuenta de usuario está configurada para MFA, el usuario debe seleccionar Autenticación universal de Azure para conectarse. Si la cuenta de usuario no requiere MFA, de todos modos puede usar las otras dos opciones de Autenticación de Azure Active Directory.

## Limitaciones de Autenticación universal para Base de datos SQL y Almacenamiento de datos SQL

- SSMS es la única herramienta que actualmente está habilitada para MFA a través de Autenticación universal de Active Directory.
- Solo una cuenta única de Azure Active Directory puede iniciar sesión en una instancia de SSMS con Autenticación universal. Para iniciar sesión con otra cuenta de Azure AD, debe usar otra instancia de SSMS. (Esta restricción se limita a Autenticación universal de Active Directory. Puede iniciar sesión en distintos servidores con Autenticación de contraseña de Active Directory, Autenticación integrada de Active Directory o Autenticación de SQL Server).
- SSMS admite Autenticación universal de Active Directory para la visualización de Explorador de objetos, Editor de consultas y Almacén de consultas.
- Ni DacFx ni el Diseñador de esquemas admiten Autenticación universal.
- Las cuentas de MSA no se admiten para Autenticación universal de Active Directory.
- Autenticación universal de Active Directory no se admite en SSMS para los usuarios que se importan a la instancia actual de Active Directory desde otras instancias de Active Directory.
- No hay ningún requisito de software adicional para Autenticación universal de Active Directory, excepto el uso de una versión compatible de SSMS.

## Pasos de configuración

Implementar Multi-Factor Authentication requiere cuatro pasos básicos.

1. **Configuración de una instancia de Azure Active Directory**: para más información, consulte [Integración de las identidades locales con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Incorporación de su nombre de dominio personalizado a Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure ahora admite la federación con Windows Server Active Directory), [Administración de su directorio de Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) y [Administrar Azure AD mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configuración de MFA**: para instrucciones detalladas, consulte [Configuración de Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md).

3. **Configuración de Base de datos SQL o Almacenamiento de datos SQL para Autenticación de Azure AD**: para instrucciones detalladas, consulte [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante Autenticación de Azure Active Directory](sql-database-aad-authentication.md).

4. **Descarga de SSMS**: en el equipo cliente, descarga la versión más reciente de SSMS (como mínimo, de agosto de 2016), desde [Descargar SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## Conexión mediante Autenticación universal con SSMS

Los pasos siguientes muestran cómo conectarse a Base de datos SQL o Almacenamiento de datos SQL con la versión más reciente de SSMS.

1. Para conectarse mediante Autenticación universal, en el cuadro de diálogo **Conectar con el servidor**, seleccione **Autenticación universal de Active Directory**. ![1mfa-universal-connect][1]

2. Como es habitual para Base de datos SQL y Almacenamiento de datos SQL, debe hacer clic en **Opciones** y especificar la base de datos en el cuadro de diálogo **Opciones**. Haga clic en **Conectar**.
3. Cuando aparezca el cuadro de diálogo **Iniciar sesión con su cuenta**, escriba la cuenta y la contraseña de su identidad de Azure Active Directory. ![2mfa-sign-in][2]

    > [AZURE.NOTE] Si está en Autenticación universal con una cuenta que no requiere MFA, conéctese en este momento. En el caso de los usuarios que requieren MFA, continúe con los pasos siguientes.
 
4. Pueden aparecer dos cuadros de diálogo de configuración de MFA. Esta operación de una sola vez depende de la configuración del administrador de MFA y, por tanto, puede ser opcional. En el caso de un dominio habilitado para MFA, a veces este paso está definido previamente (por ejemplo, el dominio requiere que los usuarios usen una tarjeta inteligente y un PIN). ![3mfa-setup][3]

5. El segundo cuadro de diálogo de un único uso que puede que aparezca le permite seleccionar los detalles del método de autenticación. El administrador configura las opciones posibles. ![4mfa-verify-1][4]
 
6. Azure Active Directory le envía la información de confirmación. Cuando reciba el código de comprobación, escríbalo en el cuadro **Escribir código de comprobación** y haga clic en **Iniciar sesión**. ![5mfa-verify-2][5]

Cuando se complete la comprobación, SSMS se conectará de manera habitual, siempre que haya acceso de firewall y credenciales válidas.

##Pasos siguientes  

Conceda a otros usuarios acceso a la base de datos: [Autorización y autenticación de Base de datos SQL: concesión de acceso](sql-database-manage-logins.md) Asegúrese de que otros usuarios pueden conectarse a través del firewall: [Configuración de una regla de firewall de nivel de servidor de Base de datos SQL de Azure mediante el Portal de Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

<!---HONumber=AcomDC_0817_2016-->