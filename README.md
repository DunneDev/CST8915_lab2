# CST8915 — Lab 2

## Sebastien Dunne — 040938887

[Link to video demo](https://youtu.be/Mjtt5SHtuHs)

[product-service repo](https://github.com/DunneDev/CST8915_lab2_product_service)

[store-front repo](https://github.com/DunneDev/CST8915_lab2_store_front)

[order-service repo](https://github.com/DunneDev/CST8915_lab2_order_service)

### Reflection Questions

**What changes did you make to the `order-service` and `product-service` to
comply with the Configurations and Backing Services factors of the 12-Factor App
methodology?**

For `order-service` I created a `.env` file in the root directory of the repo.
This is where I stored the connection string for connecting to RabbitMQ, as well
as the port to bind the service to. This conforms to the configurations factor
because the configurations are stored in the environment. This also conforms
to the backing services factor because RabbitMQ is treated as a separate resource.
We only need to change the configuration in order to swap to a different instance
of RabbitMQ.

To read the newly added configurations from the environment, I had to update
`index.js` to use the `dotenv` module. The following code was added to
achieve this.

```javascript
require('dotenv').config();

...

const RABBITMQ_CONNECTION_STRING =
  process.env.RABBITMQ_CONNECTION_STRING || "amqp://localhost";
const PORT = process.env.PORT || 3000;
```

In order to avoid pushing secrets to the repo, I made sure that the
`.env` is in the `.gitignore`. The provided files in lab 1 already had this
completed so there was nothing for me to do.

For `product-service` I created a `.env` file in the root directory of the repo.
The only environment variable stored in this service is the port to bind to.
This conforms to the configurations factor because the configurations are
stored in the environment.

To read the newly added configurations from the environment, the `main.rs`
had to have the following changes

```rust
use dotenv::dotenv;
use std::env;

...

dotenv().ok();

let port: u16 = env::var("PORT")
  .unwrap_or_else(|_| "3030".to_string())
  .parse::<u16>()
  .expect("PORT must be a valid number");
```

The `.gitignore` didn't include the `.env` file. I made sure to update the
`.gitignore` so that the `.env` doesn't get pushed.

**Why is it important to use environment variables instead of hard-coding
configurations in your application?**

Using environment variables to store configurations allows the configurations
to be separate from the source code. This makes it easier to change how the
application is deployed without any changes to the source code. If there aren't
any meaningful changes to the codebase, then the codebase should remain the same.
A benefit of this is that the application can be scaled by creating new VMs and
setting the correct configurations local to that VM. If different VMs required
a different configuration, and that configuration was hard-coded into the source
code, then the source code would have to be different on each VM. This is a
nightmare to maintain.

**Why is it important to have separate repositories for each microservice? How
does this help maintain independence and scalability of each service?**

Keeping separate repositories keeps each microservice as a self contained application
which is one of the goals of microservice service architecture. Changes can be
made to a specific microservice without the need to redeploy the entire application.
This allows each microservice to be scaled independently of each other. Should
a microservice be used more headily than others, more VMs can be deployed specifically
for that microservice without touching the others.
