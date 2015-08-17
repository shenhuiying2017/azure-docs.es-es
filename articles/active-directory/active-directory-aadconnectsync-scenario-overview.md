<properties
	pageTitle="Sincronización de Azure AD Connect: información general de escenarios de varios bosques"
    description="El objetivo de este tema es tratar algunos escenarios comunes y mostrar cómo se representan en el servicio de sincronización de Sincronización de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Sincronización de Azure AD Connect: información general de escenarios de varios bosques

Muchas organizaciones tienen entornos que incluyen varios bosques de Active Directory local. Existen varias razones para tener implementado más de un bosque de Active Directory local. Los ejemplos típicos son los diseños con bosques de cuenta-recurso, bosques relacionados con fusiones y adquisiciones o bosques utilizados para externalizar los datos.

Microsoft le proporciona DirSync, una solución de para escenarios de bosque único y FIM, una solución para escenarios de varios bosques. Sin embargo, configurar FIM puede ser complicado y es posible que sea necesario dedicarle una cantidad considerable de tiempo.

Con Sincronización de Azure AD Connect Sync puede simplificar de forma significativa la configuración y hacerla más predictiva.

En la configuración predeterminada proporcionada por Sincronización de Azure AD Connect, se dan por hecho los siguientes supuestos:

1. Los usuarios tienen solo una cuenta habilitada y el bosque donde se encuentra esta cuenta se usa para la federación del usuario.
2. Los usuarios tienen solo un buzón.
3. El bosque que aloja el buzón de un usuario tiene la mejor calidad de datos para los atributos visibles en la lista global de direcciones (GAL) de Exchange. Si no hay ningún buzón en el usuario, puede usarse cualquiera de los bosques para aportar estos valores de atributo.


El objetivo de este tema es tratar algunos escenarios comunes y mostrar cómo se representan en el servicio de sincronización de Sincronización de Azure AD:

1. Separación de tecnologías 
2. Malla completa con GALSync opcional 
3. Bosque de cuenta-recurso 


## Separación de tecnologías

En este entorno, todos los bosques locales se tratan como entidades independientes y ningún usuario estará presente en cualquier otro bosque.<br> Cada bosque tiene su propia organización de Exchange y no hay ningún GALSync entre los bosques. Esta podría ser la situación después de una fusión o adquisición, o en una organización donde cada unidad de negocio funciona de forma aislada de las demás.

![Separación de tecnologías][1]

En esta imagen, se representará cada objeto en cada bosque una vez en el metaverso y se agregará al directorio de Azure AD de destino. Esto sería el mismo resultado final como si tuviera un servidor DirSync conectado a cada bosque de AD de origen.
 




## Malla completa con GALSync opcional

Una topología de malla completa permite a los usuarios y recursos ubicarse en cualquiera de los bosques y normalmente habría confianzas bidireccionales entre los bosques.

Si Exchange está presente en más de un bosque, podría ser opcionalmente un GALSync que representa un usuario en un bosque como un contacto en los demás bosques.

En este escenario, los objetos de identidad se combinan normalmente mediante el atributo mail. Por lo tanto, un usuario con un buzón en un bosque se combina con los contactos en los otros bosques. Los grupos de distribución y de seguridad pueden encontrarse en cada bosque y pueden contener una combinación de usuarios, contactos y FSP (entidades de seguridad externa).

En la siguiente imagen se describe este escenario.

![Malla completa con GALSync opcional][2]


## Bosque de cuenta-recurso
En una topología de bosque de cuenta-recurso tiene uno o más bosques de cuenta con cuentas de usuario activas.<br> Este escenario incluye un bosque que confía en todos los bosques de cuenta. Normalmente, este bosque tiene un esquema de AD extendido con Exchange y Lync. Todos los servicios de Exchange y Lync, así como otros servicios compartidos se encuentran en este bosque. Los usuarios tienen una cuenta de usuario deshabilitada en este bosque y el buzón está vinculado al bosque de cuenta.

La imagen siguiente describe este escenario con una sola cuenta.

![Bosque de cuenta-recurso][3]


## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

 
<!--Image references-->
[1]: ./media/active-directory-aadsync-scenario-overview/ic750599.png
[2]: ./media/active-directory-aadsync-scenario-overview/ic750600.png
[3]: ./media/active-directory-aadsync-scenario-overview/ic750601.png

<!---HONumber=August15_HO6-->