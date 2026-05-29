# Prototype

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition
Every JavaScript object has an internal link to another object called its **prototype**. When a property or method isn't found on an object, the engine looks up the **prototype chain** until it finds it or reaches `null`. This is JavaScript's mechanism for inheritance.

## 2. Simple Explanation
If an object doesn't have what you ask for, it asks its "parent" (prototype), which asks its parent, and so on — like asking your family up the tree until someone has the answer or you reach the top.

## 3. Why It Is Used
Prototypes enable inheritance and method sharing without duplicating functions on every instance, saving memory and enabling extensible, reusable code. Classes are syntactic sugar over prototypes.

## 4. Key Points
- `obj.__proto__` (or `Object.getPrototypeOf(obj)`) points to its prototype.
- Functions have a `prototype` property used when called with `new`.
- Lookup traverses the **prototype chain** ending at `Object.prototype` → `null`.
- Methods defined on a constructor's `prototype` are shared by all instances.
- `class` syntax uses prototypes under the hood.

## 5. Syntax
```js
function Animal(name) { this.name = name; }
Animal.prototype.speak = function () {
  return `${this.name} makes a sound`;
};
const a = new Animal("Rex");
a.speak(); // found via prototype
```

## 6. Example
```js
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function () {
  return `${this.name} makes a sound`;
};

function Dog(name) {
  Animal.call(this, name);
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
Dog.prototype.bark = function () {
  return `${this.name} barks`;
};

const d = new Dog("Rex");
console.log(d.bark());  // "Rex barks" (own prototype)
console.log(d.speak()); // "Rex makes a sound" (inherited)
```
`Dog` inherits `speak` from `Animal` through the prototype chain while adding its own `bark`.

## 7. Real World Use Case
Built-in methods (`Array.prototype.map`, `String.prototype.trim`) work via prototypes. Libraries and frameworks use prototypal inheritance and class hierarchies for shared behavior.

## 8. Interview Questions
**Q1:** What is the prototype chain?
**A:** The series of linked prototype objects the engine searches to resolve a property, ending at `Object.prototype` then `null`.

**Q2:** Difference between `__proto__` and `prototype`?
**A:** `prototype` is a property on constructor functions; `__proto__` is the actual link on an instance pointing to its constructor's `prototype`.

**Q3:** How does `class` relate to prototypes?
**A:** `class` is syntactic sugar — methods go on the prototype and `extends` sets up the prototype chain.

**Q4:** Why define methods on the prototype instead of inside the constructor?
**A:** Prototype methods are shared by all instances, saving memory; constructor methods create a new copy per instance.

**Q5:** How do you check if a property is own vs inherited?
**A:** Use `obj.hasOwnProperty(key)` or `Object.hasOwn(obj, key)`.

## 9. Common Mistakes
- Confusing `prototype` (on functions) with `__proto__` (on instances).
- Modifying built-in prototypes (`Array.prototype`) — risky and discouraged.
- Forgetting to reset `constructor` after reassigning a prototype.
- Assuming inherited properties are own properties in loops.

## 10. Advanced Notes
- `Object.create(proto)` creates an object with a chosen prototype directly.
- Property shadowing: an own property hides an inherited one of the same name.
- Prototype lookups have a small performance cost; deep chains are slower.
- `Object.getPrototypeOf` / `setPrototypeOf` are the standard accessors; `setPrototypeOf` is slow and best avoided in hot paths.

### Prototype chain diagram
```
 d (Dog instance)
   └─▶ Dog.prototype { bark }
          └─▶ Animal.prototype { speak }
                 └─▶ Object.prototype { toString, ... }
                        └─▶ null
```
