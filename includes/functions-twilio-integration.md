En este ejemplo se usa el servicio [Twilio](https://www.twilio.com/) para enviar mensajes SMS a un teléfono móvil. Azure Functions ya compatible con Twilio a través del [enlace de Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), el ejemplo utiliza esa característica.

Lo primero que necesita es una cuenta de Twilio. Puede crear una gratis en https://www.twilio.com/try-twilio. Cuando tenga una cuenta, agregue los tres **valores de configuración de la aplicación** siguientes a la aplicación de función.

| Nombre del valor de configuración de la aplicación | Descripción del valor |
| - | - |
| **TwilioAccountSid**  | Identificador de seguridad de la cuenta de Twilio |
| **TwilioAuthToken**   | Token de autenticación de la cuenta de Twilio |
| **TwilioPhoneNumber** | Número de teléfono asociado a la cuenta de Twilio, que se utiliza para enviar mensajes SMS. |