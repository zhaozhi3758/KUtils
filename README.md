KUtils(如下所有代码来源第三方,只做代码搬运工) 
========
android快速开发常用第三方库整合,集成了优雅的日志打印(可自动格式化json,xml,日志输出无字符长度4000的限制),两行代码调用EventBus事件分发,okgo网络访问一行代码实现文件上传下载带进度,上送json xml 等参数,可设置缓存模式以及SSL认证等,万能的RecyclerView适配器BaseQuicklyAdapter,实现上啦刷新,下拉加载,item不同布局,一行代码设置头布局和脚布局.瀑布流,多媒体选择库,photoview图片预览,activity管理类,android端加解密工具类,CircleImageView圆角用户头像,android6.0权限检测


 # 一. 新增KLog 日志打印
 ## 使用方式:
 ```Java
     //全局只需初始化一次
     KLog.init(BuildConfig.LOG_DEBUG, "KLog");

     KLog.d("");
     KLog.xml("");//打印xml数据 自动格式化   看图
     KLog.json("");//打印json数据 自动格式化 看图
  ```
  ## 效果图
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/KLogImage.png)

# 二.新增EventBus事件分发

## 使用方式:
```Java
        //注册监听
         EventBus.getDefault().register(this);
         //发出事件
         EventBus.getDefault().post(new User("张三",26),"张三");
          //接收事件
         @Subscriber(tag = "张三", mode = ThreadMode.MAIN)
             public void OnEventBus_ZhangSan(User user) {
                 KLog.d("EventBus使用tag张三接收到User:" + user);
             }
          //注销事件
         EventBus.getDefault().unregister(this);
```
## 效果图
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/MainAty.png)
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/TwoAty.png)


# 三. 新增okgo网络访问

## 使用方式:
```Java
  OkHttpClient.Builder builder = new OkHttpClient.Builder();
          HttpLoggingInterceptor loggingInterceptor = new HttpLoggingInterceptor("OkGo");
  //log打印级别，决定了log显示的详细程度
          loggingInterceptor.setPrintLevel(HttpLoggingInterceptor.Level.BODY);
  //log颜色级别，决定了log在控制台显示的颜色
          loggingInterceptor.setColorLevel(Level.INFO);
          builder.addInterceptor(loggingInterceptor);
  //第三方的开源库，使用通知显示当前请求的log
          builder.addInterceptor(new ChuckInterceptor(this));
          //全局的读取超时时间
          builder.readTimeout(OkGo.DEFAULT_MILLISECONDS, TimeUnit.MILLISECONDS);
  //全局的写入超时时间
          builder.writeTimeout(OkGo.DEFAULT_MILLISECONDS, TimeUnit.MILLISECONDS);
  //全局的连接超时时间
          builder.connectTimeout(OkGo.DEFAULT_MILLISECONDS, TimeUnit.MILLISECONDS);
          //使用sp保持cookie，如果cookie不过期，则一直有效
          builder.cookieJar(new CookieJarImpl(new SPCookieStore(this)));
  //使用数据库保持cookie，如果cookie不过期，则一直有效
          builder.cookieJar(new CookieJarImpl(new DBCookieStore(this)));
  //使用内存保持cookie，app退出后，cookie消失
          builder.cookieJar(new CookieJarImpl(new MemoryCookieStore()));
  
          //方法一：信任所有证书,不安全有风险
  //        HttpsUtils.SSLParams sslParams1 = HttpsUtils.getSslSocketFactory();
  //方法二：自定义信任规则，校验服务端证书
  //        HttpsUtils.SSLParams sslParams2 = HttpsUtils.getSslSocketFactory(new SafeTrustManager());
  //方法三：使用预埋证书，校验服务端证书（自签名证书）
  //        HttpsUtils.SSLParams sslParams3 = HttpsUtils.getSslSocketFactory(getAssets().open("srca.cer"));
  //方法四：使用bks证书和密码管理客户端证书（双向认证），使用预埋证书，校验服务端证书（自签名证书）
  //        HttpsUtils.SSLParams sslParams4 = HttpsUtils.getSslSocketFactory(getAssets().open("xxx.bks"), "123456", getAssets().open("yyy.cer"));
  //        builder.sslSocketFactory(sslParams1.sSLSocketFactory, sslParams1.trustManager);
  //配置https的域名匹配规则，详细看demo的初始化介绍，不需要就不要加入，使用不当会导致https握手失败
  //        builder.hostnameVerifier(new SafeHostnameVerifier());
  
          //---------这里给出的是示例代码,告诉你可以这么传,实际使用的时候,根据需要传,不需要就不传-------------//
  //        HttpHeaders headers = new HttpHeaders();
  //        headers.put("commonHeaderKey1", "commonHeaderValue1");    //header不支持中文，不允许有特殊字符
  //        headers.put("commonHeaderKey2", "commonHeaderValue2");
  //        HttpParams params = new HttpParams();
  //        params.put("commonParamsKey1", "commonParamsValue1");     //param支持中文,直接传,不要自己编码
  //        params.put("commonParamsKey2", "这里支持中文参数");
  //-------------------------------------------------------------------------------------//
  
          OkGo.getInstance().init(this)                       //必须调用初始化
                  .setOkHttpClient(builder.build());             //设置OkHttpClient
  //                .setCacheMode(CacheMode.NO_CACHE)               //全局统一缓存模式，默认不使用缓存，可以不传
  //                .setCacheTime(CacheEntity.CACHE_NEVER_EXPIRE)   //全局统一缓存时间，默认永不过期，可以不传
  //                .setRetryCount(3) //全局统一超时重连次数，默认为三次，那么最差的情况会请求4次(一次原始请求，三次重连请求)，不需要可以设置为0
  //                .addCommonHeaders(headers)                      //全局公共头
  //                .addCommonParams(params);                       //全局公共参数
    //简单请求
 OkGo.<User>post("url").params("key", "v").execute(new AbsCallback<User>() {
            @Override
            public User convertResponse(Response response) throws Throwable {
                return null;
            }

            @Override
            public void onSuccess(com.lzy.okgo.model.Response<User> response) {

            }
        });
```
- [OkGo详细使用文档](https://github.com/devzwy/KUtils/blob/master/Word/README_OKGO.md)

# 四. 新增BaseQuicklyAdapter

## 使用方式:
```Java
    //定义适配器
    public abstract class MyAdapter extends BaseQuickAdapter<MainTab,BaseViewHolder> {
    
    
        public MyAdapter(@LayoutRes int layoutResId, @Nullable List<MainTab> data) {
            super(layoutResId, data);
        }
    }
    
    //初始化
     private MyAdapter mAdapter = new MyAdapter(R.layout.item_main_tab, null) {
            @Override
            protected void convert(BaseViewHolder helper, MainTab item) {
                helper.setText(R.id.tv_tab, item.getTabName());
                helper.addOnClickListener(R.id.tv_tab);
            }
        };
    
    //设置适配器及填充数据
      mRvMain.setLayoutManager(new LinearLayoutManager(this));//设置rv布局走向
            mAdapter.isFirstOnly(false);//item的加载动画是否仅在第一次加载时生效
            mAdapter.openLoadAnimation(BaseQuickAdapter.ALPHAIN);//设置加载动画
            mRvMain.setAdapter(mAdapter);
    
            //添加数据
            List<MainTab> l = new ArrayList<>();
            l.add(new MainTab("测试EventBus事件分发", 0));
            l.add(new MainTab("新功能1", 1));
            l.add(new MainTab("新功能2", 2));
            l.add(new MainTab("新功能3", 3));
            l.add(new MainTab("新功能4", 4));
    
            mAdapter.setNewData(l);
    
            //处理item点击事件
            mRvMain.addOnItemTouchListener(new OnItemChildClickListener() {
                @Override
                public void onSimpleItemChildClick(BaseQuickAdapter adapter, View view, int position) {
                    //item中的单控件点击事件
                    switch (mAdapter.getData().get(position).getId()) {
                        case 0:
                            //测试EventBus事件分发
                            startActivity(new Intent(MainActivity.this, TwoActivity.class));
                            break;
                        case 1:
    
                            break;
                        case 2:
    
                            break;
                        case 3:
    
                            break;
                    }
                }
            });
            mRvMain.addOnItemTouchListener(new OnItemClickListener() {
                @Override
                public void onSimpleItemClick(BaseQuickAdapter adapter, View view, int position) {
                    //item的点击事件
    
                }
            });
```

- [BaseQuicklyAdapter详细使用文档](https://github.com/devzwy/KUtils/raw/master/Word/README_BaseQuicklyAdapter)


# 五. 新增Glide加载图片 一行代码

## 实现代码
```Java
     private void initAdapter() {
            mAdapter = new MyAdapter(null);
            mAdapter.isFirstOnly(false);
            mAdapter.openLoadAnimation(BaseQuickAdapter.SCALEIN);
            mRvMain.setLayoutManager(new StaggeredGridLayoutManager(3, LinearLayoutManager.VERTICAL));
            mRvMain.setAdapter(mAdapter);
    
            List<String> l = new ArrayList<>();
            for (int i = 0; i < 80; i++) {
                if (i % 2 == 0) {
                    l.add("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1496372638951&di=be636f1362f06d68b902b8115cda13dc&imgtype=0&src=http%3A%2F%2Fd.5857.com%2Fxgs_150428%2Fdesk_005.jpg");
                } else if (i % 3 == 0) {
                    l.add("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1496373786557&di=bc8e94b8e82224a71ea7ea518d06e9ec&imgtype=0&src=http%3A%2F%2Fh7.86.cc%2Fwalls%2F20151020%2F1024x768_3fe2e5a70003597.jpg");
                } else if (i % 4 == 0) {
                    l.add("http://pic.qiantucdn.com/58pic/15/36/00/73b58PICgvY_1024.jpg!/fw/780/watermark/url/L3dhdGVybWFyay12MS4zLnBuZw==/align/center");
                } else {
                    l.add("https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1496373818671&di=d2fc21e4fbeec9792dfb53ff31c03093&imgtype=0&src=http%3A%2F%2Fimg17.3lian.com%2Fd%2Ffile%2F201701%2F23%2F10783ece0ff1eb32bf137ff9b7ba329e.jpg");
                }
                heightList.add(new Random().nextInt(300) + 200);
            }
            mAdapter.setNewData(l);
        }



        private class MyAdapter extends BaseQuickAdapter<String, BaseViewHolder> {
    
            public MyAdapter(@Nullable List<String> data) {
                super(R.layout.item_girls, data);
            }
    
            @Override
            protected void convert(BaseViewHolder helper, String item) {
                ImageView imageView = (ImageView) helper.getView(R.id.iv);
                //设置高度
                int height = heightList.get(helper.getPosition());
                //得到控件的高度
                ViewGroup.LayoutParams layoutParams = (imageView).getLayoutParams();
                //设置高度
                layoutParams.height = height;
                //使用Glide加载图片 placeholder参数为加载失败时显示的默认图片
                Glide.with(BeautyPicturesActivity.this).load(item).placeholder(R.mipmap.ic_launcher).into(imageView);
            }
        }
```
## 使用RecyclerView和BaseQuickAdapter实现的瀑布流效果图 调用请查看sample 几行代码调用(Glide加载图片只需一行代码即可)
- 瀑布流效果图<br />
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/cccc.png)

# 六.新增多媒体选择库
## 使用方式:
#### 1 在项目根目录下的build.gradle文件中添加一行代码
```Java
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }//添加这行代码
    }
}
```
#### 2 将sample中photoinfos包下的activity copy到项目中
#### 3 在AndroidManifest文件添加如下activity
```Java
    <!--图片选择库相关aty-->
            <activity
                android:name="cn.kutils.boxing.impl.ui.BoxingActivity"
                android:launchMode="singleTop"
                android:screenOrientation="portrait"
                android:theme="@style/Boxing.AppTheme.NoActionBar">
            </activity>
    
            <activity
                android:name="cn.kutils.boxing.impl.ui.BoxingViewActivity"
                android:launchMode="singleTop"
                android:screenOrientation="portrait"
                android:theme="@style/Boxing.AppTheme.NoActionBar">
            </activity>
    
            <activity
                android:name="cn.kutils.boxing.impl.ui.BoxingBottomSheetActivity"
                android:screenOrientation="portrait"
                android:theme="@style/Boxing.AppTheme.NoActionBar"/>
    
            <activity
                android:name="com.yalantis.ucrop.UCropActivity"
                android:screenOrientation="portrait"
                android:theme="@style/Theme.AppCompat.Light.NoActionBar"/>
    
            <provider
                android:name="android.support.v4.content.FileProvider"
                android:authorities="${applicationId}.file.provider"
                android:exported="false"
                android:grantUriPermissions="true">
    
                <meta-data
                    android:name="android.support.FILE_PROVIDER_PATHS"
                    android:resource="@xml/boxing_file_provider"/>
    
            </provider>

```
#### 4 在Application中进行相关初始化(全局只需初始化一次即可)
```Java
   @Override
    public void onCreate() {
        super.onCreate();
        IBoxingMediaLoader loader = new BoxingFrescoLoader(this);
        BoxingMediaLoader.getInstance().init(loader);
        BoxingCrop.getInstance().init(new BoxingUcrop());
    }

```
#### 5 打开多媒体选择器
- 打开图片选择器(多选不带裁剪功能)
```Java
                //构造配置参数
                BoxingConfig config = new BoxingConfig(BoxingConfig.Mode.MULTI_IMG); // Mode：Mode.SINGLE_IMG, Mode.MULTI_IMG, Mode.VIDEO
                config.needCamera(R.mipmap.ic_launcher_round).needGif().withMaxCount(9); // 支持gif，相机，设置最大选图数
//            .withMediaPlaceHolderRes(resInt) // 设置默认图片占位图，默认无
//            .withAlbumPlaceHolderRes(resInt) // 设置默认相册占位图，默认无
//             .withVideoDurationRes(resInt) // 视频模式下，时长的图标，默认无
//                初始化Boxing，构造Intent并启动
                Boxing.of(config).withIntent(this, BoxingActivity.class).start(this, REQUESTCODE_1);
```

- 打开图片选择器(单选带裁剪功能)
```Java

        String cachePath = BoxingFileHelper.getCacheDir(this);
                        if (TextUtils.isEmpty(cachePath)) {
                            Toast.makeText(getApplicationContext(), R.string.boxing_storage_deny, Toast.LENGTH_SHORT).show();
                            return;
                        }
                        Uri destUri = new Uri.Builder()
                                .scheme("file")
                                .appendPath(cachePath)
                                .appendPath(String.format(Locale.US, "%s.jpg", System.currentTimeMillis()))
                                .build();
                        BoxingConfig singleCropImgConfig = new BoxingConfig(BoxingConfig.Mode.SINGLE_IMG).withCropOption(new BoxingCropOption(destUri))
                                .withMediaPlaceHolderRes(R.mipmap.ic_launcher_round);
                        Boxing.of(singleCropImgConfig).withIntent(this, BoxingActivity.class).start(this, REQUESTCODE_3);
```
- 打开视频选择器
```Java
      //启动视频选择
                    BoxingConfig videoConfig = new BoxingConfig(BoxingConfig.Mode.VIDEO).withVideoDurationRes(R.mipmap.ic_launcher_round);
                    Boxing.of(videoConfig).withIntent(this, BoxingActivity.class).start(this, REQUESTCODE_2);
```
#### 6 取结果
```Java

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            //
            List<BaseMedia> medias = Boxing.getResult(data);
            //注意判断null
        }
```
- 效果图<br/>
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/imageselector.gif)</br>

# 七.新增photoview图片预览
## 使用方式同ImageView:
- 定义方式1
```Java
    <com.github.chrisbanes.photoview.PhotoView
        android:id="@+id/pv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:scaleType="fitXY"/>
```
- 定义方式2
```Java
        PhotoView photoView = new PhotoView(context);
        photoView.setScaleType(ImageView.ScaleType.FIT_XY);
```
#### 关于 PhotoView 异常捕获
```Java

    public class HackyProblematicViewGroup extends ProblematicViewGroup {
    
        public HackyProblematicViewGroup(Context context) {
            super(context);
        }
    
        @Override
        public boolean onInterceptTouchEvent(MotionEvent ev) {
            try {
                return super.onInterceptTouchEvent(ev);
            } catch (IllegalArgumentException e) {
                            //uncomment if you really want to see these errors
                //e.printStackTrace();
                return false;
            }
        }
}
```



# 八.新增activity管理类
## 使用方式:
##### 在BaseActivty的onCreate()和onDestroy()中添加如下代码:
```Java
    @Override
        protected void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.mediaaty);
            AppManager.getAppManager().addActivity(this);//将activity压入栈
     @Override
        protected void onDestroy() {
            super.onDestroy();
            AppManager.getAppManager().finishActivity(this);//将activity从栈取出
        }
```
##### 在退出应用时只需调用如下代码即可关闭所有页面
```Java
    AppManager.getAppManager().AppExit(this);
```

# 九.新增android端加解密工具类
## 使用方式 - RSA (生成密钥对 获取公钥私钥  加密  解密 )
- 生成密钥对  获取公钥私钥
```Java
     try {
                        Map<String, Object> keyPair = KRSAUtils.genKeyPair();
                        publicKey = KRSAUtils.getPublicKey(keyPair);
                        privateKey = KRSAUtils.getPrivateKey(keyPair);
                        append("密钥生成成功:\n公钥:" + publicKey + "\n私钥:" + privateKey);
                        KLog.json("publicKey:" + publicKey);
                        KLog.json("privateKey:" + privateKey);
    
                    } catch (Exception e) {
                        e.printStackTrace();
                        append("密钥对获取失败");
                    }
```
- 使用公钥加密
```Java
    try {
                        data_en = KRSAUtils.encryptByPublicKey(testData.getBytes(), publicKey);
                        append("加密成功,加密后的数据:\n" + KBase64Utils.encode(data_en));
                    } catch (Exception e) {
                        e.printStackTrace();
                        append("加密失败," + e
                                .toString());
                    }
```
- 使用私钥解密
```Java
      try {
                        data_de = KRSAUtils.decryptByPrivateKey(data_en, privateKey);
                        append("解密成功,解密结果:\n" + new String(data_de));
                    } catch (Exception e) {
                        e.printStackTrace();
                        append("解密失败," + e
                                .toString());
                    }
```
###其他使用请配合百度完成 - -
# 十.新增CircleImageView圆角用户头像
## 使用方式同ImageView:
```Java
       <cn.kutils.view.CircleImageView
                   android:id="@+id/civ"
                   android:layout_width="60dp"
                   android:layout_height="60dp"
                   android:layout_centerInParent="true"
                   app:border_color="@color/boxing_black1"
                   app:border_width="2dp"/>
```
+ 效果图 <br/>
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/ci.png)</br>

# 十一.新增android6.0权限检测
## 使用方式:
- 在清单文件配置所需activity
```Java
    <activity android:name="cn.kutils.permissionchecker.TedPermissionActivity"/>
```
- 开始检测权限
```Java
        new TedPermission(getApplicationContext())
                        .setPermissionListener(mPermissionListener)
                        .setDeniedMessage("您有未授予的权限，可能导致部分功能闪退，请点击\"设置\"授权相关权限")
                        .setPermissions(
        //                        Manifest.permission.VIBRATE,
        //                        Manifest.permission.ACCESS_COARSE_LOCATION,
        //                        Manifest.permission.ACCESS_FINE_LOCATION,
        //                        Manifest.permission.ACCESS_WIFI_STATE,
        //                        Manifest.permission.ACCESS_NETWORK_STATE,
        //                        Manifest.permission.CHANGE_WIFI_STATE,
                                Manifest.permission.READ_PHONE_STATE,
        //                        Manifest.permission.WRITE_EXTERNAL_STORAGE,
        //                        Manifest.permission.MOUNT_UNMOUNT_FILESYSTEMS,
        //                        Manifest.permission.READ_EXTERNAL_STORAGE,
        //                        Manifest.permission.READ_LOGS,
        //                        Manifest.permission.GET_TASKS,
        //                        Manifest.permission.SET_DEBUG_APP,
        //                        Manifest.permission.SYSTEM_ALERT_WINDOW,
        //                        Manifest.permission.GET_ACCOUNTS,
        //                        Manifest.permission.WRITE_SETTINGS,
        //                        Manifest.permission.RECORD_AUDIO,
        //                        Manifest.permission.WAKE_LOCK,
        //                        Manifest.permission.MODIFY_AUDIO_SETTINGS,
        //                        Manifest.permission.REQUEST_IGNORE_BATTERY_OPTIMIZATIONS,
                                Manifest.permission.CALL_PHONE)
                        .check();
                        
       //检测结果监听
       private PermissionListener mPermissionListener = new PermissionListener() {
            @Override
            public void onPermissionGranted() {
                KLog.d("全部权限已获取成功");
                //业务逻辑代码
            }
    
            @Override
            public void onPermissionDenied(ArrayList<String> deniedPermissions) {
                //deniedPermissions 为用户未授予的权限
                KLog.d("未授予的权限:" + deniedPermissions);
                //业务逻辑
            }
        };
```
- 未授予的权限日志输出:
```Java
    D/KLog: [ (MainActivity.java:191)#onPermissionDenied ] 未授予的权限:[android.permission.READ_PHONE_STATE, android.permission.CALL_PHONE]
```
- 效果图,依次弹出声明所需的权限</br>
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/t3.png)</br>
- 如果有声明的权限未授予时弹出如下页面
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/t1.png)</br>
- 点击设置按钮后自动跳转如下授权页面
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/t2.png)</br>
# 十二.新增带进度的button
## 使用方式:
```Java
<cn.kutils.view.progressbutton.AnimDownloadProgressButton
        android:id="@+id/anim_btn"
        android:layout_width="220dp"
        android:layout_height="45dp"
        android:focusable="true"
        android:focusableInTouchMode="true"
        app:progressbtn_backgroud_color="@color/colorAccent"
        app:progressbtn_backgroud_second_color="@color/ccc"
        app:progressbtn_text_size="25"/>
```
- 效果图<br/>
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/pbt.gif)</br>
 

# 十三.新增事件选择控件
## 使用方式:
- 时间日期选择  四种选择模式，年月日时分，年月日，时分，月日时分
```Java

     TimePickerView timePickerView = new TimePickerView(this, TimePickerView.Type.ALL);
                    timePickerView.setOnTimeSelectListener(new TimePickerView.OnTimeSelectListener() {
                        @Override
                        public void onTimeSelect(Date date) {
                            Toast.makeText(getApplicationContext(), new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(date), Toast.LENGTH_SHORT).show();
                        }
                    });
                    timePickerView.show();//四种选择模式，年月日时分，年月日，时分，月日时分  ALL, YEAR_MONTH_DAY, HOURS_MINS, MONTH_DAY_HOUR_MIN , YEAR_MONTH
```
- 效果图<br/>
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/date.png)</br>
- 自定义选择 可设置3级联动
```Java

    options1Items.add(new PriceBean("a"));
                    options1Items.add(new PriceBean("b"));
                    options1Items.add(new PriceBean("c"));
    
                    ArrayList<String> a_1
                            = new ArrayList<>();
                    a_1.add("a1");
                    a_1.add("a2");
                    a_1.add("a3");
                    options2Items.add(a_1);
                    ArrayList<String> b_1 = new ArrayList<>();
                    b_1.add("b1");
                    b_1.add("b2");
                    b_1.add("b3");
                    b_1.add("b4");
                    options2Items.add(b_1);
    
                    ArrayList<String> c_1 = new ArrayList<>();
                    c_1.add("c1");
                    c_1.add("c2");
                    c_1.add("c3");
                    c_1.add("c4");
                    c_1.add("c5");
                    c_1.add("c6");
                    c_1.add("c7");
                    options2Items.add(c_1);
    
                    pvOptions = new OptionsPickerView(this);
                    pvOptions.setPicker(options1Items, options2Items, true);
                    pvOptions.setTitle("XXXX");
                    pvOptions.setCyclic(false, false, false);
                    //设置默认选中的三级项目
                    //监听确定选择按钮
                    pvOptions.setSelectOptions(0, 0, 0);
                    pvOptions.setOnoptionsSelectListener(new OptionsPickerView.OnOptionsSelectListener() {
                        @Override
                        public void onOptionsSelect(int options1, int option2, int options3) {
                            Toast.makeText(getApplicationContext(), options1Items.get(options1).getPickerViewText(), Toast.LENGTH_SHORT).show();
                        }
                    });
                    pvOptions.show();
```
- 效果图<br/>
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/date2.png)</br>


# 十四.新增沉浸式状态栏设置
## 使用方式:
```Java
     //设置沉浸式状态栏
            SystemBarTintManager tintManager = new SystemBarTintManager(this);
            tintManager.setStatusBarTintEnabled(true);
            tintManager.setStatusBarTintResource(R.color.boxing_colorAccent);//通知栏所需颜色  
```



# 十五.新增九图预览
## 使用方式:
- 在application初始化图片加载器
```Java
     NineGridView.setImageLoader(new NineGridView.ImageLoader() {
                @Override
                public void onDisplayImage(Context context, ImageView imageView, String url) {
                    Glide.with(context).load(url).into(imageView);
                }
    
                @Override
                public Bitmap getCacheImage(String url) {
                    return null;
                }
            });
```
- 定义
```Java
        <cn.kutils.view.nineimages.NineGridView
            android:id="@+id/ng"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
```

- 填充数据

```Java
    List<ImageInfo> list = new ArrayList<>();
            for (int i = 0; i < medias.size(); i++) {
                list.add(new ImageInfo(medias.get(i).getPath(),medias.get(i).getPath()));
            }
            mNg.setAdapter(new NineGridViewClickAdapter(NineImagesAty.this, list));
```
- 效果图
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/nineimages.png)</br>

# 十六.新增鲁班图片压缩(仿微信朋友圈图片压缩算法)
## 使用方法:
- 一行代码完成压缩
```Java
    Luban.get(this).load(files).putGear(Luban.FIRST_GEAR).launch(new OnMultiCompressListener() {//FIRST_GEAR  CUSTOM_GEAR THIRD_GEAR
                @Override
                public void onStart() {
                    append("开始压缩,压缩时间会根据图片大小和数量相应增加");
                }
    
                @Override
                public void onSuccess(List<File> fileList) {
                    ArrayList<String> c = new ArrayList<>();
                    List<ImageInfo> list = new ArrayList<>();
                    for (int i = 0; i < fileList.size(); i++) {
                        c.add(String.valueOf(FileSizeUtil.getFileOrFilesSize(fileList.get(i).getPath(), SIZETYPE_MB))+ "Mb");
                        list.add(new ImageInfo(fileList.get(i).getPath(), fileList.get(i).getPath()));
                    }
                    append("张图片压缩完成,大小依次:" + c + "Mb");
                    append("设置到九图预览控件");
                    mNg.setAdapter(new NineGridViewClickAdapter(NineImagesAty.this, list));
                    mNg.setSingleImageSize(250);
                }
    
                @Override
                public void onError(Throwable e) {
                    append("压缩出错," + e.toString());
                }
    
            });
```
- [Luban压缩详细文档](https://github.com/devzwy/KUtils/blob/master/Word/README_LuBan.md)</br>
- 效果图
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/luban.png)</br>


# 十七.新增共享参数读写功能(不同进程也可以)
## 使用方法:
- 修改KUtils的build.gradle文件里代码 
```Java
    resValue "string", "tray__authority", "cn.kutils.sample" //将cn.kutils.sample更换为自己软件的包名
```
- 初始化
```Java
    AppPreferences appPreferences = new AppPreferences(getApplicationContext());
```
- 写入
```Java
     appPreferences.put("key", 999);//写入
```
- 读取
```Java
     String str = appPreferences.getString("key", "");//读取
```
# 十八.新增对话框进度条 一行代码显示
## 使用方法:
- 初始化 全局只需要初始化依次
```Java
    DialogUIUtils.init(this);
```
- 使用 详细请查看sample
```Java
     DialogUIUtils.show...
```
- 效果图
![image](https://github.com/devzwy/KUtils/raw/master/Screenshot/dialog.gif)</br>
### 同行共同探讨技术可加我 QQ3648415

   ##### 缺少:侧滑关闭页面   视频播放
