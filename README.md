# zap-yaml

A YAML parsing library for the [Zap programming language](https://zaplang.xyz/).

Block style only for now: mappings, sequences (including the compact
`- key: value` item form), plain/single/double-quoted scalars, null/bool/int
/float typing, comments, duplicate-key detection, and a tolerated leading
`---`. Not yet supported: flow style (`[a, b]`, `{a: b}`), anchors/aliases,
block scalars (`|`/`>`), tags, and multi-document streams.

## Usage

```zap
import "std/io";
import "yaml";

fun main() Int {
    let doc = yaml.parseYaml("name: Daniel\ntags:\n  - zig\n  - zap\n") or err {
        io.println("failed to parse: " + err.message);
        return 1;
    };

    let root = doc.getRoot().asMapping() or err {
        io.println("expected the document root to be a mapping");
        return 1;
    };

    io.println(root.getString("name") or err { "?" });

    let tags = root.getSequence("tags") or err { return 1; };
    var i: Int = 0;
    while i < tags.len() {
        io.println(tags.getString(i) or err { "?" });
        i = i + 1;
    }

    return 0;
}
```

See `src/main.zp` for a fuller runnable example (`thor run`).

## API

- `yaml.parseYaml(input: StringView) Document!Error` — parse a document.
- `Document.getRoot() Node` — the top-level value.
- `Node` — a tagged value; `kind()`, `isNull()/isBoolean()/isInteger()/isFloat()/isString()/isSequence()/isMapping()`,
  and checked accessors `asString()/asBool()/asInt()/asFloat64()/asSequence()/asMapping()`, all `!Error`.
- `Mapping` — `has(key)`, `len()`, `keyAt(index)!Error`, `get(key)!Error`, plus typed
  shortcuts `getString/getInt/getBool/getMapping/getSequence(key)!Error`.
- `Sequence` — `len()`, `at(index)!Error`, plus typed shortcuts
  `getString/getInt/getBool(index)!Error`.

## Using this as a dependency

Nothing special is needed on this side — it's a normal Zap module. From
another `thor` project, either point at it directly with a local import:

```toml
[imports]
"@yaml" = "path/to/zap-yaml/src"
```

```zap
import "@yaml/yaml";
```

or, once this is published to a git repo, add it under `[dependencies]` and
let `thor` vendor it into `vendor/` the same way it vendors `zap-toml`. See
`/home/daniel/zap/yaml-consumer-demo` for a working example of the local
form.
