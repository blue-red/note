漏洞之XML External Entity Injection(XML实体注入)

1. 产生原因：XML External Entity 攻击可利用能够处理时动态构建文档的xml功能。xml实体可动态包含来自给定资源的数据。外部实体允许xml文档包含来自外部URI的数据（除非另行配置，否正外部实体会迫使xml解析器访问由URI指定的资源）。这样的行为会将应用程序暴露给XXE（XML External Entity）攻击,从而用于拒绝本地系统服务，获取本机上文件未经授权的访问权限，扫描远程计算机，并拒绝远程系统服务。

2. 例子：xml文档介绍了XXE攻击的实例：

   ```xml
   <?xml version="1.0" encoding="ISO-8859-1"?>
   <!DOCTYPE foo[
   	<!ELEMENT foo ANY>
   	<!ENTITY xxe SYSTEM "file:///dev/random"]>
   <foo>&xxe</foo>
   ```

   如果解析器尝试使用/dev/random文件中的内容来替代实体，则此实例对应服务器崩溃（使用UNIX系统）

   ```java
   public static Object xmlToBean(String params, Object object){
       JAXBContext context;
       try {
           context = JAXBContext.newInstance(object.getClass());
           Unmarshaller unmarshaller = context.createUnmarshaller();
           object = unmarshaller.unmarshal(new StringReader(params));
       } catch (JAXBException e) {
          logger.error("xml转换失败，参数为 {} ",params,e);
       }
       return object;
   }
   ```

3. 修复方案：应对XML unmarshaller进行安全配置，使它不允许将外部实体包含再传入的XML文档中。为了避免XXE injection，请勿将直接处理的XML源的unmarshal方法用作java.io.File,java.io.Reader或java.io.InputStream。使用安全配置的解析器解析文档并使用将安全解析器作为XML源的unmarshal方法

   ```java
   public static Object xmlToBean(String params, Object object){
       JAXBContext context;
       try {
           context = JAXBContext.newInstance(object.getClass());
           XMLInputFactory xmlInputFactory = XMLInputFactory.newInstance();
           xmlInputFactory.setProperty(XMLInputFactory.IS_SUPPORTING_EXTERNAL_ENTITIES,false);
           xmlInputFactory.setProperty(XMLInputFactory.SUPPORT_DTD,true);
           Unmarshaller unmarshaller = context.createUnmarshaller();
           object = unmarshaller.unmarshal(new StringReader(params));
       } catch (JAXBException e) {
          logger.error("xml转换失败，参数为 {} ",params,e);
       }
       return object;
   }
   ```



