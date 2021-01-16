# Annotations

## Mapper

Marks an __interface__ or __abstract class__ as a mapper and activates the generation of a implementation of that type via MapStruct.

```java
@Mapper
public interface CarMapper {
    CarDto toCarDto(Car source);
}
```

## Mapping

Configures the mapping of one bean attribute or enum constant.

```java
@Mapper
public interface HumanMapper {
    @Mapping(source="companyName", target="company")
    HumanDto toHumanDto(Human human)
}
```
## ~~Mappings~~

Configures the mappings of several bean attributes.

```java
// before Java 8
@Mapper
public interface MyMapper {
    @Mappings({
        @Mapping(source = "first", target = "firstProperty"),
        @Mapping(source = "second", target = "secondProperty")
    })
    HumanDto toHumanDto(Human human);
}

// Java 8 and later
@Mapper
public interface MyMapper {
    @Mapping(source = "first", target = "firstProperty"),
    @Mapping(source = "second", target = "secondProperty")
    HumanDto toHumanDto(Human human);
}
```
## MappingTarget

Declares a parameter of a mapping method to be the target of the mapping.

__Example 1__: Update exist bean without return value
```java
@Mapper
public interface HumanMapper {
    void updateHuman(HumanDto humanDto, @MappingTarget Human human);
}
```
__Example 2__: Update exist bean and return it
```java
@Mapper
public interface HumanMapper {
    Human updateHuman(HumanDto humanDto, @MappingTarget Human human);
}
```

## Named

Marks mapping methods with the given qualifier name. Can be used to qualify a single method or all methods of a given type by specifying this annotation on the type level.

```java
@Named("TitleTranslator")
public class Titles {

    @Named("EnglishToGerman")
    public String translateTitleEG(String title) {
        // some mapping logic
    }

    @Named("GermanToEnglish")
    public String translateTitleGE(String title) {
        // some mapping logic
    }
}

@Mapper( uses = Titles.class )
public interface MovieMapper {

    @Mapping( target = "title", qualifiedByName = { "TitleTranslator", "EnglishToGerman" } )
    GermanRelease toGerman( OriginalRelease movies );

}
//generated
public class MovieMapperImpl implements MovieMapper {
    private final Titles titles = new Titles();

    @Override
    public GermanRelease toGerman(OriginalRelease movies) {
        if ( movies == null ) {
            return null;
        }

        GermanRelease germanRelease = new GermanRelease();

        germanRelease.setTitle( titles.translateTitleEG( movies.getTitle() ) );

        return germanRelease;
    }
}

```
## Qualifier

Declares an annotation type to be a qualifier. Qualifier annotations allow unambiguously identify a suitable mapping method in case several methods qualify to map a bean property, iterable element etc.

```java
// create qualifiers
@Qualifier
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.CLASS)
public @interface TitleTranslator {}

@Qualifier
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)
public @interface EnglishToGerman {}

@Qualifier
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)
public @interface GermanToEnglish {}

// we can create class with map methods
@TitleTranslator
public class Titles {
    @EnglishToGerman
    public String translateTitleEnglishToGerman(String title) {
        // some mapping logic
    }
    @GermanToEnglish
    public String translateTitleGermanToEnglish(String title) {
        // some mapping logic
    }
}

// usage
@Mapper( uses = Titles.class )
public interface MovieMapper {
    @Mapping( target = "title", qualifiedBy = { TitleTranslator.class, EnglishToGerman.class } )
    GermanRelease toGerman( OriginalRelease movies );
}

// generates
public class MovieMapperImpl implements MovieMapper {
    private final Titles titles = new Titles();
    @Override
    public GermanRelease toGerman(OriginalRelease movies) {
        if ( movies == null ) {
            return null;
        }
        GermanRelease germanRelease = new GermanRelease();
        germanRelease.setTitle( titles.translateTitleEnglishToGerman( movies.getTitle() ) );
        return germanRelease;
    }
}
```

## BeforeMapping

Marks a method to be invoked at the beginning of a generated mapping method. 

```java
@BeforeMapping
public void calledWithoutArgs() {
    // ...
}

@BeforeMapping
public void calledWithSourceAndTargetType(SourceEntity anySource, @TargetType Class<?> targetType) {
    // ...
}

@BeforeMapping
public void calledWithSourceAndTarget(Object anySource, @MappingTarget TargetDto target) {
    // ...
}

public abstract TargetDto toTargetDto(SourceEntity source);

// generates:

public TargetDto toTargetDto(SourceEntity source) {
    calledWithoutArgs();
    calledWithSourceAndTargetType( source, TargetDto.class );

    if ( source == null ) {
        return null;
    }

    TargetDto targetDto = new TargetDto();

    calledWithSourceAndTarget( source, targetDto );

    // actual mapping code

    return targetDto;
}
```

## AfterMapping

Marks a method to be invoked at the end of a generated mapping method, right before the last return statement of the mapping method.

```java
@AfterMapping
public void calledWithoutArgs() {
// ...
}

@AfterMapping
public void calledWithSourceAndTargetType(SourceEntity anySource, @TargetType Class<?> targetType) {
// ...
}

@AfterMapping
public void calledWithSourceAndTarget(Object anySource, @MappingTarget TargetDto target) {
// ...
}

public abstract TargetDto toTargetDto(SourceEntity source);

// generates:

public TargetDto toTargetDto(SourceEntity source) {
    if ( source == null ) {
        return null;
    }

    TargetDto targetDto = new TargetDto();

    // actual mapping code

    calledWithoutArgs();
    calledWithSourceAndTargetType( source, TargetDto.class );
    calledWithSourceAndTarget( source, targetDto );

    return targetDto;
}
```

## BeanMapping

Configures the mapping between two bean types.

```java
public class FruitFactory {
    public Apple createApple() {
        return new Apple();
    }
    public Orange createOrange() {
        return new Orange();
    }
}
@Mapper(uses = FruitFactory.class)
public interface FruitMapper {
    @BeanMapping(resultType = Apple.class)
    Fruit toFruit(FruitDto fruitDto);
}

// generates
public class FruitMapperImpl implements FruitMapper {
    @Override
    public Fruit toFruit(FruitDto fruitDto) {
        Apple fruit = fruitFactory.createApple();
        // ...
    }
}
```

## Context

Marks a parameter of a method to be treated as __mapping context__.

__Example 1__: 
```java
// multiple @Context parameters can be added
public abstract CarDto toCar(Car car, @Context VehicleRegistration context, @Context Locale localeToUse);

protected OwnerManualDto translateOwnerManual(OwnerManual ownerManual, @Context Locale locale) {
    // manually implemented logic to translate the OwnerManual with the given Locale
}

@BeforeMapping
protected void registerVehicle(Vehicle mappedVehicle, @Context VehicleRegistration context) {
    context.register( mappedVehicle );
}

@BeforeMapping
protected void notCalled(Vehicle mappedVehicle, @Context DifferentMappingContextType context) {
    // not called, because no context parameter of type DifferentMappingContextType is available
    // within toCar(Car, VehicleRegistration, Locale)
}

// generates:

public CarDto toCar(Car car, VehicleRegistration context, Locale localeToUse) {
    registerVehicle( car, context );

    if ( car == null ) {
        return null;
    }

    CarDto carDto = new CarDto();

    carDto.setOwnerManual( translateOwnerManual( car.getOwnerManual(), localeToUse );
    // more generated mapping code

    return carDto;
}
```   
__Example 2__:
```java
    // type of the context parameter
public class CyclicGraphContext {
    private Map<Object, Object> knownInstances = new IdentityHashMap<>();

    @BeforeMapping
    public <T extends NodeDto> T getMappedInstance(Object source, @TargetType Class<T> targetType) {
        return (T) knownInstances.get( source );
    }

    @BeforeMapping
    public void storeMappedInstance(Object source, @MappingTarget NodeDto target) {
        knownInstances.put( source, target );
    }
}

@Mapper
public interface GraphMapper {
    NodeDto toNodeDto(Node node, @Context CyclicGraphContext cycleContext);
}


// generates:

public NodeDto toNodeDto(Node node, CyclicGraphContext cycleContext) {
    NodeDto target = cycleContext.getMappedInstance( node, NodeDto.class );
    if ( target != null ) {
        return target;
    }

    if ( node == null ) {
        return null;
    }

    NodeDto nodeDto = new NodeDto();

    cycleContext.storeMappedInstance( node, nodeDto );

    nodeDto.setParent( toNodeDto( node.getParent(), cycleContext ) );
    List<NodeDto> list = nodeListToNodeDtoList( node.getChildren(), cycleContext );
    if ( list != null ) {
        nodeDto.setChildren( list );
    }

    // more mapping code

    return nodeDto;
}
```
__Example 3__:
```java
// type of the context parameter
public class ContextObjectFactory {
    @PersistenceContext(unitName = "my-unit")
    private EntityManager em;

    @ObjectFactory
    public Valve create( String id ) {
        Query query = em.createNamedQuery("Valve.findById");
        query.setParameter("id", id);
        Valve result = query.getSingleResult();
        if ( result != null ) {
            result = new Valve( id );
        }
        return result;
    }
}

@Mapper
public interface ContextWithObjectFactoryMapper {
    Valve map(ValveDto dto, @Context ContextObjectFactory factory, String id);
}


// generates:
public class ContextWithObjectFactoryMapperImpl implements ContextWithObjectFactoryMapper {

    @Override
    public Valve map(ValveDto dto, ContextObjectFactory factory, String id) {
        if ( dto == null ) {
            return null;
        }

        Valve valve = factory.create( id );

        valve.setOneWay( dto.isOneWay() );

        return valve;
    }
}
```

## IterableMapping
Configures the mapping between two iterable like types, e.g. List<String> and List<Date>.

```java
@Mapper
public interface FloatToStringMapper {
    @IterableMapping( numberFormat = "##.00" )
    List<String> sourceToTarget(List<Float> source);
}

// generates
public class FloatToStringMapperImpl implements FloatToStringMapper {
    @Override
    public List<String> sourceToTarget(List<Float> source) {
        List<String> list = new ArrayList<String>( source.size() );
        for ( Float float1 : source ) {
            list.add( new DecimalFormat( "##.00" ).format( float1 ) );
        }
    // ...
    }
}
```

## MapMapping

Configures the mapping between two map types, e.g. Map<String, String> and Map<Long, Date>.

```java
@Mapper
public interface SimpleMapper {
    @MapMapping(valueDateFormat = "dd.MM.yyyy")
    Map<String, String> longDateMapToStringStringMap(Map<Long, Date> source);
}

// generates
public class SimpleMapperImpl implements SimpleMapper {
    @Override
    public Map<String, String> longDateMapToStringStringMap(Map<Long, Date> source) } {
        Map<String, String> map = new HashMap<String, String>(); }
        for ( java.util.Map.Entry<Long, Date> entry : source.entrySet() ) } {
            String key = new DecimalFormat( "" ).format( entry.getKey() );
            String value = new SimpleDateFormat( "dd.MM.yyyy" ).format( entry.getValue() );
            map.put( key, value );
        }
        // ...
    }
}
```
## ObjectFactory

This annotation marks a method as a __factory method__ to create beans.

```java
@ApplicationScoped // CDI component model
public class ReferenceMapper {

    @PersistenceContext
    private EntityManager em;

    @ObjectFactory
    public <T extends AbstractEntity> T resolve(AbstractDto sourceDto, @TargetType Class<T> type) {
        T entity = em.find( type, sourceDto.getId() );
        return entity != null ? entity : type.newInstance();
    }
}
```

## ~~ValueMappings~~

Constructs a set of value (constant) mappings.

```java
// before Java 8
@Mapper
public interface GenderMapper {
    @ValueMappings({
        @ValueMapping(source = "MALE", target = "M"),
        @ValueMapping(source = "FEMALE", target = "F")
    })
    GenderDto mapToDto(Gender gender);
}

//Java 8 and later
@Mapper
public interface GenderMapper {
    @ValueMapping(source = "MALE", target = "M"),
    @ValueMapping(source = "FEMALE", target = "F")
    GenderDto mapToDto(Gender gender);
}
```

## ValueMapping

Configures the mapping of source constant value to target constant value.

__Example 1__:
```java
public enum OrderType { RETAIL, B2B, EXTRA, STANDARD, NORMAL }

public enum ExternalOrderType { RETAIL, B2B, SPECIAL, DEFAULT }

@ValueMapping(source = "EXTRA", target = "SPECIAL"),
@ValueMapping(source = "STANDARD", target = "DEFAULT"),
@ValueMapping(source = "NORMAL", target = "DEFAULT")
ExternalOrderType orderTypeToExternalOrderType(OrderType orderType);
```
|OrderType            |ExternalOrderType           |
|---------------------|----------------------------|
|null                 |null                        |
| OrderType.EXTRA     | ExternalOrderType.SPECIAL  |
| OrderType.STANDARD  | ExternalOrderType.DEFAULT  |
| OrderType.NORMAL    | ExternalOrderType.DEFAULT  |
| OrderType.RETAIL    | ExternalOrderType.RETAIL   |
| OrderType.B2B       | ExternalOrderType.B2B      |

__Example 2__:
```java
@ValueMapping( source = MappingConstants.NULL, target = "DEFAULT" ),
@ValueMapping( source = "STANDARD", target = MappingConstants.NULL ),
@ValueMapping( source = MappingConstants.ANY_REMAINING, target = "SPECIAL" )
ExternalOrderType orderTypeToExternalOrderType(OrderType orderType);
```
| OrderType           | ExternalOrderType          |
|---------------------|----------------------------|
| null                | ExternalOrderType.DEFAULT  |
| OrderType.STANDARD  | null                       |
| OrderType.RETAIL    | ExternalOrderType.RETAIL   |
| OrderType.B2B       | ExternalOrderType.B2B      |
| OrderType.NORMAL    | ExternalOrderType.SPECIAL  |
| OrderType.EXTRA     | ExternalOrderType.SPECIAL  |

## EnumMapping

Configured the mapping between two value types.

```java
public enum CheeseType {
    BRIE,
    ROQUEFORT
}

public enum CheeseTypeSuffixed {
    BRIE_TYPE,
    ROQUEFORT_TYPE
}

@Mapper
public interface CheeseMapper {

    @EnumMapping(nameTransformationStrategy = "suffix", configuration = "_TYPE")
    CheeseTypeSuffixed map(Cheese cheese);

    @InheritInverseConfiguration
    Cheese map(CheeseTypeSuffixed cheese);

}
```

## InheritInverseConfiguration

Advises the code generator to apply all the Mappings from an inverse mapping method to the annotated method as well. 

```java
public class Customer {

    private Long id;
    private String name;

    //getters and setter omitted for brevity
}

public class CustomerDto {

    public Long id;
    public String customerName;
}

@Mapper
public interface CustomerMapper {

    CustomerMapper INSTANCE = Mappers.getMapper( CustomerMapper.class );

    @Mapping(source = "customerName", target = "name")
    Customer toCustomer(CustomerDto customerDto);

    @InheritInverseConfiguration
    CustomerDto fromCustomer(Customer customer);
}
```
