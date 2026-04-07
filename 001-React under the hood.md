Here's the **easy-to-scan, beginner-friendly** version with no icons. Copy-paste ready:

---

## React + ReactDOM: How They Work Together

### The Pizza Shop Example

**Imagine you're building a website for a pizza shop.**

**You create 3 components:**

| Component | What it does |
|-----------|---------------|
| PizzaCard | Shows 1 pizza (name, image, price, add button) |
| Menu | Lists all PizzaCards |
| Cart | Shows selected pizzas + total price |

---

### How They Work Together (Step by Step)

**Step 1: React builds the component**

```javascript
function PizzaCard({ name, image, price }) {
  return (
    <div className="pizza-card">
      <img src={image} alt={name} />
      <h2>{name}</h2>
      <p>Price: ${price}</p>
      <button>Add to Cart</button>
    </div>
  );
}
```

**Step 2: ReactDOM puts it on screen**

```javascript
ReactDOM.render(
  <PizzaCard name="Pepperoni" image="pep.jpg" price="12.99" />,
  document.getElementById('root')
);
```

---

### What Happens Behind the Scenes

| Step | What happens |
|------|---------------|
| 1 | React creates a virtual copy of the component in memory |
| 2 | ReactDOM compares virtual copy vs real webpage |
| 3 | ReactDOM updates only what's different |
| 4 | Result: Fast + responsive apps |

---

### Simple Summary

| Tool | Job |
|------|-----|
| React | Designs the component (like a chef making pizza) |
| ReactDOM | Puts it on screen (like a waiter serving pizza) |

---

### Final Takeaway

React = "What should it look like?"

ReactDOM = "Make it appear in the browser"

Together = Fast, interactive web apps
