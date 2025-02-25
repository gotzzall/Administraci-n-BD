# Creación de una base de datos
``` sql
    -- Creacion de la base de datos
    create database paquitabd
    on primary
    (
        Name = paquitadbData, filename = 'filepath/ paquitabd.mdf',
        size = 50MB -- El tamaño minimo es de 512KB, y el predeterminado es 1MB,
        filegrowth = 25% -- Crece el 25% de 50MB. El default es de 10%,
        maxsize = 400MB -- Al sobrepasar el límite, se pone en modo solo lectura, si se quiere que sea infinita, no se coloca
    )
    log on
    (
        Name = paquitabdLog, filename = 'filepath/paquitabd_log.ldf',
        size = 25MB, 
        filegrownth = 25%,
        
    )
    
```

# Creación de un archivo adicional

``` sql
    -- Crear un archivo adicional
    alter databse paquitabd
    ADD FILE
    (
        name = 'paquitaDataNDF', 
        filename = 'path/paquitabd.ndf',
        size = 25MB, 
        maxsize = 500MB,
        filegrowth = 10MB -- Se puede poner porcentaje o una cantidad esepcífica. El minimo es 1MB.
    )to filegroup[PRIMARY];
```

# Creación de un filegroup adicional

``` sql
    -- Creacion de un filegroup adicional
    ALTER DATABASE paquitabd
    ADD FILEGROUP SECUNDARIO
    GO
```

``` sql
    -- Creación de un archivo asociado al filegroup
    ALTER TABLE paquitabd
    ADD FILE (
        NAME = 'paquitabd_parte1,
        FILENAME = 'path/paquitabd_secundario.ndf',
    ) TO FILEGROUP secundario
```
# Crear una tabla en el grupo de archivos secundarios

``` sql
    use paquitabd
    go
    
    -- Crear una tabal en el grupo de archivos secundarios
    create table ratadedospatas(
        id int not null identity(1,1),
        nombre nvarchar(100) not null,
        constraint pk_ratoadedospatas
        primary key(id),
        constraint unico_nombre
        unique(nombre)
    )ON secundario -- especificamos el grupo de archivos
```

# Modificar el grupo primario

``` sql
    use master
    ALTER DATABASE paquitabd
    MODIFY FILEGROUP [secundario] default
```

# Revisión del estado de la opción de ajuste automático del tamaño de archivos

``` sql
    SELECT DATABASEPROPERTYEX('paquitabd', ISAUTOSHRINK)
    ALTER DATABASE paquitabd
    SET AUTO_SHRINK ON WITH NO_WAIT
    GO
```

# Revisión del estado de la opción de creación de estadísticas

``` sql
    SELECT DATABASEPROPERTYEX('paquitabd', 'IsAutoCreateStatistics')
    
    ALTER DATABASE psquitabd
    SET AUTO_CREATE_STATISTICS OFF
    GO
```

# Consultar información de la base de datos

``` sql
    -- Todo lo que empiece con sp es un storeprocedure del sistema
    use master
    SP_helpdb paquitabd
```

# Consultar la información de los grupos
``` sql
    USE paquitabd
    SP_HELPFILEGROUP SECUNDARIO
```


