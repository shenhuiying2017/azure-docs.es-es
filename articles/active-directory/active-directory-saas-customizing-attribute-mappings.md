---
title: Personalización de asignaciones de atributos de Azure AD | Microsoft Docs
description: Conozca cuáles son las asignaciones de atributos para aplicaciones SaaS en Azure Active Directory y cómo puede modificarlas para satisfacer sus necesidades empresariales.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78d971b47ffceb8d845f21a731176834f004f12c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalización de asignaciones de atributos de aprovisionamiento de usuarios para aplicaciones SaaS en Azure Active Directory de usuarios
Microsoft Azure AD proporciona soporte para el aprovisionamiento de usuarios para aplicaciones SaaS de terceros como Salesforce, Google Apps y otras. Si dispone de aprovisionamiento de usuarios para una aplicación SaaS de terceros habilitada, Azure Portal controla sus valores de atributo en forma de una configuración denominada "asignación de atributos".

Hay un conjunto preconfigurado de atributos y asignaciones de atributos entre los objetos de usuario de Azure AD y los objetos de usuario de cada aplicación SaaS. Además de los usuarios, algunas aplicaciones administran otros tipos de objetos, como los grupos. <br> 
 Puede personalizar las asignaciones de atributos predeterminadas según sus necesidades empresariales. Esto significa que puede cambiar o eliminar asignaciones de atributos existentes o crear nuevas asignaciones de atributos.
 
## <a name="editing-user-attribute-mappings"></a>Edición de asignaciones de atributos de usuario

En el portal de Azure AD, se puede acceder a esta característica haciendo clic en una configuración de **Asignaciones** en **Aprovisionamiento** en la sección **Administrar** de una **aplicación empresarial**.


![Salesforce][5] 

Al hacer clic en una configuración de **Asignaciones**, se abre la pantalla relacionada **Asignación de atributos**. Hay asignaciones de atributos que una aplicación SaaS necesita para funcionar correctamente. Para los atributos necesarios, la característica **Eliminar** no está disponible.


![Salesforce][6]  

En el ejemplo anterior, puede ver que el atributo **Username** de un objeto administrado en Salesforce se rellena con el valor de **userPrincipalName** del objeto vinculado de Azure Active Directory.

Puede personalizar las **asignaciones de atributos** existentes haciendo clic en una asignación. Se abre la pantalla **Editar atributo**.

![Salesforce][7]  


### <a name="understanding-attribute-mapping-types"></a>Información sobre los tipos de asignación de atributos
Con asignaciones de atributos, puede controlar cómo se rellenan los atributos en una aplicación SaaS de terceros. Se admiten cuatro tipos de asignaciones diferentes:

* **Directa** : el atributo de destino se rellena con el valor de un atributo del objeto vinculado en Azure AD.
* **Constante** : el atributo de destino se rellena con una cadena específica que se ha especificado.
* **Expresión** : el atributo de destino se rellena según el resultado de una expresión similar a un script. 
  Para más información, consulte [Escritura de expresiones para la asignación de atributos en Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Ninguno** : el atributo de destino se deja sin modificar. Sin embargo, si el atributo de destino está vacío, se rellena con el valor predeterminado que especifique.

Además de estos cuatro tipos básicos de asignaciones de atributos, las asignaciones de atributos personalizadas admiten el concepto de una asignación de valor **predeterminada** opcional. La asignación de valor predeterminada garantiza que un atributo de destino se rellene con un valor si no hay ningún valor en Azure AD ni en el objeto de destino. La configuración más habitual consiste en dejarlo en blanco.


### <a name="understanding-attribute-mapping-properties"></a>Información sobre las propiedades de asignación de atributos

En la sección anterior, ya ha introducido la propiedad de tipo de asignación de atributos.
Además de esta propiedad, las asignaciones de atributos también admiten los siguientes atributos:

- **Atributo de origen**: especifica el atributo de usuario del sistema de origen (por ejemplo, Azure Active Directory).
- **Atributo de destino**: especifica el atributo de usuario del sistema de destino (por ejemplo, ServiceNow).
- **Hacer coincidir objetos con este atributo**: especifica si se debe usar o no esta asignación para identificar de forma unívoca a los usuarios entre los sistemas de origen y de destino. Normalmente esto se establece en el atributo userPrincipalName o mail en Azure AD, que se suele asignar a un campo de nombre de usuario en una aplicación de destino.
- **Precedencia de coincidencia**: se pueden establecer varios atributos coincidentes. Si hay varios, se evalúan en el orden definido por este campo. En el momento en que se encuentre una coincidencia, no se evaluarán más atributos coincidentes.
- **Aplicar esta asignación**
    - **Siempre**: esta asignación se aplica a las acciones de creación y actualización de usuarios
    - **Solo durante la creación**: esta asignación se aplica solo a las acciones de creación de usuarios


## <a name="editing-group-attribute-mappings"></a>Edición de asignaciones de atributos de grupo

Algunas aplicaciones seleccionadas, como ServiceNow, Box y Google Apps, admiten la posibilidad de aprovisionar objetos de grupo además de objetos de usuario. Los objetos de grupo pueden contener propiedades de grupo como nombres para mostrar y alias de correo electrónico, además de miembros de grupo.

![ServiceNow][8]  

El aprovisionamiento de grupos se puede habilitar o deshabilitar de manera opcional; para ello, seleccione la asignación de grupo en **Asignaciones** y establezca **Habilitado** en la opción deseada en la pantalla **Asignación de atributos**.

Los atributos suministrados como parte de los objetos de grupo se pueden personalizar de la misma manera que los objetos de usuario descritos anteriormente. 

>[!TIP]
>El aprovisionamiento de objetos de grupo (propiedades y miembros) es un concepto diferente al de [asignación de grupos](active-directory-coreapps-assign-user-azure-portal.md) a una aplicación. Es posible asignar un grupo a una aplicación, pero solo se pueden aprovisionar los objetos de usuario contenidos en el grupo. El aprovisionamiento de objetos de grupo completos no es necesario para usar grupos en asignaciones.


## <a name="editing-the-list-of-supported-attributes"></a>Edición de la lista de atributos admitidos

Los atributos de usuario admitidos en una determinada aplicación están preconfigurados. Las API de administración de usuarios de la mayoría de las aplicaciones no admiten la detección de esquemas; por lo tanto, el servicio de aprovisionamiento de Azure AD no puede generar dinámicamente la lista de atributos admitidos mediante llamadas a la aplicación. 

Sin embargo, algunas aplicaciones admiten atributos personalizados. Para que el servicio de aprovisionamiento de Azure AD pueda leer atributos personalizados y escribir en ellos, se deben especificar sus definiciones en Azure Portal mediante la casilla **Mostrar opciones avanzadas** de la parte inferior de la pantalla **Asignación de atributos**.

Las aplicaciones y sistemas que admiten la personalización de la lista de atributos son:

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory
* Active Directory local (como parte del conector de aprovisionamiento de usuarios de Workday)
* Aplicaciones que admiten [SCIM 2.0](https://tools.ietf.org/html/rfc7643), donde es necesario agregar los atributos definidos en el [esquema principal](https://tools.ietf.org/html/rfc7643)

>[!NOTE]
>La edición de la lista de atributos admitidos solo se recomienda para administradores que hayan personalizado el esquema de sus aplicaciones y sistemas, y tengan conocimiento de primera mano de cómo se han definido sus atributos personalizados. A veces, es necesario estar familiarizado con las API y las herramientas de los desarrolladores que se proporcionan en una aplicación o un sistema. 

![Editor][9]  

Al editar la lista de atributos admitidos, se proporcionan las siguientes propiedades:

* **Nombre**: el nombre del sistema del atributo, tal como se define en el esquema del objeto de destino. 
* **Tipo**: el tipo de datos que almacena el atributo, tal como se define en el esquema del objeto de destino. Puede ser uno de los siguientes:
   * *Binario*: el atributo contiene datos binarios.
   * *Booleano*: el atributo contiene un valor True o False.
   * *DateTime*: el atributo contiene una cadena de fecha.
   * *Entero*: al atributo contiene un entero.
   * *Referencia*: el atributo contiene un identificador que hace referencia a un valor almacenado en otra tabla en la aplicación de destino.
   * *Cadena*: el atributo contiene una cadena de texto. 
* **¿Clave principal?** : si el atributo se define o no como un campo de clave principal en el esquema del objeto de destino.
* **¿Necesario?** : si el atributo se debe rellenar o no en la aplicación o sistema de destino.
* **¿Varios valores?** : si el atributo admite o no varios valores.
* **¿Coincidir mayúsculas y minúsculas?** : si los valores de atributo se evalúan o no según el uso de mayúsculas o minúsculas.
* **Expresión de API**: no se usa, salvo que así se indique en la documentación de un conector de aprovisionamiento específico (como Workday).
* **Atributo de objeto con referencia**: si este es un atributo de tipo referencia, este menú le permite seleccionar la tabla y el atributo de la aplicación de destino que contiene el valor asociado al atributo. Por ejemplo, si tiene un atributo llamado "Department" cuyo valor almacenado hace referencia a un objeto de una tabla "Departments" independiente, seleccionaría "Departments.Name". Tenga en cuenta que las tablas de referencia y los campos de identificador principal admitidos en una determinada aplicación están preconfigurados y actualmente no se pueden editar mediante Azure Portal, pero se pueden modificar con la [Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Para agregar un nuevo atributo, desplácese hasta el final de la lista de atributos admitidos, rellene los campos anteriores mediante las entradas proporcionadas y seleccione **Agregar atributo**. Cuando termine de agregar atributos, seleccione **Guardar**. Tendrá que volver a cargar la pestaña **Aprovisionamiento** para que los nuevos atributos estén disponibles en el editor de asignación de atributos.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restauración de los atributos predeterminados y las asignaciones de atributos

Si necesita comenzar de nuevo y restablecer las asignaciones existentes de nuevo a su estado predeterminado, puede activar la casilla **Restaurar asignaciones predeterminadas** y guardar la configuración. Todas las asignaciones se restablecen como si la aplicación se acabara de agregar al inquilino de Azure AD desde la galería de aplicaciones. 

Al seleccionar esta opción se fuerza la resincronización de todos los usuarios mientras se ejecuta el servicio de aprovisionamiento. 

>[!IMPORTANT]
>Se recomienda firmemente establecer el **estado de aprovisionamiento** en **Desactivado** antes de invocar esta opción.


## <a name="what-you-should-know"></a>Qué debería saber

* Microsoft Azure AD proporciona una implementación eficaz de un proceso de sincronización. En un entorno inicializado, sólo los objetos que requieren actualizaciones se procesan durante un ciclo de sincronización. 

* Actualizar las asignaciones de atributos repercute en el rendimiento de un ciclo de sincronización. Una actualización de la configuración de la asignación de atributos requiere que se vuelvan a evaluar todos los objetos administrados. 

* Es un procedimiento recomendado mantener el número mínimo de cambios consecutivos de las asignaciones de atributos.


## <a name="next-steps"></a>Pasos siguientes

* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de ámbito para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
* [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

