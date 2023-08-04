# Power Fx cookbook 👨‍🍳
Non-official collection of solutions and examples for this new programming language, from a **host** perspective. <i>Primarily written for my own benefit, but hopefully helpful to someone else as well.</i>

***

## Table of content

- [Engine](#engine)
  - [Compile](#compile)
  - [Evaluate](#evaluate)
  - [Symbols](#symbols)
- [Errors](#errors)
  - [New ErrorKind element](#new-errorkind-element)
- [Locale](#locale)
  - [Translate error messages](#translate-error-messages)
- [Logging](#logging)
  - [Anonymized expressions](#anonymized-expressions)
- [Serialize and Deserialize](#serialize-and-deserialize)
- [Testing](#testing)
  - [Entity](#entity)

## Engine
### Compile
  Compile time checks for syntax errors, unknown symbols, and other possible errors. Some good test cases for CheckResult can be found [here](https://github.com/microsoft/Power-Fx/blob/39585c758dbf02a00b509a50911f960ebdaef1f0/src/tests/Microsoft.PowerFx.Core.Tests/CheckResultTests.cs).
  The following is the simplest way to verify if an expression is valid:
  ```
  var expression = "1 + 2"; // Some Power Fx expression
  var engine = new RecalcEngine(config);
  var checkResult = engine.Check(expression);

  if (checkResult.IsSuccess)
  {
    // Expression is valid.
  }
  ```

  The `engine.Check()` method accepts some arguments:
  Coming soon.
  
### Evaluate
  Runtime evaluates a Power Fx expression and returns a result value (FormulaValue).
  The following is the simplest way to verify if an expression is valid:
  ```
  var expression = "1 + 2"; // Some Power Fx expression
  var engine = new RecalcEngine(config);
  var result = engine.Eval(expression); // Returns a FormulaValue derived object if the expression is valid.
  ```

  The `engine.Eval()` method accepts some arguments:
  Coming soon.

### Symbols
Symbols are variables (locals, globals), enums, options sets, and functions provided to the engine. They are mutable to support sessionful scenarios and can be chained together.
- **SymbolsTable**: Stores variable definitions (names and their types). This feeds functions, variables, enums, etc into the binder. Check [these test cases](https://github.com/microsoft/Power-Fx/blob/79029952e0700255b963e2bb6d615d44dac65412/src/tests/Microsoft.PowerFx.Interpreter.Tests/SymbolValueTests.cs) for reference.
- **SymbolsValue**: Runtime values corresponding to static values described in a SymbolTable. Check [these other test cases](https://github.com/microsoft/Power-Fx/blob/79029952e0700255b963e2bb6d615d44dac65412/src/tests/Microsoft.PowerFx.Core.Tests/SymbolTableTests.cs) for reference.

Some common tricks with Symbols:
- **Compose symbols**: It is possible to combine two or more symbol objects into a single one. This can be done by calling `ReadOnlySymbolTable.Compose` ([test case](https://github.com/microsoft/Power-Fx/blob/79029952e0700255b963e2bb6d615d44dac65412/src/tests/Microsoft.PowerFx.Core.Tests/SymbolTableTests.cs#L139)) or `ReadOnlySymbolValues.Compose` ([test case](https://github.com/microsoft/Power-Fx/blob/79029952e0700255b963e2bb6d615d44dac65412/src/tests/Microsoft.PowerFx.Interpreter.Tests/SymbolValueTests.cs#L322)) methods.
- **Add services**: Coming soon.
- **Add/Remove functions**: Coming soon.

## Errors
### New ErrorKind element
  - Update **...\src\libraries\Microsoft.PowerFx.Core\Types\Enums\EnumStoreBuilder.cs**, adding `NEW_ELEMENT_NAME: NEW_VALUE` at the end of the ErrorKindEnumString definition.
  - Add a `NEW_ELEMENT_NAME = NEW_VALUE` at the end of ...\src\libraries\Microsoft.PowerFx.Core\Public\ErrorKind.cs.
  - Update strings\PowerFxResources.en-US.resx to add this element (after the definition of ErrorKind_Internal_Name).
  
```
<data name="ErrorKind_NEW_ELEMENT_NAME_Name" xml:space="preserve">
    <value>NEW_ELEMENT_NAME</value>
    <comment>{Locked} Enum value</comment>
  </data>
```

And update the code to use this new enum value.

## Locale
### Translate error messages
Error message translation can be done by specifying the desired locale to the PowerFxConfig object:

```
var config_ptBR = new PowerFxConfig(CultureInfo.GetCultureInfo("pt-BR"));
var engine = new Engine(config_ptBR);
```

Then check if an expression is valid:
```
var check = engine.Check("7E1111111");
check.Errors.Dump(); // Error 0-9: O valor numérico é grande demais.
```

## Logging
### Anonymized expressions
If you need to log which expressions are being executed but want to keep any identifier/values private (for audit purposes for example), call CheckResult.ApplyGetLogging() instance method.
```
var engine = new Engine(new PowerFxConfig());
var check = engine.Check(myValue.ToExpression());

// Set(#$firstname$#, #$number$# + #$number$#)#$error$#Launch(#$string$#, #$firstname$#.#$righthandid$#, Parent.#$righthandid$#)
check.ApplyGetLogging();
```

Please note that `check.ApplyGetLogging()` can produce different results depending if binding has been applied or not.

## Serialize and Deserialize
Power Fx offers a simple way to serialize and deserialize FormulaValue values.
To serialize, call the "ToExpression()" method on any FormulaValue derived types (StringValue, NumberValue, etc) to get a serialized value version.

```
var myValue = FormulaValue.New(100);
myValue.ToExpression().Dump(); // "100"
```

To deserialize, call Eval to rehydrate the previously serialized value back to a FormulaValue.
```
var myValue = FormulaValue.New(100);
var engine = new Engine(new PowerFxConfig());
var check = engine.Check(myValue.ToExpression());
var result = check.GetEvaluator().Eval();

result.ToObject(); // 100
```

## Testing
The following is a list of references to test different cases:
### Entity
- [Mock an entity (table or record connected to database).](https://github.com/microsoft/Power-Fx/blob/5728ac2f3d7bb27b2fdf9e194454b7db5c666b69/src/tests/Microsoft.PowerFx.Interpreter.Tests/DatabaseSimulationTests.cs)
