<properties
   pageTitle="Historial de versiones de conectores | Microsoft Azure"
   description="En este tema se incluyen todas las versiones de los conectores para Forefront Identity Manager (FIM) y Microsoft Identity Manager (MIM)."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>


# <a name="connector-version-release-history"></a>Historial de versiones de conectores
Los conectores de Forefront Identity Manager (FIM) y Microsoft Identity Manager (MIM) se actualizan con frecuencia.

>[AZURE.NOTE]
Este tema solo está disponible en FIM y MIM. Estos conectores no se admiten en Azure AD Connect.

En este tema se muestran todas las versiones de los conectores que se han publicado.

Vínculos relacionados:

- [Descargar los últimos conectores](http://go.microsoft.com/fwlink/?LinkId=717495)
- [conector LDAP genérico](active-directory-aadconnectsync-connector-genericldap.md) 
- [conector SQL genérico](active-directory-aadconnectsync-connector-genericsql.md) 
- [conector de servicios web](http://go.microsoft.com/fwlink/?LinkID=226245) 
- [conector PowerShell](active-directory-aadconnectsync-connector-powershell.md) 
- [conector Lotus Domino](active-directory-aadconnectsync-connector-domino.md) 

## <a name="1.1.117.0"></a>1.1.117.0
Publicación: marzo de 2016

**Nuevo conector**  
Versión inicial del [conector SQL genérico](active-directory-aadconnectsync-connector-genericsql.md).

**Nuevas características:**

- Conector LDAP genérico:
    - Se agregó compatibilidad para la importación diferencial con Isode.
- Conector de servicios web:
    - Se han actualizado las actividades csEntryChangeResult y setImportErrorCode para permitir que los errores de nivel de objeto se devuelvan al motor de sincronización.
    - Se han actualizado las plantillas SAP6 y SAP6User para utilizar la nueva funcionalidad de error de nivel de objeto.
- Conector Lotus Domino:
    - Al exportar, necesita un certificador por cada libreta de direcciones. Ahora puede usar la misma contraseña para todos los certificadores a fin de facilitar la administración.

**Problemas corregidos:**

- Conector LDAP genérico:
    - En el caso de IBM Tivoli DS, no se detectaban correctamente algunos atributos de referencia.
    - En el caso de Open LDAP durante una importación diferencial, se truncaban los espacios en blanco al principio y al final de las cadenas.
    - En el caso de Novell y NetIQ, se producía un error en las exportaciones que trasladaban un objeto entre UO/contenedores y, a la vez, cambiaban el nombre del objeto.
- Conector de servicios web:
    - Si el servicio web tenía varios puntos de conexión para el mismo enlace, el conector no los detectaba correctamente.
- Conector Lotus Domino:
    - No funcionaban las exportaciones del atributo fullName a una base de datos de recepción de correo electrónico.
    - Las exportaciones que agregaban y quitaban miembros de un grupo solo exportaban los miembros agregados.
    - Si un documento de notas no es válido (el atributo isValid se definía como false), se produce un error en el conector.

## <a name="older-releases"></a>Versiones anteriores
Antes de marzo de 2016, los conectores se publicaban como temas de soporte técnico.

**LDAP genérico**

- [KB3078617](https://support.microsoft.com/kb/3078617) : 1.0.0597, septiembre de 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) : 1.0.0549, marzo de 2015
- [KB3031009](https://support.microsoft.com/kb/3031009) : 1.0.0534, enero de 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) : 1.0.0419, septiembre de 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) : 4.3.1082, marzo de 2014

**Servicios web**

- [KB3008178](https://support.microsoft.com/kb/3008178) : 1.0.0419, septiembre de 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) : 1.0.0419, septiembre de 2014

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) : 1.0.0597, septiembre de 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) : 1.0.0549, marzo de 2015
- [KB2977286](https://support.microsoft.com/kb/2977286) : 5.3.0712, agosto de 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) : 5.3.1003, febrero de 2014  
- [KB2899874](https://support.microsoft.com/kb/2899874) : 5.3.0721, octubre de 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) : 5.3.0534, agosto de 2013

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).



<!--HONumber=Oct16_HO2-->


