# ipld-schema-validator

Build fast and strict JavaScript object form validators using [IPLD Schemas](https://specs.ipld.io/schemas/).

## Example

```js
import Schema from 'ipld-schema'
import SchemaValidator from 'ipld-schema-validator'

// Build an IPLD Schema from the text form
const schemaText = `
type FooBarBazStruct struct {
  bim Int
  bam String
  boom Bool
} representation tuple

type MyList [Int]

type MyStruct struct {
  foo FooBarBazStruct
  bar MyList
  baz String
}
`

// Compile it to its object descriptor form which SchemaValidator consumes
const schemaDescriptor = new Schema(schemaText).descriptor

// Create a validator function from the Schema descriptor, with 'MyStruct' as the
// root type to inspect
const myStructValidator = SchemaValidator.create(schemaDescriptor, 'MyStruct')

// An object that matches our schema
const obj = {
  foo: [1, 'one', true],
  bar: [1, 2, 3, 4],
  baz: 'baz'
}

console.log('Validating object as MyStruct:', myStructValidator(obj)) // true
obj.boop = true // modify the object, adding an additional property not defined by the schema
console.log('Validating modified object as MyStruct:', myStructValidator(obj)) // false

// Make another validator function but use the 'MyList' (`[Int]`) type as the root
const myListValidator = SchemaValidator.create(schemaDescriptor, 'MyList')

console.log('Validating [1, 2, 3] as MyStruct:', myListValidator([1, 2, 3])) // true
console.log('Validating object as MyStruct:', myListValidator(obj)) // false
console.log('Validating [1, \'one\', true] as MyStruct:', myListValidator([1, 'one', true])) // false
console.log('Validating {} as MyStruct:', myListValidator({})) // false
```

Note that **[ipld-schema](https://ghub.io/ipld-schema)** is needed to if you want to use the human-readable Schema text form. **ipld-schema-validator** consumes the object descriptor form, it is not recommended that you craft this form by hand.

## Limitations

Currently **ipld-schema-validator** supports the full IPLD Schema specification except for the following:

* Maps with `stringpairs` representation
* Struct with `stringpairs` representation
* Struct with `stringjoin` representation

## License & Copyright

Copyright 2020 Rod Vagg

Licensed under Apache 2.0 ([LICENSE-APACHE](LICENSE-APACHE) / http://www.apache.org/licenses/LICENSE-2.0)
