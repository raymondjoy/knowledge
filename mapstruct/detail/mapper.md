# Mapper

* uses
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
```java
public class Junior {
    private Long id;
    private String name;
    private TechDepartment department;
    private LocalDateTime registrationDate;
}

@Mapper(imports={LocalDateTime.class})
public interface EmployeeMapper {
    @Mapping(target = "registrationDate", expression = "java(LocalDateTime.now())")
    Junior toJunior(Employee employee);
}

```
* componentModel
  * <u>default</u>: the mapper uses no component model, instances are typically retrieved via __Mappers.getMapper(Class)__
  * cdi: the generated mapper is an application-scoped CDI bean and can be retrieved via __@Inject__
  * spring: the generated mapper is a Spring bean and can be retrieved via __@Autowired__
  * jsr330: the generated mapper is annotated with @javax.inject.Named and @Singleton, and can be retrieved via __@Inject__
* config