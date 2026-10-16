# Proyecto de Ingeniería de Software

Proyecto académico desarrollado como parte de la materia de **Ingeniería de Software** de la Licenciatura en Matemáticas Aplicadas y Computación.

El proyecto consistió en el desarrollo de un sistema web. Mi participación estuvo principalmente relacionada con el **backend y el diseño de la base de datos**, trabajando en la organización y normalización de la información.

## Tecnologías utilizadas

- PostgreSQL
- SQL
- PL/pgSQL
- pgcrypto

## Base de datos

La base de datos fue diseñada utilizando un modelo relacional para almacenar información relacionada con los usuarios del sistema.

Entre las principales tablas se encuentran:

- `usuarios`
- `perfiles_usuarios`
- `direcciones`
- `preferencias`
- `seguridad_usuario`
- `redes_sociales`

Se utilizaron:

- Llaves primarias.
- Llaves foráneas.
- Restricciones `UNIQUE`.
- Identificadores UUID.
- Campos JSONB.
- Relaciones entre diferentes tablas.
- Eliminación en cascada mediante `ON DELETE CASCADE`.

## Registro de usuarios

Se implementó una función almacenada en PostgreSQL para realizar el registro de un usuario y distribuir su información entre las distintas tablas relacionadas.

La función permite registrar información como:

- Datos generales del usuario.
- Perfil.
- Dirección.
- Preferencias.
- Información de seguridad.
- Redes sociales.

También se utilizó la extensión `pgcrypto` para almacenar las contraseñas mediante hash.

## Mi participación

Durante el desarrollo del proyecto participé principalmente en:

- Diseño de la estructura de la base de datos.
- Normalización de tablas.
- Definición de relaciones entre entidades.
- Trabajo con SQL y PostgreSQL.
- Apoyo en el desarrollo del backend.
- Organización de la información almacenada por el sistema.

## Contexto académico

Proyecto desarrollado con fines académicos para aplicar conceptos de:

- Ingeniería de software.
- Diseño de bases de datos.
- Modelado relacional.
- Normalización.
- SQL.
- Desarrollo backend.

## Autor

**Luis Alberto Avendaño Zarza**  
Licenciatura en Matemáticas Aplicadas y Computación  
Facultad de Estudios Superiores Acatlán - UNAM
