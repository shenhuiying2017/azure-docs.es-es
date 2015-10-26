
<properties 
	pageTitle="Solución de problemas de pertenencia dinámica para grupos| Microsoft Azure" 
	description="Un tema que muestra consejos para la solución de problemas para la pertenencia dinámica para grupos en Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor="Curtis"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015" 
	ms.author="femila"/>


# Solución de problemas de pertenencias dinámicas a grupos

| Síntoma | Acción |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| He configurado una regla en un grupo pero las pertenencias no se actualizan en el grupo. | Compruebe que la opción Habilitar la administración de grupos delegados está establecida en Sí en la pestaña Configuración de directorio. Tenga en cuenta que solo verá esta opción si ha iniciado sesión con una cuenta que tenga asignada una licencia de Azure Active Directory Premium. Compruebe los valores de atributos de usuario en la regla: ¿hay usuarios que cumplen la regla? |
| He configurado una regla, pero ahora se han quitado los miembros existentes de la regla. | Esto es lo esperado: los miembros existentes del grupo se quitan cuando una regla se habilita o se cambia. El conjunto resultante de usuarios de la evaluación de la regla se agregan como miembros al grupo. |
| No veo los cambios en la pertenencia al instante cuando agrego o cambio una regla, ¿por qué pasa esto? | La evaluación de pertenencia dedicada se realiza periódicamente en un proceso asincrónico en segundo plano. El número de usuarios en el inquilino y el tamaño del grupo creado como resultado influyen en el tiempo que tarda este proceso. Normalmente, los inquilinos con un número pequeño de usuarios verán los cambios en la pertenencia al grupo en unos pocos minutos. El proceso en inquilinos con un gran número de usuarios puede tardar 30 minutos o más en completarse. |

A continuación presentamos algunos temas que proporcionarán información adicional acerca de Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->