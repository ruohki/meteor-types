## zodern:types

Tooling to use Typescript types from Meteor packages in your apps.


### Meteor Apps

For Meteor apps, `zodern:types` allows typescript to find the types from Meteor packages.
Supports apps using Meteor 2.3 or newer.

To get started:

1) Add `zodern:types` to your app by running:

```bash
meteor add zodern:types
```

2) Configure typescript to find the types from packages. In your `tsconfig.json` file, add an entry to the `compilerOptions.paths` option:

```json
{
  "compilerOptions": {
    "paths": {
      "meteor/*": [
        ".meteor/local/types/packages.d.ts"
      ]
    }
  }
}
```

`zodern:types` stores the type definitions from packages in your app's `.meteor/local/types` folder.

`zodern:types` is implemented as a Meteor linter plugin. The type definitions are updated whenever your app is built. You can also update the types directly by running `meteor lint`.

### Finding types in packages

`zodern:types` tries various ways to identify which file to tell typescript to use for the package's types. It uses the first one that works:

1. If the package is a local package, and only has a single ts file used as the main module, it uses this file
2. If the package uses `zodern:types`:
  1. If a local package, the path from the `typesEntry` option in the package's `package-types.json` file is used
  2. Otherwise, uses the types that were generated when the package was published
3. If the package has a single `.d.ts` file, this file is used
4. If the package has a single file used as the main module, a file with the same name and the `.d.ts` extension is used. If the file is in a `src` folder, it will also check in a `lib` folder for the `.d.ts` file.


### Meteor Packages

For Meteor packages, `zodern:types` makes it easier to publish a package with types.

Requires Meteor 2.3 or newer when running `meteor publish`. Outside of publishing, it shouldn't have any restrictions on the Meteor versions you can run or test your package with.

When publishing your package, it handles:
- Ensuring Meteor includes any `.d.ts` and `package-types.json` files
- Types are generated
- Uninstall any npm dev dependencies, and re-install them after the package is published

In the future, it will also allow typescript to find the types of any of your package's dependencies, similar to what it does for Meteor apps.

To use:
1. Add `zodern:types` as a dependency of your package in `package.js`:

```js
Package.onUse(function (api) {
  api.use('zodern:types');
});
```

2. Add a `package-types.json` file:

```json
{
  "typesEntry": "main.ts"
}
```

While Meteor allows you to have separate main modules for each arch, Typescript only supports having a single set of types for the package. `typesEntry` should be the path to a ts file that exports the api and types that code using the package should have available. This could be the package's main module, or another ts file.

In development, typescript will use the ts file directly for the package's types. This way, the types will be automatically updated whenever Meteor rebuilds the package. When publishing, `zodern:types` will have typescript create `.d.ts` files.
