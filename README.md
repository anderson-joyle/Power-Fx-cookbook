# Power Fx cookbook 👨‍🍳
Non-official collection of solutions and examples for this new programming language. <i>Primarily written for my own benefit, but hopefully helpful to someone else as well.</i>

***

## Table of content

- [What](#what)
- [How](#how)
  - [Errors](#errors)
    - [New ErrorKind element](#new-errorkind-element)

## How
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
