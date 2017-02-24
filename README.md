# router
android下通过Schema来跳转的库


思维导图
http://naotu.baidu.com/file/7530ad7cddba222739d605529237509a

通过注解把StartActivity()彻底干掉
通过schema来解析需要跳转的类,并且把schema里面的参数通过注入的方式直接赋值给类里面的字段，基本类型都是ok的，如果
遇到对象的话，那么采用json序列化反序列化的方式来解析，传递，注入数据

类图 草图
Router.class
->Router{

    //设置schema前缀，类似http这样
    init("taobao"){}

    //单例
    private static Router getInstance()

    //跳转
    public static void action(String schema)

    //跳转--需要本地参数的，这些参数（extra）不能实现自动注入，需要用户手动去解析，这种情况不多
    public static void action(String schema，Map<String,Object> extra)

    //对需要传递的参数的依赖注入，类似ButterKnife.inject
    public static void inject(Object target);

}

//注解
@interface Router{
    //跳转对象的名字
    String name;
}

@interface Param{
    //跳转对象Bundle中key的名字
    String name;
}
//此注解控制需要登录的界面的---例如未登录下点击则先登录，登录成功后自动跳转到所需的页面
@interface NeedLogin{

}

----------其他类和apt生成的类暂不定，等待需求完善

用户使用
1.
class MyApplication  extends Application{
    public void onCreate(){
        Router.init("taobao");
    }
}
2.
@Router("main")
class MainActivity{

    @Param("paramUserId")
    public String userId;

    public void onCreate(){
        Router.inject(this);
    }
}
3.使用
findViewById(R.id.button).setOnClickListener(v->{
    //schema是这个taobao://main?paramUserId='10000'
    //这个schema可以由服务器下发
    Router.action("taobao://main?paramUserId='10000'");
});


期待有人一起来完善和开发

