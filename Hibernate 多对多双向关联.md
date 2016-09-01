http://lavasoft.blog.51cto.com/62575/39344
Hibernate 多对多双向关联
 
一、模型介绍
 
多个人（Person）对应多个地址（Address）。
一个人可对应多个地址，一个地址也可以对应多个人。
 
二、实体（省略getter、setter方法）
 
public class Personnn_sx {
    private int personid;
    private String name;
    private int age;
    private Set addresses=new HashSet();
 
public class Addressnn_sx {
    private int addressid;
    private String addressdetail;
    private Set persons = new HashSet();
 
三、表模型
 
mysql> desc person_nn_sx;
+----------+--------------+------+-----+---------+----------------+
| Field    | Type         | Null | Key | Default | Extra          |
+----------+--------------+------+-----+---------+----------------+
| personid | int(11)      | NO   | PRI | NULL    | auto_increment |
| name     | varchar(255) | YES  |     | NULL    |                |
| age      | int(11)      | YES  |     | NULL    |                |
+----------+--------------+------+-----+---------+----------------+
 
mysql> desc address_nn_sx;
+---------------+--------------+------+-----+---------+----------------+
| Field         | Type         | Null | Key | Default | Extra          |
+---------------+--------------+------+-----+---------+----------------+
| addressid     | int(11)      | NO   | PRI | NULL    | auto_increment |
| addressdetail | varchar(255) | YES  |     | NULL    |                |
+---------------+--------------+------+-----+---------+----------------+
 
mysql> desc join_nn_sx;
+-----------+---------+------+-----+---------+-------+
| Field     | Type    | Null | Key | Default | Extra |
+-----------+---------+------+-----+---------+-------+
| addressid | int(11) | NO   | PRI |         |       |
| personid  | int(11) | NO   | PRI |         |       |
+-----------+---------+------+-----+---------+-------+
 
四、生成的SQL脚本
 
/* Formatted on 2007/08/22 17:59 (QP5 v5.50) */
CREATE TABLE `address_nn_sx` (
  `addressid` int(11) NOT NULL auto_increment,
  `addressdetail` varchar(255) default NULL,
  PRIMARY KEY  (`addressid`)
) ENGINE=InnoDB DEFAULT CHARSET=gbk;
 
/* Formatted on 2007/08/22 17:59 (QP5 v5.50) */
CREATE TABLE `person_nn_sx` (
  `personid` int(11) NOT NULL auto_increment,
  `name` varchar(255) default NULL,
  `age` int(11) default NULL,
  PRIMARY KEY  (`personid`)
) ENGINE=InnoDB DEFAULT CHARSET=gbk;
 
/* Formatted on 2007/08/22 17:59 (QP5 v5.50) */
CREATE TABLE `join_nn_sx` (
  `addressid` int(11) NOT NULL,
  `personid` int(11) NOT NULL,
  PRIMARY KEY  (`personid`,`addressid`),
  KEY `FK6EBBC5EF6C600921` (`personid`),
  KEY `FK6EBBC5EF2A92FF3D` (`addressid`),
  CONSTRAINT `FK6EBBC5EF2A92FF3D` FOREIGN KEY (`addressid`) REFERENCES `address_nn_sx` (`addressid`),
  CONSTRAINT `FK6EBBC5EF6C600921` FOREIGN KEY (`personid`) REFERENCES `person_nn_sx` (`personid`)
) ENGINE=InnoDB DEFAULT CHARSET=gbk;
 
五、映射方法
 
<hibernate-mapping>
    <class name="com.lavasoft.sx._n_n.Personnn_sx" table="PERSON_nn_sx">
        <id name="personid">
            <generator class="identity"/>
        </id>
        <property name="name"/>
        <property name="age"/>
        <!--映射集合属性，关联到持久化类-->
        <!--table="join_1ntab_sx"指定了连接表的名字-->
        <set name="addresses"
             table="join_nn_sx"
             cascade="all">
            <!--column="personid"指定连接表中关联当前实体类的列名-->
            <key column="personid" not-null="true"/>
            <!--column="addressid"是连接表中关联本实体的外键-->
            <many-to-many column="addressid"
                          class="com.lavasoft.sx._n_n.Addressnn_sx"/>
        </set>
    </class>
</hibernate-mapping>
 
<hibernate-mapping>
    <class name="com.lavasoft.sx._n_n.Addressnn_sx"
           table="ADDRESS_nn_sx">
        <id name="addressid">
            <generator class="identity"/>
        </id>
        <property name="addressdetail"/>
        <!--table="join_nn_sx"是双向多对多的连接表-->
        <set name="persons"
             inverse="true"
             table="join_nn_sx">
            <!--column="addressid"是连接表中关联本实体的外键-->
            <key column="addressid"/>
            <many-to-many column="personid"
                          class="com.lavasoft.sx._n_n.Personnn_sx"/>
        </set>
    </class>
</hibernate-mapping>
 
六、测试方法
 
public class Test_nn_sx {
    public static void main(String[] args){
        Addressnn_sx add1=new Addressnn_sx();
        Addressnn_sx add2=new Addressnn_sx();
        Personnn_sx p1=new Personnn_sx();
        Personnn_sx p2=new Personnn_sx();
 
        add1.setAddressdetail("郑州市经三路");
        add2.setAddressdetail("合肥市宿州路");
        p1.setName("wang");
        p1.setAge(30);
        p2.setName("zhang");
        p2.setAge(22);
 
        p1.getAddresses().add(add1);
        p1.getAddresses().add(add2);
        p2.getAddresses().add(add2);
        add1.getPersons().add(p1);
        add2.getPersons().add(p1);
        add2.getPersons().add(p2);
 
 
        Session session= HibernateUtil.getCurrentSession();
        Transaction tx=session.beginTransaction();
        session.save(p1);
        session.save(p2);
//        session.saveOrUpdate(add1);
//        session.saveOrUpdate(add2);
        tx.commit();
        HibernateUtil.closeSession();
    }
}
 
七、测试结果
 
1) :正常保存.
        session.save(p1);
        session.save(p2);
//        session.saveOrUpdate(add1);
//        session.saveOrUpdate(add2);
 
  Hibernate: insert into PERSON_nn_sx (name, age) values (?, ?)
  Hibernate: insert into ADDRESS_nn_sx (addressdetail) values (?)
  Hibernate: insert into ADDRESS_nn_sx (addressdetail) values (?)
  Hibernate: insert into PERSON_nn_sx (name, age) values (?, ?)
  Hibernate: insert into join_nn_sx (personid, addressid) values (?, ?)
  Hibernate: insert into join_nn_sx (personid, addressid) values (?, ?)
  Hibernate: insert into join_nn_sx (personid, addressid) values (?, ?)
