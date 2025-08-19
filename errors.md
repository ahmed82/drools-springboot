

If coded like that "  not(Item($upc: upc.length() == 14))" you will get the error
```
Caused by: 

org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.kie.api.runtime.KieContainer]:

 Factory method 'kieContainer' threw exception with message: Unable to create KieModule,

Errors Existed: [Message [id=1, kieBase=defaultKieBase, level=ERROR, path=RULES.drl, line=124, column=0
   text=Unable to Analyse Expression result.fail(12, "For ABCompany, check that the case UPC is in the right format for the trading partner" + $upc );:
[Error: unable to resolve method using strict-mode: org.drools.core.rule.consequence.KnowledgeHelper.$upc()]
[Near : {... is in the right format for the trading partner" + $upc ); ....}]
```

This is a *classic* Drools beginner pitfall — mixing up **field bindings** vs **variables in the `then` block**.

Let’s dissect the case:

---

# 🔹 Your Rule

```drl
not(Item($upc: upc.length() == 14))
```

and later in the `then`:

```drl
result.fail(12, "For ABCompany, check that the case UPC is in the right format for the trading partner" + $upc);
```

---

# 🔹 The Error

```
[Error: unable to resolve method ... KnowledgeHelper.$upc()]
```

This means Drools is trying to call `$upc` **as if it were a method** — because `$upc` was **only bound inside the `not(...)` pattern** and doesn’t exist outside it.

---

# 🔹 Why It Happens

* When you write `not(Item($upc: upc.length() == 14))`,

  * `$upc` is bound **inside that pattern scope only**.
  * It’s **not visible** in the `then` block.

So by the time you use `$upc` in `then`, Drools says: *“what is `$upc`? Oh, maybe it’s a function? Nope → error.”*

---

# 🔹 How to Fix

You have two main options:

### ✅ Option 1: Bind outside `not`

If you actually want to **capture the value of `upc`**, you can’t do it in `not`. Instead, bind normally:

```drl
$c : Item($upc : upc, eval($upc.length() != 14))
then
    result.fail(12, "UPC must be 14 digits: " + $upc);
end
```

Here `$upc` is visible in the `then`.

---

### ✅ Option 2: Keep `not`, but don’t use `$upc`

If your logic is just “no item with valid upc exists,” you can’t access `$upc` (because by definition, nothing matched). Instead, just give a static message:

```drl
not( Item(upc.length() == 14) )
then
    result.fail(12, "For ABCompany, no valid 14-digit UPC found");
end
```

---

# 🎯 Rule of Thumb

* **Inside `not`, `exists`, `forall`** → you can’t use variable bindings in `then`. They don’t exist (because no actual fact matched).
* **If you need the value in `then` → bind it in a normal pattern, not in `not`.**

---

👉 Question: In this case, do you want to **show the invalid `upc` values** in the error, or just say “no valid one exists”?
That will decide whether you need Option 1 or Option 2.
