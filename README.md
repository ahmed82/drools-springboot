# drools-springboot
Drools is an open-source Business Rule Management System (BRMS) written in Java, part of the KIE (Knowledge Is Everything) project



---
The **quick way (a trick/strategy)** to get comfortable with Drools DRL (Drools Rule Language).

Here’s the trick I recommend (used by many devs learning Drools fast):

---

## 🔑 The 4-Step Trick to Master DRL

### 1. **Think in "WHEN-THEN" Sentences**

* Drools rules are just *if-then* conditions.
* Imagine explaining rules in plain English:

  > “When an `Order` has amount > 500, then apply a discount.”
* Then directly write it in DRL:

  ```drl
  rule "Apply Discount"
  when
      $o : Order(amount > 500)
  then
      $o.setDiscount(10);
      update($o);
  end
  ```

👉 Trick: First say the rule in plain English, then translate it to DRL.

---

### 2. **Use Facts Like Objects in Memory**

* In Drools, you *insert* Java objects as **facts**.
* Rules only **react** to facts in memory.
* Always ask yourself:

  > “What fact am I matching?” (in `when`)
  > “What action am I taking?” (in `then`)

👉 Trick: Think of facts as rows in an in-memory database table. DRL queries them with conditions.

---

### 3. **Practice with 3 Core Patterns**

Almost all rules fall into these patterns:

1. **Single Fact Check**

   ```drl
   when
       $c : Customer(age > 60)
   then
       $c.setCategory("Senior");
       update($c);
   end
   ```

2. **Join Facts** (relation between objects)

   ```drl
   when
       $c : Customer(age < 18)
       $o : Order(customer == $c)
   then
       System.out.println("Minor placed an order: " + $o);
   end
   ```

3. **Accumulate / Aggregate**

   ```drl
   when
       Number( $total : intValue ) from accumulate(
           Order( $a : amount ),
           sum($a)
       )
   then
       System.out.println("Total Order Amount: " + $total);
   end
   ```

👉 Trick: If you can write these 3 patterns, you can cover 80% of Drools use cases.

---

### 4. **Build Muscle Memory with Mini Rulesets**

* Start with a **tiny domain model** (`Customer`, `Order`, `Product`).
* Write **5 simple rules** using the above 3 patterns.
* Run them in a test class.
* Iterate: add conditions, add actions, combine rules.

👉 Trick: Don’t read too much theory. Instead, **practice with small working examples** until writing a rule feels like writing an if-statement.

---

✅ Summary of the trick:

* Translate business English into `when-then`.
* Treat facts like objects in memory.
* Practice 3 patterns (single, join, aggregate).
* Write small rulesets until it clicks.

---


