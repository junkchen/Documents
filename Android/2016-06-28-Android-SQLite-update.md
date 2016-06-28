# **Android SQLite数据库版本升级原理解析** #

Android使用SQLite数据库保存数据，那数据库版本升级是怎么回事呢，请看下面的详细分析。  

## **一、软件v1.0** ##

安装v1.0,假设v1.0版本只有一个account表，这时走继承SQLiteOpenHelper的onCreate，不走onUpgrade。

1、v1.0(直接安装v1.0)

## **二、软件v2.0** ##

有2种安装软件情况：
```
1、v1.0   -->  v2.0              不走onCreate，走onUpgrade

2、v2.0(直接安装v2.0)             走onCreate，不走onUpgrade
```

v1.0版本只有一个account表，软件版本升级到v2.0了，但是v2.0数据库需要新增一个member表，那怎么办呢？这里有2种情况了：一种是安装了v1.0升级到v2.0，这时不会走继承SQLiteOpenHelper的onCreate，而是直接走onUpgrade，这时就要在onUpgrade添加member表的代码了，在onCreate加了也没用，因为这种情况都不走onCreate。。另一种情况就是用户从来没有安装过这个软件，直接安装v2.0，这时走继承SQLiteOpenHelper的onCreate，不走onUpgrade,所以要在onCreate添加member表的代码。这怎么办呢？这就要合理升级数据库版本了。  

## **三、软件v3.0** ##

假设v3.0又新增一个news表，这里有三种情况：
```
1、v1.0   -->  v3.0              不走onCreate，走onUpgrade

2、v2.0   -->  v3.0              不走onCreate，走onUpgrade

3、v3.0(直接安装v3.0)             走onCreate，不走onUpgrade
```
那数据库添加表语句在那里写呢？数据库有一个版本号用DATABASE_VERSION表示

其实想一下，就知道不是onCreate写就是onUpgrade写，就是要兼容各种情况下安装app，都能把数据库表添加进去就好了。这里很巧妙：
```
1、v1.0     DATABASE_VERSION=1000    onCreate      --添加--  account

2、v2.0     DATABASE_VERSION=1001    onCreate      --添加--  account  （v1.0代码不变）  onUpgrade（DATABASE_VERSION>1000）

                                                       onUpgrade   --添加--  member 

3、v3.0     DATABASE_VERSION=1002    onCreate      --添加--  account  （v1.0代码不变）  onUpgrade（DATABASE_VERSION>1001）

                                                       onUpgrade   --添加--  member （v2.0代码不变）

                                                       onUpgrade   --添加--  news
```
这样就可以解决问题了，第一版本的都在onCreate，其他版本新增的在onUpgrade,而且在onCreate执行onUpgrade。做判断是否执行onUpgrade该怎么判断呢，所以有了数据库版本的概念了，DATABASE_VERSION保存当前的数据库版本，只要当前的数据库版本比已经安装的数据库版本大时，就进入onUpgrade，这时还会把上一个数据库版本号（oldVersion）跟安装的数据库版本号（newVersion）做比较，不同的DATABASE_VERSION添加自己所需要的表（跨版本升级数据库）。

## **简单的实例** ##

### （1）、V1.0  : DATABASE_VERSION = 1000 添加一个favorite表 ###
```java
public class DBHelper extends SQLiteOpenHelper {
 
    private static final String DATABASE_NAME = "mall.db"; 
    private static final int DATABASE_VERSION = 1000;
 
    private static DBHelper instance = null;
 
 
    public DBHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }
     
    public synchronized static DBHelper getInstance(Context context) {
        if (instance == null) {
            instance = new DBHelper(context);
        }
        return instance;
    }
 
    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL.CREATE_TABLE_FAVORITE);
         
        // 若不是第一个版本安装，直接执行数据库升级
        // 请不要修改FIRST_DATABASE_VERSION的值，其为第一个数据库版本大小
        final int FIRST_DATABASE_VERSION = 1000;
        onUpgrade(db, FIRST_DATABASE_VERSION, DATABASE_VERSION);
    }
 
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // 使用for实现跨版本升级数据库
        for (int i = oldVersion; i < newVersion; i++) {
            switch (i) {
 
            default:
                break;
            }
        }
    }
}
```

其中SQL.java是建表语句  

```java
public class SQL {
    public static final String T_FAVORITE = "favorite";
 
 
    public static final String CREATE_TABLE_FAVORITE =
            "CREATE TABLE IF NOT EXISTS " + T_FAVORITE + "(" +
                    "id VARCHAR PRIMARY KEY, " +
                    "title VARCHAR, " +
                    "url VARCHAR, " +
                    "createDate VARCHAR " +
                    ")";
}
```

### （2）、V2.0  : DATABASE_VERSION = 1001 在favorite表添加1个deleted字段 ###  

```java
public class DBHelper extends SQLiteOpenHelper {
 
    private static final String DATABASE_NAME = "mall.db"; 
    private static final int DATABASE_VERSION = 1001;
 
    private static DBHelper instance = null;
 
 
    public DBHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }
     
    public synchronized static DBHelper getInstance(Context context) {
        if (instance == null) {
            instance = new DBHelper(context);
        }
        return instance;
    }
 
    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL.CREATE_TABLE_FAVORITE);
         
        // 若不是第一个版本安装，直接执行数据库升级
        // 请不要修改FIRST_DATABASE_VERSION的值，其为第一个数据库版本大小
        final int FIRST_DATABASE_VERSION = 1000;
        onUpgrade(db, FIRST_DATABASE_VERSION, DATABASE_VERSION);
    }
 
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // 使用for实现跨版本升级数据库
        for (int i = oldVersion; i < newVersion; i++) {
            switch (i) {
            case 1000:
                upgradeToVersion1001(db);
                break;
            default:
                break;
            }
        }
    }
     
    private void upgradeToVersion1001(SQLiteDatabase db){
        // favorite表新增1个字段
        String sql1 = "ALTER TABLE "+SQL.T_FAVORITE+" ADD COLUMN deleted VARCHAR";
        db.execSQL(sql1);
    }
}
```

### （3）、V3.0  : DATABASE_VERSION = 1002 在favorite表添加message和type字段 ###  

```java
public class DBHelper extends SQLiteOpenHelper {
 
    private static final String DATABASE_NAME = "mall.db"; 
    private static final int DATABASE_VERSION = 1002;
 
    private static DBHelper instance = null;
 
 
    public DBHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }
     
    public synchronized static DBHelper getInstance(Context context) {
        if (instance == null) {
            instance = new DBHelper(context);
        }
        return instance;
    }
 
    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL.CREATE_TABLE_FAVORITE);
         
        // 若不是第一个版本安装，直接执行数据库升级
        // 请不要修改FIRST_DATABASE_VERSION的值，其为第一个数据库版本大小
        final int FIRST_DATABASE_VERSION = 1000;
        onUpgrade(db, FIRST_DATABASE_VERSION, DATABASE_VERSION);
    }
 
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // 使用for实现跨版本升级数据库
        for (int i = oldVersion; i < newVersion; i++) {
            switch (i) {
            case 1000:
                upgradeToVersion1001(db);
                break;
            case 1001:
                upgradeToVersion1002(db);
                break;
                 
            default:
                break;
            }
        }
    }
     
    private void upgradeToVersion1001(SQLiteDatabase db){
        // favorite表新增1个字段
        String sql1 = "ALTER TABLE "+SQL.T_FAVORITE+" ADD COLUMN deleted VARCHAR";
        db.execSQL(sql1);
    }
    private void upgradeToVersion1002(SQLiteDatabase db){
        // favorite表新增2个字段,添加新字段只能一个字段一个字段加，sqlite有限制不予许一条语句加多个字段
        String sql1 = "ALTER TABLE "+SQL.T_FAVORITE+" ADD COLUMN message VARCHAR";
        String sql2 = "ALTER TABLE "+SQL.T_FAVORITE+" ADD COLUMN type VARCHAR";
        db.execSQL(sql1);
        db.execSQL(sql2);
    }
}
```

就是这样，无论v1.0升级到v3.0，或者v2.0升级到3.0，还是v3.0直接安装，安装后的v3.0数据库结构都是一样的，理解透彻就是好啊，刚做sqlite数据库的肯定会遇到这些问题，所以在这里详细地写了一下，不过还是要注意一下，就是onUpgrade升级时候一定要写对，测试好，不然安装后的数据库都有问题就麻烦了。  

本文来自网络，原文： [http://www.cnblogs.com/liqw/p/4264925.html](http://www.cnblogs.com/liqw/p/4264925.html)  

个人觉得写得很好，思路清晰，明了易懂，解决了实际问题，特此分享给大家，希望对有相同需求的同志有帮助。   

欢迎加QQ群交流： **365532949**  
**Homepage:** [http://junkchen.com](http://junkchen.com)