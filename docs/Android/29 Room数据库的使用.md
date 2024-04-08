# 29 Room数据库的使用

[Android Room使用官网](https://developer.android.google.cn/training/data-storage/room?hl=zh-cn)

## 1.Room数据库使用基本概念

### 主要组件

* 数据库类 ：用于保存数据库并作为应用持久性数据底层连接的主要访问点。

  ```kotlin
  @Database(entities = [User::class], version = 1)
  abstract class AppDatabase : RoomDatabase() {
      abstract fun userDao(): UserDao
  }
  ```

* 数据实体 ： 用于表示应用的数据库中的表

  ```kotlin
  @Entity
  data class User(
      @PrimaryKey val uid: Int,
      @ColumnInfo(name = "first_name") val firstName: String?,
      @ColumnInfo(name = "last_name") val lastName: String?
  )
  ```

* 数据访问对象： 提供您的应用可用于查询、更新、插入和删除数据库中的数据的方法。

  ```kotlin
  @Dao
  interface UserDao {
      @Query("SELECT * FROM user")
      fun getAll(): List<User>
  
      @Query("SELECT * FROM user WHERE uid IN (:userIds)")
      fun loadAllByIds(userIds: IntArray): List<User>
  
      @Query("SELECT * FROM user WHERE first_name LIKE :first AND " +
             "last_name LIKE :last LIMIT 1")
      fun findByName(first: String, last: String): User
  
      @Insert
      fun insertAll(vararg users: User)
  
      @Delete
      fun delete(user: User)
  }
  ```

​		正常的使用难点在于数据访问对象，@Query可以通过sql语句实现更复杂的查询功能。

具体使用：

```kotlin
val db = Room.databaseBuilder(
            applicationContext,
            AppDatabase::class.java, "database-name"
        ).build()
val userDao = db.userDao()
val users: List<User> = userDao.getAll()
```

注意：

1.在使用kotlin时，需要使用kapt引用room-compiler。

```java
val room_version = "2.5.0"
  implementation("androidx.room:room-runtime:$room_version")
   // To use Kotlin annotation processing tool (kapt)
  kapt("androidx.room:room-compiler:$room_version")
```

同时可能使其他使用 annotationProcessor 引用的依赖不能使用，比如router,可以将其也换为kapt引入。

## 2.Room数据库升级

Room数据库升级需要使用到Migration。

```java
static final Migration MIGRATION_1_2 = new Migration(1, 2) {
    @Override
    public void migrate(SupportSQLiteDatabase database) {
        // 因为没有变化，可以是一个空实现
        //如果有变化，需要添加改变数据库结构的Sql语句
    }
};
//添加到数据库中
val db = Room.databaseBuilder(
            applicationContext,
            AppDatabase::class.java, "database-name"
        ).addMigrations(MIGRATION_1_2)
  			.build()
```

注意：

1.当从0升级到1的时候，因为本地是没有旧数据库的，因此从0到1时addMigrations()不起作用。

2.每次发生改变需要进行数据库升级，否则会报以下类似Crash

```java
java.lang.IllegalStateException: Room cannot verify the data integrity. Looks like you've changed schema but forgot to update the version number. You can simply fix this by increasing the version number.
```

3.升级时Migration需要填写正确，否则会报类似以下Crash

```java
 java.lang.IllegalStateException: Migration didn't properly handle: testNewTable(com.example.demopractice.room.testNewTable).
```

4.从2.4.0之后提供了自动迁移,主要为以下提供了规范

- 删除或重命名表。
- 删除或重命名列。

## 3.从Sqlite迁移到Room数据库

1.我采用的方式是在sqlite升级的时候，将数据迁移到Room数据库。

"sinaweibo://qa/detail?object_id=1022:2310684917667189620819&cip=61.135.152.143&ua=OPPO-PEEM00__weibo__13.6.1__android__android12"

sinaweibo://qa/detail?object_id=1022:2310684917667189620819&cip=61.135.152.143&ua=OPPO-PEEM00__weibo__13.6.1__android__android12



# SQLite知识点总结

### enableWriteAheadLogging

并行读写开关，开启以后调用事务，就可以直接使用[beginTransactionNonExclusive()](https://developer.android.com/reference/android/database/sqlite/SQLiteDatabase#beginTransactionNonExclusive())

