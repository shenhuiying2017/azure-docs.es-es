<properties
   pageTitle="Configuración de alertas de seguridad | Microsoft Azure"
   description="Aprenda cómo configurar alertas de seguridad para la extensión de Privileged Identity Management de Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Configuración de alertas de seguridad en Privileged Identity Management de Azure AD

## Alertas de seguridad
Privileged Identity Management (PIM) de Azure genera las alertas siguientes, que se pueden ver en la sección de alertas del panel de PIM.

| Alerta | Desencadenador | Recomendación |
| ----- | ------- | -------------- |
| **Activación permanente** | A un administrador se le asignó un rol permanentemente, fuera de PIM. | Revise la nueva asignación de rol y cámbiela a temporal si es necesario. |
| **Activación sospechosa de renovación de roles con privilegios** | Había demasiadas reactivaciones del mismo rol con el tiempo permitido en la configuración. | Póngase en contacto con el usuario para asegurarse de que puede activar el rol correctamente. |
| **Autenticación débil configurada para la activación del rol** | Hay roles sin MFA en la configuración. | Considere la posibilidad de exigir MFA para la activación de todos los roles. |
| **Administradores redundantes** | Hay administradores temporales que no han activado sus roles recientemente. | Quite las asignaciones de roles que ya no vayan a ser necesarias. |
| **Demasiados administradores globales** | Hay más administradores globales de lo que se recomienda. | Quite las asignaciones de roles que ya no se necesiten o convierta algunas de ellas en temporales. |

## Configuración de alertas de seguridad

### Alerta de renovación de activación sospechosa de roles con privilegios

Configure los valores de **Período de tiempo de renovación de activaciones** y **Número de renovaciones de activación** para controlar cuándo se desencadena esta alerta.

1. En la sección **Actividad** del panel, seleccione **Alertas de seguridad**. Aparecerá la hoja **Activar alertas de seguridad**.
2. Haga clic en **Configuración**.
3. Establezca el **Plazo de renovación de activación** ajustando el control deslizante o especificando el número de minutos en el campo de texto. El valor máximo es 100.
4. Establezca el **Número de renovaciones de activación** en el plazo de renovación de activación ajustando el control deslizante o especificando el número de renovaciones en el campo de texto. El valor máximo es 100.
5. Haga clic en **Guardar**.

### Alerta de administradores redundantes
1. En la sección **Actividad** del panel, seleccione **Alertas de seguridad**. Aparecerá la hoja **Activar alertas de seguridad**.
2. Haga clic en **Configuración**.
3. Seleccione el número de días permitidos sin activación del rol ajustando el control deslizante o especificando el número de días en el campo de texto.
4. Haga clic en **Guardar**.

### Alerta de demasiados administradores globales

Existen dos configuraciones que pueden desencadenar esta alerta:
- **Número mínimo de Administradores globales** desencadenará la alerta si hay más administradores globales de lo permitido.
- **Porcentaje de Administradores globales** desencadenará la alerta si el porcentaje de administradores que son administradores globales es superior al valor permitido.

1. En la sección **Actividad** del panel, seleccione **Alertas de seguridad**. Aparecerá la hoja **Activar alertas de seguridad**.
2. Haga clic en **Configuración**.
3. Establezca el **Número mínimo de administradores globales** ajustando el control deslizante o especificando el número en el campo de texto.
4. Establezca **Porcentaje de Administradores globales** ajustando el control deslizante o especificando el porcentaje en el campo de texto.
5. Haga clic en **Guardar**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->