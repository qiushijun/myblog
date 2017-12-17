---
title: Elasticsearch 学习： Java API (一)
tags: 
	- Elasticsearch
	- Java
	- Lucene
---
Elasticsearch 是一个建立在全文搜索引擎 Apache Lucene(TM) 基础上的搜索 引擎,可以说 Lucene 是当今最先进,最高效的全功能开源搜索引擎框架。
但是 Lucene 只是一个框架,要充分利用它的功能,你需要使用 JAVA,并且在 你的程序中集成 Lucene。更糟的是,你需要做很多的学习了解,才能明白它是 如何运行的,Lucene 确实非常复杂。
Elasticsearch 使用 Lucene 作为内部引擎,但是在你使用它做全文搜索时,只 需要使用统一开发好的API即可,而并不需要了解其背后复杂的 Lucene 的运行 原理。
<!-- more -->
当然 Elasticsearch 并不仅仅是 Lucene 那么简单,它不仅包括了全文搜索功 能,还可以进行以下工作:
分布式实时文件存储,并将每一个字段都编入索引,使其可以被搜索。 实时分析的分布式搜索引擎。 可以扩展到上百台服务器,处理PB级别的结构化或非结构化数据。
这么多的功能被集成到一台服务器上,你可以轻松地通过客户端或者任何你喜欢 的程序语言与 ES 的 RESTful API 进行交流。
Elasticsearch 的上手是非常简单的。它附带了很多非常合理的默认值,这让初 学者很好地避免一上手就要面对复杂的理论,它安装好了就可以使用了,用很小 的学习成本就可以变得很有生产力。
#### Java API
Elasticsearch 内置了两个客户端,你可以在你的代码 中使用:
＊ 节点客户端: 节点客户端以一个 无数据节点 的身份加入了一个集群。换句话 说,它自身是没有任何数据的,但是他知道什么数据在集群中的哪一个节点上, 然后就可以请求转发到正确的节点上并进行连接。
＊ 传输客户端: 更加轻量的传输客户端可以被用来向远程集群发送请求。他并不加 入集群本身,而是把请求转发到集群中的节点。
这两个客户端都使用 Elasticsearch 的 传输 协议,通过9300端口与 java 客户 端进行通信。集群中的各个节点也是通过9300端口进行通信。如果这个端口被 禁止了,那么你的节点们将不能组成一个集群。

###### 建立连接
```
TransportClient client = TransportClient.builder()
    .build()
    .addTransportAddress(new InetSocketTransportAddress(InetAddress
    .getByName("localhost"), 9300));       
    
```
###### Index API 创建 Index 并且插入 Document
创建索引有很多种方法，这里列举常用的 2 种：
```
HashMap<String, Object> json = new HashMap<String, Object>();
json.put("first_name","Shuang");
json.put("last_name", "Peng");
json.put("age", 24);
json.put("about", "I love coding");
IndexResponse response = client
    .prepareIndex("tseg","students","1")
    .setSource(json).get();

IndexResponse response = client.prepareIndex("tseg","students","1")
   .setSource(jsonBuilder()
   .startObject()
   .field("first_name", "Shuang")
   .field("first_name", "Peng")
   .field("age", 24)
   .field("about", "I love coding")
   .endObject())
   .get();
```
注意：Index API 只能用于创建 index，类似于关系型数据库里面的 create table，他不能对已有的数据库进行添加。追加操作可以用后面会提到的 Update 或者 Bulk 来完成。
###### Get API 获取 Document
```
GetResponse response2 = client.prepareGet("tseg", "students", "1").get();
Map<String, Object> res = response2.getSource();
for (Map.Entry<String, Object> entry: res.entrySet()){
     System.out.println(entry.getKey() + " : " + entry.getValue());
     }
```
###### Delete API 删除 Index 或者 Document
```
// 用来删除对应的 document 
DeleteResponse response3 = 
    client.prepareDelete("tesg","students","1").get();
// 用来删除对应的 index
DeleteIndexResponse response4 = 
    client.admin().indices().prepareDelete("facebook").execute().actionGet();
```
###### Update API 更新操作
更新操作也有两种方法。建议使用第一种，第二种太复杂了。。。看看就好。

第一种
```
client.prepareUpdate("tseg", "students", "1")
    .setDoc(jsonBuilder()
    .startObject().field("age", 32)
    .endObject())
    .get();
```
第二种
```
IndexRequest indexRequest = new IndexRequest("tseg", "students", "1")
    .source(jsonBuilder()
    .startObject()
    .field("first_name", "Shuang")
    .field("last_name", "Peng")
    .field("age", 32)
    .field("about", "I loving coding")
    .endObject());

UpdateRequest updateRequest = new UpdateRequest("tseg","students", "1")
    .doc(jsonBuilder()
    .startObject().field("age", 32)
    .endObject())
    .upsert(indexRequest);
 client.update(updateRequest).get();
```
不过这里提一下第二种方法，如果对应的 field 不存在的话，则更新操作自动变为插入操作，否则，就是正常的修改操作。

###### Multi Get API 多查找
MultiGetResponse API 可以一次返回多个要查找的值。下面介绍了两种方法，一种是返回一个 Map，我们可以按照不同的 field 取值；第二种方法是直接返回一个字符串（Json格式）。
```
MultiGetResponse multiGetItemResponses = client.prepareMultiGet()
    .add("tseg", "students", "1", "2", "3").get();

for (MultiGetItemResponse itemResponses : multiGetItemResponses) {
    GetResponse response5 = itemResponses.getResponse();
    if (response5.isExists()) {
    
// 第一种用法
    Map<String, Object> fields = response5.getSource();
    System.out.println(fields.get("first_name"));

// 第二种用法
    String json2 = response5.getSourceAsString();
    System.out.println(json2);
}
```
###### Bulk API 批量操作
Bulk API允许批量提交index和delete请求， 如下：
```
BulkRequestBuilder bulkRequest = client.prepareBulk();
bulkRequest.add(client.prepareIndex("tseg", "students", "1")
           .setSource(jsonBuilder()
           .startObject()
           .field("first_name", "Allen")
           .field("last_name", "Peng")
           .field("age", "22")
           .endObject()))
           .get();
           
bulkRequest.add(client.prepareIndex("tseg", "students", "2"))
            .setSource(jsonBuilder()
            .startObject()
            .field("first_name", "Hou")
            .field("last_name", "Xue")
            .field("age", "30")
            .endObject()))
            .get();

HashMap<String, Object> json2 = new HashMap<String, Object>();
List<String> list = new ArrayList<String>();
list.add("music");
list.add("football");
json2.put("first_name", "Peng");
json2.put("last_name", "Peng");
json2.put("interests", list);
BulkRequestBuilder bulkRequest2 = client.prepareBulk();

// 两种执行方法，个人倾向于第一种
bulkRequest2.add(client.prepareIndex("facebook", "info", 
    "3").setSource(json2)).get();
// 第二种方法
bulkRequest2.add(client.prepareIndex("facebook", 
    "info","1").setSource(json2)).execute().actionGet();
```
还可以这样做：
```
BulkRequestBuilder bulkRequest = client.prepareBulk();
bulkRequest.add(client.prepareIndex("index1", "type1", "id1")
    .setSource(source);
bulkRequest.add(client.prepareIndex("index2", "type2", "id2")
    .setSource(source);
BulkResponse bulkResponse = bulkRequest.execute().actionGet();
```
###### Bulk Processor API 可在批量操作完成之前和之后进行相应的操作
```
BulkProcessor bulkProcessor = BulkProcessor.builder(
        client,  
        new BulkProcessor.Listener() {
            @Override
            public void beforeBulk(long executionId,
                                  BulkRequest request) { ... } 

            @Override
            public void afterBulk(long executionId,
                                  BulkRequest request,
                                  BulkResponse response) { ... } 

            @Override
            public void afterBulk(long executionId,
                                  BulkRequest request,
                                  Throwable failure) { ... } 
        })
        .setBulkActions(10000) 
        .setBulkSize(new ByteSizeValue(1, ByteSizeUnit.GB)) 
        .setFlushInterval(TimeValue.timeValueSeconds(5)) 
        .setConcurrentRequests(1) 
         .build();
         
bulkProcessor.add(new IndexRequest("index1", "type1", "id1").source(source1));  
bulkProcessor.add(new DeleteRequest("index2", "type2", "id2"); 
```
＊ beforeBulk 会在批量提交之前执行，可以从 BulkRequest 中获取请求信息request.requests() 或者请求数量 request.numberOfActions()。
＊ 第一个 afterBulk 会在批量成功后执行，可以跟 beforeBulk 配合计算批量所需时间。
＊ 第二个 afterBulk 会在批量失败后执行。
＊ 在例子中，当请求超过 10000 个（default=1000）或者总大小超过1GB（default=5MB）时，触发批量提交动作。

