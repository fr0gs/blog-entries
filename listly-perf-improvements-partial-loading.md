# Optimize rendering performance in an Ember 1.13 application (Part 2: Partial Loading)

# Problem

This is the second part in the series of improvements to enhance **Listly** (A frontend Ember.js application for the pmX suite made by [TenForce](https://www.tenforce.com)). The [overview](link-to-overview) and the [first part (pagination)](link-to-pagination) are also available.

As explained in the overview article, when visiting an *Item* list the application eagerly fetches all the items in it. This is a waste for two reasons:

* A user won't open all the items during the life of the session, hence, that premature fetching results in a big waste of data and CPU cycles deserializing, parsing, merging and storing it.
* For the list view, you only need to show a small subset of data about the item (Title, Assigned To, Creation Date, etc..)

The solution comes by receiving a list of "partial items" from the backend when entering the *Item List* view.

![IMAGE OF THE PARTIALLY LOADED LIST REMARKING THE TICKS](listly_partial_loaded.png)

In the image we can see a tick icon per item. Whenever the user opens an item (visits it), it is fully fetched from the backend and becomes available offline as well. Going back to the list we provide the visual clue of showing each wholly loaded item ticked.  

![IMAGE OF THE LOADED LIST WITH A TICKED ITEM](listly_partial_loaded_tick.png)

# Implementation

The backend will send the list of partial items with only the essential information to render the visible fields (e.g. *Title* attribute in the previous picture), along with the `auditIsLoaded: false` attribute to signal the items are partial.

```json
"data":[
   {
      "auditIsLoaded": false,
      "breadcrumbs": [ ... ],
      "self": "/items/1234",
      "parent": "/lists/1",
      "hierarchy": [ ... ],
      "properties": { ... },
      "fields": [ ... ],
      "actions": [ ... ],
      "referencedActions": [ ... ],
      "relationships": { ... },
      "readOnly": false
   }
   ...
 ]
```


The **list/index** route contains a list of **item-link** components that conform each one of the rows of the list.


```html
...
<tbody>
    {{#each model.content as |item|}}
        {{#item-link targetRoute="list-item" item=item tagName="tr"}}
            {{#each visibleFields as |field|}}
                <td>{{render-property item=item property=field.name display=field.display}}</td>
            {{/each}}
            <td class="empty"></td>
        {{/item-link}}
    {{/each}}
</tbody>
...
```

In the **item-link** component, in order to know whether to show the item ticked/unticked, we add a class binding depending on whether the item has the `fullItem` property set.

```javascript
classNameBindings: ["activating:pulse", "fullItem:item-link-full:item-link-partial"],
classNames: "item-link",
fullItem: Ember.computed.oneWay 'item.fullItem'
```

When the user opens an item in the list, the backend sends the full payload with the `auditIsLoaded: true` property. Then, internally, **Listly** begins a process of deserializing, diffing, and merging the payload with the current partial item. It is in the **merging** process where the `fullItem` property is set to `true` if the item is whole. This is a simplified version of the code:

```javascript
update: function(model, updatedPayload, verify) {
  if (updatedPayload['auditIsLoaded'] === true) {
    model.set('fullItem', true);
  }
  if (!(updatedPayload['auditIsLoaded'] === false && model['fullItem'] === true)) {
    payload = deserialize(updatedPayload);
    merge(model, payload);
    return this.get('offlineStore').update(model, payload);
  }
}
```

This way, when going back to the list view and receiving the `auditIsLoaded: false`, we don't overwrite the item. This brings to a potential issue when the item may have changed some property seen in the list view and they will never be updated, but is fixed with a "validity timeout" that will update items whenever they become too old in the cache.  

This concludes the partial loading strategy.


Have fun!
