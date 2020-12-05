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
```json
{
    "Point": {
        "x": "f32",
        "y": "f32",
        "z": "f32",
    },
    "Attack": {
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
* "i16" - signed 16bit number (2 byte).
* "u16" - unsigned 16bit number (2 byte).
* "i32" - signed 32bit number (4 byte).
* "u32" - unsigned 8bit number (4 byte).
* "f32" - single float-point number (4 byte).
* "f64" - double float-point number (8 byte).
* "s16" - utf8 string with capacity up to 64k bytes (not utf8 symbols!).
Any user type can be used as field too ("Point" type in example above).
Each field of user type can be not only single instance, but list of them:
```json
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