Retrofit2.0学习
>目标文章[这是一份很详细的 Retrofit 2.0 使用教程（含实例讲解）](https://blog.csdn.net/carson_ho/article/details/73732076)
### 前言
在Android开发中，网络请求非常常用。<br>

而在Android的网络请求中，[Retrofit](https://github.com/square/retrofit)是当下最热的一个网络请求库。

**目录**<br>
![目录](https://github.com/liuhuan2015/LearnRetrofit/blob/master/images/mulu.png)<br>

### 一 . 简介
一个Restful的Http网络请求框架（基于Okhttp）<br>

作者：Square<br>

功能:<br>
 * 基于Okhttp，遵循Restful Api设计风格
 * 通过注解配置网络请求参数
 * 支持同步 & 异步网络请求
 * 支持多种数据的解析 & 序列化格式（Gson，Json，Xml，Protobuf）
 * 提供对Rxjava的支持

优点：
 * 功能强大：支持同步 & 异步；支持多种数据结构的解析 & 序列化格式；支持Rxjava
 * 简洁易用：通过注解配置网络请求参数，采用大量设计模式简化使用
 * 可扩展性好：功能模块高度封装、解耦彻底，如自定义Converters等
 
准确的说：Retrofit是一个Restful的Http网络请求框架的封装。<br>
 
因为其网络请求的工作本质上是由Okhttp完成的，Retrofit仅负责网络请求接口的封装,网络请求流程图如下<br>

![目录](https://github.com/liuhuan2015/LearnRetrofit/blob/master/images/request-flow.png)<br>

* App应用程序通过Retrofit请求网络，实际上是使用的Retrofit接口层封装请求参数、Header、Url等信息，之后由Okhttp完成后续的请求操作
* 在服务端返回数据之后，Okhttp将原始的结果交给Retrofit，Retrofit根据用户的需求对结果进行解析

比Retrofit出现的较早的有名的网络请求框架还有XUtils，Android-Async-Http，Volley，Okhttp。它们之间的比较这里就不写了。<br>

### Retrofit使用介绍
#### 1 . 在Gradle文件中添加相关依赖
因为Retrofit是基于Okhttp，所以一般我们还需要添加Okhttp库依赖。
```java
    //retrofit
    implementation 'com.squareup.retrofit2:retrofit:2.4.0'
    //okhttp
    implementation 'com.squareup.okhttp3:okhttp:3.11.0'
```
在AndroidManifest.xml文件中添加网络请求权限。<br>
```java
<uses-permission android:name="android.permission.INTERNET"/>
```
#### 2 . 创建接收服务器返回数据的类
这些JavaBean类一般是根据接口文档和服务器返回的数据格式来定义的，服务器返回的数据格式一般是json格式，我们可以使用GsonFormat插件来自动生成JavaBean。

#### 3 . 创建用于描述网络请求的接口
Retrofit将**Http请求** 抽象成**Java接口**，使用**注解**描述网络请求参数和配置网络请求参数
* 使用动态代理动态将接口的注解“翻译”成一个Http请求，然后执行Http请求
* 接口中每个方法的参数都需要使用注解标注，否则会报错
```java
/**
* 接口定义
*/
public interface GirlsService {

    @GET("api/data/{type}/{count}/{page}")
    Observable<GirlsBean> getGirls(@Path("type") String type, @Path("count") int count,
                                   @Path("page") int page);
}

/**
* 接口使用
*/
class DataSource{
        GirlsRetrofit.getRetrofit()
                    .create(GirlsService.class)
                    .getGirls("福利", size, page)
                    .subscribeOn(Schedulers.io())
                    .observeOn(AndroidSchedulers.mainThread())
                    .subscribe(new Observer<GirlsBean>() {
                        @Override
                        public void onCompleted() {
    
                        }
    
                        @Override
                        public void onError(Throwable e) {
                            callback.onDataNotAvailable();
                        }
    
                        @Override
                        public void onNext(GirlsBean girlsBean) {
                            callback.onGirlsLoaded(girlsBean);
                        }
                    });
}
```
注解类型，见图：<br>
![注解类型](https://github.com/liuhuan2015/LearnRetrofit/blob/master/images/zhujie-type.png)<br>

注解说明：<br>
**第一类：网络请求方法**
网络请求方法有@GET，@POST，@PUT，@DELETE，@PATH，@HEAD，@OPTIONS，@HTTP八种，<br>

前面七种方法分别对应Http中的网络请求方法，都接收一个网络地址Url（也可以不指定，通过@HTTP来设置）。<br>

@HTTP：用于替换前面七种注解 以及 进行一些功能拓展

![网络请求方法](https://github.com/liuhuan2015/LearnRetrofit/blob/master/images/http-request-method.png)<br>

@HTTP的使用：<br>
```java
/**
     * method：网络请求的方法（区分大小写）
     * path：网络请求地址路径
     * hasBody：是否有请求体
     */
    @HTTP(method = "GET", path = "blog/{id}", hasBody = false)
    Call<ResponseBody> getCall(@Path("id") int id);
    // {id} 表示是一个变量
    // method 的值 retrofit 不会做处理，所以要自行保证准确
```
**第二类：标记**
![网络请求标记](https://github.com/liuhuan2015/LearnRetrofit/blob/master/images/http-request-flag.png)<br>

标记有三种：
* @FormUrlEncoded ：表示请求体是一个Form表单
* @Multipart ：表示请求体是一个支持文件上传的Form表单
* @Streaming ：表示返回的数据以流的形式返回；适用于返回数据较大的场景；（如果没有使用该注解，默认把数据全部载入内存；之后获取数据也是从内存中读取）












 



