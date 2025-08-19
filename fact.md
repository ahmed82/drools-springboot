# Fact
 
 **Facts** is *the* key to mastering Drools.
Let’s go step by step:

---

# 🧩 What Are Facts in Drools?

* **Facts are just Java objects** that you insert into the Drools **Working Memory**.
* The **rules only react** to these facts.
* When you insert/update/retract facts, Drools re-evaluates rules automatically.

👉 Think of the Working Memory as an **in-memory database** of objects.
👉 Facts are like the **rows in that database**.

---

# 🔹 Life Cycle of a Fact

1. **Insert a fact**

   ```java
   Customer c = new Customer("Alice", 30);
   ksession.insert(c);
   ```

   → Adds the fact to working memory.

2. **Rules match the fact**

   ```drl
   rule "Adult"
   when
       $c : Customer(age >= 18)
   then
       System.out.println($c.getName() + " is an adult.");
   end
   ```

3. **Modify the fact**

   * In DRL:

     ```drl
     $c.setCategory("Adult");
     update($c);   // tell Drools fact changed
     ```
   * In Java:

     ```java
     c.setCategory("Adult");
     ksession.update(factHandle, c);
     ```

4. **Retract (remove) a fact**

   ```java
   ksession.delete(factHandle);
   ```

---

# 🔹 Example Domain Model

```java
public class Customer {
    private String name;
    private int age;
    private String category;

    // constructor, getters, setters
}
public class Order {
    private Customer customer;
    private int amount;
    // constructor, getters, setters
}
```

---

# 🔹 Fact Interaction Example

Insert facts:

```java
Customer c1 = new Customer("Bob", 70);
Order o1 = new Order(c1, 1200);

ksession.insert(c1);
ksession.insert(o1);
ksession.fireAllRules();
```

Rules act on them:

```drl
rule "Senior Customer"
when
    $c : Customer(age > 60)
then
    $c.setCategory("Senior");
    update($c);
end

rule "VIP Order"
when
    $o : Order(amount > 1000)
then
    System.out.println("VIP order: " + $o);
end
```

---

# 🔹 Key Facts Tricks

* **Always call `update($fact)`** inside `then` if you change a fact, otherwise Drools won’t know it changed.
* **Use `insert()`** in rules if you want to generate new facts:

  ```drl
  insert(new Alert("VIP Customer!"));
  ```
* **Use `delete($fact)`** to retract a fact (stops other rules from using it).

---

✅ So, in short:

* Facts = Java objects in working memory.
* Rules = patterns that match facts.
* Actions = modify/insert/delete facts → which may trigger more rules.

---


