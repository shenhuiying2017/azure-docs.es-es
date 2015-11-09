<properties 
	pageTitle="Qué es: Administración de contraseñas de Azure AD | Microsoft Azure"
	description="Descripción de las capacidades de administración de contraseñas en Azure AD, incluido el restablecimiento de contraseña, el cambio, la creación de informes de administración de contraseñas y la escritura diferida en Active Directory local." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="asteen"/>

# Administración de contraseñas desde cualquier lugar

  >[AZURE.IMPORTANT]¿Está aquí porque quiere restablecer su contraseña de Azure o O365? Si es así, [siga las instrucciones aquí](https://support.microsoft.com/kb/2606983).
  
El autoservicio ha sido durante mucho tiempo un objetivo clave para los departamentos de TI de todo el mundo como una medida de reducción de costos y ahorro de mano de obra. De hecho, el mercado está atestado de productos que le permiten administrar los grupos locales, las contraseñas o los perfiles de usuario en la nube o a nivel local. Azure AD se distingue de estas ofertas proporcionando algunas de las capacidades de autoservicio más fáciles de usar y más eficaces disponibles hoy día.

**La Administración de contraseñas de Azure AD** es un conjunto de capacidades que permiten a los usuarios administrar cualquier contraseña desde cualquier dispositivo, en cualquier momento, desde cualquier ubicación, manteniendo la conformidad con las directivas de seguridad que defina.

## Información general
Puede comenzar con una prueba piloto de la Administración de contraseñas de Azure AD en menos de cinco minutos, y puede tenerla implementada en toda la organización en horas. A continuación se muestran algunos recursos útiles para poder avanzar con el servicio:

* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis diferentes componentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md) : obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Prácticas recomendadas**](active-directory-passwords-best-practices.md): obtenga información sobre cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización.
* [**Obtener perspectivas**](active-directory-passwords-get-insights.md): obtenga información sobre nuestras capacidades integradas de creación de informes.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.


## ¿Qué se puede hacer con la Administración de contraseñas de Azure AD?
Estas son algunas de las cosas que puede hacer con las capacidades de administración de contraseñas de Azure AD.

- El **cambio de la contraseña de autoservicio** permite a los usuarios finales o administradores cambiar sus contraseñas expiradas o no expiradas sin llamar a un administrador o departamento de soporte técnico para obtener soporte técnico.
- El **restablecimiento de la contraseña de autoservicio** permite a los usuarios finales o administradores restablecer sus contraseñas automáticamente sin llamar a un administrador o departamento de soporte técnico para obtener soporte técnico. El restablecimiento de la contraseña de autoservicio requiere Azure AD Premium o Básico. Para obtener más información, consulte Ediciones de Azure Active Directory.
- El **restablecimiento de la contraseña iniciado por el administrador** permite a un administrador restablecer la contraseña de un usuario final o de otro administrador desde dentro del [Portal de administración de Azure](https://manage.windowsazure.com).
- Los **informes de actividad de administración de contraseñas** proporcionan a los administradores perspectivas sobre una actividad de registro y restablecimiento de contraseña en su organización. 
- La **escritura diferida de la contraseña** permite la administración de contraseñas locales desde la nube, por lo que todos los escenarios anteriores pueden realizarse por los usuarios sincronizados con contraseña o federados, o en nombre de ellos. La escritura diferida de la contraseña requiere Azure AD Premium. Para obtener más información, consulte Introducción a Azure AD Premium.

## ¿Por qué usar la Administración de contraseñas de Azure AD?
Estas son algunas de las razones por las que se debe usar las capacidades de administración de contraseñas de Azure AD.

- **Reducir los costos** : el restablecimiento de contraseñas con asistencia de un servicio de soporte técnico normalmente supone el 20 % del gasto en TI de una organización.
- **Mejorar las experiencias de usuario**: a los usuarios les incomoda llamar al soporte técnico y pasar una hora al teléfono cada vez que olvidan sus contraseñas.
- **Reducir los volúmenes de soporte técnico**: la administración de contraseñas es el motivo único que empuja a la mayoría de organizaciones a recurrir más veces al departamento de soporte técnico.
- **Permitir la movilidad**: los usuarios pueden restablecer sus contraseñas desde cualquier lugar en que se encuentren.

## Actualizaciones recientes de servicios

**Actualizaciones de facilidad de uso para la página de registro**, octubre de 2015

- Ahora, cuando un usuario ya tiene datos registrados, solo tiene que hacer clic en "Tiene buen aspecto" para actualizar los datos sin necesidad de volver a enviar el mensaje correo electrónico o una llamada de teléfono.

**Mejor confiabilidad de escritura diferida de contraseñas**, septiembre de 2015

- A partir de la versión de septiembre de Azure AD Connect, el agente de escritura diferida de contraseñas intentará ahora conexiones de manera más agresiva y capacidades adicionales de conmutación por error más sólidas.

**API para recuperar los datos de informes de restablecimiento de contraseña**, agosto de 2015

- Ahora, los datos que se encuentran detrás de los informes de restablecimiento de contraseña se pueden recuperar directamente de la [API de eventos e informes de Azure AD](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

**Compatibilidad con el restablecimiento de contraseñas de Azure AD durante la unión al dominio de nube**, agosto de 2015

- Ahora, cualquier usuario de nube puede restablecer su contraseña directamente desde la pantalla de inicio de sesión de Windows 10 durante la experiencia de incorporación de unión de dominio de nube. Tenga en cuenta que esto todavía no está expuesto en la pantalla de inicio de sesión de Windows 10.

**Aplicar el registro de restablecimiento de contraseña al iniciar sesión en Azure y aplicaciones federadas**, julio de 2015

- Además de aplicar el registro al iniciar sesión en myapps.microsoft.com, ahora admitimos la aplicación del registro durante inicios de sesión para el Portal de administración de Azure y cualquiera de sus aplicaciones de inicio de sesión único federadas.

**Compatibilidad de localización de preguntas de seguridad**, mayo de 2015

- Ahora tiene la opción de seleccionar preguntas de seguridad predefinidas que están localizadas en el conjunto de idiomas completo de O365 al configurar las preguntas de seguridad para el restablecimiento de contraseña.

**Compatibilidad con el desbloqueo de cuentas durante el restablecimiento de contraseña**, junio de 2015

- Si está utilizando la escritura diferida de contraseñas y restablece la contraseña cuando la cuenta está bloqueada, desbloquearemos automáticamente su cuenta de Active Directory.

**Registro de SSPR con la marca**: abril de 2015

- La página de registro del restablecimiento de contraseña ahora lleva el logotipo de su empresa.

**Preguntas de seguridad**: marzo de 2015

- ¡Hemos puesto las preguntas de seguridad a disposición de todos los usuarios!

**Desbloqueo de cuenta**: marzo de 2015

- Ahora los usuarios pueden desbloquear sus cuentas cuando se produce el restablecimiento de contraseña.

## Próximamente

A continuación se muestran algunas de las interesantes características en las que estamos trabajando en este momento.

**Compatibilidad para desbloquear cuentas de Active Directory sin restablecer una contraseña**, próximamente

- Muchas personas han solicitado la capacidad de desbloquear cuentas de AD de manera independiente del restablecimiento de contraseñas. Nos complace anunciar que estamos poniendo los toques finales en esta característica ahora mismo y que se publicará pronto para cualquier persona que use escritura diferida de contraseñas.

**Compatibilidad para recordar a los usuarios que actualicen su datos registrados durante el inicio de sesión**, trabajo en curso

- En la actualidad, admitimos recordar a los usuarios que actualicen sus datos registrados al tener acceso a myapps.microsoft.com, pero estamos trabajando en la capacidad para hacerlo para todos los inicios de sesión.

**Aplicar el registro de restablecimiento de contraseña al iniciar sesión en aplicaciones de Office 365**, trabajo en curso

- Cada vez más aplicaciones de Office se incorporan a la experiencia de inicio de sesión de Azure AD mejor y más reciente. Cuando lo hacen, se admitirán automáticamente para el registro aplicado de SSPR.

<br/> <br/> <br/>

**Recursos adicionales**


* [Funcionamiento de la administración de contraseñas](active-directory-passwords-how-it-works.md)
* [Introducción a la administración de contraseñas](active-directory-passwords-getting-started.md)
* [Personalización de la administración de contraseñas](active-directory-passwords-customize.md)
* [Prácticas recomendadas de administración de contraseñas](active-directory-passwords-best-practices.md)
* [Visión operativa con los informes de administración de contraseñas](active-directory-passwords-get-insights.md)
* [Preguntas más frecuentes sobre la administración de contraseñas](active-directory-passwords-faq.md)
* [Solución de problemas de administración de contraseñas](active-directory-passwords-troubleshoot.md)
* [Más información](active-directory-passwords-learn-more.md)
* [Administración de contraseñas en MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)

<!---HONumber=Nov15_HO1-->