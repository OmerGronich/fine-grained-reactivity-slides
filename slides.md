---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS (experimental)
css: unocss
---

# Fine-Grained Reactivity

- 📝 **what?**
- 🧑‍💻 **how?**
- 🤹 **surgical dom updates**


<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
* {
text-align: left;
}
</style>

--- 

# What is fine-grained reactivity

<p>
    Fine-grained reactivity is often described like a spreadsheet. 
    There are cells that contain data and other cells whose value is calculated based on those data cells.
</p>
<img style="width: 300px; height: 200px;" src="https://depictdatastudio.com/wp-content/uploads/2019/11/formulas_statistical-standard-deviation.gif" />

---

# What is fine-grained reactivity

<p>
    In a fine-grained reactive system, the "data cells" are often referred to as 
</p>

* Obserables
* Signals
* Refs
* Atoms
* Subjects

<p> 
    the "calculation cells" are often referred to as Observers
</p>

* Reactions, Effects, Autoruns, Watches 
* Memos, Computeds, Derivations

---

# Observables

Are a simple primitive that contains a getter and a setter.
 
They can be built with a single function with variable arguments, or even use object getter/setters or ES6 proxies.
The key part is to understand that any change needs to be captured by the setter, and all accesses get tracked upon the execution of the getter.

```ts {all|2|1-6|9|all}
function observable<T>(value: T) {
  const subscribers = [];
  
  return new Proxy({value}, {
      get(target: { value: T }, p: PropertyKey): T {
          // track subscriptions
      },
      set(target: { value: T }, p: PropertyKey, value: T): boolean {
          // trigger subscriptions
      }
  })
}
```

---

# Observers

Generally, there are 2 types of observers: Effectful Observers & Pure Observers

---

# Effectful Observers 

AKA Reactions, Autoruns, Effects, Watches

Effectful Observers create side effects.
Side effects are when you modify values outside the scope of the calling procedure.

```ts {all|2|1-6|9|all}
function autorun(fn: Callback) {
  context = fn;
  fn();
  context = null;
}
```
---

# Pure Observers (AKA Computeds, Memos, Derivations)

Are used to derive values from observables

```ts {all|2|1-6|9|all}
function computed<T>(fn: () => T) {
  let obs = observable<T>();

  autorun(() => {
    obs.value = fn();
  })

  return obs;
}
```

