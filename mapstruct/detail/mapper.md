# Mapper

* uses
> Class<?>[] uses() default { };
```java
@Mapper(uses = DepartmentMapper.class)
public interface EmployeeMapper {
    // logic
}
//generated
public class EmployeeMapperImpl implements EmployeeMapper {

    private final DepartmentMapper departmentMapper = Mappers.getMapper( DepartmentMapper.class );
    // some method
}
```
* imports
> Class<?>[] imports() default { };
* unmappedSourcePolicy
> ReportingPolicy unmappedSourcePolicy() default ReportingPolicy.IGNORE;
* unmappedTargetPolicy
> ReportingPolicy unmappedTargetPolicy() default ReportingPolicy.WARN;
* typeConversionPolicy
> ReportingPolicy typeConversionPolicy() default ReportingPolicy.IGNORE;
* componentModel
> String componentModel() default "default";
> 
> * default: the mapper uses no component model, instances are typically retrieved via Mappers.getMapper(Class)
> * cdi: the generated mapper is an application-scoped CDI bean and can be retrieved via @Inject
> * spring: the generated mapper is a Spring bean and can be retrieved via @Autowired
> * jsr330: the generated mapper is annotated with @javax.inject.Named and @Singleton, and can be retrieved via @Inject
> 
* implementationName
> String implementationName() default "<CLASS_NAME>Impl";
* implementationPackage
> String implementationPackage() default "<PACKAGE_NAME>";
* config
> Class<?> config() default void.class;
* collectionMappingStrategy
> CollectionMappingStrategy collectionMappingStrategy() default CollectionMappingStrategy.ACCESSOR_ONLY;
* nullValueMappingStrategy
> NullValueMappingStrategy nullValueMappingStrategy() default NullValueMappingStrategy.RETURN_NULL;
* nullValuePropertyMappingStrategy
> NullValuePropertyMappingStrategy nullValuePropertyMappingStrategy() default
        NullValuePropertyMappingStrategy.SET_TO_NULL;
* mappingInheritanceStrategy
> MappingInheritanceStrategy mappingInheritanceStrategy() default MappingInheritanceStrategy.EXPLICIT;
* nullValueCheckStrategy
> NullValueCheckStrategy nullValueCheckStrategy() default ON_IMPLICIT_CONVERSION;
* injectionStrategy
> InjectionStrategy injectionStrategy() default InjectionStrategy.FIELD;
* disableSubMappingMethodsGeneration
> boolean disableSubMappingMethodsGeneration() default false;
* builder
> Builder builder() default @Builder;
* mappingControl
> Class<? extends Annotation> mappingControl() default MappingControl.class;
* unexpectedValueMappingException
> Class<? extends Exception> unexpectedValueMappingException() default IllegalArgumentException.class;