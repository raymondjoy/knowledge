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
* imports;
* componentModel
  * <u>default</u>: the mapper uses no component model, instances are typically retrieved via Mappers.getMapper(Class)
  * cdi: the generated mapper is an application-scoped CDI bean and can be retrieved via @Inject
  * spring: the generated mapper is a Spring bean and can be retrieved via @Autowired
  * jsr330: the generated mapper is annotated with @javax.inject.Named and @Singleton, and can be retrieved via @Inject
* config