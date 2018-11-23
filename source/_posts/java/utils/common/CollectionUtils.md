---
title: Java 数组工具类
keywords: Java,java,java数组分组,java分组,java过滤,java数组过滤,数组过滤,数组分组,分组,java collection group util,java collection filter util
---
### 特性:
    1.group  数组分组
    2.filter 数组过滤
<!--more-->
### 代码实现
    import java.util.*;
    
    /**
     * @author:likun
     * @email:likun18@jd.com
     * @erp:likun101
     * @CreateTime:2018/11/10 13:40
     * @Project las-jdams
     */
    public class CollectionUtils {
        public interface GroupBy<K, E> {
            K groupBy(E obj);
        }
    
        public static final <K, E> Map<K, List<E>> group(Collection<E> collection, GroupBy<K, E> group) {
            if (collection == null || collection.isEmpty()) {
                return null;
            }
            if (group == null) {
                return null;
            }
            Iterator<E> iter = collection.iterator();
            Map<K, List<E>> map = new HashMap<K, List<E>>();
            while (iter.hasNext()) {
                E d = iter.next();
                K t = group.groupBy(d);
                if (map.containsKey(t)) {
                    map.get(t).add(d);
                } else {
                    List<E> list = new ArrayList<E>();
                    list.add(d);
                    map.put(t, list);
                }
            }
            return map;
        }
    
        public interface Filter<E> {
            boolean accept(E e);
        }
    
        public static final <E> List<E> filter(Collection<E> collection, Filter<E> filter) {
            if (collection == null || collection.isEmpty()) {
                return null;
            }
            if (filter == null) {
                return new ArrayList<E>(collection);
            }
            Iterator<E> iter = collection.iterator();
            List<E> res = new ArrayList<E>();
            while (iter.hasNext()) {
                E d = iter.next();
                if (filter.accept(d)) {
                    res.add(d);
                }
            }
            return res;
        }
    }