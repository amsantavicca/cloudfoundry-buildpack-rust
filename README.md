#CloudFoundry Rust Buildpack

This is an experimental [CloudFoundry
Buildpack](http://docs.cloudfoundry.org/buildpacks/) for the
[Rust](https://rust-lang.org) programming language. It has been tested
exclusively using the [IBM Bluemix](https://console.ng.bluemix.net/) platform,
but should would well with any PaaS based on the CloudFoundry stack.

##What does this buildpack do?

This buildpack uses [rustup](https://www.rustup.rs) to download and install a
Rust toolchain of your choosing.

Then, it will compile your application using the `cargo build` command plus
whatever build flags you specify in your application's `manifest.yml`.

Finally, it will determine how to start your application using the start
command you specify in your application's `manifest.yml`.

##How do you use this buildpack?

As a developer using this Rust buildpack, you need to do the following:

1. Create and use a `Cargo.toml` file to manage dependencies and build your
   Rust application
2. In your application's `manifest.yml` you should include the following
   user defined environment variables:
     - `RUST_TOOLCHAIN`: the version of the Rust compiler to use. Should be
       `nightly`, `beta`, or `stable`. Defaults to `stable` if not specified
     - `RUSTUP_VERBOSE`: tells the `rustup` installer to display verbose log
       messages. Value can be any non-empty string. Defaults to non-verbose
     - `RUSTUP_CUSTOM`: provide a custom set of arguments to the `rustup`
       installer tool. **Note**: this will override `RUST_TOOLCHAIN` and
       `RUSTUP_VERBOSE` regardless of whether they are specified
     - `CARGO_BUILD_FLAGS`: the arguments passed to the `cargo build` command
       to build your application. If not defined, will default to `cargo build
       --release`
     - `START_COMMAND`. This is the command that will be used to start your
       application. If not defined, will default to the value as defined by
       `application_name` within the `VCAP_APPLICATION` environment variable

For example, if the binary created by your build command is in
`target/release/my_app`, your `manifest.yml` should look something like:

``` yaml
---
applications:
- name: my_app
  instances: 1
  memory: 512 MB
  env:
    RUST_TOOLCHAIN: beta
    RUSTUP_VERBOSE: indeed
    CARGO_BUILD_FLAGS: -j 4 --release
    START_COMMAND: ./target/release/my_app
```
If you're not deploying with a `manifest.yml`, specifying any necessary
environment variables on the command line (e.g. via `cf push`) should work
as well (although I haven't tested this.)

##License

The CloudFoundry Rust Buildpack is released under version 2.0 of the [Apache
License](http://www.apache.org/licenses/LICENSE-2.0).
