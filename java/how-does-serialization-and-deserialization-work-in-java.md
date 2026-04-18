---
title: "How does serialization and deserialization work in Java?"
slug: how-does-serialization-and-deserialization-work-in-java
date: 2025-01-27T19:03:13
categories: ["API", "Java", "Quarkus", "Spring"]
tags: ["API", "Java", "Quarkus", "Spring"]
---
Serialization and deserialization are essential for persisting or transferring data between systems. Serialization transforms Java objects into formats like JSON or XML, enabling storage or network transmission. Deserialization, on the other hand, restores these objects, making the data reusable in applications. These processes are widely used in APIs, data persistence, and communication between distributed systems, ensuring data interoperability and integrity.

Serialization

**It's the conversion of a Java object into a stream of bytes, which can, for example, be saved to a file.**

![](https://natancode.com/wp-content/uploads/2024/12/Serialization.png)

Let's see an example; for that, we'll create a `User` class.

```
public class User {
    
    private String name;
    
    private String email;
    
    private String password;

    public User(String name, String email, String password) {
        this.name = name;
        this.email = email;
        this.password = password;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    public String getPassword() {
        return password;
    }
    
    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
    
}
```

This class will be serialized to be saved in a file.

```
import java.io.IOException;
import java.io.ObjectOutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

import static java.nio.file.StandardOpenOption.CREATE;
import static java.nio.file.StandardOpenOption.WRITE;

public class MainSerialize {

    public static void main(String[] args) {

        User user = new User("Natan", "natan@fakeemail.com", "123");

        Path path = Path.of("src/main/resources/user.txt");

        try (var outputStream = new ObjectOutputStream(
                Files.newOutputStream(path, WRITE, CREATE)
        )){
            outputStream.writeObject(user);
        } catch (IOException e) {
            throw new RuntimeException();
        }

    }
}
```

If we run the code, we will encounter an error.

```
Exception in thread "main" java.lang.RuntimeException
	at com.natancode.serialize.MainSerialize.main(MainSerialize.java:24)
```

Serialization failed because something very important is missing. In the `User` class, it is necessary to implement the `Serializable` interface; only then will serialization be possible.

```
public class User implements Serializable
```

Now, with the file created, we can proceed with deserialization.

```
¬í sr com.natancode.serialize.Userå·ýPàuXu L emailt Ljava/lang/String;L nameq ~ L passwordq ~ xpt natan@fakeemail.comt Natant 123
```

Deserialization

**Deserialization does the opposite of Serialization.**

![](https://natancode.com/wp-content/uploads/2024/12/Deserialization.png)

```
import java.io.IOException;
import java.io.ObjectInputStream;
import java.nio.file.Files;
import java.nio.file.Path;

import static java.nio.file.StandardOpenOption.READ;

public class MainDeserialize {

    public static void main(String[] args) {
        Path path = Path.of("src/main/resources/user.txt");

        try (var inputStream = new ObjectInputStream(Files.newInputStream(path, READ))) {
            User user = (User) inputStream.readObject();
            System.out.println(user);
        } catch (IOException | ClassNotFoundException e) {
            throw new RuntimeException(e);
        }
    }

}
```

```
User{name='Natan', email='natan@fakeemail.com', password='123'}
```

Be cautious with the class used in the cast, as an incorrect class will result in an exception.

```
String user = (String) inputStream.readObject();
```

```
Exception in thread "main" java.lang.ClassCastException: class com.natancode.serialize.User cannot be cast to class java.lang.String (com.natancode.serialize.User is in unnamed module of loader 'app'; java.lang.String is in module java.base of loader 'bootstrap')
	at com.natancode.serialize.MainDeserialize.main(MainDeserialize.java:16)
```

Transient

It might be unnecessary to save certain properties to the file during serialization. You can exclude a property from being serialized by using the `transient` keyword.

Imagine a scenario where a person has a password, and we don't want to save the password in the file. We can use the `transient` keyword to exclude this property during serialization.

Let's delete the previously generated file and create a new one with the `transient` keyword applied to the `password` property.

```
private transient String password;
```

After serializing and deserializing, we get the following result.

```
User{name='Natan', email='natan@fakeemail.com', password='null'}
```

serialVersionUID

Let's add a new property to the `User` class and try to deserialize the existing file.

```
import java.io.Serializable;

public class User implements Serializable {

    private String name;

    private String email;

    private transient String password;

    private String randomProperty;

    public User(String name, String email, String password) {
        this.name = name;
        this.email = email;
        this.password = password;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    public String getPassword() {
        return password;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

When trying to deserialize, we will encounter an error.

```
Exception in thread "main" java.lang.RuntimeException: java.io.InvalidClassException: com.natancode.serialize.User; local class incompatible: stream classdesc serialVersionUID = 394824122702581817, local class serialVersionUID = 2222494017154972370
	at com.natancode.serialize.MainDeserialize.main(MainDeserialize.java:19)
Caused by: java.io.InvalidClassException: com.natancode.serialize.User; local class incompatible: stream classdesc serialVersionUID = 394824122702581817, local class serialVersionUID = 2222494017154972370
```

What does this mean?

![](https://natancode.com/wp-content/uploads/2023/11/image-1.gif)

This happens because when serializing to the file, the class had a certain structure, and now we modified that structure by adding a new property. When we don't specify the class version number, it is automatically generated based on the class structure.

It is also possible to manually control the version by specifying a serialVersionUID in the class.

```
@Serial
private static final long serialVersionUID = 1;
```

This way, we have manual control. If there is a change that doesn't break class compatibility, it's fine to keep the same version. However, if the change breaks compatibility, it's necessary to specify a new version.
