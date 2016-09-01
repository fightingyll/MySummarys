http://lavasoft.blog.51cto.com/62575/39321

Hibernate 多对一外键单向关联
 
一、模型介绍
 
多个人（Person）对应一个地址（Address）。
 
二、实体（省略getter、setter方法）
 
public class Personn1fk {
    private int personid;
    private String name;
    private int age;
    private Addressn1fk addressn1fk;
 
public class Addressn1fk {
    private int addressid;
    private String addressdetail;
 
三、表模型
 
mysql> desc address_n1kf;
+---------------+--------------+------+-----+---------+----------------+
| Field         | Type         | Null | Key | Default | Extra          |
+---------------+--------------+------+-----+---------+----------------+
| addressid     | int(11)      | NO   | PRI | NULL    | auto_increment |
| addressdetail | varchar(255) | YES  |     | NULL    |                |
+---------------+--------------+------+-----+---------+----------------+
 
mysql> desc person_n1kf;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| personid  | int(11)      | NO   | PRI | NULL    | auto_increment |
| name      | varchar(255) | YES  |     | NULL    |                |
| age       | int(11)      | YES  |     | NULL    |                |
| addressId | int(11)      | YES  | MUL | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
 
四、生成的SQL脚本
 
CREATE TABLE `address_n1kf` (
  `addressid` int(11) NOT NULL auto_increment,
  `addressdetail` varchar(255) default NULL,
  PRIMARY KEY  (`addressid`)
) ENGINE=InnoDB DEFAULT CHARSET=gbk;
 
DROP TABLE IF EXISTS `person_n1kf`;
 
CREATE TABLE `person_n1kf` (
  `personid` int(11) NOT NULL auto_increment,
  `name` varchar(255) default NULL,
  `age` int(11) default NULL,
  `addressId` int(11) default NULL,
  PRIMARY KEY  (`personid`),
  KEY `FK4571AF54A2A3EE48` (`addressId`),
  CONSTRAINT `FK4571AF54A2A3EE48` FOREIGN KEY (`addressId`) REFERENCES `address_n1kf` (`addressid`)
) ENGINE=InnoDB DEFAULT CHARSET=gbk;
 
 
五、映射方法

 < hibernate-mapping>
    < class name="com.lavasoft.dx._n_1_fk.Personn1fk" table="PERSON_n1fk">
        < id name="personid">
            < generator class="identity"/>
        < /id>
        < property name="name"/>
        < property name="age"/>
        < !--用来映射关联PO column是Address在该表中的外键列名-->
        < many-to-one name="addressn1fk" column="addressId"/>
    < /class>
< /hibernate-mapping>
 
< hibernate-mapping>
    < class name="com.lavasoft.dx._n_1_fk.Addressn1fk" table="ADDRESS_n1fk">
        < id name="addressid">
            < generator class="identity"/>
        < /id>
        < property name="addressdetail"/>
    < /class>
< /hibernate-mapping>
 
 
六、测试方法
 
public class Test_n1fk {
    public static void main(String[] args){
        Personn1fk p1=new Personn1fk();
        Personn1fk p2=new Personn1fk();
 
        p1.setAge(21);
        p1.setName("p1");
 
        p2.setAge(23);
        p2.setName("p2");
 
        Addressn1fk add=new Addressn1fk();
        add.setAddressdetail("郑州市经三路");
 
        p1.setAddressn1fk(add);
        p2.setAddressn1fk(add);
 
        Session session=HibernateUtil.getCurrentSession();
        Transaction tx=session.beginTransaction();
        session.save(add);
        session.save(p1);
        session.save(p2);
        tx.commit();
        HibernateUtil.closeSession();
    }
}
 
七、测试结果
 
1) :正常保存. 推荐这么干!
 
        session.save(p1);
        session.save(p2);
 
Hibernate: insert into ADDRESS_n1kf (addressdetail) values (?)
Hibernate: insert into PERSON_n1kf (name, age, addressId) values (?, ?, ?)
Hibernate: insert into PERSON_n1kf (name, age, addressId) values (?, ?, ?)
 
2) :正常保存.
        session.save(p1);
        session.save(p2);
        session.save(add);
 
Hibernate: insert into PERSON_n1kf (name, age, addressId) values (?, ?, ?)
Hibernate: insert into PERSON_n1kf (name, age, addressId) values (?, ?, ?)
Hibernate: insert into ADDRESS_n1kf (addressdetail) values (?)
Hibernate: update PERSON_n1kf set name=?, age=?, addressId=? where personid=?
Hibernate: update PERSON_n1kf set name=?, age=?, addressId=? where personid=?
 
3) :正常保存.
        session.save(add);
//        session.save(p1);
//        session.save(p2);
 
Hibernate: insert into ADDRESS_n1kf (addressdetail) values (?)
 
4) : 发生异常,不能保存.
//        session.save(add);
        session.save(p1);
        session.save(p2);
Hibernate: insert into PERSON_n1kf (name, age, addressId) values (?, ?, ?)
Hibernate: insert into PERSON_n1kf (name, age, addressId) values (?, ?, ?)
Exception in thread "main" org.hibernate.TransientObjectException: com.lavasoft.dx._n_1_fk.Addressn1kf
 
