# Strategies
```java
public class Company {
    private Long id;
    private String name;
    private List<Employee> employees;
    private Boolean isDeleted;
    private Map<String, Object> properties;
}

public class Employee {
    private Long id;
    private String name;
    private Department department;
}

public class Department {
    private Long id;
    private String name;
}
//target
public class StockCompany {
    private Long id;
    private String name;
    private List<Employee> employees;
    private Boolean isDeleted;
    private Map<String, Object> properties;
}

public class TechDepartment {
    private Long id;
    private String name;
}

public class Junior {
    private Long id;
    private String name;
    private TechDepartment department;
}
```
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

* __RETURN_NULL__
```java
@Mapper(nullValueCheckStrategy = NullValueCheckStrategy.ALWAYS, nullValueMappingStrategy = NullValueMappingStrategy.RETURN_NULL)
public interface DepartmentMapper {
    DepartmentMapper DEPARTMENT_MAPPER = Mappers.getMapper(DepartmentMapper.class);

    TechDepartment toTechDepartment(Department department);
}
//generated
public class DepartmentMapperImpl implements DepartmentMapper {

    @Override
    public TechDepartment toTechDepartment(Department department) {
        if ( department == null ) {
            return null;
        }

        TechDepartment techDepartment = new TechDepartment();

        if ( department.getId() != null ) {
            techDepartment.setId( department.getId() );
        }
        if ( department.getName() != null ) {
            techDepartment.setName( department.getName() );
        }

        return techDepartment;
    }
}
```
* __RETURN_DEFAULT__
```java
@Mapper(nullValueCheckStrategy = NullValueCheckStrategy.ALWAYS, nullValueMappingStrategy = NullValueMappingStrategy.RETURN_DEFAULT)
public interface DepartmentMapper {
    DepartmentMapper DEPARTMENT_MAPPER = Mappers.getMapper(DepartmentMapper.class);

    TechDepartment toTechDepartment(Department department);
}
//generated
public class DepartmentMapperImpl implements DepartmentMapper {

    @Override
    public TechDepartment toTechDepartment(Department department) {
        
        TechDepartment techDepartment = new TechDepartment();

        if ( department != null ) {
            if ( department.getId() != null ) {
                techDepartment.setId( department.getId() );
            }
            if ( department.getName() != null ) {
                techDepartment.setName( department.getName() );
            }
        }

        return techDepartment;
    }
}
```

## NullValuePropertyMappingStrategy

Strategy for dealing with null or not present properties in the source bean.

* __SET_TO_NULL__
```java
@Mapper(nullValueCheckStrategy = NullValueCheckStrategy.ALWAYS, nullValuePropertyMappingStrategy =  NullValuePropertyMappingStrategy.SET_TO_NULL)
public interface CompanyMapper {
    CompanyMapper COMPANY_MAPPER = Mappers.getMapper(CompanyMapper.class);
    void updateStockCompany(Company company, @MappingTarget StockCompany stockCompany);
}
//generated
public class CompanyMapperImpl implements CompanyMapper {

    @Override
    public void updateStockCompany(Company company, StockCompany stockCompany) {
        if ( company == null ) {
            return;
        }

        if ( company.getId() != null ) {
            stockCompany.setId( company.getId() );
        }
        else {
            stockCompany.setId( null );
        }
        if ( company.getName() != null ) {
            stockCompany.setName( company.getName() );
        }
        else {
            stockCompany.setName( null );
        }
        if ( stockCompany.getEmployees() != null ) {
            List<Employee> list = company.getEmployees();
            if ( list != null ) {
                stockCompany.getEmployees().clear();
                stockCompany.getEmployees().addAll( list );
            }
        }
        else {
            List<Employee> list = company.getEmployees();
            if ( list != null ) {
                stockCompany.setEmployees( new ArrayList<Employee>( list ) );
            }
        }
        if ( company.getIsDeleted() != null ) {
            stockCompany.setIsDeleted( company.getIsDeleted() );
        }
        else {
            stockCompany.setIsDeleted( null );
        }
        if ( stockCompany.getProperties() != null ) {
            Map<String, Object> map = company.getProperties();
            if ( map != null ) {
                stockCompany.getProperties().clear();
                stockCompany.getProperties().putAll( map );
            }
        }
        else {
            Map<String, Object> map = company.getProperties();
            if ( map != null ) {
                stockCompany.setProperties( new HashMap<String, Object>( map ) );
            }
        }
    }
}
```
* __SET_TO_DEFAULT__
```java
@Mapper(nullValueCheckStrategy = NullValueCheckStrategy.ALWAYS, nullValuePropertyMappingStrategy =  NullValuePropertyMappingStrategy.SET_TO_DEFAULT)
public interface CompanyMapper {
    CompanyMapper COMPANY_MAPPER = Mappers.getMapper(CompanyMapper.class);
    
    void updateStockCompany(Company company, @MappingTarget StockCompany stockCompany);
}

@Mapper(nullValueCheckStrategy = NullValueCheckStrategy.ALWAYS, nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.SET_TO_DEFAULT)
public interface EmployeeMapper {

    void updateJunior(Employee employee,  @MappingTarget Junior junior);
}

//generated
public class CompanyMapperImpl implements CompanyMapper {
    @Override
    public void updateStockCompany(Company company, StockCompany stockCompany) {
        if ( company == null ) {
            return;
        }

        if ( company.getId() != null ) {
            stockCompany.setId( company.getId() );
        }
        else {
            //for primitive / boxed types a representation of 0 or false
            stockCompany.setId( 0L );
        }
        if ( company.getName() != null ) {
            stockCompany.setName( company.getName() );
        }
        else {
            //For String ""
            stockCompany.setName( "" );
        }
        if ( stockCompany.getEmployees() != null ) {
            List<Employee> list = company.getEmployees();
            if ( list != null ) {
                stockCompany.getEmployees().clear();
                stockCompany.getEmployees().addAll( list );
            }
        }
        else {
            //For List MapStruct generates an ArrayList
            List<Employee> list = company.getEmployees();
            if ( list != null ) {
                stockCompany.setEmployees( new ArrayList<Employee>( list ) );
            }
        }
        if ( company.getIsDeleted() != null ) {
            stockCompany.setIsDeleted( company.getIsDeleted() );
        }
        else {
            //for primitive / boxed types a representation of 0 or false
            stockCompany.setIsDeleted( false );
        }
        if ( stockCompany.getProperties() != null ) {
            Map<String, Object> map = company.getProperties();
            if ( map != null ) {
                stockCompany.getProperties().clear();
                stockCompany.getProperties().putAll( map );
            }
        }
        else {
            //For Map a HashMap
            Map<String, Object> map = company.getProperties();
            if ( map != null ) {
                stockCompany.setProperties( new HashMap<String, Object>( map ) );
            }
        }
    }
}

public class EmployeeMapperImpl implements EmployeeMapper {

    @Override
    public void updateJunior(Employee employee, Junior junior) {
        if ( employee == null ) {
            return;
        }

        if ( employee.getId() != null ) {
            junior.setId( employee.getId() );
        }
        else {
            junior.setId( 0L );
        }
        if ( employee.getName() != null ) {
            junior.setName( employee.getName() );
        }
        else {
            junior.setName( "" );
        }
        if ( employee.getDepartment() != null ) {
            if ( junior.getDepartment() == null ) {
                junior.setDepartment( new TechDepartment() );
            }
            departmentToTechDepartment( employee.getDepartment(), junior.getDepartment() );
        }
        else {
            junior.setDepartment( new TechDepartment() );
        }
    }

    protected void departmentToTechDepartment(Department department, TechDepartment mappingTarget) {
        if ( department == null ) {
            return;
        }

        if ( department.getId() != null ) {
            mappingTarget.setId( department.getId() );
        }
        else {
            mappingTarget.setId( 0L );
        }
        if ( department.getName() != null ) {
            mappingTarget.setName( department.getName() );
        }
        else {
            mappingTarget.setName( "" );
        }
    }
}

```
* __IGNORE__