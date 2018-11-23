---
title: 通过代理实现spring bean的可动态替换
keywords: spring,spring容器,动态替换,spring bean动态替换,spring动态替换,spring bean modifiable
date: 2018-07-29 12:15:20
---
### 描述
        遇到这样一个问题，有一个spring容器管理的一个实例对象A,然后A在很多
    的类中都被引用了，当某一个时候，需要重新生成A对象，而不希望重新启动服务
    的时候，就可以使用代理实现spring bean的可动态替换。具体实现方法参照下
    文。
### 功能
    1. 通过代理实现spring bean的可动态替换。
<!--more-->
### 实现方式
        通过实现spring的org.springframework.beans.factory.config.BeanPostProcessor接口，
    在spring bean（例如：A对象）初始化的时候返回A对象的代理对象PA，而代理对象PA可以动态修改实例A，
    从而实现不需要重新启动服务，只需要修改引用对象PA的代理对象A实例，即可进行动态修改。
### 实例
    /**
     * 所有被代理对象都通过spring.ModifiableBean.Modifiable#setTarget(java.lang.Object)
     * 方法进行实际对象的修改
     */
    public interface Modifiable {
    
        Object getTarget();
    
        void setTarget(Object target);
    }
    
    
    import net.sf.cglib.proxy.Enhancer;
    import net.sf.cglib.proxy.MethodInterceptor;
    import net.sf.cglib.proxy.MethodProxy;
    
    import java.lang.reflect.Method;
    
    /**
     * bean代理工具类型
     */
    public class BeanProxyUtil {
    
        /**
         * 创建可以修改的代理对象
         *
         * @param targetObject
         * @return
         */
        public static Object buildModifiableProxy(Object targetObject) {
            try {
                //增强器，动态代码生成器
                Enhancer enhancer = new Enhancer();
                enhancer.setCallback(new ModifiableInterceptor4Cglib(targetObject));
                if (targetObject != null) {
                    enhancer.setSuperclass(targetObject.getClass());
                }
                enhancer.setInterfaces(new Class[]{Modifiable.class});
                return enhancer.create();
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
    
        /**
         * 代理对象的执行策略
         */
        private static class ModifiableInterceptor4Cglib implements MethodInterceptor, Modifiable {
            private Object target;
    
            public ModifiableInterceptor4Cglib(Object target) {
                this.target = target;
            }
    
            public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
                if (method.equals(Modifiable.class.getDeclaredMethod("setTarget", Object.class))) {
                    setTarget(args[0]);
                } else if (method.equals(Modifiable.class.getDeclaredMethod("getTarget"))) {
                    return getTarget();
                } else {
                    return method.invoke(target, args);
                }
                return null;
            }
    
            @Override
            public Object getTarget() {
                return target;
            }
    
            @Override
            public void setTarget(Object target) {
                this.target = target;
            }
        }
    }
    
    
    import org.springframework.beans.BeansException;
    import org.springframework.beans.factory.config.BeanPostProcessor;
    import org.springframework.core.PriorityOrdered;
    
    /**
     *  通过实现spring的org.springframework.beans.factory.config.BeanPostProcessor接口，来进行
     *  spring bean对象的增强，实现被代理对象的可动态替换。
     *  spring.ModifiableBean.ModifiableSpringBeanPostProcess#needModifyClazzs 配置需要进行代理
     *  增强的类型。只要是配置的类型实例，都会通过
     *  spring.ModifiableBean.BeanProxyUtil#buildModifiableProxy(java.lang.Object) 方法返回对象
     *  的代理对象，返回的对象实现了spring.ModifiableBean.Modifiable接口，通过
     *  spring.ModifiableBean.Modifiable 接口方法进行动态修改被代理的对象
     *
     */
    @SuppressWarnings("unused")
    public class ModifiableSpringBeanPostProcess implements  BeanPostProcessor, PriorityOrdered {
        private String[] holdFiles;
        private Class[] needModifyClazzs ={};//这里配置要代理的类型
    
        /**
         *
         * 是否是需要修改的类
         * @param object
         * @return
         */
        public boolean needModify(Object object) {
            if(object==null||needModifyClazzs==null)
                return false;
            for(Class clazz:needModifyClazzs){
                if(object.getClass().isAssignableFrom(clazz)){
                    return true;
                }
            }
            return false;
        }
    
    
        public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
            return bean;
        }
    
    
        public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
            if (needModify(bean)) {
                //注册beanDefine
                //这里是代理的ConsumerBean ，代理成可以修改的
                return BeanProxyUtil.buildModifiableProxy(bean);
            }
            return bean;
        }
        public int getOrder() {
            return PriorityOrdered.HIGHEST_PRECEDENCE;
        }
    }
