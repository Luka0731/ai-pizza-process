# Projects Comparison
Comparison between [highcode version](./attachments/pizza-process.zip) and lowcode version (this repo)



---



## Prediction for Out of Scope Features
- custom design with css 
- animations/sounds/logic with js, 
- selfmade framework 
- rich-text editor for recipes
- pizza versioning
- decision tables
- Image upload



---



## Table
| Feature |  Highcode version | Lowcode version | Notes | Lowcode limit hit? |
|---------|-------------------|-----------------|-------|--------------------|
| F0 Roles & users | `roles.yaml` + `users.yaml` | Same yaml files, copied 1:1 | Identical. This was already config in the original. | No |
| F0 Data model | 5 JPA entities in Java (Pizza, Image, Order, OrderItem, CustomerProfile) with Lombok, Bean Validation, enums (PizzaSize, PizzaStatus, OrderStatus), price per size, plus `persistence.yaml`/`databases.yaml` for Postgres | 4 data classes (`.d.json`): Pizza, Address, Order, OrderItem. No annotations or DB config. | Minutes instead of hours. Simplified: one price per pizza (no sizes), image as URL instead of a DB blob, status as a String instead of an enum, address embedded in the order instead of a CustomerProfile entity. | Partly: no enums and no computed getters (e.g. `getTotal()`) in data classes. The total is stored as a field and computed in IvyScript later. |
