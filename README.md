# Consulta-JDBC

# JDBC y Conexión a Bases de Datos Relacionales con Scala

Este documento describe cómo conectar aplicaciones Java y Scala a bases de datos relacionales utilizando JDBC y librerías de Scala como Slick y Doobie.

## 1. ¿Qué es JDBC y cuáles son sus componentes?

### ¿Qué es JDBC?

JDBC (Java™ Database Connectivity) es una API que permite a los programas Java conectarse a sistemas de gestión de bases de datos (DBMS). Proporciona un conjunto de interfaces y clases escritas en Java para ejecutar sentencias SQL y procesar resultados. Con JDBC, los desarrolladores pueden escribir aplicaciones multiplataforma que interactúan con bases de datos de manera estándar.

### Componentes Principales de JDBC

- **Ejecución y Manipulación de Datos:**

  - `executeQuery()`: Ejecuta una consulta SQL y devuelve un objeto `ResultSet`.
  - `next()`: Avanza al siguiente registro de un conjunto de resultados.
  - `close()`: Cierra una conexión a la base de datos.
  - `executeUpdate()`: Ejecuta sentencias SQL que modifican datos y devuelve el número de filas afectadas.

- **Obtención de Datos:**

  - `getString()`: Recupera cadenas de texto.
  - `getInt()`: Recupera enteros.
  - `getDouble()`, `getFloat()`: Recuperan números con decimales.
  - `getBoolean()`: Recupera valores booleanos.
  - `getDate()`, `getTime()`, `getTimestamp()`: Recuperan valores de fecha y hora.

---

## 2. Librerías de Scala para Conexión a Bases de Datos Relacionales

Scala cuenta con librerías especializadas para conectarse a bases de datos relacionales. A continuación se describen Slick y Doobie:

### Comparación entre Slick y Doobie

| **Característica**             | **Slick**                                           | **Doobie**                                              |
| ------------------------------ | --------------------------------------------------- | ------------------------------------------------------- |
| **Paradigma**                  | Orientado a DSL funcional para generar SQL.         | Escritura directa de SQL con soporte funcional puro.    |
| **Integración funcional**      | Limitada; soporta conceptos funcionales básicos.    | Alta; integra Cats, Cats Effect, etc.                   |
| **Curva de aprendizaje**       | Moderada; requiere aprender el DSL de Slick.        | Pronunciada si no se conoce Cats o Cats Effect.         |
| **Esquemas tipados**           | Sí, con generación automática a partir del esquema. | No; las consultas son validadas en tiempo de ejecución. |
| **Asincronía**                 | Nativa; basada en el modelo de `Future`/`DBIO`.     | A través de Cats Effect (`IO`, `Resource`).             |
| **Compatibilidad**             | Amplia, optimizada para bases de datos populares.   | Total, gracias a JDBC.                                  |
| **Soporte para transacciones** | Sí.                                                 | Sí, con control funcional explícito.                    |
| **Documentación y comunidad**  | Amplia y madura.                                    | Activa, pero más pequeña en comparación.                |

### Dependencias para Scala

#### Slick

```scala
libraryDependencies ++= Seq(
  "com.typesafe.slick" %% "slick" % "3.4.1",
  "mysql" % "mysql-connector-java" % "8.1.0",
  "com.typesafe.slick" %% "slick-hikaricp" % "3.4.1" // Pool de conexiones
)
```

#### Doobie

```scala
libraryDependencies ++= Seq(
  "org.tpolecat" %% "doobie-core" % "1.0.0-RC2",
  "org.tpolecat" %% "doobie-hikari" % "1.0.0-RC2",
  "mysql" % "mysql-connector-java" % "8.1.0",
  "org.typelevel" %% "cats-effect" % "3.5.0"
)
```

---

## 3. Establecer una Conexión a MySQL desde Scala

### Pasos:

1. **Crear una Base de Datos en MySQL:**

   ```sql
   CREATE DATABASE ejemplo_db;
   USE ejemplo_db;
   CREATE TABLE prueba (
       id INT AUTO_INCREMENT PRIMARY KEY,
       nombre VARCHAR(50),
       edad INT
   );
   INSERT INTO prueba (nombre, edad) VALUES
   ('Juan', 30), ('Ana', 25), ('Luis', 28);
   ```

2. **Establecer la Conexión desde Scala:**

#### Ejemplo usando JDBC puro:

```scala
import java.sql.{Connection, DriverManager, ResultSet}

object MySQLConnection {
  def main(args: Array[String]): Unit = {
    val url = "jdbc:mysql://localhost:3306/ejemplo_db"
    val user = "root"
    val password = "contraseña"

    try {
      val connection: Connection = DriverManager.getConnection(url, user, password)
      val statement = connection.createStatement()
      val resultSet: ResultSet = statement.executeQuery("SELECT * FROM prueba")

      while (resultSet.next()) {
        println(s"ID: ${resultSet.getInt("id")}, Nombre: ${resultSet.getString("nombre")}, Edad: ${resultSet.getInt("edad")}")
      }

      resultSet.close()
      statement.close()
      connection.close()
    } catch {
      case e: Exception => e.printStackTrace()
    }
  }
}
```


