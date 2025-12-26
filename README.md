# API REST PHP – Backend para Aplicación Móvil

## Descripción
API REST desarrollada en PHP que implementa operaciones CRUD para la gestión de datos.  
Esta API es consumida por una aplicación móvil Android desarrollada en **Xamarin.Forms (C#)**, permitiendo la comunicación entre el cliente móvil y el servidor mediante solicitudes HTTP y respuestas en formato JSON.

El proyecto fue desarrollado como backend para una aplicación móvil, aplicando principios de arquitectura cliente-servidor.

## Tecnologías utilizadas
- PHP
- MySQL
- XAMPP
- JSON
- HTTP / REST
- Xamarin.Forms (C#)
- Visual Studio 2022
- Git / GitHub

## Estructura del proyecto
- `config.php` → Configuración de conexión a base de datos
- `index.php` → Punto de entrada principal de la API
- `post.php` → Lógica CRUD (Create, Read, Update, Delete)
- `utils.php` → Funciones auxiliares y manejo de respuestas

## Capturas de pantalla
### config.php
```
<?php

$db = [
    'host' => 'localhost',
    'username' => 'root',
    'password' => '',
    'db' => 'dbagencia'
];

?>
```
### index.php
```
<?php
header("HTTP/1.1 200 OK");

if($_SERVER['REQUEST_METHOD'] == 'POST'){
    
}
?>
```
### post.php
```
<?php
include "config.php";
include "utils.php";


$dbConn =  connect($db);

/*
  listar todos los usuarios o solo uno
 */
if ($_SERVER['REQUEST_METHOD'] == 'GET')
{
    if (isset($_GET['id']))
    {
      //Mostrar un usuario
      $sql = $dbConn->prepare("SELECT * FROM usuario where id=:id");
      $sql->bindValue(':id', $_GET['id']);
      $sql->execute();
      header("HTTP/1.1 200 OK");
      echo json_encode(  $sql->fetch(PDO::FETCH_ASSOC)  );
      exit();
	  }
    else {
      //Mostrar lista de usuario
      $sql = $dbConn->prepare("SELECT * FROM usuario");
      $sql->execute();
      $sql->setFetchMode(PDO::FETCH_ASSOC);
      header("HTTP/1.1 200 OK");
      echo json_encode( $sql->fetchAll()  );
      exit();
	}
}

// Crear un nuevo usuario
if ($_SERVER['REQUEST_METHOD'] == 'POST')
{
    $input = $_POST;
    $sql = "INSERT INTO usuario
          (correo, password)
          VALUES
          (:correo, :password)";
    $statement = $dbConn->prepare($sql);
    bindAllValues($statement, $input);
    $statement->execute();
    $postId = $dbConn->lastInsertId();
    if($postId)
    {
      $input['id'] = $postId;
      header("HTTP/1.1 200 OK");
      echo json_encode($input);
      exit();
	 }
}

//Borrar
if ($_SERVER['REQUEST_METHOD'] == 'DELETE')
{
	$id = $_GET['id'];
  $statement = $dbConn->prepare("DELETE FROM usuario where id=:id");
  $statement->bindValue(':id', $id);
  $statement->execute();
	header("HTTP/1.1 200 OK");
	exit();
}

//Actualizar
if ($_SERVER['REQUEST_METHOD'] == 'PUT')
{
    $input = $_GET;
    $postId = $input['id'];
    $fields = getParams($input);

    $sql = "
          UPDATE usuario
          SET $fields
          WHERE id='$postId'
           ";

    $statement = $dbConn->prepare($sql);
    bindAllValues($statement, $input);

    $statement->execute();
    header("HTTP/1.1 200 OK");
    exit();
}


//En caso de que ninguna de las opciones anteriores se haya ejecutado
header("HTTP/1.1 400 Bad Request");

?>
```
### utils.php
```
<?php

  //Abrir conexion a la base de datos
  function connect($db)
  {
      try {
          $conn = new PDO("mysql:host={$db['host']};dbname={$db['db']}", $db['username'], $db['password']);

          // set the PDO error mode to exception
          $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

          return $conn;
      } catch (PDOException $exception) {
          exit($exception->getMessage());
      }
  }


 //Obtener parametros para updates
 function getParams($input)
 {
    $filterParams = [];
    foreach($input as $param => $value)
    {
            $filterParams[] = "$param=:$param";
    }
    return implode(", ", $filterParams);
	}

  //Asociar todos los parametros a un sql
	function bindAllValues($statement, $params)
  {
		foreach($params as $param => $value)
    {
				$statement->bindValue(':'.$param, $value);
		}
		return $statement;
   }
 ?>
```

## Funcionalidades
- Crear registros (POST)
- Obtener registros (GET)
- Actualizar registros (PUT)
- Eliminar registros (DELETE)
- Respuestas estructuradas en JSON
- Validación básica de datos

## Cómo ejecutar el proyecto
1. Clonar el repositorio: git clone https://github.com/MReyes-Soft/agencia.git
2. Copiar el proyecto en el servidor local (XAMPP / WAMP)
3. Configurar credenciales en `config.php`
4. Importar la base de datos MySQL
5. Consumir los endpoints desde la app móvil Xamarin

## Integración con la aplicación móvil
La API es consumida desde una aplicación Android desarrollada en **Xamarin.Forms**, utilizando peticiones HTTP desde C# para enviar y recibir datos en formato JSON.

## Estado del proyecto
API funcional utilizada como backend para aplicación móvil.

## Autor
Miguel Reyes Sanchez
Ingeniero de Software con Inteligencia Artificial
GitHub: https://github.com/MReyes-Soft
