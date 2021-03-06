### 添加依赖


$lastVersion = [![](https://jitpack.io/v/xwdz/OkHttpUtils.svg)](https://jitpack.io/#xwdz/OkHttpUtils)


```

implementation 'com.xwdz:okHttpUtils:$lastVersion'
implementation 'com.squareup.okhttp3:okhttp:3.5.0'

or

compile 'com.xwdz:okHttpUtils:$lastVersion'
compile 'com.squareup.okhttp3:okhttp:3.5.0'

```


### 提供两种获取OkHttpManager方法

```
1. 直接实例化使用内置配置

    OkHttpManager okHttpManager = new OkHttpManager();

    默认配置如下:
    
    public OkHttpManager() {
        HttpLoggingInterceptor logInterceptor = new HttpLoggingInterceptor(new HttpLog("XHttp"));
        logInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
        mOkHttpClient = new OkHttpClient.Builder()
                .writeTimeout(20, TimeUnit.SECONDS)
                .readTimeout(20, TimeUnit.SECONDS)
                .addInterceptor(logInterceptor)
                .writeTimeout(20, TimeUnit.SECONDS).build();
    }
    
    
2.  自定义一些简单配置

okHttpManager = new OkHttpManager.Builder()
                   .addInterceptor(interceptor)
                   .addNetworkInterceptor(interceptor)
                   .readTimeout(long readTimeout, TimeUnit timeUnit)
                   .connectTimeout(long connectTimeout, TimeUnit timeUnit)
                   .writeTimeout(long writeTimeout, TimeUnit timeUnit)
                   .build();
                   
2.1 如果以上配置不够可直接传入开发者自定义buidler
okHttpManager = new OkHttpManager.Builder()
                .newBuilder(OkHttpClient.Builder builder)
                .build();
                   
     
```



### 特性

- 支持自动解析JSON返回实体类
- UI线程回调(设置`setCallBackToMainUIThread(true)`则回调到主线程,反之则在子线程)
- 支持文件下载
- 支持Activity/fragment绑定生命周期

## 请求

### Get

	 okHttpManager.get("https://api.github.com/search/users")
	                .tag(MainActivity.class.getName())
                    .addParams("q", "a")
                    .addParams("page", "1")
                    .addParams("per_page", "10")
                    .callbackMainUIThread(true)
                    .execute(new JsonCallBack<Response<List<User>>>() {
                        @Override
                        public void onSuccess(Call call, Response<List<User>> response) {
                            for (User item : response.items) {
                                Log.e("XHttp", "res = " + item.toString());
                            }
                        }
    
                        @Override
                        public void onFailure(Call call, Exception e) {
    
                        }
                    });


### POST
	
	 okHttpManager.post("https:xxx")
	                     .tag(MainActivity.class.getName())
                         .addParams("q", "xwdz")
                         .addParams("page", "1")
                         .addParams("per_page", "10")
                         .callbackMainUIThread(true)
                         .execute(new JsonCallBack<Response<List<User>>>() {
                             @Override
                             public void onSuccess(Call call, Response<List<User>> response) {
                                 for (User item : response.items) {
                                     Log.e("XHttp", "res = " + item.toString());
                                 }
                             }
         
                             @Override
                             public void onFailure(Call call, Exception e) {
         
                             }
                         });

##### 取消一个请求

调用okHttpManager.cancel(tag) 方法，参数tag即是标记request的一个tag

```

okHttpManager.cancel(MainActivity.class.getName());

```

##### 取消全部请求

```

okHttpManager.cancelAll();

```


### 默认支持Callback如下

- StringCallBack
- JsonCallBack
- FileCallBack

### 如需要其他解析扩展，继承AbstractCallBack 即可

**比如**


	public abstract class StringCallBack extends AbstractCallBack<String> {
	
	    @Override
	    protected String parser(Call call, Response response) throws IOException {
	        final String result = response.body().string();
	        onSuccess(call, result);
	        return result;
	    }
	
	    protected abstract void onSuccess(Call call, String response);
	}











