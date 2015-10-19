<properties
	pageTitle="Ediciones de Azure Active Directory | Microsoft Azure"
	description="Un tema que explica las opciones de las ediciones gratuitas y de pago de Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="markvi"/>

# Ediciones de Azure Active Directory

Azure Active Directory es un servicio que proporciona capacidades de administración de identidades y acceso completas en la nube. Combina los servicios de directorio, la gobernanza de identidades avanzada, la administración de acceso de la aplicación y una plataforma enriquecida basada en estándares para los desarrolladores. Para obtener más información, [consulte este vídeo](http://azure.microsoft.com/documentation/videos/teched-europe-2014-cloud-identity-microsoft-azure-active-directory-explained/).

Basado en un gran conjunto de capacidades gratuitas de Microsoft Azure Active Directory, las ediciones Azure Active Directory Premium y Basic proporcionan un conjunto de características más avanzadas para ayudar a las empresas a satisfacer sus necesidades de administración de identidades y acceso más exigentes. Para ver las opciones de precios para estas ediciones, consulte [Precios de Azure Active Directory](http://azure.microsoft.com/pricing/details/active-directory/). Al suscribirse a Azure, podrá elegir entre las ediciones gratuitas y de pago de Azure Active Directory:

- **Gratuita**: la edición Gratuita de Azure Active Directory forma parte de todas las suscripciones de Azure. No hay nada a licencia y no hay nada que instalar. Con ella, puede administrar cuentas de usuario, sincronizar directorios locales, obtener un inicio de sesión único en Azure, Office 365 y miles de aplicaciones SaaS populares como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc.
- **Básica**: la edición Básica de Azure Active Directory proporciona acceso a la aplicación y los requisitos de administración de identidades de autoservicio para los trabajadores de tareas con primeras necesidades en la nube. Con la edición Básica de Azure Active Directory, obtendrá todas las capacidades que Azure Active Directory Gratis tiene que ofrecer, además de una administración de acceso basada en grupos, un restablecimiento de la contraseña de autoservicio para aplicaciones en la nube, proxy de aplicación de Azure Active Directory (para publicar aplicaciones web locales con Azure Active Directory), un entorno personalizable para el inicio de las aplicaciones en la nube para usuarios y empresas, y un SLA de nivel empresarial con un tiempo de actividad del 99,9 por ciento. Un administrador con licencia para la edición Basic de Azure Active Directory también puede activar una versión de prueba de Azure Active Directory Premium.
- **Premium**: con la edición Premium de Azure Active Directory, obtendrá todas las capacidades que ofrecen las ediciones Gratuita y Básica de Azure Active Directory, además de las capacidades adicionales de administración de identidades de nivel empresarial con una gran cantidad de características que se explican a continuación.

Para suscribirse y empezar a usar Active Directory Premium hoy, vea [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]Azure Active Directory Premium y Azure Active Directory Basic no se admiten actualmente en China. Póngase en contacto con nosotros en el [Foro de Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory) para obtener más información.

## Características de Azure Active Directory Básico

La edición Básico de Active Directory es una oferta de pago de Azure Active Directory e incluye las siguientes características:

- **Información de marca** : para mejorar aún más la experiencia de usuario final, puede agregar el logotipo de la empresa o esquemas de color a las páginas del panel de acceso e inicio de sesión de la organización. Una vez que haya añadido el logotipo, también tiene la opción de agregar versiones localizadas del logotipo para diferentes idiomas y configuraciones regionales. Para obtener más información, consulte [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md).
- **Acceso a las aplicaciones basadas en grupos** : use grupos para aprovisionar usuarios y asignar acceso de usuario de forma masiva a miles de aplicaciones SaaS. Estos grupos pueden crearse únicamente en la nube o puede aprovechar los grupos existentes que se han sincronizado desde Active Directory local. Para obtener más información, consulte [Asignación de acceso de un grupo a la aplicación SaaS en Azure AD](active-directory-accessmanagement-group-saasapps.md).
- **Restablecimiento de contraseña de autoservicio**: Azure siempre ha permitido a los administradores de directorios restablecer las contraseñas. Con Azure Active Directory Básico, ahora puede reducir las llamadas de soporte técnico cuando los usuarios olviden una contraseña proporcionando a todos los usuarios en el directorio la capacidad de restablecer su contraseña mediante la misma experiencia de inicio de sesión que tienen con Office 365. Para obtener más información, consulte [Administración de contraseñas desde cualquier lugar](active-directory-passwords.md).
- **SLA empresarial del 99,9 %**: garantizamos al menos una disponibilidad del 99,9 % del servicio Azure Active Directory Básico.
- [**Provisión de acceso remoto seguro a aplicaciones locales**](active-directory-application-proxy-get-started.md): proporcione a sus empleados acceso seguro a aplicaciones locales como SharePoint y Exchange/OWA desde la nube con Azure Active Directory.

## Características de Azure Active Directory Premium

La edición Premium de Active Directory es una oferta de pago de Azure Active Directory e incluye todas las características de las ediciones gratuita y básica además de las siguientes:

- **Administración de grupos de autoservicio**: Azure Active Directory Premium simplifica la administración diaria de grupos permitiendo a los usuarios crear grupos, solicitar acceso a otros grupos, delegar la propiedad de grupos de manera que los demás puedan aprobar solicitudes y mantener sus suscripciones a grupos.

    Para obtener más información, consulte [Administración de grupos de autoservicio para usuarios en Azure AD](https://msdn.microsoft.com/library/azure/dn641267.aspx).

- **Alertas e informes de seguridad avanzados**: supervise y proteja el acceso a las aplicaciones en la nube visualizando los registros detallados que muestras anomalías más avanzadas e informes de diseño de acceso incoherentes. Los informes avanzados están basados en el aprendizaje mecánico y puede ayudarle a obtener nuevas perspectivas para mejorar la seguridad de acceso y dar respuesta a las amenazas potenciales.

    Para obtener más información, consulte [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).

- **Multi-Factor Authentication**: Multi-Factor Authentication se incluye ahora con la edición Premium y puede ayudarle a obtener acceso seguro a aplicaciones locales (VPN, RADIUS, etc.), Azure, Microsoft Online Services como Office 365 y Dynamics CRM Online, y miles de servicios en la nube que no son de MS integrados previamente con Azure Active Directory. Simplemente habilite Multi-Factor Authentication para las identidades de Azure Active Directory y se solicitará a los usuarios que configuren una comprobación adicional la próxima vez que inicien sesión.

    Para obtener más información, consulte [Qué es Azure Multi-Factor Authentication](multi-factor-authentication.md).

- **Microsoft Identity Manager (MIM)**: la versión Premium incorpora la opción de garantizar los derechos de uso de un servidor MIM (y CAL) en la red local para admitir cualquier combinación de soluciones de identidad híbrida. Esto es una buena opción si tiene una variación de bases de datos y directorios locales que desea sincronizar directamente con Azure Active Directory. No hay ningún límite en el número de servidores MIM que puede usar, sin embargo, las CAL de MIM se conceden basándose en la asignación de una licencia de usuario de Azure Active Directory Premium.

    Para obtener más información, vea [Microsoft Identity Manager](http://www.microsoft.com/es-es/server-cloud/products/microsoft-identity-manager/default.aspx).

- **SLA empresarial del 99,9 %**: garantizamos al menos una disponibilidad del 99,9 % del servicio Azure Active Directory Premium.

    Para obtener más información, consulte [SLA de Active Directory Premium](http://azure.microsoft.com/support/legal/sla/).

- **Restablecimiento de contraseña con reescritura**: puede volver a escribir el restablecimiento de la contraseña de autoservicio en directorios locales.

- [Azure Active Directory Connect Health](active-directory-aadconnect-health.md): permite supervisar el mantenimiento de la infraestructura de Active Directory local y obtener análisis de uso.



## Comparación de las ediciones Gratis, Básico y Premium

<br>Disponible en esta edición:![Lista de comprobación](./media/active-directory-editions/ic195031.png)


<table>
	<tr>
		<th>&#160;</th>
		<th>Características </th>
		<th>Edición gratuita </th>
		<th>Edición básica </th>
		<th>Premium Edition </th>
	</tr>
	<tr>
		<td rowspan="8">
		<p>Características frecuentes</p>
		</td>
		<td>
		<p>Directorio como servicio</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /><br />
		Hasta 500 000 objetos [1]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /><br />
		Sin límite de objetos</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /><br />
		Sin límite de objetos</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Administración de usuario y de grupo mediante la interfaz de usuario o cmdlets de Windows PowerShell</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Registro de dispositivos</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Portal de Panel de acceso para el acceso de usuario basado en SSO a aplicaciones SaaS y personalizadas</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /><br />
		Hasta 10 aplicaciones por usuario [2]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /><br />
		Hasta 10 aplicaciones por usuario [2]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /><br />
		Sin límite de aplicaciones</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Aprovisionamiento y administración de acceso a aplicaciones basadas en usuarios</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Cambio de contraseña de autoservicio para usuarios en la nube</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Azure AD Connect: para la sincronización entre directorios locales y Azure Active Directory</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Informes de seguridad estándar</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td rowspan="5">
		<p>Características premium y básicas</p>
		</td>
		<td>
		<p>Tiempo de actividad de SLA de alta disponibilidad (99,9&#160;%)</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Aprovisionamiento y administración de acceso a aplicaciones basadas en grupos</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Personalización de colores y logotipos empresariales para las páginas de panel de acceso e inicio de sesión</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Restablecimiento de contraseña de autoservicio para usuarios en la nube</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Proxy de aplicación: acceso remoto seguro y SSO en aplicaciones web locales</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td rowspan="9">
		<p>Características exclusivas de Premium</p>
		</td>
		<td>
		<p>Informes de uso avanzado de las aplicaciones</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Administración de grupos de autoservicio para usuarios en la nube</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Restablecimiento de contraseña de autoservicio con reescritura local</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Licencias de usuario de Microsoft Identity Manager (MIM): para la administración de identidades y acceso local</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /> [3]</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Informes de seguridad avanzados de anomalías (basados en aprendizaje automático)</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>
		<a href="http://channel9.msdn.com/Series/EMS/Azure-Cloud-App-Discovery">Detección de aplicaciones de nube</a> </p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Servicio Multi-Factor Authentication para usuarios en la nube</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Servicio Multi-Factor Authentication para usuarios locales</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>
		<a href="https://msdn.microsoft.com/es-es/library/azure/dn906722.aspx">Azure Active Directory Connect Health</a>: permite supervisar el mantenimiento de la infraestructura de Active Directory local y obtener análisis de uso.</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="Lista de comprobación" src="./media/active-directory-editions/ic195031.png" title="Lista de comprobación" xmlns="" /></p>
		</td>
	</tr>
</table>


[1] El límite de 500 000 objetos no se aplica a Office 365, Microsoft Intune o cualquier otro servicio en línea de Microsoft que se base en Azure Active Directory para servicios de directorio.

[2] Con las versiones gratuita y básica de Azure Active Directory, los usuarios finales a los que se les haya asignado acceso a cada aplicación SaaS, pueden ver hasta 10 aplicaciones en el Panel de acceso y obtener acceso SSO a ellas (suponiendo que el administrador las haya configurado primero con SSO). Los administradores pueden configurar SSO y asignar acceso de usuario a tantas aplicaciones SaaS como deseen con Gratis. Sin embargo, los usuarios finales solo verán 10 aplicaciones en su panel de acceso a la vez.

[3] Con las licencias de Windows Server (cualquier edición), se conceden derechos de software de servidor de Microsoft Identity Manager. Dado que Microsoft Identity Manager se ejecuta en el sistema operativo Windows Server, se puede instalar y usarse en ese servidor siempre y cuando el servidor esté ejecutando una copia con licencia válida. No se necesita ninguna otra licencia de servidor de Microsoft Identity Manager.



<br> <br>









## Características actualmente en versión de vista previa pública

Las siguientes características están actualmente en versión de vista previa pública y se agregarán pronto:

- [Unidades administrativas](https://msdn.microsoft.com/library/azure/dn832057.aspx): un nuevo contenedor de Active Directory de Azure de recursos que se puede usar para delegar permisos administrativos con subconjuntos de los usuarios y aplicar las directivas a un subconjunto de usuarios.
- [Incorporación de sus aplicaciones SaaS](https://msdn.microsoft.com/library/azure/dn893637.aspx) a Azure Active Directory.
- Sustitución de contraseña para Facebook, Twitter y LinkedIn. Para obtener más información, consulte [este artículo](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx).
- Pertenencia a grupos dinámicos. Para obtener más información, consulte [este artículo](https://msdn.microsoft.com/library/azure/dn913807.aspx).
- [Acceso condicional](https://msdn.microsoft.com/library/azure/dn906877.aspx): autenticación multifactor por aplicación.
- Integración de aplicaciones de recursos humanos: día de trabajo
- Administración de identidad con privilegios: la administración de identidad con privilegios proporciona una supervisión mejorada para ayudar a cumplir los acuerdos de nivel de servicio y los requisitos de cumplimiento de normativas.
- Solicitudes de aplicación de autoservicio: los administradores pueden proporcionar una lista de aplicaciones SaaS a los usuarios para que puedan elegir las que deseen usar; las aplicaciones estarán disponibles de inmediato o bien tras su aprobación.
- API de informes de Azure: los datos de cada informe de seguridad de Azure Active Directory estarán disponibles para otras herramientas de supervisión o SIEM.


## Pasos siguientes

- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)
- [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)

<!---HONumber=Oct15_HO2-->