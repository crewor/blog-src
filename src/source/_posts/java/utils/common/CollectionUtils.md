---
title: Java 数组工具类
---
### 特性:
    1.支持数组分组
<!--more-->
### 代码实现
    import java.util.*;
    
    /**
     * 集合工具类
     */
    public class CollectionUtils {
    
        /**
         * 分组接口
         * @param <K>
         * @param <E>
         */
        public interface GroupBy<K,E> {
            /**
             * 获取分组的key
             * @param obj
             * @return
             */
            K groupBy(E obj);
        }
    
        /**
         * 对集合进行分组
         * @param collection    集合
         * @param groupBy 分组
         * @param <K>   返回Map的K 类
         * @param <E>   集合类型
         * @return  分组结果
         */
        public static final <K, E> Map<K, List<E>> group(Collection<E> collection, GroupBy<K,E> groupBy) {
            if (collection == null || collection.isEmpty()) {
                return null;
            }
            if (groupBy == null) {
                return null;
            }
            Iterator<E> iter = collection.iterator();
            Map<K, List<E>> map = new HashMap<K, List<E>>();
            while (iter.hasNext()) {
                E d = iter.next();
                K t = groupBy.groupBy(d);
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
    
    }