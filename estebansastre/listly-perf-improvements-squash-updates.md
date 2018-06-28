# Optimize rendering in an Ember 1.13 application (Part 3: Squash UI Updates)


# Introduction

This is the third part in the series of improvements to enhance **Listly** (A frontend Ember.js application for the pmX suite made by [TenForce](https://www.tenforce.com)). The [overview](link-to-overview), the [first part (pagination)](link-to-pagination) and the [second part (partial loading)](link-to-partial-loading) are also available.

# Problem

Every update to the user interface results in diffing, merging changes, serializing & sending it to the backend. This is extremely costly. Instead, it should be possible to create a small "update squashing service" for UI updates that would add a small debounce and create a single joint update to the item. This way, if the user is actively modifying fields (toggle-buttons, checkboxes, etc..) the application won't suffer from constant recalculations and UI repainting.

# Implementation

Whenever a property is modified, the **edit-property/<specific-component>** component will fire a `pullProperty` action captured in the upper **edit-property** component. Then, the item is updated offline with the modified property and the updated model is sent to the squash service to delay the update.

```javascript
changesSquashService: Ember.inject.service('changes-squash-service'),
actions: {
  pullProperty: function(value) {
    if (this.get(`item.properties.${this.get('property')}`) !== value) {
      this.get('store').update(this.get('item'), this.createPayload(value));
      return this.get('changesSquashService').add(this.get('item'), this.get('property'), this.createPayload(value));
    }
  }
}
```

The squash service is a simple service (**changes-squash-service**) that debounces the update every time a new update is added into the `changesObject` object.

```javascript
var ChangesSquashServiceService = Ember.Service.extend({
  store: Ember.inject.service('lazy-store'),
  addDebounceTimeout: 8000,
  changesObject: Ember.Object.create(),

  // Adds a property change to the list of changes.
  add: function(item, property, payload) {
    this.get('changesObject').set(property, payload);
    return Ember.run.debounce(this, this.flushChanges, item, this.get('addDebounceTimeout'));
  },
  flushChanges: function(item) {
    let payload = {
      'properties': {}
    };
    Object.keys(this.get('changesObject')).forEach((key) => {
      return payload['properties'][key] = this.get(`changesObject.${key}.properties.${key}`);
    });
    return this.get('store').update(item, payload);
  }
});
```


That's it, simple yet effective, and with big improvement in UI responsiveness.


Have fun!
