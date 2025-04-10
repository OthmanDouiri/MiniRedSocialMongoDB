
#### 1. **Definición de la base de datos y colecciones:**
Vamos a crear tres colecciones:  
- **Usuarios (`users`)**
- **Publicaciones (`posts`)**
- **Comentarios (`comments`)**

#### 2. **Operaciones básicas en MongoDB**

Aquí tienes una serie de comandos que puedes ejecutar directamente en el **MongoDB shell** para crear y gestionar estas colecciones.

### **Pasos en MongoDB Shell**

1. **Conectar a MongoDB:**
   Si tienes **MongoDB instalado** y corriendo, abre el terminal de MongoDB para conectarte:
   ```bash
   mongo
   ```

2. **Crear una base de datos:**
   Vamos a crear la base de datos llamada `red_social`.
   ```javascript
   use red_social
   ```

3. **Crear las colecciones:**
   No es necesario crear explícitamente las colecciones, ya que MongoDB las crea automáticamente cuando insertamos el primer documento, pero si quieres asegurar que existen:
   ```javascript
   db.createCollection("users")
   db.createCollection("posts")
   db.createCollection("comments")
   ```

---

### **Operaciones en las colecciones**

#### **1. Insertar un usuario (colección `users`):**
Para agregar un nuevo usuario a la colección `users`:
```javascript
db.users.insertOne({
   username: "juan",
   email: "juan@correo.com"
})
```

Después de insertar el documento en la colección `users`, puedes verlo con estos comandos:  

### 1️⃣ **Ver todos los documentos en la colección `users`**  
```javascript
db.users.find().pretty()
```
Esto mostrará todos los usuarios de la colección de forma legible.  

### 2️⃣ **Buscar solo el usuario recién insertado**  
Como el `email` es único en este caso, puedes buscarlo así:  
```javascript
db.users.findOne({ email: "juan@correo.com" })
```

### 3️⃣ **Si tienes el `_id` del documento insertado**  
Si el `insertOne()` te devolvió un `insertedId`, puedes buscarlo con:  
```javascript
db.users.findOne({ _id: ObjectId("67e42b06564135f3c0f3b453") })
```
(reemplaza el ID con el que se generó en tu inserción).


#### **2. Crear una publicación (colección `posts`):**
Para insertar una nueva publicación, relacionada con un usuario:
```javascript
db.posts.insertOne({
   user_id: ObjectId("ID_DEL_USUARIO"), // Aquí debes usar el ObjectId de un usuario previamente insertado.
   content: "¡Hola a todos! Esta es mi primera publicación.",
   created_at: new Date()
})
```

#### **3. Crear un comentario (colección `comments`):**
Para insertar un comentario en una publicación:
```javascript
db.comments.insertOne({
   post_id: ObjectId("ID_DE_LA_PUBLICACION"),  // Aquí debes usar el ObjectId de la publicación
   user_id: ObjectId("ID_DEL_USUARIO"),        // Usamos el ObjectId del usuario
   comment: "¡Qué interesante publicación!",
   created_at: new Date()
})
```

#### **4. Ver todos los usuarios:**
Para listar todos los usuarios:
```javascript
db.users.find().pretty()
```

#### **5. Ver todas las publicaciones:**
Para mostrar todas las publicaciones:
```javascript
db.posts.find().pretty()
```

#### **6. Ver los comentarios de una publicación específica:**
Para ver todos los comentarios de una publicación:
```javascript
db.comments.find({ post_id: ObjectId("ID_DE_LA_PUBLICACION") }).pretty()
```

---

### **Resumen de operaciones posibles**

1. **Usuarios**  
   - Insertar un usuario
   - Consultar todos los usuarios

2. **Publicaciones**  
   - Insertar una publicación
   - Consultar todas las publicaciones
   - Consultar publicaciones de un usuario específico

3. **Comentarios**  
   - Insertar un comentario
   - Consultar los comentarios de una publicación

---

### **Implementación de Interfaz de Usuario en la Consola**

Si quieres una experiencia de usuario más interactiva en consola, puedes crear **scripts** de **MongoDB** que ejecuten estas operaciones automáticamente según las opciones que el usuario seleccione.

## **en java**
Crear la Conexión a MongoDB
En el paquete principal, crea una nueva clase llamada DatabaseConnection.java.

Agrega el siguiente código
``` java
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoDatabase;

public class DatabaseConnection {
    private static final String URI = "mongodb://127.0.0.1:27017";
    private static final String DB_NAME = "red_social";
    private static MongoClient mongoClient;
    private static MongoDatabase database;

    public static MongoDatabase getDatabase() {
        if (mongoClient == null) {
            mongoClient = MongoClients.create(URI);
            database = mongoClient.getDatabase(DB_NAME);
        }
        return database;
    }

    public static void closeConnection() {
        if (mongoClient != null) {
            mongoClient.close();
        }
    }
}
```
Crear la Clase Principal
Crea un archivo llamado RedSocial.java.
``` java
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;
import org.bson.types.ObjectId;
import java.util.Scanner;

public class RedSocial {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        MongoDatabase database = DatabaseConnection.getDatabase();

        System.out.println("📢 Bienvenido a la red social en MongoDB con Java!");

        while (true) {
            System.out.println("\n📌 Seleccione una opción:");
            System.out.println("1️⃣ Agregar usuario");
            System.out.println("2️⃣ Crear publicación");
            System.out.println("3️⃣ Ver publicaciones");
            System.out.println("4️⃣ Comentar publicación");
            System.out.println("5️⃣ Salir");

            int opcion = scanner.nextInt();
            scanner.nextLine(); // Consumir el salto de línea

            switch (opcion) {
                case 1:
                    agregarUsuario(database, scanner);
                    break;
                case 2:
                    crearPublicacion(database, scanner);
                    break;
                case 3:
                    verPublicaciones(database);
                    break;
                case 4:
                    comentarPublicacion(database, scanner);
                    break;
                case 5:
                    System.out.println("🔴 Saliendo...");
                    DatabaseConnection.closeConnection();
                    scanner.close();
                    return;
                default:
                    System.out.println("⚠ Opción no válida, intente de nuevo.");
            }
        }
    }

    public static void agregarUsuario(MongoDatabase db, Scanner scanner) {
        System.out.print("👤 Nombre de usuario: ");
        String username = scanner.nextLine();
        System.out.print("📧 Correo electrónico: ");
        String email = scanner.nextLine();

        MongoCollection<Document> users = db.getCollection("users");
        users.insertOne(new Document("username", username).append("email", email));
        System.out.println("✅ Usuario agregado con éxito.");
    }

    public static void crearPublicacion(MongoDatabase db, Scanner scanner) {
        System.out.print("🆔 ID del usuario: ");
        String userId = scanner.nextLine();
        System.out.print("📝 Publicación: ");
        String content = scanner.nextLine();

        MongoCollection<Document> posts = db.getCollection("posts");
        posts.insertOne(new Document("user_id", new ObjectId(userId))
                .append("content", content)
                .append("created_at", new java.util.Date()));

        System.out.println("✅ Publicación creada con éxito.");
    }

    public static void verPublicaciones(MongoDatabase db) {
        MongoCollection<Document> posts = db.getCollection("posts");
        MongoCollection<Document> users = db.getCollection("users");
        MongoCollection<Document> comments = db.getCollection("comments");

        FindIterable<Document> postList = posts.find();

        if (!postList.iterator().hasNext()) {
            System.out.println("❌ No hay publicaciones disponibles.");
            return;
        }

        for (Document post : postList) {
            ObjectId userId = post.getObjectId("user_id");
            Document user = users.find(new Document("_id", userId)).first();
            String username = (user != null) ? user.getString("username") : "Usuario desconocido";

            System.out.println("\n---------------------------------");
            System.out.println("📌 Publicación ID: " + post.getObjectId("_id"));
            System.out.println("👤 Autor: " + username + " (" + userId + ")");
            System.out.println("📄 Contenido: " + post.getString("content"));
            System.out.println("📅 Fecha: " + post.getDate("created_at"));

            // Obtener comentarios
            FindIterable<Document> commentList = comments.find(new Document("post_id", post.getObjectId("_id")));

            if (!commentList.iterator().hasNext()) {
                System.out.println("   ❌ No hay comentarios.");
            } else {
                System.out.println("💬 Comentarios:");
                for (Document comment : commentList) {
                    ObjectId commentUserId = comment.getObjectId("user_id");
                    Document commentUser = users.find(new Document("_id", commentUserId)).first();
                    String commentUsername = (commentUser != null) ? commentUser.getString("username") : "Usuario desconocido";
                    System.out.println("   🗨️ " + comment.getString("comment") + " (Usuario: " + commentUsername + ")");
                }
            }
            System.out.println("---------------------------------\n");
        }
    }

    public static void comentarPublicacion(MongoDatabase db, Scanner scanner) {
        System.out.print("🆔 ID de la publicación: ");
        String postId = scanner.nextLine();
        System.out.print("🆔 ID del usuario: ");
        String commenterId = scanner.nextLine();
        System.out.print("💬 Comentario: ");
        String comment = scanner.nextLine();

        MongoCollection<Document> comments = db.getCollection("comments");
        comments.insertOne(new Document("post_id", new ObjectId(postId))
                .append("user_id", new ObjectId(commenterId))
                .append("comment", comment)
                .append("created_at", new java.util.Date()));

        System.out.println("✅ Comentario agregado con éxito.");
    }
}

``` 
## **en node.js**

``` Script node.js 
const readlineSync = require('readline-sync');
const { MongoClient, ObjectId } = require('mongodb');

async function main() {
    const client = new MongoClient("mongodb://127.0.0.1:27017");
    await client.connect();
    const db = client.db("red_social");

    console.log("Bienvenido a la red social en MongoDB!");

    while (true) {
        console.log("\nSeleccione una opción:");
        console.log("1. Agregar usuario");
        console.log("2. Crear publicación");
        console.log("3. Ver publicaciones y comentarios");
        console.log("4. Comentar publicación");
        console.log("5. Salir");

        let opcion = parseInt(readlineSync.question("Ingrese una opcion: "));

        if (opcion === 1) {
            let username = readlineSync.question("Ingrese el nombre de usuario: ");
            let email = readlineSync.question("Ingrese el correo del usuario: ");
            await db.collection("users").insertOne({ username, email });
            console.log("Usuario agregado con éxito.");
        } 
        else if (opcion === 2) {
            let user_id = readlineSync.question("Ingrese el ID del usuario: ");
            let content = readlineSync.question("Ingrese la publicación: ");
            await db.collection("posts").insertOne({ user_id: new ObjectId(user_id), content, created_at: new Date() });
            console.log("Publicación creada con éxito.");
        } 
        else if (opcion === 3) {
            const posts = await db.collection("posts").find().toArray();
            if (posts.length === 0) {
                console.log("No hay publicaciones disponibles.");
            } else {
                for (let post of posts) {
                    let user = await db.collection("users").findOne({ _id: post.user_id });
                    let username = user ? user.username : "Usuario desconocido";

                    console.log("\n---------------------------------");
                    console.log(`📌 Publicación ID: ${post._id}`);
                    console.log(`👤 Autor: ${username} (${post.user_id})`);
                    console.log(`📄 Contenido: ${post.content}`);
                    console.log(`📅 Fecha: ${post.created_at}`);

                    // Obtener comentarios del post
                    const comments = await db.collection("comments").find({ post_id: post._id }).toArray();
                    if (comments.length > 0) {
                        console.log("💬 Comentarios:");
                        for (let comment of comments) {
                            let commentUser = await db.collection("users").findOne({ _id: comment.user_id });
                            let commentUsername = commentUser ? commentUser.username : "Usuario desconocido";
                            console.log(`   🗨️ ${comment.comment} (Usuario: ${commentUsername}) (ID: ${comment.user_id})`);
                        }
                    } else {
                        console.log("   ❌ No hay comentarios.");
                    }
                    console.log("---------------------------------\n");
                }
            }
        } 
        else if (opcion === 4) {
            let post_id = readlineSync.question("Ingrese el ID de la publicación: ");
            let user_id = readlineSync.question("Ingrese el ID del usuario: ");
            let comment = readlineSync.question("Ingrese su comentario: ");
            await db.collection("comments").insertOne({ post_id: new ObjectId(post_id), user_id: new ObjectId(user_id), comment, created_at: new Date() });
            console.log("Comentario agregado con éxito.");
        } 
        else if (opcion === 5) {
            console.log("Saliendo...");
            await client.close();
            break;
        } 
        else {
            console.log("Opción no válida, intente de nuevo.");
        }
    }
}

main().catch(console.error);

```
## Instalar Node.js y readline-sync

```powershell
npm install readline-sync mongodb
```

## Ejecutar el script en Node.js

```powershell
node menu.js
```

