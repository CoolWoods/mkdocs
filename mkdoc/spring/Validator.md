# Validator

Spring 提供了一个可用于验证对象的 Validator 接口。Validator 接口使用 Errors 对象工作，以便在验证时，验证器可以向 Errors 对象报告验证失败。

## Validator 接口实现

以下是一个数据对象

```java
public class Person {

    private String name;
    private int age;

    // the usual getters and setters...
}
```

实现`org.springframework.validation.Validator`接口的两个方法能为 Person 类提供验证行为：

- supports(Class)：当前 Validator 支持的 Class 类型。
- validator(Object, org.springframework.validation.Errors)：校验指定的对象，或有校验错误，则在 Errors 对象中注册。

Validator 接口实现：

```java
public class PersonValidator implements Validator {

    /**
     * This Validator validates *just* Person instances
     */
    public boolean supports(Class clazz) {
        return Person.class.equals(clazz);
    }

    public void validate(Object obj, Errors e) {
        ValidationUtils.rejectIfEmpty(e, "name", "name.empty");
        Person p = (Person) obj;
        if (p.getAge() < 0) {
            e.rejectValue("age", "negativevalue");
        } else if (p.getAge() > 110) {
            e.rejectValue("age", "too.darn.old");
        }
    }
}
```

ValidationUtils 类的静态方法 rejectIfEmpty()方法用于当 name 属性为 null 或空字符串时拒绝该属性，更多功能可以查看 javadocs 获取。

## Validator 嵌套

一个单独的 Validator 类可以验证对象中的每个嵌套对象，但最好在每个嵌套对象类自身的 Validator 实现中封装验证逻辑。

Customer 对象由两个 String 类型的属性（firstname and lastname）以及一个复杂对象 Address 组成。

Customer 对象

```java
public class Customer {

    private String firstName;
    private String lastName;
    private Address address;

    // the usual getters and setters...
}
```

Address 对象

```java
clas Address{
    private String country;
    private String province;
    private String city;
}
```

Address 对象可以独立于 Customer 对象使用，因此实现了一个独立的 AddressValidator。如果希望 CustomerValidator 复用 AddressVaidator 类的逻辑，可以使用依赖注入或者实例化 CustomerValidator 中的 AddressValidator 并使用它。

```java
public class CustomerValidator implements Validator {

    private final Validator addressValidator;

    public CustomerValidator(Validator addressValidator) {
        if (addressValidator == null) {
            throw new IllegalArgumentException("The supplied [Validator] is " +
                "required and must not be null.");
        }
        if (!addressValidator.supports(Address.class)) {
            throw new IllegalArgumentException("The supplied [Validator] must " +
                "support the validation of [Address] instances.");
        }
        this.addressValidator = addressValidator;
    }

    /**
     * This Validator validates Customer instances, and any subclasses of Customer too
     */
    public boolean supports(Class clazz) {
        return Customer.class.isAssignableFrom(clazz);
    }

    public void validate(Object target, Errors errors) {
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "firstName", "field.required");
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "surname", "field.required");
        Customer customer = (Customer) target;
        try {
            errors.pushNestedPath("address");
            ValidationUtils.invokeValidator(this.addressValidator, customer.getAddress(), errors);
        } finally {
            errors.popNestedPath();
        }
    }
}
```

## Validator 使用

在使用之前，首先向 IOC 窗口中注入 Validator 对象：

```java
@Configuration
public class ValidatorConfig {
    @Bean
    public Validator getPersonValidator(){
        return new PersonValidator();
    }
}
```

如果不希望向 IOC 窗口注入对象，也可以直接创建 Validator 对象来使用。

### 编程式

Validator 对象可以在任何地方使用，编程式使用方法一般是通过工具类 ValidationUtils 操作。

```java
@Slf4j
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {RootConfig.class})
public class TestSpringBean {

    @Autowired
    private PersonValidator personValidator;

    @Test
    public void test1() {
        Person person = new Person();
        person.setAge(-1);
        person.setStart(new Date());

        Errors errors = new DirectFieldBindingResult(person, "person");
        ValidationUtils.invokeValidator(personValidator, person, errors);
        System.out.println(errors);
    }

}
```

### 在 SpringMVC 中使用

Validator 对象可以在任何地方使用，但是应用中使用最多的地方应该还是对接口参数进行校验。

@Validator 注解的作用是将参数标记为需要校验。请求参数在加上@Validator 注解之后，可以添加自定义的 validator 到 binder 进行校验，也可以通过在属性上加注解的方式对参数验证。

有一个添加了@Validator 注解的 savePerson 接口，如下：

```java
    @PostMapping("/baseCost")
    public String savePerson(@RequestBody @Validator Person person){
        // do something
        return "success";
    }
```

#### 使用自定义 Validator

添加 Validator 到 WebDataBinder：

```java
    private PersonValidator personValidator;
    /**
     * person参数校验器
     * @param binder
     */
    @InitBinder(value = "person")
    protected void initBinder(WebDataBinder binder) {
        binder.addValidators(personValidator);
    }
```

- 需要注意的是 initBinder 在当前控制器生效，如果没有指定 value 则会对控制器下的参数都进行校验。如果控制器下有多个方法，且请求参数不一致时，必须明确指定 initBinder 的 value，否则在请求 Validator 不支持的参数所在接口时，会报错。

#### Spring 支持的注解

注解使用示例：
Spring Validator 支持 javax.validation.constraints以及org.hibernate.validator.constraints包下的注解。
```java
public class Person {
    @NotEmpty(message = "name can't null")
    private String name;
    @Min(value = "0", message = "age min 0")
    @Max(value = "150", message = "age max 150")
    private int age;

    // the usual getters and setters...
}
```

Spring Validator 支持 javax.validation.constraints 包下的注解：

- NotNull
- Max
- Min
- DecimalMin
- DecimalMax
- Size
- Digits
- Pattern

详情可以查询 javadoc 文档。

Spring 还提供了对 org.hibernate.validator.constraints 包下注解的支持：

- NotEmpty
- NotBlank
- Email
- URL

详情可以查询 javadoc 文档。

## SmartValidator
未完待续