---
title: "How to sort Java Collections?"
slug: how-to-sort-java-collections
date: 2025-01-06T23:53:06
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
Sorting in Java Collections is an essential feature for organizing collection elements in ascending or descending order. Using the **_Comparable_** or **_Comparator_** interfaces, custom sorting criteria can be defined. The Collections class provides utility methods, such as **_sort()_**, to apply these rules. This enables efficient and flexible data processing.

Hands on

![](https://natancode.com/wp-content/uploads/2024/12/giphy-2.webp)

The following example is a list of prices that will be sorted in natural order, that is, from lowest to highest.

```
  Locale.setDefault(Locale.US);
  
  List<BigDecimal> prices =  new ArrayList<>();
  prices.add(new BigDecimal("20.50"));
  prices.add(new BigDecimal("240.50"));
  prices.add(new BigDecimal("59.50"));
  prices.add(new BigDecimal("500.50"));
  prices.add(new BigDecimal("10.50"));
  
  Collections.sort(prices);
  System.out.println(prices);
```

We have the following result sorted in natural order:

```
[10.50, 20.50, 59.50, 240.50, 500.50]
```

How is this possible?

![](https://natancode.com/wp-content/uploads/2025/01/giphy.webp)

By using the `sort` method, we can sort the collection because **_List_** inherits from `Collection`.

![](https://natancode.com/wp-content/uploads/2024/12/image-3.png)

That alone would not be enough for the sorting to work, as it is necessary to use the `Comparable` interface, which `BigDecimal` implements.

![](https://natancode.com/wp-content/uploads/2024/12/image-5.png)

![](https://natancode.com/wp-content/uploads/2024/10/image-1.webp)

To perform reverse order sorting, we also use the `Collections.sort` method. This time, it is necessary to provide a `Comparator`.

```
Collections.sort(prices, Comparator.reverseOrder());
```

```
[500.50, 240.50, 59.50, 20.50, 10.50]
```

Now let's create a record called `Product`.

Comparable Interface

```
import java.math.BigDecimal;

public record Product(String name, BigDecimal price) implements Comparable<Product> {

    @Override
    public int compareTo(Product o) {
        return name().compareTo(o.name());
    }

}
```

The record implements the `Comparable<Product>` interface, enabling comparisons between `Product` instances. The implementation of the `compareTo` method uses the `name` field.

The method compares the `name` of the current product with the `name` of the product passed as an argument (`o`) using `String.compareTo()`. Possible return values from `compareTo()` are:

*   Negative value: this.name is less than o.name.
*   Zero: this.name is equal to o.name.
*   Positive value: this.name is greater than o.name.

```
  Locale.setDefault(Locale.US);

  List<Product> products = new ArrayList<>();
  products.add(new Product("Samsung Galaxy S24 Ultra", new BigDecimal("949.99")));
  products.add(new Product("ORFEU - Special Coffee Classic", new     
  BigDecimal("16.85")));
  products.add(new Product("Chocolate", new BigDecimal("36.95")));
  products.add(new Product("Perfume", new BigDecimal("84.95")));

  Collections.sort(products);

  products.forEach(System.out::println);
```

When executed, we get the following result:

```
Product[name=Chocolate, price=36.95]
Product[name=ORFEU - Special Coffee Classic, price=16.85]
Product[name=Perfume, price=84.95]
Product[name=Samsung Galaxy S24 Ultra, price=949.99]
```

When specifying a property in `hashCode` and `equals`, it is good practice to specify the same property in `compareTo`. For example, if the `name` property is used in `hashCode` and `equals`, it should also be used in `compareTo`.

```
import java.math.BigDecimal;
import java.util.Objects;

public record Product(String name, BigDecimal price) implements Comparable<Product> {

    @Override
    public int compareTo(Product o) {
        return name().compareTo(o.name());
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Product product = (Product) o;
        return Objects.equals(name, product.name);
    }

    @Override
    public int hashCode() {
        return Objects.hashCode(name);
    }
}
```

Since the `Product` record already specifies ordering by name, what happens if it is necessary to sort by another property?

![](https://natancode.com/wp-content/uploads/2023/11/image-1.gif)

It is not necessary to modify the record; we can use the Comparator interface as shown in the following example.

Comparator Interface

```
import java.util.Comparator;

public class PriceProductComparator implements Comparator<Product> {

    @Override
    public int compare(Product o1, Product o2) {
        return o1.price().compareTo(o2.price());
    }

}
```

**Class `PriceProductComparator`:**

*   It implements the Comparator interface, which defines an alternative sorting criterion based on the products' price.
*   This Comparator is explicit and can be used to override the default behavior of compareTo.

To sort by price, simply provide the Comparator in the sort method.

```
Collections.sort(products, new PriceProductComparator());
```

We have the following result.

```
Product[name=ORFEU - Special Coffee Classic, price=16.85]
Product[name=Chocolate, price=36.95]
Product[name=Perfume, price=84.95]
Product[name=Samsung Galaxy S24 Ultra, price=949.99]
```

We can reverse the order by specifying it in the Comparator.

```
Collections.sort(products, new PriceProductComparator().reversed());
```

```
Product[name=Samsung Galaxy S24 Ultra, price=949.99]
Product[name=Perfume, price=84.95]
Product[name=Chocolate, price=36.95]
Product[name=ORFEU - Special Coffee Classic, price=16.85]
```

Multiple Comparisons

It is possible to perform multiple comparisons. Let's add another product with the same price and a different name. After sorting by price, we'll sort by name. To avoid creating a new Comparator, we can use the compareTo method that already exists in the Product record. There are multiple ways to do this.

```
Collections.sort(products, new PriceProductComparator().reversed().thenComparing(Comparator.naturalOrder()));
```

```
Product[name=Samsung Galaxy S24 Ultra, price=949.99]
Product[name=Perfume, price=84.95]
Product[name=Chocolate, price=36.95]
Product[name=Special Coffee, price=36.95]
Product[name=ORFEU - Special Coffee Classic, price=16.85]
```

Or

```
Collections.sort(products, new PriceProductComparator().reversed().thenComparing(Product::compareTo));
```

```
Product[name=Samsung Galaxy S24 Ultra, price=949.99]
Product[name=Perfume, price=84.95]
Product[name=Chocolate, price=36.95]
Product[name=Special Coffee, price=36.95]
Product[name=ORFEU - Special Coffee Classic, price=16.85]
```

This way, it is possible to chain multiple comparisons in a simple and flexible manner.

**`Comparable`:** Defines a default sorting order for the object.

**`Comparator`:** Allows specifying alternative sorting orders.
