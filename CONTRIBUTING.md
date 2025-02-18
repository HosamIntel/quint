# Contributing

**This file is under construction...**

This project is part of the [Apalache][] ecosystem.  Hence, we apply the
same principles in Quint, see [Contributing to Apalache][].

## Coordinating work

Development on Quint is distributed. As with any distributed system, establishing
effective synchronization and consensus on key properties requires careful
attention.

## Project structure

Currently, the project consists of two npm packages (published locally):

 - [quint](./quint) is the transpiler package, see the [quint manual][].
 - [vscode/quint](./vscode/quint) is the VSCode plugin for Quint, depends on `quint`.
   This plugin has two subpackages:

   - [vscode/quint/server](./vscode/quint/server) is the server-side package,
     implementing [Language server protocol][] used by VSCode and other
     code editors.

   - [vscode/quint/client](./vscode/quint/client) is the client-side package,
     implementing the language extension for VSCode.

## Dependencies

For setting up the local build, you would have to install TypeScript and npm.
This is usually done via your local package manager.

### Formatting

We use [eslint][] to enforce the good coding practices of JavaScript and
TypeScript. This is especially important in the context of these languages, as
JavaScript has plenty of bad parts.

## Testing

### Build and run quint and the VSCode plugin from source

These steps are currently a bit complicated. Check the dedicated pages:

 - [Installing quint][],
 - [Installing the VSCode plugin][].

### Tests

#### Unit tests for quint

In general, we are using the [Mocha][] test framework to write and run unit
tests. We are using [Chai][] to write assertions, without going into BDD
testing too much. For details, see [quint unit tests][].

#### Integration tests for quint

We are using the [txm][] framework to write integration tests. For details,
check [quint integration tests][].

#### End-to-end tests for the VSCode plugin

We do not have unit tests for the VSCode plugin. Instead we have end-to-end
tests that run via VSCode:

```sh
cd vscode/quint
npm run test
```

## Coding practices

This section is especially important for this project, as it is very easy to
write very bad code in JavaScript and TypeScript. Hence, we pay special
attention to the coding practices. We keep refining them, as we are learning
the language in the process.

In general, we are trying to leverage good practices of functional programming
(FP) in JavaScript/TypeScript. However, it is not always possible to write nice
FP code in this language, so we keep the balance between readability and
FPness.  When the idiomatic JavaScript code is shorter and clearer than
equivalent FP code, we write the idiomatic JavaScript code.

### Using Either

TODO

### Using Maybe

When there is too much undefinedness in the code, we are using the option type,
which is implemented in the [sweet-monads/maybe][] package.

**Caveat:** In contrast to the FP languages, equality in JS/TS is not structural.
Hence, the following code always returns `false`:

```js
> import { none, just } from '@sweet-monads/maybe'
> just(true) === just(true)
false
> none() === none()
false
```

For this reason, we are using `isEqual` provided by the [lodash.isequal][] package:

```js
> import { none, just } from '@sweet-monads/maybe'
> import isEqual from 'lodash.isequal'
> isEqual(just(true), just(true))
true
> isEqual(none(), none())
true
```

## Using the VSCode plugin from source

There are many ways to use the VSCode plugin from source. You also may want to use it with an unpublished version of `quint`.
This section is a suggestion on how to do it, and its also how we do it.

To build the vscode plugin, run the `vscode` make target from [the root of this repo](../../):

```sh
make vscode
```

To install the plugin for use, link the combined plugin into your vscode
extensions. From the root of this repo, you can run:

```sh
ln -s $PWD/vscode/quint-vscode/ $HOME/.vscode/extensions/informal.quint-vscode
```

### Using the VSCode plugin with an unpublished version of `quint`

We use `yalc` to manage unpublished packages. To install it, run

``` sh
npm i yalc -g
```

Then use the `local` make target to replace the published version of `quint` with the local one and build the plugin:

``` sh
make local
```

Make sure you have the folder linked to your vscode extensions as described above.

### Troubleshooting

Between installing the plugin from different sources, you may end up with multiple versions and VSCode can get confused about which version to use. To fix this, follow these steps:

1. Navigate to `$HOME/.vscode/extensions` and delete folders called `informal.quint-vscode*` except for the one you want to keep.
2. `rm $HOME/.vscode/extensions/extensions.json`.
3. `rm $HOME/.vscode/extensions/.init-default-profile-extensions`.
4. Restart VSCode **twice**. The first time it will recreate the `extensions.json` file, the second time it will install the extensions. Reloading won't work, you need to actually close and reopen VSCode.

[Apalache]: https://github.com/informalsystems/apalache
[Contributing to Apalache]: https://github.com/informalsystems/apalache/blob/unstable/CONTRIBUTING.md
[eslint]: https://eslint.org/
[quint manual]: ./doc/quint.md
[Installing quint]: https://github.com/informalsystems/quint/blob/main/quint/README.md#how-to-install
[Installing the VSCode plugin]: https://github.com/informalsystems/quint/blob/main/vscode/quint/README.md#temp-how-to-run-it-locally
[Language server protocol]: https://microsoft.github.io/language-server-protocol/
[quint unit tests]: https://github.com/informalsystems/quint/blob/main/quint/README.md#unit-tests
[quint integration tests]: ./quint/README.md#integration-tests
[Mocha]: https://mochajs.org/
[Chai]: https://www.chaijs.com/
[txm]: https://www.npmjs.com/package/txm
[sweet-monads/maybe]: https://www.npmjs.com/package/@sweet-monads/maybe
[lodash.isequal]: https://www.npmjs.com/package/lodash.isequal
