---
layout: post
published: false
title: Python Pusher in AWS Lambda
---
Trabajando con la libreria de Pusher para Python en Lambda, me encontre con un pequeño problema de compilación al momento de hacer deploy (luego logre resolverlo, creando una nueva instancia EC2 para compilar para lambda), luego de intentar varias cosas, sin gran resultado, hice lo mas simple, crear un snippet para enviar peticiones a Pusher usando su api.

El resultado pueden verlo acá: 

Lo ire desmenuzando.

Como esta pensado para funcionar en aws lambda, utilice serverless 1.0 para crearlo, utilizando la libreria `requests`, luego una pequeña clase que se encarga de tokenizar el mensaje que se usa en su API REST (esto es pedido por Pusher en su [documentación](https://pusher.com/docs/rest_api)).

Para ello debemos construir la url firmada para realizar la petición.

```python
   def send_event(self, pusher_message):
        partial_path = '/apps/{0}/events'.format(self.app_id)
        query_string = self.create_signed_query(
            partial_path, 'POST', pusher_message)

        full_path = 'https://api.pusherapp.com/{0}?{1}'.format(
            partial_path, query_string)

        headers = {'Content-Type': 'application/json'}
        response = requests.post(
            full_path,
            data=json.dumps(pusher_message),
            headers=headers)

        print response.status_code
        if response.status_code == 200:
            return True

        print response.content
        return False
```

Primero necesitamos el mensaje a enviar:
```
message = {
	"name": '<nombre-del-evento>,
    "data": '<contenido-del-mensaje>',
    "channel": '<nombre-del-canal-a-enviar-el-mensaje>'
}
```

Y parte de la url del metodo que queremos usar, en este caso iremos a generar un evento:
`/apps/{app_id}/events`, luego el metodo de petición a utilizar, en este caso usaremos `POST` y por ultimo necesitamos el mensaje a enviar.

Con esto se genera el `query_string` y se construye el path final para hacer la petición, luego es solamente armar la petición utilizando headers para `json` y agregando el mensaje al body. Despues de obtener un `status_code == 200`, damos por terminado el envio.

## ¿Pero y como firmamos el `query_string` ?

```python
def sign(self, content):
        return hmac.new(self.secret, content, hashlib.sha256).hexdigest()

    def create_signed_query(self, partial_path, method, request_params):
        params = {
            'auth_key': self.key,
            'auth_timestamp': int(time.time()),
            'auth_version': '1.0'
        }

        if request_params is not None:
            params['body_md5'] = md5.new(
                json.dumps(request_params)).hexdigest()

        keys = sorted(params.keys())
        params_list = []
        for k in keys:
            params_list.append('{0}={1}'.format(k, params[k]))

        query_string = '&'.join(params_list)

        sign_data = '\n'.join([method, partial_path, query_string])
        query_string += '&auth_signature=' + self.sign(sign_data)
        return query_string
```

Si hay datos en el `request_params`, entonces, creamos un parametro `body_md5` creando un hash md5 de él. Luego agregamos todo el listado de params que necesita la api:

```python
params = {
	'auth_key': <key-de-pusher>,
    'auth_timestamp': <int-del-tiempo-actual>,
    'auth_version': '1.0'  # Para esta versión de la api
}
```




    



Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
