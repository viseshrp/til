# Update all submodules to latest commit on origin
 The object spread syntax (...) makes a shallow clone of an object,
 but a shallow clone is only useful for cloning primitive values in the object.
 If you have values like arrays or objects, they aren’t cloned, but the reference to the original object is cloned.
 In late 2021, when Deno, Node.js and Firefox released support for structuredClone, followed in 2022 by the other browsers.
The structuredClone algorithm is a deep clone algorithm, like Python’s `copy.deepcopy()`.

```js
const mushrooms1 = {
  amanita: ["muscaria", "virosa"],
};

const mushrooms2 = structuredClone(mushrooms1);

mushrooms2.amanita.push("pantherina");
mushrooms1.amanita.pop();

console.log(mushrooms2.amanita); // ["muscaria", "virosa", "pantherina"]
console.log(mushrooms1.amanita); // ["muscaria"]
```

[MDN docs](https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone)
