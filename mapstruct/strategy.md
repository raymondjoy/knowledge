# Strategies

## CollectionMappingStrategy

Strategy for propagating the value of collection-typed properties from source to target.
* <u>ACCESSOR_ONLY</u>
* SETTER_PREFERRED
* ADDER_PREFERRED
* TARGET_IMMUTABLE

## MappingInheritanceStrategy

Strategy for inheriting configurations given for methods of prototype mapping methods (declared on mapper config classes) to actual mapping methods declared on mappers referring to such config class via Mapper.config().

* EXPLICIT
* AUTO_INHERIT_FROM_CONFIG
* AUTO_INHERIT_REVERSE_FROM_CONFIG
* AUTO_INHERIT_ALL_FROM_CONFIG

## NullValueCheckStrategy

Strategy for dealing with null source values.

* ON_IMPLICIT_CONVERSION
* ALWAYS

## NullValueMappingStrategy

Strategy for dealing with null values passed to mapping methods.

* RETURN_NULL
* RETURN_DEFAULT

## NullValuePropertyMappingStrategy

Strategy for dealing with null or not present properties in the source bean.

* SET_TO_NULL
* SET_TO_DEFAULT
```mermaind
graph LR

 List((List))--> ArrayList((ArrayList))
 
```
* IGNORE