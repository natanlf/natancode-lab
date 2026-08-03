---
title: "What is Shallow Copy and Deep Copy in Java?"
slug: what-is-shallow-copy-and-deep-copy-in-java
date: 2025-02-17T13:21:52
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
When developing software, there may be situations where it is necessary to create copies of objects to prevent unwanted behaviors, whether to avoid different parts of the system modifying the same object or for any other reason that prevents undesired behavior. Java provides the **Cloneable** interface, which allows object copying. There are two types of copies, and it is important to understand each one to avoid issues.

*   Shallow copy: Creates a new object but does not copy the nested objects; the nested objects have their references copied.
*   Deep Copy: Creates a new object and also copies the nested objects.

We need to keep in mind which type of copy will meet our needs because, in the case of a shallow copy, we have shared references, which can lead to unwanted behaviors in some contexts.

Hands on

![](https://natancode.com/wp-content/uploads/2024/12/giphy-2.webp)

Shallow Copy

```
class Address {
    String city;

    public Address(String city) {
        this.city = city;
    }

    @Override
    public String toString() {
        return "Address{" +
                "city='" + city + '\'' +
                '}';
    }
}

class Person implements Cloneable {
    String name;
    Address address;

    public Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }


    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone(); // shallow copy
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", address=" + address +
                '}';
    }
}
```

We create the `Person` and `Address` classes, where a person has an address. Let's do the following: create a person, make a copy of that person, and change the name and address of the copy.

```
  Person bob = new Person("Bob", new Address("City One"));

  Person mary = (Person) bob.clone();
  mary.name = "Mary";
  mary.address.city = "New City";

  System.out.println(bob);
  System.out.println(mary);
```

We created Bob, cloned him, and changed the `name` property to "Mary" and `address.city` to "New City." When printing, we get the following result.

```
Person{name='Bob', address=Address{city='New City'}}
Person{name='Mary', address=Address{city='New City'}}
```

Notice that Bob's address was also changed. This happens because it's a shallow copy—the same reference created in Bob is present in the copy.

Deep Copy

When performing a deep copy, this issue is resolved because the same reference is no longer used when cloning.

```
class Address implements Cloneable {
    String city;

    public Address(String city) {
        this.city = city;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    @Override
    public String toString() {
        return "Address{" +
                "city='" + city + '\'' +
                '}';
    }
}
```

```
class Person implements Cloneable {
    String name;
    Address address;

    public Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }


    @Override
    protected Object clone() throws CloneNotSupportedException {
        Person person = (Person) super.clone();
        person.address = (Address) person.address.clone();
        return person;
    }


    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", address=" + address +
                '}';
    }
}
```

We implemented Cloneable in Address and modified the clone method in the Person class. By doing this, we create a deep copy and have the following result.

```
Person{name='Bob', address=Address{city='City One'}}
Person{name='Mary', address=Address{city='New City'}}
```

Conclusion

Although **deep copy** creates copies of nested objects, **shallow copy** is faster when copying. No type of copy is better than the other; it is important to evaluate the situation to understand which will best address the problem.
