Undo/Redo support for [easy peasy](https://easy-peasy.now.sh/).

`undo-peasy`

- automatically saves a history of state changes made in your application.
- provides ready to use undo and redo actions.

## Usage

1. Attach `undoRedo` middlware in `createStore`.
   The middleware will automatically save every state change made to an `undoable` model.
   `const store = createStore(appModel, { middleware: [undoRedo()], });`
1. If using typescript, extend `WithUndo` in the root application model.
   `WithUndo` will add types for undo actions and metadata.
   `interface Model extends WithUndo { count: number; increment: Action<Model>; }`
1. Use `undoable` to wrap the root application model instance.
   `undoable()` will make undo/redo actions available and save state changes forwarded by the middleware.
   `const appModel: Model = undoable({ count: 0, increment: action((state) => { state.count++; }), });`
1. Profit
   ```
   const undo = useStoreActions((actions) => actions.undoUndo);
   ```

## Supported Actions

- **`undoUndo`** - restore state to the most recently saved version.
- **`undoRedo`** - restore state to the most recently undone version.
- `undoGroupStart` - start a group, no states will be saved until group completes.
- `undoGroupComplete` - complete a group of changes and save state.
- `undoGroupIgnore` - complete a group of changes and don't save state.
- `undoReset` - erases saved undo/redo history and saves the current state.
- `undoSave` - save current application state to undo history.
  (undoSave is generated automatically by the middleware.)

## Configuration

The `undoable()` function accepts an optional configuration object as its second parameter.

- `maxHistory` - maximum number of history states to save. The oldest states are dropped to prevent the history from growing without bounds.
- `noSaveKeys` - a function that tells undoRedo not to save certain keys inside the state model
  to undo/redo history. e.g. view state in the model.
- `skipAction` - a function that tells undoRedo not to save state after user specified actions
  or state conditions.
- `logDiffs` - set to true to see some debug logging about changes to undo state

History is persisted in the browser's localStorage.

## Hooks

`useUndoGroup()` - returns a function that can be used to group a related set of changes into
one undo/redo state.

```
  const undoGroup = useUndoGroup();

  undoGroup(() => {
    /* state changes in here will be saved as a single undo/redo state */
  });
```

`useUndoIgnore()` - returns a function that can be used to defer a set of changes.

```
  const undoIgnore = useUndoIgnore();

  undoIgnore(() => {
    /* state changes in here will be deferred, and not stored as separate undo/redo entries */
  });

  /* The next recorded change will combine with the deferred state into a single undo/redo entry */
```
