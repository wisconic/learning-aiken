# learning_aiken

Write validators in the `validators` folder, and supporting functions in the `lib` folder using `.ak` as a file extension.x

## Building

```sh
aiken build
```

## Testing

You can write tests in any module using the `test` keyword. For example:

```gleam
test foo() {
  1 + 1 == 2
}
```

To run all tests, simply do:

```sh
aiken check
```

To run only tests matching the string `foo`, do:

```sh
aiken check -m foo
```

## Documentation

If you're writing a library, you might want to generate an HTML documentation for it.

Use:

```sh
aiken docs
```

## Resources

Find more on the [Aiken's user manual](https://aiken-lang.org).


## Scaffolding (Getting Started)

Create scaffolding for a new aiken project:

```sh
aiken new <github_username>/learning_aiken
```

You should see:

```output
Your Aiken project <github_username>/learning_aiken has been successfully created.
The project can be compiled and tested by running these commands:

    cd learning_aiken
    aiken check
    aiken build

hint: You may want to update the stdlib version in aiken.toml.
```

## First Validator Script -- Always Succeed

To demonstrate how to create a validator script, we will create the most basic on imaginable -- one that _always_ succeeds.

Create a new `always_succeed.ak` file in our `validators/` directory.

```sh
touch validators/always_succeed.ak
```

Add this code to `validators/always_succeed.ak` which will always return true, therefore, _always_ succeeding.

```gleam
validator {
  fn always_succeed(_datum: Data, _redeemer: Data, _context: Data) -> Bool {
    True
  }
}
```

NOTICE: we are doing nothing with `datum`, `redeemer`, and `context`, so we prefix with `_` to get rid of warnings for unused variables.

### Check and Build

Check and run tests (of which we have none).

```sh
aiken check
```

Build...

```sh
aiken build
```

This command (`aiken build`) generates a [CIP-0057 Plutus blueprint](https://github.com/cardano-foundation/CIPs/pull/258) as `plutus.json` at the root of your project.

From the Aiken docs:

> This blueprint describes your on-chain contract and its binary interface. In particular, it contains the generated on-chain code that will be executed by the ledger, and a hash of your validator(s) that can be used to construct addresses.
>
> This format is framework-agnostic and is meant to facilitate interoperability between tools. The blueprint is fully integrated into Aiken, which can automatically generate it based on your type definitions and comments.

## Hello, World

Follow Aiken's `Hello, World` example to go a little deeper.

- https://aiken-lang.org/example--hello-world

Create a new file: `validators/hello_world.ak`

```sh
touch validators/hello_world.ak
```

In `validators/hello_world.ak`, paste following code:

```gleam
use aiken/hash.{Blake2b_224, Hash}
use aiken/list
use aiken/transaction.{ScriptContext}
use aiken/transaction/credential.{VerificationKey}

type Datum {
  owner: Hash<Blake2b_224, VerificationKey>,
}

type Redeemer {
  msg: ByteArray,
}

validator {
  fn hello_world(datum: Datum, redeemer: Redeemer, context: ScriptContext) -> Bool {
    let must_say_hello =
      redeemer.msg == "Hello, World!"

    let must_be_signed =
      list.has(context.transaction.extra_signatories, datum.owner)

    must_say_hello && must_be_signed
  }
}
```

From Aiken docs:

> Our (hello world) validator is rudimentary. It is parameterized by a verification key hash (owner) and a message (msg). Remember that, in the eUTxO model, the datum is set when locking funds in the contract and can be therefore seen as configuration. Here, we'll indicate the owner of contract and require a signature from them to unlock funds -- very much like it already works on a typical non-script address. Moreover, because there's no "Hello, World!" without a proper "Hello, World!" our little contract also demands this very message, as a UTF-8-encoded byte array, to be passed as redeemer (i.e. when spending from the contract).

### Check and Build

Check and run tests (of which we have none).

```sh
aiken check
```

Build...

```sh
aiken build
```

NOTICE: now our `plutus.json` file has _two_ validators listed, as well as definitions for the types used in the hello world script.