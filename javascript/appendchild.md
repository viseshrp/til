# appendChild
appendChild

Move an element from its current position to the new position.
Also can be used across different containers.

```js
const container = document.querySelector(".container");
container.appendChild(container.firstElementChild);

// carousel effect
setInterval(() => {
    const container = document.querySelector('.container');
    container.appendChild(container.firstElementChild);
  }, 1000)
```
