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
| F2 Persistence + test data | Postgres in Docker, `databases.yaml` + `persistence.yaml` (Hibernate, `hbm2ddl`), generic repository/service classes, `TestDataSeeder.java` (~170 lines), images as byte[] in an `Image` table | **Business Data** (`ivy.repo.save/search/delete`). It is stored by the engine itself, so there's no DB setup, no entities, and no repositories. Seed = one Admin-only start with one IvyScript block (~40 lines). Pictures are files in the **CMS** (`cms/images/pizzas/`). | Worked on the first run. Huge setup saving: no Docker, no DB config, no migration worries. Trade-offs: no SQL/joins and no schema. Business Data is a document store (search via `textField`/`numberField` filters). | Partly: there's no process element for Business Data, so the seed is an IvyScript block, longer than the "few lines" low-code ideal. |
| F4 Catalog (select pizza) | Hand-written JSF pages (MenuPage, PizzaDetailPage) with custom components (PizzaCard, FilterBar, ItemCard) and CSS, a `SelectAPizzaDialogBean`, `PizzaFilter`/`PizzaSort` classes, and JPQL queries with a price range in `PizzaRepository`. Pizza cards with picture, a detail page, sizes. | One **Form** dialog `SelectPizza` (no xhtml, CSS or Java). A DataTable lists active pizzas from Business Data; columns are sortable; ＋/－ buttons per row and an "In cart" column. Name / min / max price filter = ~20 lines of IvyScript in a dialog method. Returns the cart as `List<OrderItem>`. | Fast to build, but the design had to follow the Form Editor's limits rather than the requirement. It looks plain (default table), with no cards and no detail page. Filter edge cases (empty field arrives as 0) needed explicit handling. | **Yes:** (1) no image component, and Markdown text isn't rendered, so **no pictures** in pure Forms. (2) Buttons inside a table only refresh the table, so the "choose → details panel" pattern isn't possible. (3) No row-click event on read-only tables. |
