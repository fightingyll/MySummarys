http://lavasoft.blog.51cto.com/62575/39279

Hibernate 一对一外键单向关联
 
    事实上，单向1-1与N-1的实质是相同的，1-1是N-1的特例，单向1-1与N-1的映射配置也非常相似。只需要将原来的many-to-one元素增加unique="true"属性，用于表示N的一端也必须是唯一的，在N的一端增加了唯一的约束，即成为单向1-1。基于外键的单向1-1的配置将与无连接表N-1关联的many-to-one增加unique="true"属性即可。
 
一、模型介绍
 
一个人（Person）对应一个地址（Address）。
 
二、实体（省略getter、setter方法）
 
public class Person11fk {
    private int personid;
    private String name;
    private int age;
    private Address11fk address11fk;
 
public class Address11fk {
    private int addressid;
    private String addressdetail;
 
三、表模型
 
mysql> desc address_11fk;
+---------------+--------------+------+-----+---------+----------------+
| Field         | Type         | Null | Key | Default | Extra          |
+---------------+--------------+------+-----+---------+----------------+
| addressid     | int(11)      | NO   | PRI | NULL    | auto_increment |
| addressdetail | varchar(255) | YES  |     | NULL    |                |
+---------------+--------------+------+-----+---------+----------------+
 
mysql> desc person_11fk;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| personid  | int(11)      | NO   | PRI | NULL    | auto_increment |
| name      | varchar(255) | YES  |     | NULL    |                |
| age       | int(11)      | YES  |     | NULL    |                |
| addressId | int(11)      | YES  | UNI | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
 
四、生成的SQL脚本
 
CREATE TABLE `address_11fk` ( 
    `addressid` int(11) NOT NULL auto_increment, 
    `addressdetail` varchar(255) default NULL, 
    PRIMARY KEY    (`addressid`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=gbk;
    
CREATE TABLE `person_11fk` (
    `personid` int(11) NOT NULL auto_increment, 
    `name` varchar(255)default NULL,
    `age` int(11) default NULL, 
    `addressId` int(11) default NULL, 
    PRIMARY KEY    (`personid`),
    KEY `FK68A8818F3F45AA77` (`addressId`), 
    CONSTRAINT `FK68A8818F3F45AA77` FOREIGN KEY (`addressId`) REFERENCES `address_11fk` (`addressid`) 
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=gbk;
 
 
五、映射方法：
 
    在Person中添加Address属性，映射配置为：
        <!--用来映射关联PO column是Address在该表中的外键列名,增加unique变成“1-1”-->
        <many-to-one name="address11fk" column="addressId" unique="true"/>
 
<hibernate-mapping>
        <classname="com.lavasoft.dx._1_1_fk.Address11fk"table="ADDRESS_11fk">
                <idname="addressid">
                        <generatorclass="identity"/>
                </id>
                <propertyname="addressdetail"/>
        </class>
</hibernate-mapping>
 
<hibernate-mapping>
        <classname="com.lavasoft.dx._1_1_fk.Person11fk"table="PERSON_11fk">
                <idname="personid">
                        <generatorclass="identity"/>
                </id>
                <propertyname="name"/>
                <propertyname="age"/>
                <!--用来映射关联PO column是Address在该表中的外键列名,增加unique变成“1-1”-->
                <many-to-onename="address11fk"column="addressId"unique="true"/>
        </class>
</hibernate-mapping>
       
 
六、测试方法
 
public class Test_11fk { 
        public staticvoid main(String[] args){ 
                Person11fk p1=new Person11fk();
    
                p1.setAge(21); 
                p1.setName("p1"); 
    
                Address11fk add1=new Address11fk();
                add1.setAddressdetail("郑州市经三路");
    
                p1.setAddress11fk(add1); 
    
                Session session= HibernateUtil.getCurrentSession(); 
                Transaction tx=session.beginTransaction(); 
                session.save(add1); 
                session.save(p1); 
    //注意：如果上面manytoone里面加了cascade=all 那么表示级联 那么直接 session.save(p1); 系统会自动 session.save(add1);
                tx.commit(); 
                HibernateUtil.closeSession(); 
        } 
}
 
 
七、测试结果
 
1) :正常保存. 推荐这么干!
        session.save(add1);
        session.save(p1);
 
Hibernate: insert into ADDRESS_11fk (addressdetail) values (?)
Hibernate: insert into PERSON_11fk (name, age, addressId) values (?, ?, ?)
 
2) :正常保存.
        session.save(p1);
        session.save(add1);
 
Hibernate: insert into PERSON_11fk (name, age, addressId) values (?, ?, ?)
Hibernate: insert into ADDRESS_11fk (addressdetail) values (?)
Hibernate: update PERSON_11fk set name=?, age=?, addressId=? where personid=?
 
3) :正常保存.
//        session.save(p1);
        session.save(add1);
 
Hibernate: insert into ADDRESS_11fk (addressdetail) values (?)
 
4) : 发生异常,不能保存.
        session.save(p1);
//        session.save(add1);
 
Hibernate: insert into PERSON_11fk (name, age, addressId) values (?, ?, ?)
Exception in thread "main" org.hibernate.TransientObjectException: com.lavasoft.dx._1_1_fk.Address11fk

