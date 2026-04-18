---
title: "How does the Set interface work in Java?"
slug: how-does-the-set-interface-work-in-java
date: 2024-02-09T22:45:54
categories: ["Java"]
tags: ["Java"]
---
![](https://natancode.com/wp-content/uploads/2024/02/diagrama-de-classes-set.png)

The `Set` interface inherits from the `Collection` interface. There are several implementations of the `Set` interface, such as `HashSet`, `LinkedHashSet`, and `TreeSet`, each with its own characteristics. Unlike the `List` interface, we cannot access an element by index in a `Set`, but it is possible to iterate over the elements. We will see some usage examples, although we won't cover all possibilities. I'll provide a list of methods and a link to the documentation for more details.

Modifier and Type

Method and Description

`boolean`

`**[add](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#add-E-)**([E](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html) e)`Adds the specified element to this set if it is not already present (optional operation).

`boolean`

`**[addAll](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#addAll-java.util.Collection-)**([Collection](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)<? extends [E](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html)> c)`Adds all of the elements in the specified collection to this set if they're not already present (optional operation).

`void`

`**[clear](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#clear--)**()`Removes all of the elements from this set (optional operation).

`boolean`

`**[contains](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#contains-java.lang.Object-)**([Object](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html) o)`Returns true if this set contains the specified element.

`boolean`

`**[containsAll](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#containsAll-java.util.Collection-)**([Collection](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)<?> c)`Returns true if this set contains all of the elements of the specified collection.

`boolean`

`**[equals](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#equals-java.lang.Object-)**([Object](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html) o)`Compares the specified object with this set for equality.

`int`

`**[hashCode](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#hashCode--)**()`Returns the hash code value for this set.

`boolean`

`**[isEmpty](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#isEmpty--)**()`Returns true if this set contains no elements.

`[Iterator](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)<[E](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html)>`

`**[iterator](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#iterator--)**()`Returns an iterator over the elements in this set.

`boolean`

`**[remove](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#remove-java.lang.Object-)**([Object](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html) o)`Removes the specified element from this set if it is present (optional operation).

`boolean`

`**[removeAll](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#removeAll-java.util.Collection-)**([Collection](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)<?> c)`Removes from this set all of its elements that are contained in the specified collection (optional operation).

`boolean`

`**[retainAll](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#retainAll-java.util.Collection-)**([Collection](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)<?> c)`Retains only the elements in this set that are contained in the specified collection (optional operation).

`int`

`**[size](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#size--)**()`Returns the number of elements in this set (its cardinality).

`default [Spliterator](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)<[E](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html)>`

`**[spliterator](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#spliterator--)**()`Creates a `Spliterator` over the elements in this set.

`[Object](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html)[]`

`**[toArray](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#toArray--)**()`Returns an array containing all of the elements in this set.

`<T> T[]`

`**[toArray](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#toArray-T:A-)**(T[] a)`Returns an array containing all of the elements in this set; the runtime type of the returned array is that of the specified array.

https://docs.oracle.com/javase/8/docs/api/java/util/Set.html

HashSet

*   It has better performance than other implementations;
*   Does not guarantee the insertion order of elements;
*   Does not allow duplicate values;
*   Uses the HashTable data structure;
*   Uses hash code to insert and identify elements;
*   Allows null values.

Next, we'll see an example of usage:

```
package com.natancode.blog.java.set.domain;

import java.util.Objects;

public class CountryPopulation {
    
    private String name;
    private long population;

    public CountryPopulation(String name, long population) {
        this.name = name;
        this.population = population;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public long getPopulation() {
        return population;
    }

    public void setPopulation(long population) {
        this.population = population;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        CountryPopulation that = (CountryPopulation) o;
        return Objects.equals(name, that.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name);
    }

    @Override
    public String toString() {
        return "CountryPopulation{" +
                "name='" + name + '\'' +
                ", population=" + population +
                '}';
    }
}
```

I created a `CountryPopulation` class with properties for the country's name and population quantity. Note that it has `hashCode` and `equals` methods. It's important to mention that when providing the `name` parameter in the `equals` method, it should also be included in the `hashCode` method.

```
import com.natancode.blog.java.set.domain.CountryPopulation;

import java.util.HashSet;
import java.util.Set;

public class main {
    public static void main(String[] args) {
        Set<CountryPopulation> countriesPopulation = new HashSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));

        countriesPopulation.forEach(System.out::println);
    }
}
```

In the main class, we add elements to the Set.

When running the code, we get the following result:

![](https://natancode.com/wp-content/uploads/2024/02/image.png)

Note that the insertion order of elements was not preserved.

```
Set<CountryPopulation> countriesPopulation = new HashSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));

        countriesPopulation.forEach(System.out::println);
```

I attempted to add Brazil again.

![](https://natancode.com/wp-content/uploads/2024/02/image-1.png)

After executing the code, we can see that Brazil was not added again because it does not allow duplicate elements.

```
Set<CountryPopulation> countriesPopulation = new HashSet<>();
        System.out.println(countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L)));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
        System.out.println(countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L)));

        countriesPopulation.forEach(System.out::println);
```

In the first attempt to add Brazil, I added a print statement to show this attempt, and I did the same for the second attempt as well.

![](https://natancode.com/wp-content/uploads/2024/02/image-2.png)

The add method returns a boolean value. When it is true, it means that the value was successfully added to the Set, and false means it was not.

```
Set<CountryPopulation> countriesPopulation = new HashSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(null);

        countriesPopulation.forEach(System.out::println);
```

We tried to add a null value, and here's the result:

![](https://natancode.com/wp-content/uploads/2024/02/image-3.png)

As mentioned earlier, it's also possible to add a null value.

There are several ways to iterate over a Set. We can't use a for loop to access indexes.

```
        countriesPopulation.forEach(System.out::println);

        for(var countryPopulation: countriesPopulation) {
            System.out.println(countryPopulation);
        }

        Iterator<CountryPopulation> iterator = countriesPopulation.iterator();
        while (iterator.hasNext()) {
            var countryPopulation = iterator.next();
            System.out.println(countryPopulation);
        }
```

How does the Set know if the element has already been added or not?

```
countriesPopulation.add(new CountryPopulation("Brazil2", 216_422_446L));
```

When trying to add the following element, we get the following result.

![](https://natancode.com/wp-content/uploads/2024/02/image-4.png)

It was added to the Set because the equals method checks the name. The quantity can be repeated, but if I try to add an element with the same name, we won't be able to, even if the quantity is different. In this case, the quantity is irrelevant because we only check the name.

```
Set<CountryPopulation> countriesPopulation = new HashSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_447L));

        countriesPopulation.forEach(System.out::println);
```

![](https://natancode.com/wp-content/uploads/2024/02/image-5.png)

TreeSet

It is an implementation of the SortedSet interface that inherits from the Set interface.

*   Maintains the natural order of elements;
*   Needs to implement the Comparable interface in the class or Comparator if it's not possible to alter the class code;
*   Cannot add null elements;
*   Has less performance than other implementations of the Set interface;
*   Does not allow duplicate elements.

```
Set<CountryPopulation> countriesPopulation = new TreeSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));

        countriesPopulation.forEach(System.out::println);
```

When running the code, we have the following result.

```
Exception in thread "main" java.lang.ClassCastException: class com.natancode.blog.java.set.domain.CountryPopulation cannot be cast to class java.lang.Comparable (com.natancode.blog.java.set.domain.CountryPopulation is in unnamed module of loader 'app'; java.lang.Comparable is in module java.base of loader 'bootstrap')
	at java.base/java.util.TreeMap.compare(TreeMap.java:1569)
	at java.base/java.util.TreeMap.addEntryToEmptyMap(TreeMap.java:776)
	at java.base/java.util.TreeMap.put(TreeMap.java:785)
	at java.base/java.util.TreeMap.put(TreeMap.java:534)
	at java.base/java.util.TreeSet.add(TreeSet.java:255)
	at main.main(main.java:11)
```

This error occurs because we don't have an implementation of the Comparable interface in the CountryPopulation class.

```
package com.natancode.blog.java.set.domain;

import java.util.Objects;

public class CountryPopulation implements Comparable<CountryPopulation> {
    
    private String name;
    private long population;

    public CountryPopulation(String name, long population) {
        this.name = name;
        this.population = population;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public long getPopulation() {
        return population;
    }

    public void setPopulation(long population) {
        this.population = population;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        CountryPopulation that = (CountryPopulation) o;
        return Objects.equals(name, that.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name);
    }

    @Override
    public String toString() {
        return "CountryPopulation{" +
                "name='" + name + '\'' +
                ", population=" + population +
                '}';
    }

    @Override
    public int compareTo(CountryPopulation o) {
        return this.getName().compareTo(o.getName());
    }
}
```

After running the code following the implementation of Comparable, we have the following result.

![](https://natancode.com/wp-content/uploads/2024/02/image-6.png)

The natural order of names has been maintained because that's what we check in the `compareTo` method, and this check is performed when adding elements.

Let's try adding an element that already exists in the Set.

```
Set<CountryPopulation> countriesPopulation = new TreeSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
```

![](https://natancode.com/wp-content/uploads/2024/02/image-7.png)

It's not possible to add "Brazil" again because it already exists in the Set.

We also cannot add null elements.

```
        Set<CountryPopulation> countriesPopulation = new TreeSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
        countriesPopulation.add(null);
```

```
Exception in thread "main" java.lang.NullPointerException
	at java.base/java.util.Objects.requireNonNull(Objects.java:208)
	at java.base/java.util.TreeMap.put(TreeMap.java:809)
	at java.base/java.util.TreeMap.put(TreeMap.java:534)
	at java.base/java.util.TreeSet.add(TreeSet.java:255)
	at main.main(main.java:17)
```

If it's not possible to implement Comparable in the class because it's closed for modification, you can use a Comparator instead. You can pass the Comparator as an argument to the TreeSet constructor to specify the sorting order.

```
package com.natancode.blog.java.set.domain;

import java.util.Comparator;

public class CountryPopulationComparator implements Comparator<CountryPopulation> {
    @Override
    public int compare(CountryPopulation o1, CountryPopulation o2) {
        return o1.getName().compareTo(o2.getName());
    }
}
```

```
Set<CountryPopulation> countriesPopulation = new TreeSet<>(new CountryPopulationComparator());
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
```

![](https://natancode.com/wp-content/uploads/2024/02/image-8.png)

LinkedHashSet

*   Inherits from HashSet and maintains the insertion order of elements;
*   Has good performance, though inferior to HashSet;
*   Uses a doubly linked list, which is why it maintains the insertion order;
*   Does not allow duplicate elements;
*   Allows the addition of null elements.

```
Set<CountryPopulation> countriesPopulation = new LinkedHashSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
```

![](https://natancode.com/wp-content/uploads/2024/02/image-9.png)

Maintains the insertion order of elements.

```
Set<CountryPopulation> countriesPopulation = new LinkedHashSet<>();
        countriesPopulation.add(new CountryPopulation("Brazil", 216_422_446L));
        countriesPopulation.add(new CountryPopulation("United States", 339_996_563L));
        countriesPopulation.add(new CountryPopulation("Japan", 123_294_513L));
        countriesPopulation.add(new CountryPopulation("Mexico", 128_455_567L));
        countriesPopulation.add(new CountryPopulation("Egypt", 112_716_598L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
        countriesPopulation.add(new CountryPopulation("Uruguay", 3_423_108L));
        countriesPopulation.add(null);
```

![](https://natancode.com/wp-content/uploads/2024/02/image-10.png)

Duplicates are not allowed, but adding a null element is permitted.

Conclusion

**Implementation**

**Performance**

**Order**

_HashSet_

Performance is more important.

Doesn't guarantee the order of elements.

_LinkedHashSet_

Performance is important.

Maintains the order of insertion of elements.

_TreeSet_

Performance is less important.

Maintains the natural order of elements.

Comparison
