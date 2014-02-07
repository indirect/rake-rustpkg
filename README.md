# rake-rustpkg

Rustpkg was deprecated and removed, but I already had a project set up to use it. This replaces rustpkg with a small Rakefile, and provides rake tasks for each crate in src.

## Usage

```
$ rake build
$ rake test
$ ./bin/crate
```

### Filesystem layout

```
$ tree .
.
├── bin
│   └── crate
├── lib
│   └── libcrate-abc123-0.1.rlib
└── src
    └── crate
        ├── lib.rs
        ├── main.rs
        └── test.rs
```

