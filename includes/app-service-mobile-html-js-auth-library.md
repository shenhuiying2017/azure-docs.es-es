##<a name="server-auth"></a>Autenticación con un proveedor (flujo de servidor)

Para que Servicios móviles administre el proceso de autenticación en su aplicación, debe registrar esta última en el proveedor de identidades. A continuación, en el Servicio de aplicaciones de Azure, tendrá que configurar el identificador y el secreto de la aplicación proporcionados por el proveedor. Para obtener más información, consulte el tutorial [Incorporación de autenticación a la aplicación de Servicios móviles].

Cuando haya registrado el proveedor de identidades, llame simplemente al método .login() con el nombre de su proveedor. Por ejemplo, para iniciar sesión con Facebook, use el siguiente código:

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Si usa un proveedor de identidades que no sea Facebook, cambie el valor transferido al método de inicio de sesión anterior por uno de los siguientes: `microsoftaccount`, `facebook`, `twitter`, `google` o `aad`.

En este caso, el Servicio de aplicaciones de Azure administra el flujo de autenticación de OAuth 2.0: muestra la página de inicio de sesión del proveedor seleccionado y genera un token de autenticación del Servicio de aplicaciones después de que se realice un inicio de sesión correcto con el proveedor de identidades. La función login, cuando se completa, devuelve un objeto JSON (user) que expone el identificador de usuario y el token de autenticación del Servicio de aplicaciones en los campos userId y authenticationToken, respectivamente. El token puede almacenarse en caché y volver a usarse hasta que expire.

##<a name="client-auth"></a>Autenticación con un proveedor (flujo de cliente)

La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y proporcionar el token devuelto al Servicio de aplicaciones para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

### Ejemplo básico de autenticación social

Este ejemplo usa el SDK de cliente de Facebook para la autenticación:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
En este ejemplo se asume que el token proporcionado por el SDK del proveedor correspondiente se almacena en la variable token.

### Ejemplo de Cuenta Microsoft

El siguiente ejemplo usa el SDK de Live, que admite el inicio de sesión único para aplicaciones de la Tienda Windows mediante la cuenta de Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

En este ejemplo simplificado se obtiene un token de Live Connect, que se suministra al Servicio de aplicaciones mediante la llamada a la función login.

##<a name="auth-getinfo"></a>Obtención de información sobre el usuario autenticado

Se puede recuperar la información de autenticación del usuario actual desde el punto de conexión `/.auth/me` mediante cualquier método de AJAX. Por ejemplo, para usar la API de captura:

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

También podría utilizar jQuery u otra API de AJAX para capturar la información. Los datos se reciben como un objeto JSON.

<!----HONumber=AcomDC_0309_2016-->
