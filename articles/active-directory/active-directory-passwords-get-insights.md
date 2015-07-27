<properties 
	pageTitle="Obtener información sobre los informes de administración de contraseñas de Azure AD | Microsoft Azure" 
	description="En este artículo se describe cómo usar los informes para obtener información sobre las operaciones de administración de contraseñas en su organización." 
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
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# Visión operativa con los informes de la administración de contraseñas
En esta sección se describe cómo puede usar informes de administración de contraseñas de Azure Active Directory para ver cómo utilizan los usuarios el restablecimiento y el cambio de contraseña en su organización.

- [**Información general de los informes de administración de contraseñas**](#overview-of-password-management-reports)
- [**Visualización de los informes de administración de contraseñas**](#how-to-view-password-management-reports)
- [**Visualización de la actividad de registro de restablecimiento de contraseña en su organización**](#view-password-reset-registration-activity)
- [**Visualización de la actividad de restablecimiento de contraseña en su organización**](#view-password-reset-activity)

## Información general de los informes de administración de contraseñas
Después de implementar el restablecimiento de contraseña, uno de los siguientes pasos más comunes es ver cómo se usa en su organización. Por ejemplo, puede que desee saber cómo se registran los usuarios para el restablecimiento de contraseña o cuántos restablecimientos de contraseña se han realizado en los últimos días. Estas son algunas de las preguntas comunes que podrá responder con los informes de administración de contraseñas que existen actualmente en el [Portal de administración de Azure](https://manage.windowsazure.com):

- ¿Cuántas personas se han registrado para el restablecimiento de contraseña?
- ¿Quién se ha registrado para el restablecimiento de contraseña?
- ¿Qué datos está registrando la gente?
- ¿Cuántas personas restablecieron sus contraseñas en los últimos 7 días?
- ¿Cuáles son los métodos más comunes que utilizan los usuarios o administradores para restablecer sus contraseñas?
- ¿Cuáles son los problemas comunes que encuentran los usuarios o administradores al intentar utilizar el restablecimiento de contraseña?
- ¿Qué administradores restablecen sus propias contraseñas con frecuencia?
- ¿Hay alguna actividad sospechosa en relación con el restablecimiento de contraseña?


## Visualización de los informes de administración de contraseñas
Para buscar los informes de administración de contraseñas, siga estos pasos:

1.	Haga clic en la extensión de **Active Directory** en el [Portal de administración de Azure](https://manage.windowsazure.com).
2.	Seleccione el directorio de la lista que aparece en el portal.
3.	Haga clic en la pestaña **Informes**.
4.	Busque en la sección **Registros de actividad**.
5.	Seleccione el informe **Actividad de restablecimiento de contraseña** o el informe **Actividad de registro de restablecimiento de contraseña**.

    ![][001]

## Visualización de la actividad de registro de restablecimiento de contraseña en su organización

El informe de actividad de registro de restablecimiento de contraseña muestra todos los registros de restablecimiento de contraseña que se han producido en su organización. En este informe, se muestra un registro de restablecimiento de contraseña para cualquier usuario que haya registrado correctamente la información de autenticación en el portal de registro de restablecimiento de contraseña ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Intervalo de tiempo máximo**: 1 mes
- **Número máximo de filas**: ilimitado
- **Se puede descargar**: Sí, en un archivo CSV

    ![][002]

### Descripción de las columnas del informe
La siguiente lista explica en detalle cada una de las columnas del informe:

- **Usuario**: usuario que intentó una operación de registro de restablecimiento de contraseña.
- **Rol**: rol del usuario en el directorio.
- **Fecha y hora**: fecha y hora del intento.
- **Datos registrados**: datos de autenticación que el usuario proporcionó durante el registro de restablecimiento de contraseña.

### Descripción de los valores del informe
En la tabla siguiente se describen los distintos valores permitidos para cada columna:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Columna</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Valores permitidos y su significado</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Datos registrados</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Correo electrónico alternativo</strong>: el usuario utilizó un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Teléfono de la oficina</strong>: el usuario utilizó el teléfono de la oficina para autenticarse.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Teléfono móvil</strong>: el usuario utilizó un teléfono móvil o un teléfono de autenticación para autenticarse.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Preguntas de seguridad</strong>: el usuario utilizó preguntas de seguridad para autenticarse.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong> Cualquier combinación de los valores anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)</strong>: tiene lugar cuando se especifica una directiva de 2 puertas y muestra los dos métodos que el usuario utilizó para autenticar su solicitud de restablecimiento de contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Visualización de la actividad de restablecimiento de contraseña en su organización

Este informe muestra todos los intentos de restablecimiento de contraseña que se han producido en su organización.

- **Intervalo de tiempo máximo**: 1 mes
- **Número máximo de filas**: ilimitado
- **Se puede descargar**: Sí, en un archivo CSV

    ![][003]

### Descripción de las columnas del informe
La siguiente lista explica en detalle cada una de las columnas del informe:

1. **Usuario**: usuario que intentó una operación de restablecimiento de contraseña (basado en el campo Id. de usuario cuando el usuario intenta restablecer una contraseña).
2. **Rol**: rol del usuario en el directorio.
3. **Fecha y hora**: fecha y hora del intento.
4. **Métodos usados**: métodos de autenticación que utiliza el usuario para esta operación de restablecimiento.
5. **Resultado**: resultado final de la operación de restablecimiento de contraseña.
6. **Detalles**: detalles de por qué el restablecimiento de contraseña dio ese resultado. También incluye los pasos de mitigación que podría seguir para resolver un error inesperado.

### Descripción de los valores del informe
En la tabla siguiente se describen los distintos valores permitidos para cada columna:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Columna</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Valores permitidos y su significado</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Métodos usados</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Correo electrónico alternativo</strong>: el usuario utilizó un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Teléfono de la oficina</strong>: el usuario utilizó el teléfono de la oficina para autenticarse.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Teléfono móvil</strong>: el usuario utilizó un teléfono móvil o un teléfono de autenticación para autenticarse.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Preguntas de seguridad</strong>: el usuario utilizó preguntas de seguridad para autenticarse.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong> Cualquier combinación de los valores anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)</strong>: tiene lugar cuando se especifica una directiva de 2 puertas y muestra los dos métodos que el usuario utilizó para autenticar su solicitud de restablecimiento de contraseña.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Resultado</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Abandonado</strong>: el usuario inició un restablecimiento de contraseña, pero lo interrumpió antes de que finalizara.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Bloqueado</strong>: se impidió que la cuenta de usuario usara el restablecimiento de contraseña debido a un intento de utilizar la página de restablecimiento de contraseña o una sola puerta de restablecimiento de contraseña demasiadas veces en un período de 24 horas<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Cancelado</strong>: el usuario inició un restablecimiento de contraseña, pero después hizo clic en el botón Cancelar para cancelar la sesión en mitad del proceso. <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Administrador contactado</strong>: el usuario tuvo un problema durante su sesión que no pudo resolver, por lo que hizo clic en el vínculo "Póngase en contacto con el administrador" en lugar de finalizar el flujo de restablecimiento de contraseña.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Con error</strong>: el usuario no pudo restablecer una contraseña, probablemente porque el usuario no estaba configurado para usar esta característica (por ejemplo, no tiene licencia, falta información de autenticación, la contraseña se administra en el entorno local pero la escritura diferida está desactivada).<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Correcto</strong>: la contraseña se restableció correctamente.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Detalles</p>
              </td>
              <td>
                <p>Consulte la tabla siguiente:</p>
              </td>
            </tr>
          </tbody></table>

### Valores permitidos para la columna Detalles
A continuación se muestra la lista de los tipos de resultados que puede esperar cuando usa el informe de actividad de restablecimiento de contraseña:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Detalles</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Tipo de resultado</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras completar la opción de comprobación de correo electrónico.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras completar la opción de comprobación por SMS en el teléfono móvil.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras completar la opción de comprobación por llamada de voz al teléfono móvil.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras completar la opción de comprobación por llamada de voz a la oficina.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras completar la opción de preguntas de seguridad.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó después de escribir su identificador de usuario.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras iniciar la opción de comprobación de correo electrónico.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras iniciar la opción de comprobación por SMS en el teléfono móvil.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras iniciar la opción de comprobación por llamada de voz al teléfono móvil.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras iniciar la opción de comprobación por llamada de voz a la oficina.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó tras iniciar la opción de preguntas de seguridad.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó antes de seleccionar una nueva contraseña.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario abandonó mientras seleccionaba una nueva contraseña.</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario escribió demasiados códigos de comprobación de correo electrónico no válidos y se ha bloqueado durante 24 horas.</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario escribió demasiados códigos de comprobación por SMS no válidos y se ha bloqueado durante 24 horas.</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario intentó la comprobación por llamada de voz al teléfono móvil demasiadas veces y se ha bloqueado durante 24 horas.</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario intentó la comprobación por llamada de voz al teléfono de la oficina demasiadas veces y se ha bloqueado durante 24 horas.</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario intentó responder las preguntas de seguridad demasiadas veces y se ha bloqueado durante 24 horas.</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario intentó comprobar un número de teléfono demasiadas veces y se ha bloqueado durante 24 horas.</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario lo canceló antes de pasar a los métodos de autenticación requeridos.</p>
              </td>
              <td>
                <p>Cancelado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario lo canceló antes de enviar una contraseña nueva.</p>
              </td>
              <td>
                <p>Cancelado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario se puso en contacto con un administrador después de intentar la opción de comprobación de correo electrónico.</p>
              </td>
              <td>
                <p>Administrador contactado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por SMS en el teléfono móvil.</p>
              </td>
              <td>
                <p>Administrador contactado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por llamada de voz al teléfono móvil.</p>
              </td>
              <td>
                <p>Administrador contactado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por llamada de voz a la oficina.</p>
              </td>
              <td>
                <p>Administrador contactado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario se puso en contacto con un administrador después de intentar la opción de comprobación con preguntas de seguridad.</p>
              </td>
              <td>
                <p>Administrador contactado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El restablecimiento de contraseña no está habilitado para este usuario. Habilite el restablecimiento de contraseña en la pestaña Configurar para resolver este problema.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario no tiene una licencia. Puede agregar una licencia al usuario para resolver este problema.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario intentó el restablecimiento desde un dispositivo sin las cookies habilitadas.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>La cuenta del usuario no tiene definidos suficientes métodos de autenticación. Agregue información de autenticación para resolver este problema.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>La contraseña del usuario se administra en el entorno local. Puede habilitar la escritura diferida de contraseña para resolver este problema.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>No pudimos obtener acceso a su servicio de restablecimiento de contraseña local. Compruebe el registro de eventos de su máquina de sincronización.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Se encontró un problema durante el restablecimiento de la contraseña local del usuario. Compruebe el registro de eventos de su máquina de sincronización.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Este usuario no es miembro del grupo de usuarios de restablecimiento de contraseña. Agregue este usuario a ese grupo para resolver este problema.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El restablecimiento de contraseña se ha deshabilitado por completo para este inquilino. Consulte <a href="http://aka.ms/ssprtroubleshoot">http://aka.ms/ssprtroubleshoot</a> para resolver este problema.</p>
              </td>
              <td>
                <p>Con error</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>El usuario restableció la contraseña correctamente.</p>
              </td>
              <td>
                <p>Correcto</p>
              </td>
            </tr>
          </tbody></table>

<br/> <br/> <br/>

**Recursos adicionales**


* [Qué es la administración de contraseñas](active-directory-passwords.md)
* [Funcionamiento de la administración de contraseñas](active-directory-passwords-how-it-works.md)
* [Introducción a la administración de contraseñas](active-directory-passwords-getting-started.md)
* [Personalización de la administración de contraseñas](active-directory-passwords-customize.md)
* [Prácticas recomendadas de la administración de contraseñas](active-directory-passwords-best-practices.md)
* [Preguntas más frecuentes sobre la administración de contraseñas](active-directory-passwords-faq.md)
* [Solución de problemas de administración de contraseñas](active-directory-passwords-troubleshoot.md)
* [Más información](active-directory-passwords-learn-more.md)
* [Administración de contraseñas en MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"
 

<!---HONumber=July15_HO3-->