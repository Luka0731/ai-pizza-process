### Projects Comparison
Comparison between [highcode version](https://bitbucket.org/frox-ag/axon-ivy-learning-path/src/dev-luka/) and lowcode version (this repo)



---



### Prediction for Out of Scope Features
- custom design with css 
- animations/sounds/logic with js, 
- selfmade framework 
- rich-text editor for recipes
- pizza versioning
- decision tables
- Image upload



---



### Table
| Feature |  Highcode version | Lowcode version | Notes | Lowcode limit hit? |
|---------|-------------------|-----------------|-------|--------------------|
| F0 Roles & users | `roles.yaml` + `users.yaml` | Same yaml files, copied 1:1 | Identical. This was already config in the original. | No |
| F0 Data model | 5 JPA entities in Java (Pizza, Image, Order, OrderItem, CustomerProfile) with Lombok, Bean Validation, enums (PizzaSize, PizzaStatus, OrderStatus), price per size, plus `persistence.yaml`/`databases.yaml` for Postgres | 4 data classes (`.d.json`): Pizza, Address, Order, OrderItem. No annotations or DB config. | Minutes instead of hours. Simplified: one price per pizza (no sizes), image as URL instead of a DB blob, status as a String instead of an enum, address embedded in the order instead of a CustomerProfile entity. | Partly: no enums and no computed getters (e.g. `getTotal()`) in data classes. The total is stored as a field and computed in IvyScript later. |
| F1 Process (pools, lanes, messages) | 2 separate processes (customer, worker). Message flows via a custom Java `Signaler` class: signal start events with payload parsing, plus waiting tasks found and released through custom fields + task queries. The timer is a delayed TaskSwitchEvent. | 1 process with 2 pools (customer; vendor with clerk/chef/delivery lanes). Message flows: **Trigger** element (order, "where is my pizza?") and a **Signal boundary event** on the customer's waiting task (pizza received). Timer = task **delay**. One shared placeholder Form dialog. Only code: 1 line of IvyScript to send the signal. | Much less code, and the diagram matches the BPMN closely. Triggers attach to the same business case, so the Portal shows one case per order. Debugging took most of the time: Ivy's runtime errors (empty signal code, unassigned task) didn't point to the cause. | Not a low-code limit, but a trap: data class fields with an empty `modifiers` list are *not persistent*, and their data disappears at task switches. The editor doesn't warn you. |
