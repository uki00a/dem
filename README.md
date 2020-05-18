# dem

[![Build Status](https://github.com/syumai/dem/workflows/test/badge.svg?branch=master)](https://github.com/syumai/dem/actions)

- A simple module version manager for Deno.
- dem creates `dem.json` and links script files with version.
  - linked scripts are stored in `vendor` directory.
- dem provides an **easy way to update dependencies.**

## Example

**Using `https://deno.land/std/http/server.ts` at `v0.51.0`**

- `dem.json`
  - generated file like a package.json

```json
{
  "modules": [
    {
      "protocol": "https",
      "path": "deno.land/std",
      "version": "v0.51.0",
      "files": [
        "/http/server.ts"
      ]
    }
  ]
}
```

- `vendor/https/deno.land/std/http/server.ts`
  - generated linked script includes version specified in `dem.json`

```ts
export * from "https://deno.land/std@v0.51.0/http/server.ts";
```

- `example.ts`
  - script to run

```ts
// Now you can import module without the version! (because version `v0.51.0` is stored in linked script)
import { serve } from "./vendor/https/deno.land/std/http/server.ts";
```

## Install

```console
deno install --allow-read --allow-write -f -n dem https://deno.land/x/dem@0.7.0/cmd.ts
```

## Usage

### Getting Started

#### 1. `dem init` to create `dem.json`

```console
$ dem init
successfully initialized the project.
```

#### 2. `dem add` to add module.

```console
$ dem add https://deno.land/std@v0.51.0
successfully added new module: https://deno.land/std, version: v0.51.0
```

#### 3. Edit your script and import module from `vendor`.

`example.ts`

```ts
import * as path from './vendor/https/deno.land/std/fs/path.ts';

console.log(path.join(Deno.cwd(), 'example'));
```

#### 4. `dem ensure` to resolve modules.

```console
$ dem ensure
successfully created link: https://deno.land/std@v0.51.0/fs/path.ts
```

#### 5. Run your script.

```console
$ deno example.ts
```

## Standard Usage

### Updating module

* `dem update` updates versions in `dem.json` and linked scripts.

```console
$ dem update https://deno.land/std@v0.52.0
successfully updated module: https://deno.land/std, version: v0.52.0
```

## Advanced Usage

### Alias

* With alias, shortened script paths are available.

`example.ts`

```ts
// Original
import * as dejs from "./vendor/https/deno.land/x/dejs/mod.ts"
// With alias
import * as dejs from "./vendor/dejs.ts"
```

#### How to use alias

1. import file with alias path

```ts
import * as dejs from "./vendor/dejs.ts"
```

2. execute `alias` and `ensure`.
   - if link is already created, you don't have to execute `ensure`.

```console
# create alias for module.
$ dem alias https://deno.land/x/dejs/mod.ts dejs.ts

# create link for `./vendor/https/deno.land/x/dejs/mod.ts`.
$ dem ensure
```

## Commands

```console
dem init                                          // initialize dem.json
dem add https://deno.land/x/dejs@0.1.0            // add module `dejs` and set its version to `0.1.0`
dem link https://deno.land/x/dejs/mod.ts          // create link of `dejs@0.1.0/mod.ts` and put it into vendor.
dem update https://deno.land/x/dejs@0.2.0         // update module to `0.2.0`
dem unlink https://deno.land/x/dejs/mod.ts        // remove link of `dejs@0.2.0/mod.ts`.
dem remove https://deno.land/x/dejs               // remove module `dejs`
dem ensure                                        // resolve file paths of added modules used in project and link them.
dem prune                                         // remove unused modules and linked scripts.
dem alias https://deno.land/x/dejs/mod.ts dejs.ts // create alias for module and put it into vendor.
dem unalias dejs.ts                               // remove alias for module.
```

### Unsupported features

- default export
- manage `.d.ts` file

## Author

syumai

## License

MIT
