# @trutoo/event-bus

Created: May 7, 2022 10:34 AM
Finished: No
Source: https://www.npmjs.com/package/@trutoo/event-bus#purpose
Tags: #tool

![338e4905a2684ca96e08c7780fc68412.png](@trutoo%20event-bus%203d3ff5b8b3ff408db6a7f0c10b5f21a6/338e4905a2684ca96e08c7780fc68412.png)

## Purpose

This project was created to improve upon some of the deficits CustomEvents has in relation to event communication between separate web components or fragments, which often is the preferred way of communication. Below points are some of the benefits of using this pub-sub solution over the native solution.

1. 
    
    Each fragment can register on a custom named event channel with an optional [JSON schema draft-04](https://tools.ietf.org/html/draft-zyp-json-schema-04) to ensure all future traffic on that channel follows the specification. This means incompatibilities are reported before any payload is sent and every payload will be typesafe.
    
2. 
    
    The individual event channels stores the last payload allowing new fragments or asynchronous subscriptions to ask for a replay of the last payloads data as a first callback.
    
3. 
    
    CustomEvents require a polyfill to work in older browsers, while this project works out of the box with Internet Explorer 11.
    

## Installation

Either add the Trutoo GitHub Package registry to your `.npmrc`

```
@trutoo:registry=https://npm.pkg.github.com/trutoo
```

or install using the registry flag

```
npm install @trutoo/event-bus --registry=https://npm.pkg.github.com/trutoo
```

or install from the [npm registry @trutoo/event-bus](https://www.npmjs.com/package/@trutoo/event-bus)

```
npm install @trutoo/event-bus
```

Then either import the side effects only exposing a `eventBus` global instance.

```
import '@trutoo/event-bus';
// or
require('@trutoo/event-bus');

eventBus.register(/*...*/);
```

or import the `EventBus` class to create your own instance.

```
import { EventBus } from '@trutoo/event-bus';
// or
const { EventBus } = require('@trutoo/event-bus');

const myEventBus = new EventBus();
myEventBus.register(/*...*/);
```

or using the UMD module and instance.

```
<script src="https://unpkg.com/@trutoo/event-bus@latest/dist/index.umd.min.js"></script>
<script>
  eventBus.register(/*...*/);
  // or
  const myEventBus = new EventBus.EventBus();
  myEventBus.register(/*...*/);
</script>
```

## Usage

Simple event bus registration with communication between a standard web component and a React component, as the event bus is framework agnostic. In addition a basic [JSON schema draft-04](https://tools.ietf.org/html/draft-zyp-json-schema-04) is used to restrict communication to a single boolean. Below outlines the basic usage, but can be can also be seen under `[/docs](https://github.com/trutoo/event-bus/tree/master/docs)` folder.

`JSON Schema`

```
{
  "type": "boolean"
}
```

`Fragment One - Web Component`

```
class PublisherElement extends HTMLElement {
  connectedCallback() {
    eventBus.register('namespace:eventName', { type: 'boolean' });
    this.render();
    this.firstChild && this.firstChild.addEventListener('click', this.send);
  }
  send() {
    eventBus.publish('namespace:eventName', true);
  }
  render() {
    this.innerHTML = `<button type="button">send</button>`;
  }
  disconnectedCallback() {
    this.firstChild && this.firstChild.removeEventListener('click', this.send);
  }
}
```

`Fragment Two - React Component`

```
import React, { useState, useEffect } from 'react';

function SubscriberComponent() {
  const [isFavorite, setFavorite] = useState(false);

  useEffect(() => {
    function handleSubscribe(favorite: boolean) {
      setFavorite(favorite);
    }
    eventBus.register('namespace:eventName', { type: 'boolean' });
    const sub = eventBus.subscribe<boolean>('namespace:eventName', handleSubscribe);
    return function cleanup() {
      sub.unsubscribe();
    };
  }, []);

  return isFavorite ? 'This is a favorite' : 'This is not interesting';
}
```

### Advanced Schema

Following is an example of a more a more complex use-case with a larger [JSON schema draft-04](https://tools.ietf.org/html/draft-zyp-json-schema-04) and registration on multiple channels.

`JSON Schema`

```
{
  "type": "object",
  "required": [
    "name",
    "amount",
    "price"
  ],
  "properties": {
    "name": {
      "type": "string"
    },
    "amount": {
      "type": "string"
    },
    "price": {
      "type": "number"
    },
    "organic": {
      "type": "boolean"
    },
    "stores": {
      "type": "array",
      "items": {
        "type": "object",
        "required": [],
        "properties": {
          "name": {
            "type": "string"
          },
          "url": {
            "type": "string"
          }
        }
      }
    }
  }
}
```

`Fragment - Angular Component`

```
import { Component } from '@angular/core';
import eventSchema from './event-schema.json';

@Component({
  selector: 'app-store',
  template: '<button (onClick)="onSend()">Add to cart</button>',
})
export class StoreComponent implements OnInit, OnDestroy {
  private subs: { unsubscribe(): void }[] = [];

  ngOnInit() {
    // Register on add to cart channel
    eventBus.register('store:addToCart', eventSchema);

    // No need to register if no schema is required
    this.sub.push(eventBus.subscribe<boolean>('store:newDeals', this.onNewDeals));
  }

  onNewDeals() {
    /* handle new deals ... */
  }

  onSend() {
    eventBus.publish('store:addToCart', {
      name: 'Milk',
      amount: '1000 ml',
      price: 0.99,
      organic: true,
      stores: [
        {
          name: 'ACME Food AB',
          url: 'acme-food.com'
        }
      ]
    });
  }

  ngOnDestroy() {
    this.subs.forEach(sub => sub.unsubscribe());
  }
}
```

## API

### Register

Register a schema for the specified event type and equality checking on subsequent registers. Subsequent registers must use an equal schema or an error will be thrown.

```
register(channel: string, schema: object): boolean;
```

### Parameters

[Untitled](@trutoo%20event-bus%203d3ff5b8b3ff408db6a7f0c10b5f21a6/Untitled%20Database%202e6bb9ffef0d49e99e0a316095c7c51f.csv)

**Returns** - returns true if event channel already existed of false if a new one was created.

### Unregister

Unregister the schema for the specified event type if channel exists.

```
unregister(channel: string): boolean;
```

### Parameters

[Untitled](@trutoo%20event-bus%203d3ff5b8b3ff408db6a7f0c10b5f21a6/Untitled%20Database%205e208a7aa8754432a6dc79547a1ec5a0.csv)

**Returns** - returns true if event channel existed and an existing schema was removed.

### Subscribe

Subscribe to an event channel triggering callback on received event matching type, with an optional replay of last event at initial subscription. The channel may be the wildcard `'*'` to subscribe to all channels.

```
subscribe<T>(channel: string, callback: Callback<T>): { unsubscribe(): void };

subscribe<T>(channel: string, replay: boolean, callback: Callback<T>): { unsubscribe(): void };
```

Callbacks will be fired when event is published on a subscribed channel with the argument:

```
{
  channel: string,
  payload: T,
}
```

### Parameters

[Untitled](@trutoo%20event-bus%203d3ff5b8b3ff408db6a7f0c10b5f21a6/Untitled%20Database%207c11a939686a427baf258f0531b8780c.csv)

**Returns** - object containing an unsubscribe method

### Publish

Publish to event channel with an optional payload triggering all subscription callbacks.

```
publish<T>(channel: string, payload?: T): void;
```

### Parameters

[Untitled](@trutoo%20event-bus%203d3ff5b8b3ff408db6a7f0c10b5f21a6/Untitled%20Database%201a2f75476c81441491fac8ec9c005e64.csv)

**Returns** - void

### Get Latest

Get the latest published payload on the specified event channel.

```
getLatest<T>(channel: string): T | undefined;
```

### Parameters

[Untitled](@trutoo%20event-bus%203d3ff5b8b3ff408db6a7f0c10b5f21a6/Untitled%20Database%2077b62852262e46418e355df98164a90c.csv)

**Returns** - the latest payload or `undefined`

### Get Schema

Get the schema registered on the specified event channel.

```
getSchema<T>(channel: string): any | undefined;
```

### Parameters

[Untitled](@trutoo%20event-bus%203d3ff5b8b3ff408db6a7f0c10b5f21a6/Untitled%20Database%20764b5a5d47304806a2d87657923cf881.csv)

**Returns** - the schema or `undefined`