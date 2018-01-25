## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Respuesta del servidor proxy de APIM con certificados SSL en el protocolo de enlace TLS

### <a name="clients-calling-with-sni-header"></a>Clientes que llaman con encabezado SNI
Si el cliente tiene uno o varios dominios personalizados configurados para el proxy, APIM puede responder a las solicitudes HTTPS desde los dominios personalizados (por ejemplo, contoso.com), así como desde el dominio predeterminado (por ejemplo, apim-service-name.azure-api.net). Según la información del encabezado de Indicación de nombre de servidor (SNI), APIM responde con el certificado de servidor correspondiente.

### <a name="clients-calling-without-sni-header"></a>Clientes que llaman sin encabezado SNI
Si el usuario usa un cliente, que no envía el encabezado [SNI](https://tools.ietf.org/html/rfc6066#section-3), APIM crea respuestas basadas en la lógica siguiente:

* Si el servicio tiene un solo dominio personalizado configurado para el proxy, el certificado predeterminado es el certificado emitido para el dominio personalizado de proxy.
* Si el servicio tiene varios dominios personalizados configurados para el proxy (solo se admite en el nivel **Premium**), el cliente puede designar qué certificado debe ser el certificado predeterminado. Para establecer el certificado predeterminado, la propiedad [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#hostnameconfiguration) debe establecerse en true ("defaultSslBinding":"true"). Si el cliente no establece la propiedad, el certificado predeterminado es el certificado emitido para el dominio de proxy predeterminado hospedado en *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Compatibilidad para la solicitud PUT/POST con una carga grande

El servidor proxy de APIM admite la solicitud con una carga grande cuando se usan certificados HTTPS del lado cliente (por ejemplo, carga > 40 KB). Para evitar que la solicitud del servidor se quede congelada, los clientes pueden establecer la propiedad ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#hostnameconfiguration) en el nombre de host del proxy. Si la propiedad está establecida en true, el certificado de cliente se solicita en el tiempo de conexión SSL/TLS, antes de intercambiar cualquier solicitud HTTP. Puesto que la configuración se aplica al nivel de **Nombre de host del proxy**, todas las solicitudes de conexión piden el certificado de cliente. Los clientes pueden configurar hasta veinte dominios personalizados para el proxy (solo se admite en el nivel **Premium**) y aplicar una solución temporal para esta limitación.

