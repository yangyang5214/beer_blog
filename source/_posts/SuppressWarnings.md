---
title: SuppressWarnings
date: 2019-06-10 10:23:44
tags: java
---


> @SuppressWarnings 注解的使用。起因是：项目的 es 使用的是 5.6 版本的es，而使用的 restClient 版本为 7.0.0 的。众所周知，es 7.0 版本没有 type，所以在使用的时候（编译打包）会一直报 方法已过时。

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/20190610102702_es_deprecated.png)


### 解决

方法上加 ： @SuppressWarnings("deprecation")

之后编译、打包，就不会出现方法过时的警告了。

<!--more-->
### 注解 SuppressWarnings

```
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
    /**
     * The set of warnings that are to be suppressed by the compiler in the
     * annotated element.  Duplicate names are permitted.  The second and
     * successive occurrences of a name are ignored.  The presence of
     * unrecognized warning names is <i>not</i> an error: Compilers must
     * ignore any warning names they do not recognize.  They are, however,
     * free to emit a warning if an annotation contains an unrecognized
     * warning name.
     *
     * <p> The string {@code "unchecked"} is used to suppress
     * unchecked warnings. Compiler vendors should document the
     * additional warning names they support in conjunction with this
     * annotation type. They are encouraged to cooperate to ensure
     * that the same names work across multiple compilers.
     * @return the set of warnings to be suppressed
     */
    String[] value();
}
```

@Retention(RetentionPolicy.SOURCE)

Annotations are to be discarded by the compiler.

见到的第一个 compiler 时期的注解。


### 使用

后面见到了就补充

[Excluding warnings using @SuppressWarnings](https://www.ibm.com/support/knowledgecenter/en/SSQ2R2_9.5.1/org.eclipse.jdt.doc.user/tasks/task-suppress_warnings.htm)
- all to suppress all warnings

- boxing to suppress warnings relative to boxing/unboxing operations

    自动拆箱/装箱

- cast to suppress warnings relative to cast operations
- dep-ann to suppress warnings relative to deprecated annotation
- deprecation to suppress warnings relative to deprecation

    过时的方法
    
    ```
    @SuppressWarnings("deprecation")
    ```

- fallthrough to suppress warnings relative to missing breaks in switch statements

    switch 方法必须有 default,所以这个用处不大吧 

- finally to suppress warnings relative to finally block that don't return
- hiding to suppress warnings relative to locals that hide variable
- incomplete-switch to suppress warnings relative to missing entries in a switch statement (enum case)
- javadoc to suppress warnings relative to javadoc warnings
- nls to suppress warnings relative to non-nls string literals
- null to suppress warnings relative to null analysis
- rawtypes to suppress warnings relative to usage of raw types
- resource to suppress warnings relative to usage of resources of type Closeable
- restriction to suppress warnings relative to usage of discouraged or forbidden references
- serial to suppress warnings relative to missing serialVersionUID field for a serializable class
- static-access to suppress warnings relative to incorrect static access
- static-method to suppress warnings relative to methods that could be declared as static
- super to suppress warnings relative to overriding a method without super invocations
- synthetic-access to suppress warnings relative to unoptimized access from inner classes
- sync-override to suppress warnings because of missing synchronize when overriding a synchronized method
- unchecked to suppress warnings relative to unchecked operations
- unqualified-field-access to suppress warnings relative to field access unqualified
- unused to suppress warnings relative to unused code and dead code

