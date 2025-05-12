# Configuración de Conexión entre PHP y SQL Server

Este documento explica paso a paso cómo se configuró la conexión entre una página PHP y una base de datos SQL Server para el proyecto **PuntoVentaCine**.

---

## Requisitos Previos

- Tener instalado **SQL Server** y **SQL Server Management Studio (SSMS)**.
- Tener instalado **XAMPP** con PHP 8.2 o superior.
- Acceso a un navegador para probar la conexión vía `localhost`.

---

## 1. Habilitar extensiones necesarias en PHP

PHP no soporta SQL Server de forma nativa, por lo tanto, es necesario habilitar las extensiones:

### Archivos DLL necesarios

- `php_pdo_sqlsrv.dll`
- `php_sqlsrv.dll`

### Ubicación

Asegúrate de que los archivos `.dll` estén en la carpeta:

C:/xampp/php/ext/

shell
Mostrar siempre los detalles

Copiar

### Activarlas en el archivo `php.ini`

Abrir el archivo de configuración PHP ubicado en:
``` makefile
C:/xampp/php/php.ini
```

- Y agregar o descomentar estas líneas:

```ini
extension=php_sqlsrv.dll
extension=php_pdo_sqlsrv.dll
```

Luego, reinicia Apache desde el panel de control de XAMPP.

### 2. Verificar que sqlsrv esté activo
Crear un archivo llamado info.php en la carpeta del proyecto:

```php
<?php
phpinfo();
?>
```
Luego ingresar en el navegador:

Copiar
http://localhost/PROYECTO/info.php
Presionar Ctrl + F y buscar sqlsrv. Si aparece, significa que las extensiones están correctamente habilitadas.

### 3. Crear la base de datos PuntoVentaCine
Desde SSMS (SQL Server Management Studio):

Ejecutar el siguiente script:
sql
Mostrar siempre los detalles

```
IF DB_ID('PuntoVentaCine') IS NULL
BEGIN
    CREATE DATABASE PuntoVentaCine;
END;
GO

USE PuntoVentaCine;
GO
```
Y luego, ejecutar el resto del script con la estructura de tablas del proyecto (como Cliente, Pelicula, Sala, etc.).

### 4. Configurar el usuario SQL (sa)
A. Habilitar autenticación SQL Server
Clic derecho sobre el servidor en SSMS → Properties

En la pestaña Security, seleccionar:

SQL Server and Windows Authentication mode

Reiniciar el servidor (Right click > Restart)

B. Configurar usuario sa
Ir a:

Security > Logins > sa
Clic derecho en sa → Properties

Establecer una contraseña:

Ejemplo: D0m1n1c_@

En la pestaña Status:

Login: Enabled

### 5. Crear archivo conexion.php
Crear el archivo en la raíz del proyecto:
```php
<?php
$serverName = "ADESS";
$connectionOptions = array(
    "Database" => "PuntoVentaCine",
    "Uid" => "sa",
    "PWD" => "D0m1n1c_@",
    "CharacterSet" => "UTF-8"
);

$conn = sqlsrv_connect($serverName, $connectionOptions);

if ($conn) {
    echo "✅ Conexión exitosa a SQL Server.";
} else {
    echo "❌ Error en la conexión.<br>";
    die(print_r(sqlsrv_errors(), true));
}
?>
🧪 Probar conexión
```
Ingresar en el navegador:

http://localhost/PROYECTO/conexion.php
Resultado: Si todo fue configurado correctamente, se mostrará:

Conexión exitosa a SQL Server.
Esto indica que tu servidor web PHP ahora está comunicándose correctamente con SQL Server.
