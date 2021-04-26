---
title: '''AndroidPart1'''
date: 2020-04-17 11:34:14
tags: 安卓开发
categories: 安卓
---

## 第一个应用

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.button).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
				//当被点击时出发的行为
                Toast.makeText(MainActivity.this,"爱你哦",Toast.LENGTH_LONG).show();
            }
        });
    }

}

=> Buile APK

## 产品提出了一些需求
新建一个应用
	有一个页面
	版本号为1.0.0
		D:\ASWork\app\build.gradle中修改
	        versionCode 1
			versionName "1.0.0"
	修改应用图标
		D:\ASWork\app\src\main\AndroidManifest.xml中修改android:icon="@mipmap/ic_launcher"
		首字母不可大写
	添加启动界面
-----------------------------------------------------------------------------
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/splashBackgroundColor"
    android:gravity="center">

    <TextView
		android:id="@+id/title_text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/splash_text"
        android:textColor="@color/white"
        android:textSize="24sp"/>

    <Button
        android:id="@+id/enter_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="@dimen/button_margin"
        android:text="@string/click"/>

</LinearLayout>	
--------------------------------------------------------------------	
	首页四个按钮,分别进入不同页面传递标题
		布局
		跳转
-------------------------------------------------------------------------		
public class SplashActivity extends Activity {

    //定义按钮开关
    private Button mEnterButton;
    //定义点击触发事件
    private View.OnClickListener mOnClickListener = new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            switch (v.getId()){
                case R.id.enter_button:
                    Intent intent = new Intent(SplashActivity.this,MainActivity.class);
                    startActivity(intent);
                    break;
            }
        }
    };

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_splash);
        mEnterButton = (Button)findViewById(R.id.enter_button);

        mEnterButton.setOnClickListener(mOnClickListener);
    }
}
-----------------------------------------------------------------------

## Activity

在启动页面停留一秒自动进入下一个页面
public class SplashActivity extends Activity {
    Handler mHandler = new Handler();
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_splash);

        //延迟1000ms后跳转
        mHandler.postDelayed(new Runnable() {
            @Override
            public void run() {
                //跳转到MainActivity
                Intent intent = new Intent(SplashActivity.this,MainActivity.class);
                startActivity(intent);
            }
        },1000);
    }
}



Activity间传递数据
SplashActivity
        //获取到textView
        TextView textView = (TextView) findViewById(R.id.title_text_view);
        final String title = textView.getText().toString();

        //延迟1000ms后跳转
        mHandler.postDelayed(new Runnable() {
            @Override
            public void run() {
                //跳转到MainActivity
                Intent intent = new Intent(SplashActivity.this,MainActivity.class);
                //把数据传递到下一个页面,先获取这个数据
                intent.putExtra("title",title);
                startActivity(intent);
            }
        },1000);
--------------------------------------------------------------------------------------
MainActivity
	    //接收数据,得到intent
        Intent intent = getIntent();
        if (intent != null){
            String title = intent.getStringExtra("title");
            setTitle(title);
        }

传递对象
//对象需要序列化
public class UserInfo implements Serializable {
    private String mUserName;
    private Integer mAge;

    public UserInfo(String mUserName, Integer mAge) {
        this.mUserName = mUserName;
        this.mAge = mAge;
    }
}
-------------------------------------------------------------
SplashActivity
            @Override
            public void run() {

                UserInfo userInfo = new UserInfo("CC",18);

                //跳转到MainActivity
                Intent intent = new Intent(SplashActivity.this,MainActivity.class);
                //把数据传递到下一个页面,先获取这个数据
                intent.putExtra("title",title);
                intent.putExtra("userInfo", userInfo);
                startActivity(intent);
            }
--------------------------------------------------------------------------------
MainActivity
        //接收数据,得到intent
        Intent intent = getIntent();
        if (intent != null){
            String title = intent.getStringExtra("title");

            //接收序列化之后的对象
            UserInfo userInfo = (UserInfo) intent.getSerializableExtra("userInfo");
            setTitle(userInfo.getmUserName());
        }
---------------------------------------------------------------


小问题：
intent.putExtra("title",title);
intent.putExtra("userInfo", userInfo);
将"title"，"userInfo"提取成常量，方便修改代码，ctrl+alt+C
    public static final String TITLE = "title";
    public static final String USER_INFO = "userInfo";
	
获取数据时，传递引用的常量
        //接收数据,得到intent
        Intent intent = getIntent();
        if (intent != null){
            String title = intent.getStringExtra(SplashActivity.TITLE);

            //接收序列化之后的对象
            UserInfo userInfo = (UserInfo) intent.getSerializableExtra(SplashActivity.USER_INFO);
            setTitle(userInfo.getmUserName());
        }
		

//ctrl+alt+F 快捷设置全局变量		

Activity回传数据


SplashActivity
//              startActivity(intent);
                //传递出去再接收回传数据
                startActivityForResult(intent, REQUEST_CODE);

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        Log.i(TAG,"requestCode:"+requestCode+",resultCode:"+resultCode);
        //04-09 03:56:47.971 4435-4435/com.example.sqtian I/SplashActivity: requestCode:9999,resultCode:1234
        //判断链接页面是否正确
        if(requestCode==REQUEST_CODE&&resultCode==MainActivity.RESULT_CODE){
            if(data != null){
                String title = data.getStringExtra(TITLE);
                mTextView.setText(title);
            }
        }

    }
----------------------------------------------------------------------------------------
MainActivity
        findViewById(R.id.button_first).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.putExtra(SplashActivity.TITLE,"我是主页,我送礼回来了");
                setResult(RESULT_CODE,intent);
                finish();
            }
        });
		
	
	
## Activity的生命周期		

onCreate()
onStart()
onResume()
onPause()
onStop()
onDestroy()
onRestart()
    @Override
    protected void onStart() {
        super.onStart();
        Log.i(TAG,"onStart");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.i(TAG,"onResume");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.i(TAG,"onPause");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.i(TAG,"onStop");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.i(TAG,"onDestroy");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.i(TAG,"onRestart");
    }
	

## 控件
控件View的通用属性:宽高, 颜色, 边距, 是否可见, 内容居中, 点击事件

TextView 显示文本 CheckedTextView

EditText 编辑框 hint password lines singlines maxlines
phoneNumber等

Button 点击按钮 .9图

ImageButton 图片按钮 extends ImageView

ImageView 图片视图
    <ImageView
        android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@mipmap/cc_background"
        android:scaleType="center"
        app:srcCompat="@mipmap/cc1" />

滑动条 
    <SeekBar
        android:id="@+id/seekBar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        />

进度条
    <ProgressBar
        android:id="@+id/progressBar"
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:max="100"
        android:progress="50"/>
		
WebView：加载网页
ListView：显示列表
GridView：显示表格式列表
ScrollView：内容可滚动视图
SufaceView：非常重要的绘图容器


## Android开发布局详解
五大布局
线性布局：LinearLayout
<LinearLayout
android:orientation="vertical" 垂直或者水平
<TextView
android:layout_weight="1" 设置占屏幕比重
相对布局：RelativeLayout
android:layout_alignParentRight="true" 在页面右边
android:layout_above="@+id/button" 在button的上面
android:layout_toRightOf="@+id/button" 在button的右面
android:layout_alignTop="@+id/button" 与button上边缘对齐
android:layout_marginLeft="100dp" 距离左边界100dp
android:paddingLeft="40dp" 内部文字距离边框40dp

帧布局：FrameLayout
可重叠

绝对布局：AbsoluteLayout
表格布局：TableLayout

布局技巧与优化
官方建议布局层次 10层
利用相对布局，尽量减少布局层次

布局引用相同部分
    <include layout="@layout/activity_splash"/>
减少视图层级
<merge/> 
需要时才加载
<ViewStub/>

总结:如何优化布局
1. 减少层次
2. 删除无用布局
3. 布局结构要清晰
4. 选择合适的布局
小技巧:
1. 不要嵌套多个使用layout_weight属性的LinearLayout
2. Android lint
3. HierarchyViewer

## 无比重要的ListView

常用属性：
	listSelector
	scrollingCache
	cacheColorHint
	fastScrollEnabled
	......
常用方法
	addHeaderView
	addFooterView
	
1. 设置ListView视图 activity_listview_demo.xml,list_view呈现列表数据
2. ListViewDemoActivity中获得list_view,利用phoneBookAdapter将数据与视图绑定
3. phoneBookAdapter继承BaseAdapter,getView方法返回视图
4. mLayoutInflater.inflate(R.layout.item_phone_book_friend, null)将页面解析为视图
5. 从解析的视图中获取TextView控键,和数据进行绑定


public class ListViewDemoActivity extends Activity {

    //ListView视图作为一个容器，房子
    private ListView mphoneBookListView;
    private List<UserInfo> mUserInfos;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_listview_demo);
        mphoneBookListView = (ListView)findViewById(R.id.list_view);
        mUserInfos = new ArrayList<>();
        mUserInfos.add(new UserInfo("西门吹雪", 18));
        mUserInfos.add(new UserInfo("李寻欢", 17));
        mUserInfos.add(new UserInfo("楚留香", 20));
        mUserInfos.add(new UserInfo("陆小凤", 19));



        //数据列表和数据绑定,adapter适配器，房子里的柜子，柜子放进房子
        final PhoneBookAdapter phoneBookAdapter = new PhoneBookAdapter(ListViewDemoActivity.this, mUserInfos);
        //房子(mphoneBookListView)里面有柜子(phoneBookAdapter)，柜子里面有格子(name_text_view)，
        //格子里面放数据private String[] mNames = {"西门吹雪","李寻欢"}
        mphoneBookListView.setAdapter(phoneBookAdapter);

        //刷新更新数据
        phoneBookAdapter.notifyDataSetChanged();
        //条目点击事件
        mphoneBookListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                //点击第一条数据时更新
                if(position==0){
                    // 新建另外一批数据
                    mUserInfos.clear();
                    mUserInfos.add(new UserInfo("我是新数据一",20));
                    mUserInfos.add(new UserInfo("我是新数据二",20));
                    mUserInfos.add(new UserInfo("我是新数据三",20));
                    // 替换掉老的数据,数据通过PhoneBookAdapter传递,因此要在PhoneBookAdapter中创建refreshData方法
                    // 刷新ListView,让他更新自己的视图
                    phoneBookAdapter.refreshData(mUserInfos);
                }


                Toast.makeText(ListViewDemoActivity.this, mUserInfos.get(position).getmUserName()+"被点击了",Toast.LENGTH_LONG).show();
            }
        });
        //长按事件
        mphoneBookListView.setOnItemLongClickListener(new AdapterView.OnItemLongClickListener() {
            @Override
            public boolean onItemLongClick(AdapterView<?> parent, View view, int position, long id) {
                Toast.makeText(ListViewDemoActivity.this, mUserInfos.get(position).getmUserName()+"被长按了",Toast.LENGTH_LONG).show();
                return false;
            }
        });

    }
}
---------------------------------------------------------------------------------------
D:\ASWork\app\src\main\res\layout\activity_listview_demo.xml
	<TextView
        android:id="@+id/phone_text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:text="@string/phone_book"/>


    <ListView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_below="@+id/phone_text_view"
        android:id="@+id/list_view"/>
		
--------------------------------------------------------------------------------
public class PhoneBookAdapter extends BaseAdapter {

    private Context mcontext;
    //解析layout
    private LayoutInflater mLayoutInflater;

    private List<UserInfo> mUserinfos = new ArrayList<>();


    public PhoneBookAdapter(Context mcontext, List<UserInfo> userInfos) {
        this.mcontext = mcontext;
        this.mUserinfos = userInfos;
        this.mLayoutInflater = (LayoutInflater) mcontext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    }

    @Override
    public int getCount() {
        // 有多少条数据
        return mUserinfos.size();
    }

    @Override
    public Object getItem(int position) {
        //返回某一条数据对象
        return mUserinfos.get(position);
    }

    @Override
    public long getItemId(int position) {

        return position;
    }

    /**
     * 每一行数据显示在界面,用户能够看到,就会调用一次geiView
     * @param position
     * @param convertView
     * @param parent
     * @return
     */
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        //返回一个视图, 柜子很多层，把数据塞进柜子，item_phone_book_friend

        ViewHolder viewHolder;
        //空则创建,非空则直接使用,提高代码复用率
        if(convertView == null){
            convertView = mLayoutInflater.inflate(R.layout.item_phone_book_friend, null);
            viewHolder = new ViewHolder();
            //把viewHolder存到convertView里
            convertView.setTag(viewHolder);


            //把名字数据塞进name_text_view
            //从实现保存的ViewHolder中获取控件
            viewHolder.nameTextView     = (TextView) convertView.findViewById(R.id.name_text_view);
            viewHolder.ageTextView      = (TextView) convertView.findViewById(R.id.age_text_view);
            viewHolder.avatarImageView = (ImageView) convertView.findViewById(R.id.avatar_image_view);
        }else{
            viewHolder = (ViewHolder)convertView.getTag();
        }

        //和数据进行绑定
        viewHolder.nameTextView.setText(mUserinfos.get(position).getmUserName());
        viewHolder.ageTextView.setText(mUserinfos.get(position).getmAge()+"岁");
        viewHolder.avatarImageView.setImageResource(R.drawable.cc1);


        return convertView;


    }
    //定义一个类把视图先保存起来
    class ViewHolder{
        TextView nameTextView;
        TextView ageTextView;
        ImageView avatarImageView;
    }

    public void refreshData(List<UserInfo> userInfos){
        //刷新数据
        mUserinfos = userInfos;
        //刷新视图
        notifyDataSetChanged();
    }
}

-------------------------------------------------------------------------------------------
D:\ASWork\app\src\main\res\layout\item_phone_book_friend.xml
	<ImageView
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:id="@+id/avatar_image_view"/>

    <TextView
        android:id="@+id/name_text_view"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="6dp"
        android:layout_toRightOf="@+id/avatar_image_view"
        android:text="呈呈" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="18岁"
        android:layout_toRightOf="@+id/avatar_image_view"
        android:layout_below="@+id/name_text_view"
        android:id="@+id/age_text_view"/>
		
-----------------------------------------------------------------------------------

## GridView
显示表格式列表
与ListView的相似之处和区别
相似之处：
	GridView extends AbsListView
	ListView extends AbsListView
	adapter，数据，点击事件，刷新都一样
不同之处：
	样式（宫格式）
	
    <GridView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:numColumns="auto_fit"
        android:horizontalSpacing="10dp"
        android:verticalSpacing="10dp"
        android:id="@+id/grid_view"/>
		

## ScrollView
内容可滚动视图
不是列表的内容区滚动
只支持垂直滚动
里面只能由一个视图
    <ScrollView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/grid_view_button"
        android:layout_centerHorizontal="true">
        <LinearLayout
            android:layout_height="match_parent"
            android:layout_width="match_parent"
            android:orientation="vertical">

            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"

                android:text="我是来自于ScrollView中的"/>
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="我是来自于ScrollView中的"/>
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="我是来自于ScrollView中的"/>
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="我是来自于ScrollView中的"/>
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="我是来自于ScrollView中的"/>
        </LinearLayout>
    </ScrollView>
	
	
## 自定义控件
px：像素点
dp：与像素密度密切相关 
sp：相当于dp（用来修饰文字）
dip：=dp

文字的尺寸一律用sp单位
非文字的尺寸一律使用dp单位
偶尔需要使用px单位：例如需要在屏幕上画一条细的分割线：1px

LayoutInflater：将xml布局解析成实际的视图View

获得LayoutInflater实例的三种方式：
        mLayoutInflater = getLayoutInflater();
        mLayoutInflater = (LayoutInflater) getSystemService(LAYOUT_INFLATER_SERVICE);
        mLayoutInflater = LayoutInflater.from(MainActivity.this);
        
        View view = mLayoutInflater.inflate(R.layout.activity_main,null);

提取布局属性：theme&style
	Theme是针对窗体级别的，改变窗体样式
	Style是针对窗体元素级别的，改变指定控件或者Layout的样式
	抽象view的共同属性
	可继承
	
	<style name="CustomTextView">
        <item name="android:background">@color/blue</item>
        <item name="android:textSize">20sp</item>
        <item name="android:textColor">#FF5722</item>
    </style>

    <style name="StudyTextView" parent="CustomTextView">
        <item name="android:gravity">right</item>
    </style>

## View如何工作
构造器->初始化
onMesure() 定大小
onLayout() 定位置
onDraw() 绘制
invalidate() 刷新

自定义控件的三种主要形式
1. 继承已有的控件来实现自定义控件 如Button extends TextView
2. 继承一个布局文件实现 如RelativeLayout
3. 通过view类来实现	

练习 需求:
	1. 做一个圆形的红色按钮
	2. 中间有一个白色的数字
	3. 数字起始为20
	4. 每点击一次减少1
	

----------------------------------------------------------------------
D:\ASWork\app\src\main\java\com\example\sqtian\TestRedButton.java

/**
 * 自定义控件
 * 	1. 做一个圆形的红色按钮
 * 	2. 中间有一个白色的数字
 * 	3. 数字起始为20
 * 	4. 每点击一次减少1
 * author ：HASEE
 * date : 2020/4/13 10:20
 * package：com.example.sqtian
 * description :
 */


public class TestRedButton extends View implements View.OnClickListener {

    private Paint mPaint;
    private Rect mRect;
    private int mNumber = 20;

    private int mBackgroundColor;
    private int mTextSize;
    private int mTextColor;


    public TestRedButton(Context context) {
        this(context,null,0);
    }

    public TestRedButton(Context context, @Nullable AttributeSet attrs) {
        this(context, attrs,0);
    }

    public TestRedButton(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(context,attrs);
    }

    @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
    public TestRedButton(Context context, @Nullable AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
        init(context, attrs);
    }



    //绘制
    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        //给画布设置颜色
//        mPaint.setColor(Color.RED);
        mPaint.setColor(mBackgroundColor);
        //canvas画图，画一个圆
        canvas.drawCircle(getWidth()/2,getHeight()/2,getWidth()/2 ,mPaint);
        //中间有白色数字
        mPaint.setColor(mTextColor);
        mPaint.setTextSize(mTextSize);
        String text = String.valueOf(mNumber);
        //文字边距,起始0,结束text.length()
        mPaint.getTextBounds(text,0,text.length(),mRect);
        int textWedth = mRect.width();
        int textHeight = mRect.height();

        //把文字画在圆圈中间
        canvas.drawText(text,getWidth()/2-textWedth/2,getHeight()/2+textHeight/2,mPaint);

    }

    //初始化
    private void init(Context context,AttributeSet attrs) {
        //画布
        mPaint = new Paint();
        //创建一个新的空矩形
        mRect = new Rect();
        //点击监听
        this.setOnClickListener(this);

        //获取自定义的属性
        @SuppressLint("Recycle") TypedArray typedArray = context.obtainStyledAttributes(attrs,R.styleable.TestRedButton);
        mBackgroundColor = typedArray.getColor(R.styleable.TestRedButton_backgroundColor, Color.RED);
        mTextSize = typedArray.getDimensionPixelSize(R.styleable.TestRedButton_textSize,18);//接收dp
        mTextColor = typedArray.getColor(R.styleable.TestRedButton_textColor,Color.WHITE);

    }

    @Override
    public void onClick(View v) {
        //每点击一次减少一
        if(mNumber>0){
            mNumber--;

        }else{
            mNumber = 20;
        }
        //刷新界面
        invalidate();


    }
}
---------------------------------------------------------------------------------
D:\ASWork\app\src\main\res\layout\activity_test_red_button.xml

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.example.sqtian.TestRedButton
        android:layout_width="300dp"
        android:layout_height="300dp"
        app:backgroundColor="@color/blue"
        app:textColor="@color/splashBackgroundColor"
        app:textSize="100sp"/>

</LinearLayout>

----------------------------------------------------------------------------------
D:\ASWork\app\src\main\java\com\example\sqtian\TestViewButtonActivity.java

public class TestViewButtonActivity extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_test_red_button);
    }
}


----------------------------------------------------------------------------------
如何自定义视图属性
在哪里创建属性: values中新建源文件
可以创建哪些属性
D:\ASWork\app\src\main\res\values\attrs.xml

<resources>

    <declare-styleable name="TestRedButton">
        <attr name="backgroundColor" format="color"/>
        <attr name="textSize" format="dimension"/>
        <attr name="textColor" format="color"/>
    </declare-styleable>
</resources>
-----------------------------------------------------------
如何使用这些属性
	代码中引用
	布局中设置
	
--------------------------------------------------------------------------
        //获取自定义的属性
        @SuppressLint("Recycle") TypedArray typedArray = context.obtainStyledAttributes(attrs,R.styleable.TestRedButton);
        mBackgroundColor = typedArray.getColor(R.styleable.TestRedButton_backgroundColor, Color.RED);
        mTextSize = typedArray.getDimensionPixelSize(R.styleable.TestRedButton_textSize,18);//接收dp
        mTextColor = typedArray.getColor(R.styleable.TestRedButton_textColor,Color.WHITE);

----------------------------------------------------------------------------------

## 使用Fragment
Fragment是activity的界面中的一部分
多个Fragment组合到一个activity中
多个activity中可重用一个Fragment

activity大房子,Fragment小房间
总结:
	Fragment是activity相当于模块化的一段activity
	具有自己的生命周期,接收自己的事件
	在activity运行时被添加或删除
	
使用:
	1. Create Fragment
		onCreate()
		onCreateView()
		onPause()
	2. Add Fragment
		Java Code
		Layout
	3. Replace Fragment
	
在使用fragment的时候,先创建了一个fragment,然后为他创建布局,
并在oncreateview中返回载入该视图的后返回的view,
在activity的布局文件里,使用xml布局,用fragment的name标签直接引用这个fragment,
然后居然爆出来该类不是fragment的错误,表示非常不理解,
然后把原来fragment换成android.app.fragment就没有这些问题,
于是问题也就慢慢的浮出水面,xml中载入的fragment是使用的android.app.fragment,
假设我们的fragment引用的是v4包里面的,就会遇到该类不是fragment的问题.
解决的办法要么是全都引用android.app.fragment里面的fragment,
否则就使用动态载入的方法去载入该fragment,over,问题记录完成
	
-----------------------------------------------------------------
D:\ASWork\app\src\main\java\com\example\sqtian\TestFragment.java

//需要继承android.app.Fragment
public class TestFragment extends android.app.Fragment {

    private static final String TAG = TestFragment.class.getSimpleName();
    public static final String ARGUMENT_NAME = "argument_name";
    public static final String ARGUMENT_NUMBER = "argument_number";
    private int mNumber;
    private String mName;

    //传递参数进去
    public static TestFragment newInstance(String nameString,int number){
        TestFragment testFragment = new TestFragment();

        //传递参数
        Bundle bundle = new Bundle();
        bundle.putString(ARGUMENT_NAME,nameString);
        bundle.putInt(ARGUMENT_NUMBER,number);
        testFragment.setArguments(bundle);
        return testFragment;
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.i(TAG,"onCreate");

        //获取参数,抽取成全局变量,在onCreateView中就可以直接使用了
        Bundle bundle = getArguments();
        if(bundle != null){
            mName = bundle.getString(ARGUMENT_NAME);
            mNumber = bundle.getInt(ARGUMENT_NUMBER);
        }
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        Log.i(TAG,"onCreateView");
        View view = inflater.inflate(R.layout.item_phone_book_friend,container,false);
        //把页面中的控件拿出来
        TextView nameTextView = (TextView) view.findViewById(R.id.name_text_view);
        nameTextView.setText(mName);

        return view;
    }
    
}
--------------------------------------------------------------------------------------------
布局使用item_phone_book_friend

---------------------------------------------------------------------------------------
public class TestFragmentActivity extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //1. 通过界面添加
        setContentView(R.layout.activity_test_fragment);

        //2. 通过代码添加
        // 获取管理器
        FragmentManager fragmentManager = getFragmentManager();
        FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
        // 创建一个fragment对象实例
        TestFragment testFragment =  TestFragment.newInstance("Fragment",15);
        //需要传递一个ViewGroup,将其添加到ViewGroup
        fragmentTransaction.add(R.id.fragment_view,testFragment).commit();


    }
}
----------------------------------------------------------------------------------------
D:\ASWork\app\src\main\res\layout\activity_test_fragment.xml

<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/blue">


    <fragment
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:name="com.example.sqtian.TestFragment"
        android:id="@+id/fragment_test"/>

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="400dp"
        android:layout_marginTop="200dp"
        android:background="@color/white"
        android:id="@+id/fragment_view">

    </RelativeLayout>


</RelativeLayout>

----------------------------------------------------------------------


## Handler

多线程与异步

什么是Handler
发送处理Message和Runnable

用来做什么
定时执行Message和MessageQueue
在不同线程中执行

Message和MessageQueue
Message 
2个整形数值：轻量级存储int类型的数据
一个Object：任意对象
replyTo：线程通信的时候使用
What：用户自定义的消息码让接收者识别消息

MessageQueue
Message的队列
每一个线程最多只可以拥有一个
Thread->Looper->MessageQueue

Lopper
消息泵
是MessageQueue的管理者
Looper.prepare()
每一个Looper对象和一个线程关联
Looper.myLooper()可以获得当前线程的Looper对象
Looper从MessageQueue中取出Message
交由Handler的handleMessage进行处理
调用Message.recycle()将其放入Message Pool中

实践:
倒计时功能
public class HandlerActivity extends Activity {

    public static final int MESSAGE_CODE = 888888;
//    //先创建一个Handler
//    //直接使用Handler内部类容易出现内存泄漏
//    Handler mHandler = new Handler(){
//        //重写接收消息的方法
//        @Override
//        public void handleMessage(@NonNull Message msg) {
//            super.handleMessage(msg);
//            //接收消息
//            switch(msg.what){
//                case MESSAGE_CODE:
//                    int value = (int) msg.obj;
//                    mTextView.setText(String.valueOf(value/1000));
//
//                    //正在使用的message无法再用,重新创建一个msg
//                    msg = Message.obtain();
//                    msg.arg1 = 0;
//                    msg.arg2 = 1;
//                    msg.what = MESSAGE_CODE;
//                    //倒计时,每次-1s
//                    msg.obj = value-1000;
//
//                    if(value > 0){
//                        //大于0时继续倒计时,循环发送消息
//                        sendMessageDelayed(msg,1000);
//                    }
//                    break;
//            }
//        }
//    };
    private TextView mTextView;
    private TestHandler mTestHandler = new TestHandler(this);

    //
    public TextView getTextView() {
        return mTextView;
    }

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_handler);

        mTextView = (TextView) findViewById(R.id.handler_text_view);

        //创建一个Message
        Message message = mTestHandler.obtainMessage();

        message.arg1 = 0;
        message.arg2 = 1;
        //消息识别码
        message.what = MESSAGE_CODE;
        //传递对象
        message.obj = 10000;
        //发送消息,延迟一秒

//        mHandler.sendMessageDelayed(message,1000);
        //用外部类发送
        mTestHandler.sendMessageDelayed(message,1000);

        //handler还可以定时,延时
        mTestHandler.postDelayed(new Runnable() {
            @Override
            public void run() {

            }
        },1000);


    }
    
    //用外部类解决内存泄漏问题,继承Handler,activity销毁后,这个类也会自动结束
    public  static class TestHandler extends Handler{
        //弱引用HandlerActivity
         public final WeakReference<HandlerActivity> mHandlerActivityWeakReference;

        public TestHandler(HandlerActivity activity) {

            mHandlerActivityWeakReference = new WeakReference<>(activity);
        }

        @Override
        public void handleMessage(@NonNull Message msg) {
            super.handleMessage(msg);

            HandlerActivity handlerActivity = mHandlerActivityWeakReference.get();

            //接收消息
            switch(msg.what){
                case MESSAGE_CODE:
                    int value = (int) msg.obj;
                    handlerActivity.getTextView().setText(String.valueOf(value/1000));

                    //正在使用的message无法再用,重新创建一个msg
                    msg = Message.obtain();
                    msg.arg1 = 0;
                    msg.arg2 = 1;
                    msg.what = MESSAGE_CODE;
                    //倒计时,每次-1s
                    msg.obj = value-1000;

                    if(value > 0){
                        //大于0时继续倒计时,循环发送消息
                        sendMessageDelayed(msg,1000);
                    }
                    break;
            }

        }
    }
}
---------------------------------------------------------------------------------------------------------





## Service
在后台长时间工作的应用主件,不会提供一个用户界面
不是单独的进程,也不是线程, 不能做耗时操作,需要new一个线程才可以做耗时操作
两种形式: start,stop  bind,unbind

public class MusicServiceActivity extends Activity implements View.OnClickListener {

    private Button mStartButton;
    private Button mStopButton;
    private MusicService mMusicService;
    private ServiceConnection mServiceConnection = new ServiceConnection() {

        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            //通过Binder获取了Service
            MusicService.localBinder locaBinder = (MusicService.localBinder) service;
            mMusicService = locaBinder.getService();
            if(mMusicService != null){
                int progress = mMusicService.getMusicPlayProgress();
            }
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {

        }
    };


    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_music_service);

        mStartButton = findViewById(R.id.start_button);
        mStopButton = findViewById(R.id.stop_button);

        mStartButton.setOnClickListener(this);
        mStopButton.setOnClickListener(this);


    }

    @Override
    public void onClick(View v) {
        switch(v.getId()){
            case R.id.start_button:
                //启动服务
                startService(new Intent(MusicServiceActivity.this,MusicService.class));
                //bind绑定
                bindService(new Intent(MusicServiceActivity.this,MusicService.class),mServiceConnection,BIND_AUTO_CREATE);


                break;
            case R.id.stop_button:
                //如果使用了bind绑定,直接stop就停不掉了,要先解绑
                unbindService(mServiceConnection);
                //停止服务
                stopService(new Intent(MusicServiceActivity.this,MusicService.class));
                break;
        }
    }
}

------------------------------------------------------------------


IntentService
如果想要任务按队列运行,就用IntentService

-------------------------------------------------------------------
public class TestIntentService extends IntentService {
    /**
     * Creates an IntentService.  Invoked by your subclass's constructor.
     *
     * @param name Used to name the worker thread, important only for debugging.
     */
    public TestIntentService(String name) {
        super(name);
    }

    @Override
    public int onStartCommand(@Nullable Intent intent, int flags, int startId) {
        //不能做太多操作, UI线程  如果>10秒 --> ANR application not response 应用无响应

        //如果想做耗时操作,需要新建一个进程
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {

            }
        });

        thread.start();

        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        // 排队 --> 向MessageQueue 同步操作: 排队领书,处理Intent数据

    }
}
------------------------------------------------------------------------





## Broadcast
四大组件之一，广播接收器

系统使用了很多广播
	通知时间改变
	电池电量变低
	拍摄了照片
	改变了语言
没有用户界面
extends BroadcastReceiver

需要注册，有两种注册方式
	1. 静态注册
		在manifest里注册
		
		<receiver android:name=".TestBroadcastReceiver">
            <intent-filter>
                <action android:name="com.example.sqtian.broadcast"/>
            </intent-filter>
        </receiver>
	2. 动态注册
		在代码里注册
	public class SendBroadcastActivity extends Activity {
    
		private  TestBroadcastReceiver mTestBroadcastReceiver = new TestBroadcastReceiver();
		
		@Override
		protected void onCreate(@Nullable Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);

			//动态注册广播
			IntentFilter intentFilter = new IntentFilter();

			intentFilter.addAction("com.example.sqtian.broadcast");
			registerReceiver(mTestBroadcastReceiver,intentFilter);
			
		}
	}
	

BroadcastReceiver生命周期 
Register
SendBroadcast
onReceive
unRegister

适当的用，不能滥用广播
信息需要很多地方都收到时，可以用

----------------------------------------------------------------------------------
public class SendBroadcastActivity extends Activity {

    public static final String COM_EXAMPLE_SQTIAN_BROADCAST = "com.example.sqtian.broadcast";
    private  TestBroadcastReceiver mTestBroadcastReceiver = new TestBroadcastReceiver();
    private Button mSendBroadcastButton;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_test_broadcast);



        //获取broadcastButton

        mSendBroadcastButton = (Button) findViewById(R.id.send_broadcast_button);

        mSendBroadcastButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.setAction(COM_EXAMPLE_SQTIAN_BROADCAST);
                intent.putExtra("toast","this is my toast of broadcast");

                //普通广播
                sendBroadcast(intent);

//                //有序广播,有优先级的,类似intentService
//                sendOrderedBroadcast()

//                //本地广播,只在本应用中的广播
//                LocalBroadcastManager

            }
        });
    }


    //在onstart中动态注册
    @Override
    protected void onStart() {
        super.onStart();
        //动态注册广播
        IntentFilter intentFilter = new IntentFilter();

        intentFilter.addAction(COM_EXAMPLE_SQTIAN_BROADCAST);
        registerReceiver(mTestBroadcastReceiver,intentFilter);

    }

    //在onStop中反注册


    @Override
    protected void onStop() {
        super.onStop();
        unregisterReceiver(mTestBroadcastReceiver);
    }
}
-----------------------------------------------------------------------------------
D:\ASWork\app\src\main\res\layout\activity_test_broadcast.xml
	<Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="BroadcastButton"
        android:id="@+id/send_broadcast_button"/>
		
----------------------------------------------------------------------------------
public class TestBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        // receive broadcast 接收,处理数据

        //之间不能做耗时操作,new一个线程
        if(intent != null){
            if(TextUtils.equals(intent.getAction(),SendBroadcastActivity.COM_EXAMPLE_SQTIAN_BROADCAST)){
                String toastString = intent.getStringExtra("tot");
                Toast.makeText(context,toastString,Toast.LENGTH_LONG).show();
            }
        }
    }
}

-------------------------------------------------------------------------------------


## WebView：加载网页
网页显示器
用处：
	加载线上url
	加载本地html
	和JS进行交互
	历史记录
	导航
	
要在manifest中声明权限 <uses-permission android:name="android.permission.INTERNET"/>

-----------------------------------------------------------------------------------------------
public class WebViewButtonActivity extends Activity {

    private WebView mWebView;

    public class TestJSEvent{
        @JavascriptInterface
        public void showToast(String toast){
            Toast.makeText(WebViewButtonActivity.this,toast,Toast.LENGTH_LONG).show();

        }

    }

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_webview);

        mWebView = (WebView) findViewById(R.id.web_view);
        mWebView.loadUrl("http://www.baidu.com");
//        // 加载自己的html
//        mWebView.loadUrl("file:///android_asset/HelloWorld.html");


        //从百度页面跳转到别的页面,需要与JavaScript交互,打开才可以跳转
        mWebView.getSettings().setJavaScriptEnabled(true);
        //JS调用原生App方法showToast,即在HelloWorld.html里调用应用里的方法
        mWebView.addJavascriptInterface(new TestJSEvent(),"TestApp");

//        //原生App调用JS
//        mWebView.loadUrl("javascript:(html里方法的名称)");

        //让页面在应用内部打开
        mWebView.setWebViewClient(new WebViewClient(){
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                //是否要重新加载(拦截页面
                //比如一开始访问的网页失效了,404页面,可以重新加载一个页面
                if(url.contains("404")){
                    mWebView.loadUrl("http://www.zhihu.com");
                }

                return super.shouldOverrideUrlLoading(view, url);
            }

            @Override
            public void onPageStarted(WebView view, String url, Bitmap favicon) {
                //页面开始时,可以设置loading动画
                super.onPageStarted(view, url, favicon);
            }

            @Override
            public void onPageFinished(WebView view, String url) {
                //页面完成后,隐藏的loading动画
                super.onPageFinished(view, url);
            }
        });

//        mWebView.setWebChromeClient(new WebChromeClient(){
//            @Override
//            public void onProgressChanged(WebView view, int newProgress) {
//                //进度条
//                super.onProgressChanged(view, newProgress);
//            }
//
//            @Override
//            public void onReceivedTitle(WebView view, String title) {
//                // 获取界面的title
//                super.onReceivedTitle(view, title);
//            }
//
//            @Override
//            public void onShowCustomView(View view, CustomViewCallback callback) {
//                super.onShowCustomView(view, callback);
//            }
//        });
        
        
    }

    //让返回时不会直接退出,而是返回上一网页
    @Override
    public void onBackPressed() {

        if(mWebView != null && mWebView.canGoBack()){

//            //导航
//            WebBackForwardList webBackForwardList = mWebView.copyBackForwardList();
//
//            WebHistoryItem historyItem = webBackForwardList.getItemAtIndex(0);
//
//            String historyUrl = historyItem.getUrl();


            //回退
            mWebView.goBack();
//            //前进
//            mWebView.goForward();

        }else{
            super.onBackPressed();
        }

    }
}

--------------------------------------------------------------------------------------
    <WebView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/web_view"/>

-----------------------------------------------------------------------------------------


更深入的使用：
	Cookie
	History
	页面导航
	Hybird
	如何拦截网络请求
	如何显示进度
	调试
	......
	

## Widget
相当于挂件
从一个小Demo做起
首先新建一个TestWidget extends AppWidgetProvider
在AndroidManifest中声明TestWidget
在xml目录定义App Widget的初始化xml文件 setting_widget.xml
实现Widget具体布局的widget_layout.xml
继承AppWidgetProvider类，实现具体的Widget业务逻辑

public class TestWidget extends AppWidgetProvider {
    public static final String WIDGET_BUTTON_ACTION = "widget_button_action";
    //桌面上的插件和应用需要通信，因此使用了广播机制，AppWidgetProvider extends BroadcastReceiver
    //广播需要注册，因此Widget也需要注册


    @Override
    public void onReceive(Context context, Intent intent) {
        super.onReceive(context, intent);

        //intent不为空且action一致时接收广播信息
        if(intent != null && WIDGET_BUTTON_ACTION.equals(intent.getAction())){
            Log.i(WIDGET_BUTTON_ACTION,"be clicked");

            RemoteViews remoteViews = new RemoteViews(context.getPackageName(),R.layout.widget_layout);
            remoteViews.setTextViewText(R.id.widget_text_view,"be clicked");
            remoteViews.setTextColor(R.id.widget_text_view, Color.BLUE);

            //获取appWidgetManager.updateAppWidget更新控件
            AppWidgetManager appWidgetManager = AppWidgetManager.getInstance(context);
            //没有appWidgetIds,用另一个传ComponentName的方法
            ComponentName componentName = new ComponentName(context,TestWidget.class);
            appWidgetManager.updateAppWidget(componentName,remoteViews);

        }
    }

    @Override
    public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) {
        super.onUpdate(context, appWidgetManager, appWidgetIds);
        RemoteViews remoteViews = new RemoteViews(context.getPackageName(),R.layout.widget_layout);

        Intent intent = new Intent();
        intent.setClass(context,TestWidget.class);
        intent.setAction(WIDGET_BUTTON_ACTION);

        //未来意图,将要执行的intent,点击以后才会发送广播
        PendingIntent pendingIntent = PendingIntent.getBroadcast(context,0,intent,0);
        remoteViews.setOnClickPendingIntent(R.id.widget_button,pendingIntent);

        //更新控件
        appWidgetManager.updateAppWidget(appWidgetIds,remoteViews);
    }
}

---------------------------------------------------------------------------------------------------
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="140dp"
    android:minHeight="140dp"
    android:previewImage="@drawable/cc1"                   //预览的图片
    android:updatePeriodMillis="200000"                    //更新周期
    android:widgetCategory = "home_screen"                 //挂件安装地点
    android:initialLayout="@layout/widget_layout">



</appwidget-provider>
--------------------------------------------------------------------------------------------------------
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/widget_text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="TextView" />

    <Button
        android:id="@+id/widget_button"
        android:layout_gravity="center"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button" />
</LinearLayout>
--------------------------------------------------------------------------------------
	
其他
与service通信
Widget控件的交互方法
如何做一个桌面播放器Widget
