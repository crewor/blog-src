---
title: 一个简单的类mybatis的xml解析sql工具
keywords: sql解析工具,xml解析sql工具,xml解析sql,mybatis,sql parser,xml parser
---
### 特性:
    1.支持ognl表达式语句条件
    2.使用Map封装sql赋值参数
<!--more-->
### 代码实现
    package tag;
    
    import ognl.Ognl;
    import ognl.OgnlException;
    import org.w3c.dom.Document;
    import org.w3c.dom.NamedNodeMap;
    import org.w3c.dom.Node;
    import org.w3c.dom.NodeList;
    import org.xml.sax.InputSource;
    
    import javax.xml.parsers.DocumentBuilder;
    import javax.xml.parsers.DocumentBuilderFactory;
    import java.io.ByteArrayInputStream;
    import java.util.Map;
    
    /**
     * @author:likun
     * @email:likun18@jd.com
     * @erp:likun101
     * @CreateTime:2018/10/30 11:20
     * @Project notes
     */
    public class SqlParser {
    
        public static String foreachNode(Node node, Object params) throws OgnlException {
            StringBuilder sb = new StringBuilder();
    //打印该元素的文本
            NodeList nodeList2 = node.getChildNodes();
            for (int i = 0; i < nodeList2.getLength(); i++) {
                Node node1 = nodeList2.item(i);
                if (node1.getNodeName().equals("#text")) {
                    sb.append(fillModel(node1.getTextContent(), params));
                } else {
                    if (node1.getNodeName().equals("if")) {
                        try {
                            sb.append(IfTag.parse(node1, params));
                        } catch (OgnlException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
            return sb.toString();
        }
    
        public static String parse(String sourceSql, Map<String, Object> parmas) throws Exception {
    
            StringBuilder sb = new StringBuilder();
            sb.append("<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"no\"?>")
                    .append("<mapper>")
                    .append(sourceSql)
                    .append("</mapper>");
            //得到解析工厂DocumentBuilderFactory
            DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
            //得到解析器DocumentBuilders
            DocumentBuilder builder = factory.newDocumentBuilder();
            //解析指定的XML文档，得到代表内存DOM树的Document对象
            Document document = builder.parse(new InputSource(new ByteArrayInputStream(sb.toString().getBytes("UTF-8"))));
            //根据标签的名称获取所有的作者元素
            Node root = document.getFirstChild();
            return foreachNode(root, parmas);
        }
    
    
        /**
         * 对字符串填充值
         *
         * @param ret
         * @param params
         * @return
         */
        public static String fillModel(String ret, Object params) throws OgnlException {
            StringBuilder retBuilder = new StringBuilder();
            int index = 0;
            int sublen = 0;
            while ((index = ret.indexOf("#{", index)) > -1) {
                retBuilder.append(ret.substring(sublen, index));
                int nextEnd = ret.indexOf("}", index);
                String property = ret.substring(index + 2, nextEnd);
                sublen = index = nextEnd + 1;
                retBuilder.append(Ognl.getValue(property.trim(), params));
            }
            if (sublen < ret.length()) {
                retBuilder.append(ret.substring(sublen));
            }
            return retBuilder.toString();
        }
    
    
        public static class IfTag {
            /**
             * @param ifNode 节点
             * @param params 参数
             * @return
             */
            public static String parse(Node ifNode, Object params) throws OgnlException {
                StringBuilder ifRet = new StringBuilder();
    //if tag ,判断条件，是否转换
                NamedNodeMap namedNodeMap = ifNode.getAttributes();
                Node test = namedNodeMap.getNamedItem("test");
                String condition = test.getNodeValue();
                Object obj = Ognl.getValue(condition, params);
                if (Boolean.TRUE.equals(obj)) {
                    //todo 如果有嵌套标签，后面进行拓展
                    String ret = ifNode.getTextContent();
    //                ifRet.append(" ").append(SqlParser.fillModel(ret, params)).append(" ");
                    ifRet.append(" ").append(foreachNode(ifNode, params)).append(" ");
                }
                return ifRet.toString();
            }
    
    
        }
        /**
        ** TEST DEMO
        **/
        public static void testParse() throws Exception {
                Map<String, Object> parmas = new HashMap<String, Object>() {{
                    put("categoryStr", "100000");
                    put("wareMode", "100000");
                    put("out", "(CASE mrt.idCompany WHEN 6 THEN 6.0 WHEN 3 THEN 60.0 WHEN 10 THEN 60.0 ELSE NULL END)");
                }};
                String str = "select count(0) as sumNum,mrt.idCompany as idCompany,sum(if(TIMESTAMPDIFF(MINUTE,mrt.create_time,now())>#{out} ,1,0)) as outSumNum from mcs_repair_audit_task mrt join mcs_repair_order mro on mrt.r_order_id=mro.id where mrt.state=10 \n" +
                        "<if test=\"companyStr!=null\">\n" +
                        "\tand mrt.idcompany=#{companyStr}\n" +
                        "</if>\n" +
                        "<if test=\"categoryStr!=null\">\n" +
                        "\tand mrt.ware_mode=#{categoryStr}\n" +
                        "</if>\n" +
                        "<if test=\"companyStr==null\">\n" +
                        "\tgroup by mrt.idcompany\n" +
                        "</if>";
                String sql = SqlParser.parse(str, parmas);
                System.out.println(sql);
        
        }
        public static void main(String[] args) throws Exception {
            testParse();
        }
    }
