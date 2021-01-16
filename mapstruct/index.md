# Mapstruct

## Object Mapping的技术分类

1. 运行期:[Dozer](http://dozer.sourceforge.net/),[Orika](http://orika-mapper.github.io/orika-docs/),[ModelMapper](http://modelmapper.org/)
2. 编译期:MapStruct,[JMapper](http://jmapper.sourceforge.net/)

* [Performance of Java Mapping](https://www.baeldung.com/java-performance-mapping-frameworks)

## Introduction

MapStruct is a Java annotation processor for the generation of type-safe bean mapping classes

>Advantages
* Fast execution
* Compile-time type safety
* Clear error-reports at build time

## Configuration options for set up

1. mapstruct.defaultComponentModel: 
   * <u>default</u>: Mapper#getMapper(Class)
   * cdi: @Inject
   * spring: @Autowired
   * [jsr330](https://yq.aliyun.com/articles/626235?utm_content=m_1000013558): @Inject
* If a component model is given for a specific mapper via @Mapper#componentModel(), the value from the annotation takes precedence.
2. mapstruct.unmappedTargetPolicy:
   * ERROR
   * <u>WARN</u>
   * IGNORE

## Using MapStruct