# Autenticación en SQL SERVER
1. Autenticación en Windows
2. Auntenticación SQL

El flujo de autenticacíon es:

login -> servidor -> base de datos



``` sql
-- Crear un login de SQL
-- Este login da acceso al servidor
use master
go

-- Login con autenticación SQL
CREATE LOGIN developmentUser WITH PASSWORD=123456790

-- Login con la autenticación en Windows este debe existir en Windows
CREATE USER 'dominio\nombremaquina' FROM WINDOWS;

-- Crear un usuario asociado al login y mapeando una BD
--              usuario                    login
USE baseDeDatos;
CREATE USER  developmentUser FOR LOGIN developmentUser;

-- Permisos de manera individual para la base de datos
-- Permiso para crear tablas
GRANT CREATE TABLE TO developmentUser

-- Crea un esquema y se le indica quien tiene autorización
CREATE SCHEMA informatica
AUTHORIZATION developmentUser
go

-- Se crea el usuaio y se le asina el esquema

CREATE USER developmentUser
FOR LOGIN developmentUser
WITH DEFAULT_SCHEMA = informatica

-- Crear una tabla
GRANT CREATE TABLE TO developmentUser
-- Ver todas las tablas
GRANT SELECT TO developmentUser
-- Eliminar datos
GRANT DELETE to developmentUser
-- Versión corta
GRANT DELETE, INSERT, SELECT TO developmentUser

-- Deny le quita un permiso en específico
DENY DELETE TO developmentUser

-- Reboca los permisos
REVOKE CREATE TABLE TO developmentUser
REVOKE DELETE TABLE TO developmentUser

-- Permisos por tabla
GRANT SELECT ON tabla TO developmentUser


```

Roles de servidor:

    => Es lo que puede hacer un usuario en el servidor.

    => Server role public solo sirve para entrar, pero no puede hacer algo.

    => User mapping es para asignar la base de datos al usuario.

    => Roles de base de datos (db owner puede hacer todo).

    => El nombre que sale en segurity, es el login, también se puede ver específicamente en la base de datos.

    => Schema, divide la base de datos en carpetas