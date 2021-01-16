# Annotations

## Mapper

Marks an interface or abstract class as a mapper and activates the generation of a implementation of that type via MapStruct.

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

```java
//Update exist bean without return value
@Mapper
public interface HumanMapper {
    void updateHuman(HumanDto humanDto, @MappingTarget Human human);
}
//Update exist bean and return it
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
```
