Pruebas de Docker con Slim Framework

### Prerequisitos:

Tener en la máquina instalado composer, docker, docker-compose

### Configuración:

- Crear una estructura de directorio tal que:

```
.
├── [Nombre proyecto]
│   └── src
│       └── public
```

- En public crear un .htaccess:
```
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule . index.php [L]`
```

- Y un index.php de ejemplo:

```php
<?php
use \Psr\Http\Message\ServerRequestInterface as Request;
use \Psr\Http\Message\ResponseInterface as Response;

require '../vendor/autoload.php';

$app = new \Slim\App;
$app->get('/hello/{name}', function (Request $request, Response $response) {
    $name = $request->getAttribute('name');
    $response->getBody()->write("Hello, $name");

    return $response;
});
$app->run();
```

- En la raiz del proyecto crear un docker-compose.yaml con este contenido de ejemplo:

```yaml
version: '2'
services:
 
  slim-example:
    image: comicrelief/php7-slim:latest
    volumes:
      - ~/Examples/slim-example:/var/www/html
    ports:
      - "9001:80"
    depends_on:
      - slim-example-mysql
    environment:
      APPLICATION_ENV: local
 
  slim-example-mysql:
    image: mysql:5
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: mypassword
      MYSQL_DATABASE: slim-example
```

### Instalación: 

- Crear la nueva instancia de Slim en src:

```
composer require slim/slim "^3.0"
```

- Ejecutar:

```
docker-compose up -d
```

- Alternativamente se puede ejecutar ```docker-compose down``` para parar los contenedores de docker activos

### Comprobación

Si todo ha salido correctamente, en http://localhost:9001/hello/loquesea debe aparecer "Hello loquesea"