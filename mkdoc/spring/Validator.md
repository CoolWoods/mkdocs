# Validator

Spring 提供了一个可用于验证对象的 Validator 接口。Validator 接口使用 Errors 对象工作，以便在验证时，验证器可以向 Errors
对象报告验证失败。

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

ValidationUtils 类的静态方法 rejectIfEmpty()方法用于当 name 属性为 null 或空字符串时拒绝该属性，更多功能可以查看 javadocs
获取。

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

Address 对象可以独立于 Customer 对象使用，因此实现了一个独立的 AddressValidator。如果希望 CustomerValidator 复用
AddressVaidator 类的逻辑，可以使用依赖注入或者实例化 CustomerValidator 中的 AddressValidator 并使用它。

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

### 在 SpringMvc 中使用

Validator 对象可以在任何地方使用，但是应用中使用最多的地方应该还是对接口参数进行校验。

在SpringMvc中，RequestResponseBodyMethodProcessor用于解析@RequestBody标注的参数以及处理@ResponseBody标注方法的返回值。

```java
public class RequestResponseBodyMethodProcessor extends AbstractMessageConverterMethodProcessor {
    @Override
    public Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
                                  NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
 
        parameter = parameter.nestedIfOptional();
        //将请求数据封装到DTO对象中
        Object arg = readWithMessageConverters(webRequest, parameter, parameter.getNestedGenericParameterType());
        String name = Conventions.getVariableNameForParameter(parameter);
 
        if (binderFactory != null) {
            WebDataBinder binder = binderFactory.createBinder(webRequest, arg, name);
            if (arg != null) {
                // 执行数据校验
                validateIfApplicable(binder, parameter);
                if (binder.getBindingResult().hasErrors() && isBindExceptionRequired(binder, parameter)) {
                    throw new MethodArgumentNotValidException(parameter, binder.getBindingResult());
                }
            }
            if (mavContainer != null) {
                mavContainer.addAttribute(BindingResult.MODEL_KEY_PREFIX + name, binder.getBindingResult());
            }
        }
        return adaptArgumentIfNecessary(arg, parameter);
    }
}
```

resolveArgument()调用了validateIfApplicable()进行参数校验。在validateIfApplicable()方法中，
首先会判断参数是否含有@Validated注解以及Valid开头的注解，最后调用WebDataBinder的validate()方法进行校验。

```java
protected void validateIfApplicable(WebDataBinder binder, MethodParameter parameter) {
    // 获取参数注解，比如@RequestBody、@Valid、@Validated
    Annotation[] annotations = parameter.getParameterAnnotations();
    for (Annotation ann : annotations) {
        // 先尝试获取@Validated注解
        Validated validatedAnn = AnnotationUtils.getAnnotation(ann, Validated.class);
        //如果直接标注了@Validated，那么直接开启校验。
        //如果没有，那么判断参数前是否有Valid起头的注解。
        if (validatedAnn != null || ann.annotationType().getSimpleName().startsWith("Valid")) {
            Object hints = (validatedAnn != null ? validatedAnn.value() : AnnotationUtils.getValue(ann));
            Object[] validationHints = (hints instanceof Object[] ? (Object[]) hints : new Object[] {hints});
            //执行校验
            binder.validate(validationHints);
            break;
        }
    }
}
```
WebDataBinder的validate()会获取WebDataBinder中的所有Validator对象，执行Validator对象的validate()方法。
```java
	public void validate(Object... validationHints) {
		for (Validator validator : getValidators()) {
			if (!ObjectUtils.isEmpty(validationHints) && validator instanceof SmartValidator) {
				((SmartValidator) validator).validate(getTarget(), getBindingResult(), validationHints);
			}
			else if (validator != null) {
				validator.validate(getTarget(), getBindingResult());
			}
		}
	}
```

@Validator 注解的作用是将参数标记为需要校验。参数在加上@Validator 注解、自定义的 Valid 开头的注解或者@Valid 注解之后，可以添加自定义的
validator 到 binder 进行校验，也可以通过在被校验对象属性上加注解的方式对参数验证。

有一个添加了@Validator 注解的 savePerson 接口，如下：

```java
    @PostMapping("/baseCost")
    public String savePerson(@RequestBody @Validator Person person){
        // do something
        return "success";
    }
```

#### 使用自定义 Validator

添加自定义的 PersonValidator 到 WebDataBinder：

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

- 需要注意的是 initBinder 在当前控制器生效，如果没有指定 value 则会对控制器下的参数都进行校验。如果控制器下有多个方法，且请求参数不一致时，必须明确指定
  initBinder 的 value，否则在请求 Validator 不支持的参数所在接口时，会报错。

#### Spring 支持的注解

Spring Validator 支持 javax.validation.constraints 以及 org.hibernate.validator.constraints 包下的注解。

注解使用示例：

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

有时需要动态、有条件的校验参数。SmartValidator 继承了 Validator 接口，增加了对验证提示的支持，相比 Validator
接口提供了更精细化的校验。Spring 默认提供了对 SmartValidator 的支持，我们也可以自行实现 SmartValidator 的逻辑。

```java
void validate(@Nullable
              java.lang.Object target,
              Errors errors,
              java.lang.Object... validationHints)
```

Parameters:
target - 待验证对象 (可以为空)
errors - 验证过程上下文 (不允许为空)
validationHints - 传递给验证引擎的一个或者多个提示对象

@Validated 注解接收接口类型的 Class 对象。

```java
@Validated({AddPerson.class, Default.class})
```

分组接口

```java
public interface Addperson{}
```

需要注意的是，传入自定义的分组对象会覆盖掉默认值（Default.class），如果想保留默认的校验需要同时传入自定义的分组对象和
Default.class 对象。

实现 SmartValidator 接口，SmartValidator 对象绑定到 WebDataBinder 之后，validationHints 参数就是@Validator
注解中传入的数组。有了这个参数就可以实现自定义的分组逻辑。

分组标记示例：

```java
public class Person {
    @NotEmpty(message = "name can't null", groups=AddPerson.class)
    private String name;
    @Min(value = "0", message = "age min 0")
    @Max(value = "150", message = "age max 150")
    private int age;

    // the usual getters and setters...
}
```

- name @NotEmpty 注解对 AddPerson 组生效
- age @Min 和@Max 注解对 Default 组生效

对于 Person 实例，生效条件取决于@Validator 指定的组。如果没有指定，则默认组生效，其他组不生效。

## 自定义 Validator 注解

Spring Validator 已经提供了非常通用的一些注解，但是对于一些特定的场景，需要自行实现。

以下是一个验证性别的例子。

定义 @Sex 注解

```java
@Target({ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = SexValidator.class)
public @interface Sex {
    String message() default "{Sex error}";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };
}
```

@Constraint 注解指定验证类，验证类必需实现 ConstraintValidator 接口。ConstraintValidator 接口有两个方法，`initialize`
用于初始化验证器。`isValid`用于实际校验。

SexValidator实现ConstraintValidator接口。

```java
public class SexValidator implements ConstraintValidator<Sex, String> {
    @Override
    public void initialize(Sex constraintAnnotation) {
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        return "男".equals(value) || "女".equals(value);
    }
}
```

Person sex字段添加@Sex注解。

```java
public class Person {
    private String name;
    private int age;
    @Sex
    private String sex;

    // the usual getters and setters...
}
```

## 总结

- Spring
  Validator可以通过实现接口（Validator或者SmartValidator）的方式实现验证，也可以通过注解（自定义注解需要实现ConstraintValidator接口，并且添加@Constraint注解）的方式验证。
- 注解方式可以满足单个字段的验证，如果需要同时验证多个字段，则可以使用实现接口的方式。如果涉及到分组，则需要实现SmartValidator。
- SpringMVC框架中可以使用@Valid、@Validated或者任意Valid开头的注解触发Spring Validator验证。