# redux-json-api-helper
[![npm](https://img.shields.io/npm/v/redux-json-api-helper.svg?maxAge=2592000)]()
[![Travis](https://img.shields.io/travis/codeandgraphics/redux-json-api-helper.svg)]()

A package for consuming and accessing [JSON API](https://jsonapi.org/) data with Redux.
It will take a standard JSON API response, automatically flatten its structure, and
update your redux store. For most apps, this greatly cuts down on the number of reducers
that need to be written.

Note: This package does not make/handle network requests. Its purpose is to consume
JSON API data that has already been retrieved from a server.

## Hooking up the store
```javascript
import { reducer as entities } from 'redux-json-api-helper';
import * as reducers from './reducers';

const store = createStore(
    combineReducers({...reducers, entities}),
);
```

## Consuming JSON data
```javascript
import { loadJsonApiEntityData } from 'redux-json-api-helper';

dispatch(loadJsonApiEntityData(jsonApiResponseFromServer));
```

## Manipulating entities
```javascript
import { addRelationshipToEntity, removeRelationshipFromEntity, updateEntity } from 'redux-json-api-helper';

/**
 * dispatch(updateEntity('article', articleId, {
 *     isUserFavorite: true
 * }));
 */
updateEntity(entityKey, entityId, dataObject);

/**
 * dispatch(addRelationshipToEntity('article', '54321', 'readers', {
 *     type: 'user',
 *     id: '12345',
 *     attributes: { name: "Bob Ross" }
 * }));
 */
addRelationshipToEntity(entityKey, entityId, relationshipKey, relationshipJsonApiObject);

/**
 * You can also add relationships by ID.
 * dispatch(addRelationshipToEntity('article', '54321', 'readers', '12345'));
 */
addRelationshipToEntity(entityKey, entityId, relationshipKey, relationshipJsonApiObject);


// dispatch(removeRelationshipFromEntity('article', '54321', 'readers', '12345'));
removeRelationshipFromEntity(entityKey, entityId, relationshipKey, relationshipId);
```

## Retrieving entities from the store
```javascript
import { getEntity, getEntities } from 'redux-json-api-helper';

// Get single article
const article = getEntity(state.entities, 'article', articleId);

// Get all articles
const articles = getEntities(state.entities, 'articles');

// Get array of articles
const articles = getEntities(state.entities, 'articles', [id1, id2, id3]);
```

## Removing entities from the store
```javascript
import { removeEntity, clearEntityType } from 'redux-json-api-helper';

// Remove a single entity
dispatch(removeEntity('articles', '1'));

// Remove all entities from an entity type
dispatch(clearEntityType('articles'));
```

## Metadata
```javascript
import { updateEntitiesMeta, updateEntityMeta, getEntitiesMeta, getEntityMeta } from 'redux-json-api-helper';

// Set a metadata value for an Entity type
dispatch(updateEntitiesMeta('articles', 'isLoading', true));

// Get all metadata for an Entity type
const metadata = getEntitiesMeta(state.entities, 'articles');

// Get a specific metadata value for an Entity type
const isLoading = getEntitiesMeta(state.entities, 'articles', 'isLoading');

// Set a metadata value for a specific Entity
dispatch(updateEntityMeta('articles', '123', 'isLoading', true));

// Get all metadata for a specific Entity
const metadata = getEntityMeta(state.entities, 'articles', '123');

// Get a specific metadata value for a specific Entity
const isLoading = getEntityMeta(state.entities, 'articles', '123', 'isLoading');
```

## Helpers
```javascript
import { getId, getIds } from 'redux-json-api-helper';

// Extract item ID from JSON API response
getId(jsonResponse);

// Extract collection ID's from JSON API response
getIds(jsonResponse);
```

## Generate an entity locally
Sometimes you may need to generate and store an entity that didn't actually come from a JSON API.    
__redux-json-api-helper__ provides a simple `generateEntity` helper function for that.

```javascript
import { generateEntity, loadJsonApiEntityData, addRelationshipToEntity } from 'redux-json-api-helper';

// Generate an Article entity and store it
// generateEntity(entityKey, attributes);
const article = generateEntity('article', { id: '123', title: 'Example Title' });
dispatch(loadJsonApiData(article));

// If no ID is provided, one will be generated automatically using UUID v4
const user = generateEntity('user', { name: 'Bob Ross' });
dispatch(addRelationshipToEntity('articles', '123', 'readers', user));
```
