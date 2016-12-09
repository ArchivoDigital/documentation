# Archivo Digital
[![N|Solid](https://archivo.digital/images/archivo-digital-white.png)](https://archivo.digital/#/)

Esta guía te ayudará a desarrollar tus apps con Archivo.Digital, si tiene alguna duda escríbenos a soporte@archivo.digital

## Table of Contents
1. [Cuenta](#Cuenta)
 1.1. [Crear una cuenta en Archivo Digital](#Creación)
 1.2. [Dominio](#Dominio)
 1.3. [Modelo](#Modelo)
2. [User](#User)
    2.1.  [Registro](#Registro)
    2.2.  [Login](#Login)
3. [Modelos](#Modelos)
    3.1.  [insert](#Insertar)
    3.2.  [update](#Actualizar)
    3.3.  [delete](#Eliminar)
    3.4.  [find](#Consultas)
    3.5.  [find where](#Filtros)

## Cuenta

#### Creación
Para poder utilizar el API, primero debes tener una cuenta en [Archivo.Digital](https://archivo.digital/app/#/register), no te preocupes, registrarte es totalmente gratuito.

#### Dominio
Un dominio es una agrupación de Archivos + Datos + Usuarios, puedes crear cuantos dominios desees, por ejemplo, puedes tener un dominio por cada entorno de un proyecto (desarrollo, pruebas, producción) o puedes tener un dominio por cada cliente de tu app, ya todo depende de la manera que quieras modelar tu proyecto.

#### Modelo
Un modelo podría decirse que es el equivalente a una tabla en una base de datos. A su vez cada modelo va a tener propiedades, estas propiedades pueden ser de diferentes tipos de datos:

- String:  Para almacenar textos cortos como nombres, títulos, referencias, etc.
- Text: Para almacenar textos largos, como descripciones, el cuerpo de una publicación, etc.
- Int: Para almacenar números enteros.
- Date: Para almacenar fechas con su respectivo tiempo.
- Float: Para almacenar números con decimales, por defecto se manejan  4 dígitos de precisión.
- Boolean: Para almacenar valores booleanos(verdadero / falso).
- Reference: Este tipo de dato especial representa la relación entre dos modelos, algo así como una llave foránea.


## User
A diferencia de los modelos, un User es una entidad global en Archivo Digital, que puede estar en uno o más dominios. Esto quiere decir que, si necesitas tener en tu dominio un usuario ya existente no debes registrarlo como un usuario nuevo sino enlazarlo con tu dominio.

#### Registro
Para registrar un usuario no existente en archivo digital debes enviar una petición de tipo GET. En la cabecera o Header debes colocarle “App-Key” cuyo valor es el APIKEY de tu APP en archivo digital.  La url de la peticion es https://archivo.digital/api/user/v1/register y los parametros a enviar son  email,  password,  name,  login (que sería como el username) y el id de tu dominio.
Si se envía el id del dominio, el usuario quedará enlazado con dicho dominio, si no se envía, el usuario quedará registrado en archivo digital pero no pertenecerá a tu dominio. 
##### curl

```curl
curl -X GET -H "App-Key: MY_APP_KEY" "https://archivo.digital/api/user/v1/register?email=MY_USER_EMAIL&password=MY_USER_PASSWORD&name=MY_NAME&login=MY_USER_NAME&domain=MY_DOMAIN"
```
##### javascript JQuery
```javascript
var settings = {
  "async": true,
  "crossDomain": true,
  "url": "https://archivo.digital/api/user/v1/register?email=MY_USER_EMAIL&password=MY_USER_PASSWORD&name=MY_NAME&login=MY_USER_NAME&domain=MY_DOMAIN",
  "method": "GET",
  "headers": {
    "app-key": "MY_APP_KEY"
  }
}
$.ajax(settings).done(function (response) {
  console.log(response);
});
```

Si lo que deseas es enlazar un usuario existente a tu dominio, se realizaría una petición que debe ser ejecutada por el owner del dominioal cual se quiere enlazar:

##### curl

```curl
curl -X GET -H "App-Key: APP_KEY" -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" -H "Accept-Encoding: gzip" "https://archivo.digital/api/domain/v1/member/add?user=USER_ID&domain=MY_DOMAIN"
```
##### javascript JQuery
```javascript
var settings = {
  "async": true,
  "crossDomain": true,
  "url": "https://archivo.digital/api/domain/v1/member/add?user=USER_ID&domain=MY_DOMAIN",
  "method": "GET",
  "headers": {
    "app-key": "MY_APP_KEY",
    "Content-Type": "application/json",
    "Authorization": "Bearer TOKEN",
    "Accept-Encoding": "gzip"
  }
}
$.ajax(settings).done(function (response) {
  console.log(response);
});
```

donde

- user: ID del usuario que se enlazará con el dominio
- domain: ID del dominio al cual enlazar
- MY_APP_KEY: key de tu app en archivo digital
- TOKEN: token del usuario owner del dominio al cual se quiere enlazar (Este token se obtiene después de un loguin)

## Login
Para realizar el login de un usuario debes enviar una peticion GET y colocarle en la cabecera el APPKEY y  Accept-Encoding con valor gzip. La url es  https://archivo.digital/api/user/v1/login y los parametros son login y password. Dentro de la respuesta a esta petición hay un TOKEN que es con que ser realizaría la mayoria de sus peticiones.

##### curl

```curl
curl -X GET -H "App-Key: APPKEY" -H "Accept-Encoding: gzip" "https://archivo.digital/api/user/v1/login?login=MY_USERNAME&password=MY_PASSWORD"
```
##### javascript JQuery
```javascript
var settings = {
  "async": true,
  "crossDomain": true,
  "url": "https://archivo.digital/api/user/v1/login?login=MY_USERNAME&password=MY_PASSWORD",
  "method": "GET",
  "headers": {
    "app-key": "APPKEY",
    "accept-encoding": "gzip"
  }
}
$.ajax(settings).done(function (response) {
  console.log(response);
});
```

##### Respuesta
La respuesta generada tiene el siguiente formato 
```json
{
  "success": true,
  "token": "211ac1c4-6f06-8766-aac7-asdf76e",
  "user": {
    "id": 417, 
    "name": "my name",
    "code": null,
    "email": "myemail@email.com",
    "login": "myusername",
    "role": "CUSTOMER",
    "domain": "root",
    "display_name": "Carpeta Personal",
    "domain_id": 0,
    "membership_role": "USER",
    "member_of": [
      {
        "domain_id": 12,
        "domain": "mydominio",
        "display_name": "mydominio",
        "role": "USER",
        "name": "mydominio",
        "home": null,
        "config": {},
        "app": 16
      },
      {
        "domain_id": 0,
        "domain": "root",
        "display_name": "Carpeta Personal",
        "role": "USER"
      }
    ],
    "following": [],
    "home_folder_key": "ef64abcf-763b-9d8d-8e92-ad76d7abb37e"
  }
}
```
Donde "token" es el token de seguridad que permitirá realizar las demás operaciones  y "member_of" es el array de dominios a los que pertenece dicho usuario.

## Insertar

Para insertar un registro (o fila) en una tabla o Modelo se debe realizar una petición de tipo POST. Los parametros de la cabezera o header son App-Key,   Content-Type cuyo valor debe ser  application/json, Autorization cuyo valor debe ser la palabra Bearer y el TOKEN del usuario registrado, o del usuario dueño de la aplicación si el usuario no está registrado; además debe estar en la cabezera Accept-Encoding con valor gzip. Los parametros del Body debe ser un JSOn con los siguientes campos:

- domain: id del dominio
- row_model: la tabla o modelo donde vas a guardar la información
- rows: un array de Json con la informacion de la o la s filas que quieras agregar.Ejemplo:
-- [{"name":"Juan", "apellido":"Gomez"} ] una fila
-- [{"name":"Pedro", "apellido":"Peralta"}, {"name":"Pepita", "apellido":"Perez"}] dos filas

Esto se envía a la url  https://archivo.digital/api/data/v1/row

Supongamos que tenemos un modelo de nombre Producto que contine los atributos nombre, precio y una referencia a categoría. Nótese que el campo referencia, en este caso categoría, es el Key de la categoría a la cuál queremos hacer referencia.

##### curl

```curl
curl -X POST -H "App-Key: APP_KEY" -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" -H "Accept-Encoding: gzip" -d '{
"domain":"15", "row_model":"Producto", "rows":[{"nombre":"Papa","precio":500,"categoria":"ab87-87abc0-ab7600-76ef"} ]
}
' "https://archivo.digital/api/data/v1/row"
```
##### javascript JQuery
```javascript
var settings = {
  "async": true,
  "crossDomain": true,
  "url": "https://archivo.digital/api/data/v1/row",
  "method": "POST",
  "headers": {
    "app-key": "APP_KEY",
    "content-type": "application/json",
    "authorization": "Bearer TOKEN",
    "accept-encoding": "gzip"
  },
  "data" : {
    "domain":"15",
    "row_model":"Producto",
    "rows":[{
            "nombre":"Papa",
            "precio":500,
            "categoria":"ab87-87abc0-ab7600-76ef"
            }]
    }
}
$.ajax(settings).done(function (response) {
  console.log(response);
});
```

La respuesta a esta petición te dará un Key, que es el identificador de ese registro en la base de datos. Si tu modelo contiene referencias a otros modelos, por ejemplo una Factura contiene una referencia a otro modelo que se llama Orden de Venta, se debe guardar primero la Orden de Venta y luego cuando se desee guardar el registro de la factura, en el campo de ordenDeVenta de la factura a guardar se coloca el key de la Orden que se guardó.

## Actualizar

Es igual al agregar registro solo que la url de la petición es  https://archivo.digital/api/data/v1/row/update y se debe agregar el campo _KEY por cada fila que se desea actualizar.

##### curl

```curl
curl -X POST -H "Content-Type: application/json" -H "App-Key: APP_KEY" -H "Authorization: Bearer TOKEN" -H "Accept-Encoding: gzip" -d '{"domain":"15", "row_model":"Producto", "rows":[{"_KEY":"6fc1d120-ea1f-428d-8025-684ee5ebe85f","precio":700 }]
}
' "https://archivo.digital/api/data/v1/row/update"
```
##### javascript JQuery
```javascript
var settings = {
  "async": true,
  "crossDomain": true,
  "url": "https://archivo.digital/api/data/v1/row/update",
  "method": "POST",
  "headers": {
    "content-type": "application/json",
    "app-key": "APP_KEY",
    "authorization": "Bearer TOKEN",
    "accept-encoding": "gzip"
  },
  "data":{
    "domain":"15",
    "row_model":"Producto",
    "rows":[
        {"_KEY":"6fc1d120-ea1f-428d-8025-684ee5ebe85f",
        "precio":700 }]
    }
}
$.ajax(settings).done(function (response) {
  console.log(response);
});
```
## Consultas
Para realizar consultas sobre los datos basta con hacer una petición a "https://archivo.digital/api/data/v1/row/find" enviando como parametro el dominio y el modelo a consultar.


##### curl

```curl
curl -X POST -H "Content-Type: application/json" -H "App-Key: bcd864-abb2fb-48bab1-34fbca-ca76a3" -H "Authorization: Bearer 211ac0c4-6f06-4894-aac7-a2ea7967176e" -H "Accept-Encoding: gzip" -d '{"domain":85,"row_model":"personalInfo"}' "https://archivo.digital/api/data/v1/row/find"
' "https://archivo.digital/api/data/v1/row/update"
```

##### javascript JQuery
```javascript
var settings = {
  "async": true,
  "crossDomain": true,
  "url": "https://archivo.digital/api/data/v1/row/find",
  "method": "POST",
  "headers": {
    "content-type": "application/json",
    "app-key": "bcd864-abb4fb-48bab1-34fbca-ca76a3",
    "authorization": "Bearer 211ac1c4-6f06-4894-aac7-a2ea7967176e",
    "accept-encoding": "gzip"
  },
  "processData": false,
  "data": "{\"domain\":85,\"row_model\":\"personalInfo\"}"
}

$.ajax(settings).done(function (response) {
  console.log(response);
});

```

[//]: # ()
