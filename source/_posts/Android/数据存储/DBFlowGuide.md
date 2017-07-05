---
title: DBFlow 使用
date: 2016-09-21 13:18:12
tags: [DBFlow]
category: Android

---

# 配置

`build.gradle`

    buildscript {
        repositories {
          // required for this library, don't use mavenCentral()
            jcenter()
        }
        dependencies {
            classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
        }
    }

Because DBFlow3 is still not officially released, you need also add https://jitpack.io to your allprojects -> repositories dependency list:

    allprojects {
        repositories {
            jcenter()
            maven { url "https://jitpack.io" }
        }
    }

`app/build.gradle`:

    apply plugin: 'com.neenbedankt.android-apt'

    dependencies {
        apt "com.github.Raizlabs.DBFlow:dbflow-processor:3.1.1"
        compile "com.github.Raizlabs.DBFlow:dbflow-core:3.1.1"
        compile "com.github.Raizlabs.DBFlow:dbflow-sqlcipher:3.1.1@aar"
        compile 'net.zetetic:android-database-sqlcipher:3.5.1'

        compile "com.github.Raizlabs.DBFlow:dbflow:3.1.1"
    }

<!--more-->

# 创建一个数据库

    @Database(name = MyDatabase.NAME, version = MyDatabase.VERSION)
    public class MyDatabase {

        public static final String NAME = "MyDataBase";

        public static final int VERSION = 1;
    }

# 定义一个表

**注意：**必须必须要有 @PrimaryKey

    @Table(database = MyDatabase.class)
    public class Organization extends BaseModel {

      @Column
      @PrimaryKey(autoincrement = true)
      int id;

      @Column
      String name;
    }

    @Table(database = MyDatabase.class)
    public class User extends BaseModel {

        @Column
        @PrimaryKey
        int id;

        @Column
        String name;

        @Column
        @ForeignKey(saveForeignKeyModel = false)
        Organization organization;

        public void setOrganization(Organization organization) {
            this.organization = organization;
        }

        public void setName(String name) {
            this.name = name;
        }
    }



# Using with the Parceler library

    @Table(database = MyDatabase.class)
    @Parcel(analyze={User.class})   // add Parceler annotation here
    public class User extends BaseModel {
    }

# 初始化 DBFlow

    public class MyApplication extends Application {

        @Override
        public void onCreate() {
            super.onCreate();
            FlowManager.init(new FlowConfig.Builder(this).build());
        }
    }

# 基本的CRUD操作

## C 创建一行数据

一个类对应一个表，有表注解的实体类可以调用`.save`方法保存到数据库，添加一行数据：

    // Create organization
    Organization organization = new Organization();
    organization.setId(1);
    organization.setName("CodePath");
    organization.save();

    // Create user
    User user = new User();
    user.setName("John Doe");
    user.setOrganization(organization);
    user.save();

## R 读取数据

### 例子

    // Query a List
    SQLite.select().from(SomeTable.class).queryList();
    SQLite.select().from(SomeTable.class).where(conditions).queryList();

    // Query Single Model
    SQLite.select().from(SomeTable.class).querySingle();
    SQLite.select().from(SomeTable.class).where(conditions).querySingle();

    // Query a Table List and Cursor List
    SQLite.select().from(SomeTable.class).where(conditions).queryTableList();
    SQLite.select().from(SomeTable.class).where(conditions).queryCursorList();

    // Query into a ModelContainer!
    SQLite.select().from(SomeTable.class).where(conditions).queryModelContainer(new MapModelContainer<>(SomeTable.class));

    // SELECT methods
    SQLite.select().distinct().from(table).queryList();
    SQLite.select().from(table).queryList();
    SQLite.select(Method.avg(SomeTable_Table.salary))
      .from(SomeTable.class).queryList();
    SQLite.select(Method.max(SomeTable_Table.salary))
      .from(SomeTable.class).queryList();

    // Transact a query on the DBTransactionQueue
    TransactionManager.getInstance().addTransaction(
        new SelectListTransaction<>(new Select().from(SomeTable.class).where(conditions),
        new TransactionListenerAdapter<List<SomeTable>>() {
          @Override
          public void onResultReceived(List<SomeTable> someObjectList) {
            // retrieved here
    });

    // Selects Count of Rows for the SELECT statment
    long count = SQLite.selectCountOf()
        .where(conditions).count();


### Order  

    // true为'ASC'正序, false为'DESC'反序
    SQLite.select()
      .from(Customer.class)
      .where()
      .orderBy(Customer_Table.customer_id, true)
      .queryList();

源码：

    public Where<TModel> orderBy(IProperty property, boolean ascending) {

    public Where<TModel> orderBy(NameAlias nameAlias, boolean ascending) {

    public Where<TModel> orderBy(OrderBy orderBy) {

参数：

1. IProperty 代表某个字段

    属于抽象类：

        public interface IProperty<P extends IProperty> extends Query {

    其子类有：

        ![](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/dbflow_iproperty.png)  

    **注意：**一般我们不需要自己创建这个类来使用，而是通过 `Customer_Table.customer_id` （创建表的类名_Table.表的字段名或者说类的属性名） 来获取字段对应的 IProperty 。 `Customer_Table.class` 这个类根据我们定义的表类自动生成。


2. ascending：true 正序（0->1）, false 反序 (1->0)

3. NameAlias

    获取方式：IProperty.getNameAlias();


## Group By

    SQLite.select()
      .from(table)
      .groupBy(Customer_Table.customer_id, Customer_Table.customer_name)
      .queryList();

## Having

    SQLite.select()
      .from(table)
      .groupBy(Customer_Table.customer_id, Customer_Table.customer_name))
      .having(Customer_Table.customer_id.greaterThan(2))
      .queryList();

## LIMIT + OFFSET (分页)

    SQLite.select()
      .from(table)
      .limit(3)
      .offset(2)
      .queryList();

## U 更新一条数据

｀save()｀

    // Native SQL wrapper
    Where<Ant> update = SQLite.update(Ant.class)
      .set(Ant_Table.type.eq("other"))
      .where(Ant_Table.type.is("worker"))
        .and(Ant_Table.isMale.is(true));

    // TransactionManager (more methods similar to this one)
    TransactionManager
            .getInstance()
            .addTransaction(
                    new QueryTransaction(
                            DBTransactionInfo.create(BaseTransaction.PRIORITY_UI),
                    update);



## D 删除一条数据

`.delete()`

    // Delete a whole table
    Delete.table(MyTable.class, conditions);

    // Delete multiple instantly
    Delete.tables(MyTable1.class, MyTable2.class);

    // Delete using query
    SQLite.delete(MyTable.class)
      .where(DeviceObject_Table.carrier.is("T-MOBILE"))
        .and(DeviceObject_Table.device.is("Samsung-Galaxy-S5"))
      .query();

# 数据库事务

使用`ProcessModelTransaction`类，比如：

    ArrayList<User> users = new ArrayList<>();

    // fetch users from the network

    // save rows
    FlowManager.getDatabase(AppDatabase.class)
              .beginTransactionAsync(new ProcessModelTransaction.Builder<>(
              new ProcessModelTransaction.ProcessModel<User>() {
                  @Override
                  public void processModel(User user) {
                       // do work here -- i.e. user.delete() or user.update()
                       user.save();
                  }
              }).addAll(users).build())  // add elements (can also handle multiple)
              .error(new Transaction.Error() {
                  @Override
                  public void onError(Transaction transaction, Throwable error) {

                  }
              })
              .success(new Transaction.Success() {
                  @Override
                  public void onSuccess(Transaction transaction) {

                  }
              }).build().execute();

# 数据库升级


## 新增表

1. 做出需要的修改，创建表类 （BaseModel 子类）
2. 修改AppDatabase的版本号就可以了。（版本号+1）

## 新增字段

1. 做出需要的修改
2. 修改版本号
3. 编译工程： build－》Make Project ， Mac：command＋F9
3. 编写描述：Migrations

比如给People类添加一个字段：

    @Database(name = AppDatabase.NAME, version = AppDatabase.VERSION)
    public class AppDatabase {
      //数据库名称
      public static final String NAME = "AppDatabase";
      //数据库版本号，这里修改2
      public static final int VERSION = 2;
    }

    @ModelContainer
    @Table(database = AppDatabase.class)
    public class People extends BaseModel {
        //自增ID
        @PrimaryKey(autoincrement = true)
        public Long id;
        @Column
        public String name;
        @Column
        public int gender;
        @Column
        public String email;
    }

    @Migration(version = 2, database = AppDatabase.class)
    public class Migration_2_People extends AlterTableMigration<People> {
        //类名可以更加自己喜欢定义,这里按照数据库版本号和需要更新的数据表来命名，需要注意是：version = 2



        public Migration_2_People(Class<People> table) {
            super(table);
        }

        @Override
        public void onPreMigrate() {
            addColumn(SQLiteType.TEXT, People_Table.email.getNameAlias().getNameAsKey());
        }
    }

# 如果编译报错

查看错误：

![https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/androidstudio_gradle_console.png](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/androidstudio_gradle_console.png)


参考：

- [https://guides.codepath.com/android/DBFlow-Guide#using-with-the-parceler-library](https://guides.codepath.com/android/DBFlow-Guide#using-with-the-parceler-library)
- [http://www.codeceo.com/article/android-orm-dbflow.html](http://www.codeceo.com/article/android-orm-dbflow.html)
- [https://yumenokanata.gitbooks.io/dbflow-tutorials/content/sql_wrapper_classes.html](https://yumenokanata.gitbooks.io/dbflow-tutorials/content/sql_wrapper_classes.html)
