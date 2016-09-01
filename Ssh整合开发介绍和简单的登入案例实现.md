Ssh整合开发介绍和简单的登入案例实现


Ssh整合开发介绍和简单的登入案例实现  
一  介绍：  
Ssh是strtus2-2.3.1.2+ spring-2.5.6+hibernate-3.6.8整合的开发，这是目前我的整合开发的使用技术和版本，使用的数据库为mySql。使用的开发工具是eclipse,eplipse的版本为Indigo Service Release 2  
二  搭建环境  
1．  首先要先引入struts2和sping和hibernate所需要的包  
（1）struts2的包为：  
struts-2.3.1.2\lib\ struts2-core-2.3.1.2.jar  
struts-2.3.1.2\lib\ognl-3.0.4.jar  
struts-2.3.1.2\lib\ xwork-core-2.3.1.2.jar  
struts-2.3.1.2\lib\ freemarker-2.3.18.jar  
struts-2.3.1.2\lib\commons-fileupload-1.2.2.jar  
struts-2.3.1.2\lib\ commons-io-2.0.1.jar  
struts-2.3.1.2\lib\commons-lang-2.5.jar  
struts-2.3.1.2\lib\commons-logging-1.1.1.jar  
struts-2.3.1.2\lib \struts2-json-plugin-2.3.1.2.jar  
struts-2.3.1.2\lib \struts2-spring-plugin-2.3.1.2.jar  
(2)引入hibernate的包  
hibernate-distribution-3.6.8.Final\lib\required\*.jar  所有的jar包  
hibernate-distribution-3.6.8.Final\lib\jpa\hibernate-jpa-2.0-api-1.0.1.Final.jar  
hibernate-distribution-3.6.8.Final\ hibernate3.jar  
(3)spring的包为：  
    spring-framework-2.5.6\dist\ spring.jar  
    spring-framework-2.5.6\lib\c3p0\c3p0-0.9.1.2.jar  
    spring-framework-2.5.6\lib\aspectj\*.jar  
    spring-framework-2.5.6\lib\j2ee\common-annotations.jar  
    spring-framework-2.5.6\lib\log4j\log4j-1.2.15.jar  
    spring-framework-2.5.6\lib\jakarta-commons\ commons-logging.jar  
spring-framework-2.5.6\lib\cglib\cglib-nodep-2.1_3.jar    
spring-framework-2.5.6\lib\dom4j\dom4j-1.6.1.jar  
2．  配置spring下所需要的文件  
（1）首先配置spring所需要的xml文件  
    我们在class下，即在src下创建一个applicationContext.xml的xml文件，文件的头文件因为要用到各种标签，所以我们在引入头文件的时候尽量引的比较多点，代码为:  
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:context="http://www.springframework.org/schema/context"  
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans           http://www.springframework.org/schema/beans/spring-beans-2.5.xsd   
           http://www.springframework.org/schema/aop  
           http://www.springframework.org/schema/aop/spring-aop-2.5.xsd   
           http://www.springframework.org/schema/context  
           http://www.springframework.org/schema/context/spring-context-2.5.xsd  
           http://www.springframework.org/schema/tx  
           http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">  
</beans>  
（2）在xml中配置和数据库相关联，并用c3p0来配置数据库连接池  
    <!-- 数据库的连接池 -->  
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"  
        destroy-method="close">  
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>  
        <property name="jdbcUrl"  
    value="jdbc:mysql://localhost:3306/spring?useUnicode=true&characterEncoding=UTF-8" />  
        <property name="user" value="root" />  
        <property name="password" value="1234" />  
        <!--初始化时获取的连接数，取值应在minPoolSize与maxPoolSize之间。Default: 3 -->  
        <property name="initialPoolSize" value="1" />  
        <!--连接池中保留的最小连接数。 -->  
        <property name="minPoolSize" value="1" />  
        <!--连接池中保留的最大连接数。Default: 15 -->  
        <property name="maxPoolSize" value="300" />  
        <!--最大空闲时间,60秒内未使用则连接被丢弃。若为0则永不丢弃。Default: 0 -->  
        <property name="maxIdleTime" value="60" />  
        <!--当连接池中的连接耗尽的时候c3p0一次同时获取的连接数。Default: 3 -->  
        <property name="acquireIncrement" value="5" />  
        <!--每60秒检查所有连接池中的空闲连接。Default: 0 -->  
        <property name="idleConnectionTestPeriod" value="60" />  
    </bean>  
  
当然，这样所写的可以在一个properties中读取。读取外部文件在xml中的使用为：  
<!-- 读取外部的文件 -->  
<context:property-placeholder location="jdbc.properties" />  
  
(3)配置sessionFactory工厂，相当于是hibernate.cfg.xml里面的配置  
    <!-- sessionFactory工厂 -->  
    <bean id="sessionFactory"  
        class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">  
        <!-- 注入数据源 -->  
        <property name="dataSource" ref="dataSource"></property>  
        <!-- hibernate映射文件的引入 -->  
        <property name="mappingResources">  
            <list>  
                <value>cn/csdn/hr/s2sh/domain/Admin.hbm.xml</value>  
            </list>  
        </property>  
        <!-- 配置hibernate属性的设置 -->  
        <property name="hibernateProperties">  
            <props>  
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>  
                <prop key="hibernate.hbm2ddl.auto">update</prop>  
                <prop key="hibernate.show_sql">true</prop>  
                <prop key="hibernate.format_sql">true</prop>  
            </props>  
        </property>  
    </bean>  
3．配置struts2.xml中需要的内容  
(1)配置基本的struts2.xml   
 <!DOCTYPE struts PUBLIC  
    "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"  
    "http://struts.apache.org/dtds/struts-2.3.dtd">  
<struts>  
    <!--使用spring创建管理struts2的action操作 -->  
    <constant name="struts.objectFactory" value="spring"/>  
    <include file="struts-admin.xml"></include>  
</struts>  
注：其中include是引入的文件，一下的语句是非常重要的：  
<constant name="struts.objectFactory" value="spring"/>，它是struts2和spring的连接的关键  
(2)引入的struts-admin.xml文件，意在检测并作出一个简单的登入的实现  
  <!DOCTYPE struts PUBLIC  
    "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"  
    "http://struts.apache.org/dtds/struts-2.3.dtd">  
<struts>  
    <package name="admin" extends="struts-default" namespace="/csdn">  
        <!-- class的名称  等于spring中action配置文件中的id名称 -->  
        <action name="loginAdmin" class="adminAction" method="login">  
            <result name="success">../index.jsp</result>  
        </action>  
    </package>  
</struts>  
4．  Hibernate.cfg.xml中的内容可以省略，它已经在spring中的xml中配置了  
5．搭建层之间的关系  
(1)首先是domain，包为cn.csdn.hr.s2sh.domain,我们以admin为例  
属性为id和name和pass  
封装的实体类为:  
package cn.csdn.hr.s2sh.domain;  
import java.io.Serializable;  
public class Admin implements Serializable {  
    private static final long serialVersionUID = 1L;  
    private Integer id;  
    private String name;  
    private String pass;  
  
    public Admin() {  
        super();  
        // TODO Auto-generated constructor stub  
    }  
  
    public Admin(Integer id, String name, String pass) {  
        super();  
        this.id = id;  
        this.name = name;  
        this.pass = pass;  
    }  
  
    public Integer getId() {  
        return id;  
    }  
  
    public void setId(Integer id) {  
        this.id = id;  
    }  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public String getPass() {  
        return pass;  
    }  
  
    public void setPass(String pass) {  
        this.pass = pass;  
    }  
  
    @Override  
    public String toString() {  
        return "Admin [id=" + id + ", name=" + name + ", pass=" + pass + "]";  
    }  
}  
(2)在同一个包下的实体类的映射文件  
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"  
                                   "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">  
<hibernate-mapping package="cn.csdn.hr.s2sh.domain">  
    <class name="Admin" table="admin">  
        <id name="id" column="id">  
            <generator class="native"></generator>  
        </id>  
        <property name="name" column="name"></property>  
        <property name="pass" column="pass"></property>  
    </class>  
</hibernate-mapping>  
  
(3)创建dao层，创建的包名为cn.csdn.hr.s2sh.dao，创建的接口名为AdminDao,类名为AdminDaoImpl  
下面是创建的接口 AdminDao.java  
package cn.csdn.hr.s2sh.dao;  
  
import java.util.List;  
  
import cn.csdn.hr.s2sh.domain.Admin;  
  
public interface AdminDao {  
    public Admin login(final String name,final String pass);  
}  
  
创建的类为AdminDaoImpl.java  
package cn.csdn.hr.s2sh.dao;  
import java.sql.SQLException;  
import java.util.List;  
import org.hibernate.HibernateException;  
import org.hibernate.Session;  
import org.springframework.orm.hibernate3.HibernateCallback;  
import org.springframework.orm.hibernate3.support.HibernateDaoSupport;  
  
import cn.csdn.hr.s2sh.domain.Admin;  
  
@SuppressWarnings("unchecked")  
public class AdminDaoImpl extends HibernateDaoSupport implements AdminDao {  
    public Admin login(final String name, final String pass) {  
  
        return (Admin) this.getHibernateTemplate().execute(  
                new HibernateCallback() {  
  
                    public Object doInHibernate(Session session)  
                            throws HibernateException, SQLException {  
                        // TODO Auto-generated method stub  
  
                        Object obj = session  
                                .createQuery(  
                                        "from Admin where name=:name and pass=:pass")  
                                .setString("name", name)  
                                .setString("pass", pass).uniqueResult();  
                        return obj;  
                    }  
                });  
    }  
}  
  
(4)创建service层，创建的包名为cn.csdn.hr.s2sh.service  
创建的AdminService.java接口  
package cn.csdn.hr.s2sh.service;  
  
import cn.csdn.hr.s2sh.dao.AdminDao;  
  
  
public interface AdminService extends AdminDao{  
  
}  
创建的AdminServiceImpl.java  
package cn.csdn.hr.s2sh.service;  
import java.util.List;  
import org.springframework.transaction.TransactionStatus;  
import org.springframework.transaction.support.TransactionCallback;  
import org.springframework.transaction.support.TransactionTemplate;  
import cn.csdn.hr.s2sh.dao.AdminDao;  
import cn.csdn.hr.s2sh.domain.Admin;  
public class AdminServiceImpl implements AdminService {  
  
    private AdminDao adminDao;  
    public void setAdminDao(AdminDao adminDao) {  
        this.adminDao = adminDao;  
    }  
    public Admin login(String name, String pass) {  
        // TODO Auto-generated method stub  
        return adminDao.login(name, pass);  
    }  
}  
  
  
6.创建访问struts2的时候的action，我们把action放到一个bean-action.xml中  
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:context="http://www.springframework.org/schema/context"  
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans  
           http://www.springframework.org/schema/beans/spring-beans-2.5.xsd   
           http://www.springframework.org/schema/aop  
           http://www.springframework.org/schema/aop/spring-aop-2.5.xsd   
           http://www.springframework.org/schema/context  
           http://www.springframework.org/schema/context/spring-context-2.5.xsd  
           http://www.springframework.org/schema/tx  
           http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">  
  
    <!-- 创建struts2中的action -->  
    <!-- 配置admin的action -->  
    <bean id="adminAction" class="cn.csdn.hr.s2sh.action.AdminAction"  
        scope="prototype">  
        <property name="adminService" ref="adminService"></property>  
    </bean>  
</beans>  
  
而ref的adminServiceImpl是在bean-service.xml中  
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:context="http://www.springframework.org/schema/context"  
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans  
           http://www.springframework.org/schema/beans/spring-beans-2.5.xsd   
           http://www.springframework.org/schema/aop  
           http://www.springframework.org/schema/aop/spring-aop-2.5.xsd   
           http://www.springframework.org/schema/context  
           http://www.springframework.org/schema/context/spring-context-2.5.xsd  
           http://www.springframework.org/schema/tx  
           http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">  
  
    <!-- admin的业务操作 -->  
    <bean id="adminServiceImpl" class="cn.csdn.hr.s2sh.service.AdminServiceImpl">  
        <property name="adminDao" ref="adminDao"></property>  
    </bean>  
</beans>  
  
上面的ref是adminDaoImpl，在beans-dao.xml  
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:context="http://www.springframework.org/schema/context"  
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://www.springframework.org/schema/beans  
           http://www.springframework.org/schema/beans/spring-beans-2.5.xsd   
           http://www.springframework.org/schema/aop  
           http://www.springframework.org/schema/aop/spring-aop-2.5.xsd   
           http://www.springframework.org/schema/context  
           http://www.springframework.org/schema/context/spring-context-2.5.xsd  
           http://www.springframework.org/schema/tx  
           http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">  
  
  
    <!-- hibernatedao的模板类 HibernateDaoSuppor 抽象类不可以实例化 加上abstract="true" -->  
    <bean id="hibernateDaoSupport"  
        class="org.springframework.orm.hibernate3.support.HibernateDaoSupport"  
        abstract="true">  
        <property name="sessionFactory" ref="sessionFactory"></property>  
    </bean>  
    <!-- admin的dao对象 -->  
    <bean id="adminDao" class="cn.csdn.hr.s2sh.dao.AdminDaoImpl"  
        parent="hibernateDaoSupport" />  
</beans>  
  
然后我们把上面的三个beans.xml导入到applicationContext.xml中  
<!-- 导入其他的配置文件 -->  
<import resource="beans-dao.xml" />  
<import resource="beans-service.xml" />  
<import resource="beans-action.xml" />  
  
7．配置web.xml  
<?xml version="1.0" encoding="UTF-8"?>  
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"  
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"  
    id="WebApp_ID" version="2.5">  
    <display-name>s2sh</display-name>  
    <welcome-file-list>  
        <welcome-file>index.html</welcome-file>  
        <welcome-file>index.htm</welcome-file>  
        <welcome-file>index.jsp</welcome-file>  
        <welcome-file>default.html</welcome-file>  
        <welcome-file>default.htm</welcome-file>  
        <welcome-file>default.jsp</welcome-file>  
    </welcome-file-list>  
  
    <!-- 指定spring的配置文件，默认从web根目录寻找配置文件，我们可以通过spring提供的classpath:前缀指定从类路径下寻找 -->  
    <context-param>  
        <param-name>contextConfigLocation</param-name>  
        <param-value>classpath:applic*.xml</param-value>  
    </context-param>  
    <!-- 对Spring容器进行实例化 -->  
    <listener>  
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>  
    </listener>  
  
    <!-- struts2 的配置 -->  
    <filter>  
        <filter-name>struts2</filter-name>  
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>  
    </filter>  
  
    <filter-mapping>  
        <filter-name>struts2</filter-name>  
        <url-pattern>/*</url-pattern>  
    </filter-mapping>  
  
    <!-- 配置 使用spring解决hibernate因session关闭导致的延迟加载例外问题 -->  
    <filter>  
        <filter-name>OpenSessionInViewFilter</filter-name>  
        <filter-class>org.springframework.orm.hibernate3.support.OpenSessionInViewFilter</filter-class>  
        <init-param>  
            <!-- 指定org.springframework.orm.hibernate3.LocalSessionFactoryBean在spring配置文件中的名称,默认值为sessionFactory.如果LocalSessionFactoryBean在spring中的名称不是sessionFactory,该参数一定要指定,否则会出现找不到sessionFactory的例外 -->  
            <param-name>sessionFactoryBeanName</param-name>  
            <param-value>sessionFactory</param-value>  
        </init-param>  
    </filter>  
    <filter-mapping>  
        <filter-name>OpenSessionInViewFilter</filter-name>  
        <url-pattern>/*</url-pattern>  
    </filter-mapping>  
  
    <!-- 使用spring解决struts2的中文乱码的问题 -->  
    <filter>  
        <filter-name>encoding</filter-name>     <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>  
        <init-param>  
            <param-name>encoding</param-name>  
            <param-value>UTF-8</param-value>  
        </init-param>  
    </filter>  
    <filter-mapping>  
        <filter-name>encoding</filter-name>  
        <url-pattern>/*</url-pattern>  
    </filter-mapping>  
</web-app>  
  
8．我们来做一个简单的登入，使用的jsp页面为：  
<body>  
    <div>  
        <form action="/s2sh/csdn/loginAdmin.action" method="get">  
            用户名：<input type="text" name="admin.name"/><br/>  
            密  码：<input type="password" name="admin.pass"/><br/>  
            <input type="submit" value="登入"/>  
            <input type="reset" value="重置"/>  
        </form>  
    </div>  
    <div>  
        用户名为:${admin.name}  
    </div>  
</body>  
  
访问到的action的处理为：  
package cn.csdn.hr.s2sh.action;  
import cn.csdn.hr.s2sh.domain.Admin;  
import cn.csdn.hr.s2sh.service.AdminService;  
import com.opensymphony.xwork2.ActionSupport;  
public class AdminAction extends ActionSupport {  
    private static final long serialVersionUID = 1L;  
  
    private AdminService adminService;  
  
    private Admin admin;  
  
    public Admin getAdmin() {  
        return admin;  
    }  
  
    public void setAdmin(Admin admin) {  
        this.admin = admin;  
    }  
  
    // 注入  
    public void setAdminService(AdminService adminService) {  
        this.adminService = adminService;  
    }  
  
    public String login() {  
        System.out.println("用户名:" + admin.getName());  
        admin = adminService.login(admin.getName(), admin.getPass());  
        return SUCCESS;  
    }  
}  