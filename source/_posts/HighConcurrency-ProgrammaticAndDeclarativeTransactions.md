---
title: 编程式事务和声明式事务
date: 2018-07-28 15:07:00
categories: 高并发
tags: [事务]
---
本篇文章承接[《springboot的mybatis示例》](https://www.haijunyin.com/Framework-DemoSpringbootMybatis.html)
## 编程式事务
编程式事务是代码级别的，类似于JDBC的事务管理，Spring管理使用TransactionTemplate事务
1.配置事务模板
```java
    /**
     * 数据源加入事务管理
     * @param masterDataSource
     * @return
     */
    @Bean(name = "transactionManager")
    @Primary
    public DataSourceTransactionManager masterDataSourceTransactionManager(@Qualifier("masterDataSource") DataSource masterDataSource){
        final DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(masterDataSource);
        return dataSourceTransactionManager;
    }

 /**
     * 事务管理加入事务模板
     * @param transactionManager
     * @return
     */
    @Bean(name = "transactionTemplate")
    @Primary
    public TransactionTemplate masteerTransactionTemplate(@Qualifier("transactionManager") DataSourceTransactionManager transactionManager){
        final TransactionTemplate transactionTemplate = new TransactionTemplate();
        transactionTemplate.setTransactionManager(transactionManager);
        transactionTemplate.setIsolationLevelName("ISOLATION_DEFAULT");
        transactionTemplate.setPropagationBehaviorName("PROPAGATION_REQUIRED");
        return transactionTemplate;
    }
```
2.使用
```java
    @Autowired
    private TransactionTemplate transactionTemplate;
```
```java
  @Override
    public void addUsers(List<User> users){
        TransactionCallback transactionCallback = new TransactionCallback() {
            @Nullable
            @Override
            public Object doInTransaction(TransactionStatus status) {
                try {
                    for (User user : users) {
                        int i = userMapper.insert(user);
                        if (i == 1) {
                            System.out.println("添加成功");
                        } else {
                            System.out.println("添加失败");
                        }
                    }
                }catch (Exception e){
                    e.printStackTrace();
                    System.out.println("出现异常，回滚");
                    //设置事务回滚
                    status.setRollbackOnly();
                }
                return null;
            }
        };
        transactionTemplate.execute(transactionCallback);
    }
```
3.测试，先把user的name加上唯一索引
```sql
ALTER TABLE `user` ADD UNIQUE ( `name` ) ;
```
```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = SpringbootdemoMybatisApplication.class)
@WebAppConfiguration
public class SpringbootdemoMybatisApplicationTests {

	@Resource
	UserService myUserService;

	@Test
	public void testUser() throws Exception {
		List<User> users = new ArrayList<>();
		User user1 = new User();
		user1.setAge(11);
		user1.setName("明明4");
		user1.setSocialPriceAmount(new BigDecimal(1000));
		user1.setUserLevel(UserLevelEnum.LEVEL_1);
		user1.setCreatedTime(new Date());
		user1.setUpdateTime(new Date());
		User user2 = new User();
		user2.setAge(22);
		user2.setName("欢欢");
		user2.setSocialPriceAmount(new BigDecimal(12000));
		user2.setUserLevel(UserLevelEnum.LEVEL_2);
		user2.setCreatedTime(new Date());
		user2.setUpdateTime(new Date());
		users.add(user1);
		users.add(user2);
		myUserService.addUsers(users);
	}

}
```
4.结果
由于数据库中name属性有唯一索引，索引该demo中两条数据都不会添加成功

## 声明式事务
管理建立在AOP基础上，本质是对方法前后进行拦截，所以声明式事务是方法级别的，使用的时候只需要在方法前面加上@Transactional注解
1.使用,不需要做其他配置了
```java
 @Transactional(rollbackFor = Exception.class)
    @Override
    public void addUsers(List<User> users){
        for (User user : users) {
            int i = userMapper.insert(user);
            if (i == 1) {
                System.out.println("添加成功");
            } else {
                System.out.println("添加失败");
            }
        }
    }
```
2.测试，同上
3.结果，同上