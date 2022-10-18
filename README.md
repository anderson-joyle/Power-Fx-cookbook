# Power Fx cookbook 👨‍🍳
Non-official collection of solutions and examples for this new programming language. <i>Primarily written for my own benefit, but hopefully helpful to someone else as well.</i>

***

## Table of content

- [What](#what)
- [How to](#how-to)
  - [Serialize and Deserialize](#serialize-and-deserialize)
  - [Errors](#errors)
    - [New ErrorKind element](#new-errorkind-element)

## How to
### Serialize and Deserialize
Power Fx offers a simple way to serialize and deserialize FormulaValue values.
To serialize, call "ToExpression" method on any FormulaValue derived types (StringValue, NumberValue, etc) to get a serialized value version.

```
var myValue = FormulaValue.New(100);
myValue.ToExpression().Dump(); // "100"
```

To deserialize, call Eval to rehidrate the previously serialized value back to a FormulaValue.
```
var myValue = FormulaValue.New(100);
var engine = new Engine(new PowerFxConfig());
var check = engine.Check(myValue.ToExpression());
var result = check.GetEvaluator().Eval();

result.ToObject(); // 100
```

### Errors
#### New ErrorKind element
  - Update **...\src\libraries\Microsoft.PowerFx.Core\Types\Enums\EnumStoreBuilder.cs**, adding `NEW_ELEMENT_NAME: NEW_VALUE` at the end of the ErrorKindEnumString definition
  - Add a `NEW_ELEMENT_NAME = NEW_VALUE` at the end of ...\src\libraries\Microsoft.PowerFx.Core\Public\ErrorKind.cs
  - Update strings\PowerFxResources.en-US.resx to add this element (after the definition of ErrorKind_Internal_Name)
  
```
<data name="ErrorKind_NEW_ELEMENT_NAME_Name" xml:space="preserve">
    <value>NEW_ELEMENT_NAME</value>
    <comment>{Locked} Enum value</comment>
  </data>
```

And update the code to use this new enum value
