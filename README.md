# Lightweight simple binary format.
Specification for data format for save to file / transfer data over network.

# Goals
Lightweight replacement for protobuf / flatbuffers without fat boilerplate code around.

This format - attempt to create simplest binary serialization with:
* Simple readable data scheme.
* Support for multiple languages.
* Lightwight processing in runtime.

# Data scheme description
Scheme - standard json and can be parsed without special lexer/parser:
```jsonc
{
    // Point in 3d space. Yes, single line comments are supported inside config.
    "Point": {
        "x": "f32",
        "y": "f32",
        "z": "f32",
    },
    "Attack": {
        /*
        Id of attacker unit.
        Yes, multiple line
        comments are supported
        inside config too.
        */
        "attackerId": "i32",
        "target": "Point",
        "damage": "f32"
    }
}
```
Each scheme record - separate user type (packet) with nested fields / properties.

Each field of user type can be one of simple types:
* "i8" - signed 8bit number (1 byte).
* "u8" - unsigned 8bit number (1 byte).
* "i16" - signed 16bit number (2 bytes).
* "u16" - unsigned 16bit number (2 bytes).
* "i32" - signed 32bit number (4 bytes).
* "u32" - unsigned 8bit number (4 bytes).
* "f32" - single float-point number (4 bytes).
* "f64" - double float-point number (8 bytes).
* "s16" - utf8 string with capacity up to 64k bytes. Important - data length in bytes, not in chars, real possible string length will be smaller on non-latin symbols!

Any user type can be used as field too ("Point" type in example above).

Each field of user type can be not only single instance, but list of them:
```jsonc
{
    "Item": {
        "id": "u32",
        "count": "u32"
    },
    "Inventory": {
        "items": "Item[]"
    }
}
```

Enums can be described in scheme too:
```jsonc
    // enum description - with "/enum" suffix.
    "ClientType/enum": [
        "iOS",
        "Android"
    ],
    "Client": {
        // enum usage as field - type should contains full name with suffix.
        "type": "ClientType/enum",
        "name": "s16",
        "pass": "s16"
    }
```
> Only enums with less than 256 items are supported (will be packed as `u8` field).

Scheme can be extended by "including" other schemes (with recursive support, but without circular loop checking, use with care):
```jsonc
{
    /*
    "#include" node name hardcoded, all content
    will be included at start of final scheme.
    */
    "#include": [
        // each line of array is filename of external scheme file.
        "./common.json"
    ],
    "Item": {
        "id": "u32",
        "count": "u32"
    },
    "Inventory": {
        "items": "Item[]"
    }
}
```

Types can be extended by "common" fields that will be included to all type schemas:
```jsonc
{
    /*
    "#common" node name hardcoded, all content
    will be included at start of each user type.
    */
    "#common": {
        "id":"u16",
        "name":"s16"
    },
    "Item": {
        /*
        invalid schema - field with name "id"
        already exists (injected by "#common").
        */
        "id": "u32",
        "count": "u32"
    },
    "Inventory": {
        "items": "Item[]"
    }
}
```
# Features / Limits
* Amount of user types limited to 64k.
* Amount of items in field of "array" type limited to 64k.
* No optional fields.
* All fields of "string" type inited with empty string by default.
* All fields of any number type inited with zero value by default.


# Code generation
[Node-based generator](https://github.com/Leopotam/simplebinary-gen-node.git)

## Support code
* [Typescript](https://github.com/Leopotam/simplebinary-ts.git)
* [C#](https://github.com/Leopotam/simplebinary-cs.git)