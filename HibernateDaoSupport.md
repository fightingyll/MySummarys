一，Spring为Hibernate的DAO提供工具类：HibernateDaoSupport。该类主要提供了两个方法： 　　publicfinalHibernateTemplategetHi.........
　　一，Spring为Hibernate的DAO提供工具类：HibernateDaoSupport。该类主要提供了两个方法：
　　public final HibernateTemplate getHibernateTemplate() ；
　　public final void setSessionFactory(SessionFactory sessionFactory) ；
　　其中，setSessionFactory方法接收来自Spring的applicationContext的依赖注入，接收了配置在Spring 中的SessionFactory实例，getHibernateTemplate方法用来利用刚才的SessionFactory生成Session， 再生成HibernateTemplate来完成数据库的访问。
　　典型的继承HibernateDaoSupport的DAO代码如下：
　　public class UserDAOImpl extends HibernateDaoSupport implements UserDAO{
　　public void save(Users transientInstance) {
　　log.debug("saving Users instance");
　　try {
　　getHibernateTemplate().save(transientInstance);
　　log.debug("save successful");
　　} catch (RuntimeException re) {
　　log.error("save failed", re);
　　throw re;
　　}
　　}
　　………………
　　}
　　实 际上，DAO的实现依然借助了HibernateTemplate的模板访问方式，只是，HibernateDaoSupport将依赖注入 SessionFactory的工作已经完成，获取HibernateTemplate的工作也已经完成。注意，这种方法须在Spring的配置文件中配 置SessionFactory。
　　在继承HibrnateDaoSupport的DAO实现里，Hibernate Session的管理完全不需要Hibernate代码打开，而由Spring来管理。Spring会根据实际的操作，采用“每次事务打开一次 session”的策略，自动提高数据库访问的性能。