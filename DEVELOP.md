tfmicro
======

## Developing

Update submodules:

```
git submodule init
git submodule update
```

Then we need to tell tensorflow to download its own dependencies. Actually
building one of the tensorflow micro examples does this, and as a side
effect checks that tensorflow is working.

```
cd submodules/tensorflow
make -f tensorflow/lite/micro/tools/make/Makefile test_micro_speech_test
cd ../..
```

Then we can build!

```
cargo test
```

We use the `env_logger` crate for log output in tests, try

```
RUST_LOG=info cargo test
```

Changes in the tensorflow source tree aren't tracked by cargo. If the
tensorflow source has changed, use the `build` feature gate to force a
re-build.

```
cargo build --features build
```

To debug `build.rs` itself, try `cargo build -vv`

## Updating tensorflow

Some tips for trying out new tensorflow versions

#### Use the build script to automatically run git bisect

```
cd submodules/tensorflow
```

Mark the current revision as good

```
git bisect start
git bisect good
```

Grab the latest head

```
git fetch origin
git checkout origin/master
git bisect bad     # Presumably it's bad
```

Run the build script

```
git bisect run ../../build.sh
```

Go do something else for a few hours, then git will tell you which commit was
bad.

## Releasing

1. Update version in Cargo.toml and lib.rs
   * Update CHANGELOG.md
   * Then commit it

```
git commit -m "v0.1.0"
```

2. Run `publish_crate.sh`. The script does these tasks:
   * Checks the README is up-to-date with lib.rs
   * Checks that crate builds with only files from the include list in Cargo.toml
   * Checks the resulting package size is ok
   * Publishes to crates.io

3. Push and tag release commit:

```
git push
git tag -a 'v0.1.0' -m 'v0.1.0'
git push origin v0.1.0
```
