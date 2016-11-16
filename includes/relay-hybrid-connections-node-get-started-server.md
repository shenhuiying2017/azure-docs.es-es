### <a name="create-a-nodejs-application"></a>Creación de una aplicación Node.js
* Cree un nuevo archivo JavaScript denominado `listener.js`.

### <a name="add-the-relay-npm-package"></a>Adición del paquete Relay NPM
* Ejecute `npm install hyco-ws` desde un símbolo del sistema del nodo en la carpeta del proyecto.

### <a name="write-some-code-to-receive-messages"></a>Escritura de código para recibir mensajes
1. Agregue lo siguiente `constant` en la parte superior del archivo `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Agregue la siguiente instancia de Relay `constants` a `listener.js` para los detalles de conexión de la conexión híbrida. Reemplace los marcadores de posición entre corchetes por los valores adecuados obtenidos al crear la conexión híbrida.
   
   1. `const ns`: el espacio de nombres de Relay
   2. `const path`: el nombre de la conexión híbrida
   3. `const keyrule`: el nombre de la clave SAS
   4. `const key`: el valor de la clave SAS
3. Agregue el siguiente código al archivo `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Este es el aspecto que debería tener listener.cs:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```



<!--HONumber=Nov16_HO2-->


