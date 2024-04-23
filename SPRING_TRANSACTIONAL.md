Here we discuss the Spring `@Transactional` annotation. We'll explore its usage, understand common pitfalls, and delve a little into the theory behind it. This annotation forms part of Spring's transaction management, known as Declarative Transaction Management, which is widely used in Spring applications. It's a frequently asked topic in interviews, making it worthwhile to learn.

### How to use `@Transactional`

You can find core concepts of transactions in this article: [Transactions](https://en.wikipedia.org/wiki/Database_transaction).

This discussion focuses on the Spring `@Transactional` annotation. To use `@Transactional` in Spring Boot, simply add a "data" starter to your project, such as `spring-boot-starter-data-jpa` or `spring-boot-starter-data-jdbc`. This adds the necessary dependencies and configurations to your project.

If you are not using Spring Boot, you will need to manually add the necessary dependencies for the `@Transactional` annotation, enable Spring transaction management with the `@EnableTransactionManagement` annotation, and configure a transaction manager.

### Key points about `@Transactional` and limitations

The main idea behind `@Transactional`, like many things in the Spring Framework, is proxying through AOP. When you use `@Transactional`, Spring creates a proxy around your class and intercepts the method calls. In simple terms, this proxied call goes through the transaction manager to start a transaction, commit or rollback it, and close the transaction. You can find more detailed explanation in the [Spring documentation](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-decl-explained.html).

The `@Transactional` annotation supports `class` and `method` levels. Even if you apply it at the class level, you can override it by putting it on a method level.

Because of the proxying nature of `@Transactional`, it has some limitations. Here are the most important ones:

- It works only on public methods. Yes, in the latest versions of Spring (since 6.0), you can use it on protected and package-private methods, but if you're not sure, use it only on public methods. And of course, it will NOT work on private methods.

- It is not recommended, according to Spring documentation, to place the annotation on interfaces. You can achieve that it will work by using a non-default proxying mechanism, but if you don't have any specific reason, don't do that; just put it on the implementation level.

- Methods annotated with `@Transactional` MUST be called from outside the class. It's because of the proxying mechanism; if you call the method from the same class, the proxy will not be used, and the transaction will not be started. For example, let's say you have a class like this:

    ```java

    @Service
    public class SomeService {

        @Transactional
        public void methodA() {
            this.methodB();
        }

        @Transactional(propagation = Propagation.REQUIRES_NEW)
        public void methodB() {
            // some logic
        }
    }

    ```
    
    If you call `methodA` from another class, the transaction will be started, but only one transaction will be created, despite the fact that `methodB` has `REQUIRES_NEW` propagation. Even worse, if you call `methodB` from `methodA`, without any outside call, the transaction will not be started at all. You can make things work by adding some programmatic transaction management, or by injecting the class inside itself, but it's not recommended; it's a sign that you are doing something wrong.

- If a method annotated with `@Transactional` throws a checked exception, the transaction will not be rolled back by default (by default, rollbacks occur only on RuntimeException and Error). You should specify the exception in the rollbackFor attribute. Also, be careful with the specified exceptions; they should match the exception that can be thrown by the method, otherwise, rollback will not occur.

There are not all limitations and pitfalls, but these are the most common and important ones. For example, when using `@Transactional` with multiple data sources, you should be careful with the transaction manager configuration, and so on.

You can find more information in the [Spring documentation](https://docs.spring.io/spring-framework/reference/data-access/transaction.html).


### Common parameters of `@Transactional`

- **transactionManager** or **value** - The name of the transaction manager bean to use. If not specified, the default transaction manager will be used. You can have multiple transaction managers, for example, for different data sources.
  
- **propagation** - The propagation type of the transaction. It can be one of the following:
    - REQUIRED - Default value. If a transaction exists, it will be used. If not, a new transaction will be created.
    - SUPPORTS - If a transaction exists, it will be used. If not, the method will be executed without a transaction.
    - MANDATORY - A transaction must exist. If not, an exception will be thrown.
    - REQUIRES_NEW - A new transaction will be created. If a transaction exists, it will be suspended (depends on the transaction manager implementation).
    - NOT_SUPPORTED - The method will be executed without a transaction. If a transaction exists, it will be suspended (depends on the transaction manager implementation).
    - NEVER - The method will be executed without a transaction. If a transaction exists, an exception will be thrown.
    - NESTED - A nested transaction will be created. If a transaction exists, the method will be executed within that transaction. If not, a new transaction will be created (depends on the transaction manager implementation).

- **isolation** - The isolation level of the transaction. It can be one of the following:
    - DEFAULT - default isolation level if not specified.
    - READ_UNCOMMITTED
    - READ_COMMITTED
    - REPEATABLE_READ
    - SERIALIZABLE
    
    I will not go into details about isolation levels, you can find more theory [here](https://en.wikipedia.org/wiki/Isolation_(database_systems)).

- **readOnly** - flag that indicates that the transaction is read-only. Default value is false. It can be used for optimization purposes on runtime. It's good practice to use it when you are sure that the transaction will not modify the data.
  
- **rollbackFor** - The exception types that trigger a rollback.
  
- **noRollbackFor** - The opposite of rollbackFor. The exception types that do not trigger a rollback.


### `@Lock` with `@Transactional`

Even though the lock is not directly related to transactions, it's worth mentioning that you can use the `@Lock` annotation along with `@Transactional`. The `@Lock` annotation is used to lock an entity in the database, helping prevent concurrent access. For example, it can prevent two users from updating the same entity at the same time within a transaction. These are two different concepts, but they can be used together. You can find more about locking and its types in the [Wikipedia article](https://en.wikipedia.org/wiki/Lock_(computer_science)#Database_locks).

### Conclusion

With the Spring `@Transactional` annotation, you can easily manage transactions in your application without the need to write extensive configurations—unless you have specific requirements.

It's a powerful tool that can save you a lot of time and effort. My favorite aspect is its declarative approach to handling transactions.

However, despite the ease of using `@Transactional` in Spring, as demonstrated above, it's also very easy to make mistakes. If you're unsure about something, remember the advice Stilgar gives to Paul Atreides in Dune: "Be simple. Be direct. Nothing fancy." Moreover, it's beneficial to understand how it works under the hood and to use it properly. Make sure to read the documentation and articles that I mentioned above.

### Additional resources

- [Transactions](https://en.wikipedia.org/wiki/Database_transaction)
- [Spring documentation on transactional proxying](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-decl-explained.html)
- [Spring documentation on transactions](https://docs.spring.io/spring-framework/reference/data-access/transaction.html)
- [Isolation levels](https://en.wikipedia.org/wiki/Isolation_(database_systems))
- [Database locks](https://en.wikipedia.org/wiki/Lock_(computer_science)#Database_locks)