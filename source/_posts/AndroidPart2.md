---
title: '''AndroidPart2'''
date: 2020-04-27 15:13:01
tags: 安卓开发
categories: 安卓
---

## Android 网络与数据的存储

使用SharedPerferences方便地存储数据
数据持久化
适用于存储一些简单的数据

public class ListViewDemoActivity extends Activity implements View.OnClickListener {

    public static final String LIST_VIEW_DATA_COUNTS = "list_view_data_counts";
    public static final String PREFERENCE_NAME = "preference_name";
    public static final int DEFAULT_VALUE = 10;
    //ListView视图作为一个容器，房子
    private ListView mphoneBookListView;
    private List<UserInfo> mUserInfos;
    private int mDataCounts = 10;
    private Button mConfirmButton;
    private PhoneBookAdapter mPhoneBookAdapter;
    private EditText mDataCountsEditText;
    private SharedPreferences mSharedPreferences;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_listview_demo);
        findViews();
        setData();
        setListeners();


    }

    private void findViews() {
        mphoneBookListView = findViewById(R.id.list_view);
        mDataCountsEditText = findViewById(R.id.data_counts_edit_text);
        mConfirmButton = findViewById(R.id.confirm_button);
    }

    private void setData() {
        //每次创建时读取
        SharedPreferences sharedPreferences = getSharedPreferences(PREFERENCE_NAME,Context.MODE_PRIVATE);

        mDataCounts = sharedPreferences.getInt(LIST_VIEW_DATA_COUNTS, DEFAULT_VALUE);

        mDataCountsEditText.setText(String.valueOf(mDataCounts));
        mUserInfos = new ArrayList<>();

        for (int index = 0; index < mDataCounts; index++) {
            mUserInfos.add(new UserInfo("西门吹雪", 18));

        }


        //数据列表和数据绑定,adapter适配器，房子里的柜子，柜子放进房子
        mPhoneBookAdapter = new PhoneBookAdapter(ListViewDemoActivity.this, mUserInfos);
        //房子(mphoneBookListView)里面有柜子(phoneBookAdapter)，柜子里面有格子(name_text_view)，
        //格子里面放数据private String[] mNames = {"西门吹雪","李寻欢"}
        mphoneBookListView.setAdapter(mPhoneBookAdapter);
    }


    private void setListeners() {
        //刷新更新数据
        mPhoneBookAdapter.notifyDataSetChanged();
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
                    mPhoneBookAdapter.refreshData(mUserInfos);
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

        // 给确认按钮设置点击事件
        mConfirmButton.setOnClickListener(this);

    }


    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.confirm_button:
                //点击确认时,首先取到输入的数字
                mDataCounts= Integer.valueOf(mDataCountsEditText.getText().toString());
                refreshListView();

                //保存起来,不然editText设置了2,退出后回来还是原来地个数,因此要保存起来,下次打开时自动读取
                saveData2Preference(mDataCounts);
                break;
        }


    }

    private void saveData2Preference(int dataCounts) {

        // 系统会自动帮我们创建一个XML文件,名字是"preference_name",地址在Data/Data/Shard_prefs
        mSharedPreferences = getSharedPreferences(PREFERENCE_NAME, Context.MODE_PRIVATE);

        SharedPreferences.Editor editor = mSharedPreferences.edit();
        //数据持久化地存起来了
        editor.putInt(LIST_VIEW_DATA_COUNTS,dataCounts);
        
//        删除
//        editor.remove(LIST_VIEW_DATA_COUNTS);
        
        //与commit不同,apply后台写数据,另开线程,和网络相关和IO操作相关的都要用异步的apply
        editor.apply();

    }

    private void refreshListView() {
        //刷新数据
        mUserInfos.clear();
        for (int index = 0; index < mDataCounts; index++) {
            mUserInfos.add(new UserInfo("李寻欢", 18));

        }

        mPhoneBookAdapter.refreshData(mUserInfos);
        mPhoneBookAdapter.notifyDataSetChanged();
    }
}

----------------------------------------------------------------------------------------------------
 <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="条数"
        android:id="@+id/data_counts"/>

    <EditText
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:layout_below="@+id/data_counts"
        android:id="@+id/data_counts_edit_text"
        />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="确定"
        android:layout_toRightOf="@+id/data_counts_edit_text"
        android:id="@+id/confirm_button"/>

    <TextView
        android:id="@+id/phone_text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_below="@+id/data_counts_edit_text"
        android:text="@string/phone_book"/>






    <ListView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_below="@+id/phone_text_view"
        android:id="@+id/list_view">
    </ListView>

---------------------------------------------------------------------



作业：做一个小应用，启动时有两张引导界面，只有第一次启动时，显示，看完后下一次就不显示了



## 如何随己所欲管理文件


//测试文件, 自己用file创建文件
    private void testFileDemo() {
        //在internal storage里创建了一个新文件
        File file = new File(getFilesDir(),"test.txt");

        Log.i("MainActivity","getFilesDir:"+ getFilesDir().getAbsolutePath());
        Log.i("MainActivity","file path:"+file.getAbsolutePath());

        String string = "I am handsome";

        try {
            boolean isSucess = file.createNewFile();
        } catch (IOException e) {

            Log.i("MainActivity","test.txt create error:"+e.toString());

            e.printStackTrace();
        }


        try {
            FileOutputStream fileOutputStream = openFileOutput("test2.txt", Context.MODE_PRIVATE);
            fileOutputStream.write(string.getBytes());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        //检查 external storage 是否可用
        String state = Environment.getExternalStorageState();
        
        if(TextUtils.equals(state,Environment.MEDIA_MOUNTED)){
            
        }
        
    }
	
-------------------------------------------------------------------------------------------
	
操作assets目录下的文件

    void testAssets() throws IOException {
        //assets下的文件不会被编译改变

        //第一种,直接读路径
        WebView webView = new WebView(this);
        webView.loadUrl("file:///android_asset/HelloWorld.html");

        //open的只能是文件,不能是文件夹
        //读网页
        InputStream inputStream = getResources().getAssets().open("HellowWorld.html");


        //读列表
//        String[] fileNames = getAssets().list("images/");

        //读图片

        InputStream IMGinputStream = getAssets().open("images/dog.jpg");
        //把图片转为bitmap
        Bitmap bitmap = BitmapFactory.decodeStream(IMGinputStream);
        ImageView imageView = new ImageView(this);
        imageView.setImageBitmap(bitmap);


        //读音乐
        AssetFileDescriptor assetFileDescriptor = getAssets().openFd("bgm.mp3");
        MediaPlayer player = new MediaPlayer();
        player.reset();
        player.setDataSource(assetFileDescriptor.getFileDescriptor(),assetFileDescriptor.getStartOffset(),assetFileDescriptor.getLength());
        player.prepare();
        player.start();

    }
	
-------------------------------------------------------------------------------------




操作raw目录下的文件  操作res目录下的文件
    void testResFile(){
        //读音乐
        InputStream inputStream = getResources().openRawResource(R.raw.bgm);

        //读color
        getResources().getColor(R.color.blue);
        //读String
        getResources().getString(R.string.app_name)
    }
--------------------------------------------------------------------------------------

操作sd卡(外部存储)文件
    void testSDCard(){
//        File file = new File("/sdcard/test/a.txt");//直接写路径,不推荐
        String filePath = Environment.getExternalStorageDirectory().getAbsolutePath();
        File file = new File(filePath);
        
        Environment.getDataDirectory(); //获取安卓中的data数据目录
        Environment.getDownloadCacheDirectory(); //获取安卓中download目录 ...
        
    }


-----------------------------------------------------------------------------------

drawable放图片,mipmap放头像,layout放布局,values放值

相同点和区别:
放在assets和raw下的文件创建apk时都会原封不动地打在包里,不会再编译成二进制
但是assets是纯原封不动,raw系统会自动映射到R文件里的id,可以直接通过R.引用
res中不可以再有目录结构



## SQLite数据库
特色： 轻量级，独立，隔离，跨平台，多语言接口，安全性
对象关系映射ORM
public class DatabaseActivity extends AppCompatActivity {

    private SQLiteDatabase mSqLiteDatabase;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_database);

        //新建一个数据库文件
        DatabaseHelper databaseHelper = new DatabaseHelper(this);
        mSqLiteDatabase = databaseHelper.getWritableDatabase();


        //Add
        findViewById(R.id.add_button).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //IO操作,建议后台操作,直接在onClick里操作容易卡死

                //新建键值对
                ContentValues contentValues = new ContentValues();
                contentValues.put(DatabaseHelper.USERNAME, "无名剑主");
                contentValues.put(DatabaseHelper.PASSWORD, "1234567");

                long rowNumber = mSqLiteDatabase.insert(DatabaseHelper.TABLE_NAME, null, contentValues);

                if (rowNumber != -1) {
                    Toast.makeText(DatabaseActivity.this, "插入成功", Toast.LENGTH_SHORT).show();
                }


                queryData();

            }
        });


        findViewById(R.id.delete_button).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Delete


                String whereClauseString = "username=?";
                String[] whereArgsString = {"无名剑主"};
                mSqLiteDatabase.delete(DatabaseHelper.TABLE_NAME, whereClauseString, whereArgsString);


                queryData();
            }
        });


        findViewById(R.id.update_button).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Update

                ContentValues contentValues = new ContentValues();
                contentValues.put(DatabaseHelper.PASSWORD, "7654321");
                String whereClauseString = "username=?";
                String[] whereArgsString = {"无名剑主"};
                mSqLiteDatabase.update(DatabaseHelper.TABLE_NAME, contentValues, whereClauseString, whereArgsString);


                queryData();
            }
        });


        //事务，批量操作

        //开始事务,此时数据库就会被锁定
        mSqLiteDatabase.beginTransaction();
        try {
            //需要的操作

            mSqLiteDatabase.setTransactionSuccessful();
        } catch (Exception e){
            e.printStackTrace();
        }finally {
            mSqLiteDatabase.endTransaction();
        }
    }
    private void queryData() {
        //Query

        //游标
        Cursor cursor =    mSqLiteDatabase.query(DatabaseHelper.TABLE_NAME,null,null,null,null,null,null,null);

        if(cursor.moveToFirst()){
            int count = cursor.getCount();
            for (int i = 0; i < count; i++) {
                String userName = cursor.getString(cursor.getColumnIndexOrThrow(DatabaseHelper.USERNAME));
                String password = cursor.getString(cursor.getColumnIndexOrThrow(DatabaseHelper.PASSWORD));

                Log.i(MainActivity.class.getSimpleName(),i+":"+userName+"||"+password);
            }
        }
    }
}
----------------------------------------------------------------------------------------------
public class DatabaseHelper extends SQLiteOpenHelper {


    public static final String TABLE_NAME = "user";
    public static final String USERNAME = "username";
    public static final String PASSWORD = "password";
    public static final String DATABASE_NAME = "test.db";

    public DatabaseHelper(Context context) {
        super(context, DATABASE_NAME, null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        //新建表
        db.execSQL("create table " + TABLE_NAME + "(" + USERNAME + " varchar(20) not null," + PASSWORD + " varchar(60) not null);");

    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        //升级数据库
    }
}
------------------------------------------------------------------------------------------
    <Button
        android:id="@+id/add_button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Add" />

    <Button
        android:id="@+id/delete_button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Delete" />

    <Button
        android:id="@+id/update_button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Update" />
------------------------------------------------------------------------------------------


## Content Provider
四大组件之一（activity，service，broadcast receiver）
应用程序间共享数据的一种方式
为存储和获取数据提供了统一的接口
Android为常见的一些数据提供了默认的ContentProvider

定义：
Content Provider 将一些特定的应用程序数据供给其他应用程序使用
数据可以存储于文件系统，SQLite数据库或其他方式
内容提供者继承于ContentProvider 基类，为其他应用程序取用和存储他管理的数据实现了一套标准方法
应用程序并不能直接调用这些方法，而是使用ContentResolver对象，调用他的方法作为替代
ContentResover可以与任意内容提供者进行会话，与其合作来对所有相关交互通讯进行管理





------------------------------------------------------------------------------
public class URIList {

    public static final String CONTENT = "content://";
    //包名
    public static final String AUTHORITY = "com.example.sqtian";
    //表面
    public static final String USER_URI = CONTENT + AUTHORITY + "/" + DatabaseHelper.USER_TABLE_NAME;
    public static final String BOOK_URI = CONTENT + AUTHORITY + "/" + DatabaseHelper.BOOK_TABLE_NAME;


}

-----------------------------------------------------------------------------
public class DatabaseHelper extends SQLiteOpenHelper {


    public static final String USER_TABLE_NAME = "user_table_name";
    public static final String USERNAME = "username";
    public static final String PASSWORD = "password";
    public static final String DATABASE_NAME = "test.db";
    public static final String BOOK_TABLE_NAME = "book_table_name";
    public static final String BOOK_NAME = "book_name";
    public static final String BOOK_PRICE = "book_price";

    public DatabaseHelper(Context context) {
        super(context, DATABASE_NAME, null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        //新建表
        db.execSQL("create table " + USER_TABLE_NAME + "(" + USERNAME + " varchar(20) not null," + PASSWORD + " varchar(60) not null);");
        db.execSQL("create table " + BOOK_TABLE_NAME + "(" + BOOK_NAME + " varchar(20) not null," + BOOK_PRICE + " varchar(60) not null);");


    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        //升级数据库
    }
}


------------------------------------------------------------------------------
public class TestContentProvider extends ContentProvider {

    private static UriMatcher sUriMatcher;
    private static final int URI_MATCH_USER = 1;
    private static final int URI_MATCH_BOOK = 2;

    static {
        sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);

        // 将content://com.example.sqtian/user_table_name 用int URI_MATCH_USER来代表
        // 将content://com.example.sqtian/book_table_name 用int URI_MATCH_BOOK来代表
        sUriMatcher.addURI(URIList.AUTHORITY, DatabaseHelper.USER_TABLE_NAME,URI_MATCH_USER);
        sUriMatcher.addURI(URIList.AUTHORITY, DatabaseHelper.BOOK_TABLE_NAME,URI_MATCH_BOOK);
    }

    private DatabaseHelper mDatabaseHelper;


    //用代号进行匹配,传入uri得到表名
    private String getTableName(Uri uri){
        int type = sUriMatcher.match(uri);
        String tableName = null;
        switch(type){
            case URI_MATCH_USER:
                tableName = DatabaseHelper.USER_TABLE_NAME;
                break;
            case URI_MATCH_BOOK:
                tableName = DatabaseHelper.BOOK_TABLE_NAME;
                break;
        }
        return tableName;
    }

    @Override
    public boolean onCreate() {

        mDatabaseHelper = new DatabaseHelper((getContext()));
        return false;
    }

    @Nullable
    @Override
    public Cursor query(@NonNull Uri uri, @Nullable String[] projection, @Nullable String selection, @Nullable String[] selectionArgs, @Nullable String sortOrder) {
        //实现自己的query方法

        //先通过uri得知是那张表
        String tableName = getTableName(uri);
        if(TextUtils.isEmpty(tableName)){
            return null;
        }

        Cursor cursor = mDatabaseHelper.getWritableDatabase()
                .query(tableName,projection,selection,selectionArgs,null,null,sortOrder);

        return cursor;
    }

    @Nullable
    @Override
    public String getType(@NonNull Uri uri) {
        return null;
    }

    @Nullable
    @Override
    public Uri insert(@NonNull Uri uri, @Nullable ContentValues values) {
        String tableName = getTableName(uri);
        if(TextUtils.isEmpty(tableName)){
            return null;
        }

        long id = mDatabaseHelper.getWritableDatabase().insert(tableName,null,values);

        //返回一个uri和id拼接后的结果
        return ContentUris.withAppendedId(uri,id);
    }

    @Override
    public int delete(@NonNull Uri uri, @Nullable String selection, @Nullable String[] selectionArgs) {
        String tableName = getTableName(uri);
        if(TextUtils.isEmpty(tableName)){
            return -1;
        }

        int count = mDatabaseHelper.getWritableDatabase().delete(tableName, selection, selectionArgs);
        //返回的是改变的条数
        return count;
    }

    @Override
    public int update(@NonNull Uri uri, @Nullable ContentValues values, @Nullable String selection, @Nullable String[] selectionArgs) {
        String tableName = getTableName(uri);
        if(TextUtils.isEmpty(tableName)){
            return -1;
        }

        int count = mDatabaseHelper.getWritableDatabase().update(tableName,values,selection,selectionArgs);
        //返回的是改变的条数
        return count;
    }
}

-----------------------------------------------------------------------------------------
        //内容解析器
        ContentResolver contentResolver = getContentResolver();

//        //资源路径
//        //content://package_name/table_name/id/row_name
//        Uri uri = Uri.parse("content://com.android.contacts/contacts");

        //游标
        Cursor cursor = contentResolver.query(Uri.parse(URIList.USER_URI),null,null,null,null);

//        if(cursor != null && cursor.moveToFirst()){
//            //遍历把数据取出来
//        }

        // ContentProvider 提供者 提供增删改查的方法,query(uri)...

---------------------------------------------------------------------------------------






## 网络与数据处理

public class NetworkActivity extends Activity implements View.OnClickListener {

    private Button mButton;
    private TextView mTextView;
    private EditText mEditText;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_network);

        findViews();

        mButton.setOnClickListener(this);

        String url = getEditTextUrl();
    }

    private void findViews() {
        mEditText = findViewById(R.id.address_edit_text);
        mTextView = findViewById(R.id.result_text);
        mButton = findViewById(R.id.get_data_button);



    }

    private String getEditTextUrl() {

        return mEditText != null? mEditText.getText().toString():"";

    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.get_data_button:
                String url = getEditTextUrl();
                //请求网络数据,需要manifest设置网络权限
                String data = requestData(url);
//                mTextView.setText(data); 放到异步操作中去处理

                new RequestNstworkDataTask().execute(url);
                break;
        }
    }

    private String requestData(String urlString) {

        try {
            URL url = new URL(urlString);
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setConnectTimeout(30000);
            connection.setRequestMethod("GET");

            connection.connect();
            int responseCode = connection.getResponseCode();
            String responseMessage = connection.getResponseMessage();

            InputStream inputStream = connection.getInputStream();
            Reader reader = new InputStreamReader(inputStream,"UTF-8");
            char[] buffer = new char[1024];
            reader.read(buffer);
            String content = new String(buffer);

            return content;


        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }


        return null;
    }

    //异步任务处理,同步会卡死

    class RequestNstworkDataTask extends AsyncTask<String,Integer,String> {


        //在后台work之前


        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            //主线程
        }

        @Override
        protected String doInBackground(String[] params) {

            String result = requestData(params[0]);
            return null;
        }


        @Override
        protected void onPostExecute(String result) {
            super.onPostExecute(result);
            //执行完之后在主线程中
            mTextView.setText(result);

        }
    }
}
--------------------------------------------------------------------

    <EditText
        android:id="@+id/address_edit_text"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="请输入网址"
        android:layout_gravity="center_horizontal"
        />

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="获取数据"
        android:layout_gravity="center_horizontal"
        android:id="@+id/get_data_button"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/result_text"
        android:text="This is Result"/>
		
		
		
-----------------------------------------------------------------------------



## xml解析

test.xml
<?xml version="1.0" encoding="utf-8"?>
<web>
    <item id="0" url="http://www.baidu.com">百度</item>
    <item id="1" url="http://www.taobao.com">淘宝</item>
    <item id="2" url="http://www.qq.com">腾讯</item>
    <item id="3" url="http://www.zhihu.com">知乎</item>

</web>
--------------------------------------------------------------------------
public class WebURL {

    private int mID;
    private String mUrl;
    private String mContent;

    public int getID() {
        return mID;
    }

    public void setID(Integer ID) {
        mID = ID;
    }

    public String getUrl() {
        return mUrl;
    }

    public void setUrl(String url) {
        mUrl = url;
    }

    public String getContent() {
        return mContent;
    }

    public void setContent(String content) {
        mContent = content;
    }
}
---------------------------------------------------------------------------------------

## SAX

首先得到一个XMLReader，然后setContentHandler

---------------------------------------------------------------------------------
public class SAXParseHandler extends DefaultHandler {

    public static final String ITEM = "item";
    List<WebURL> mWebURLS;
    WebURL mWebURL;

    @Override
    public void startDocument() throws SAXException {
        super.startDocument();

        mWebURLS = new ArrayList<>();
    }

    @Override
    public void endDocument() throws SAXException {
        super.endDocument();
    }

    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        super.startElement(uri, localName, qName, attributes);
        mWebURL = new WebURL();
        if(TextUtils.equals(localName, ITEM)){
            for (int i = 0; i < attributes.getLength(); i++) {
                if(TextUtils.equals(attributes.getLocalName(i),"id")){
                    mWebURL.setID(Integer.valueOf(attributes.getValue(i)));
                }
                //不同的属性情况
                
                
            }
        }

    }

    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        super.endElement(uri, localName, qName);
    }

    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        super.characters(ch, start, length);
    }

    public List<WebURL> gerXMLList() {
        return mWebURLS;
    }
}

--------------------------------------------------------------------------------------------

// parse file by SAX, SAX方法解析XML
    private void testSAXParse() throws ParserConfigurationException, SAXException, IOException {

        //首先创建一个工厂,从工厂里取出一个解析器,解析器读出一个reader
        SAXParserFactory saxParserFactory = SAXParserFactory.newInstance();
        SAXParser saxParser = saxParserFactory.newSAXParser();
        XMLReader xmlReader = saxParser.getXMLReader();

        //给reader设置处理器
        SAXParseHandler saxParseHandler = new SAXParseHandler();
        xmlReader.setContentHandler(saxParseHandler);

        //给reader数据流
        InputStream inputStream = getResources().openRawResource(R.raw.test);
        InputSource inputSource = new InputSource(inputStream);
        xmlReader.parse(inputSource);

        //得到数据
        saxParseHandler.gerXMLList();

    }
	
--------------------------------------------------------------------------------------
PULL

        //pull xml里才能读
        XmlResourceParser xmlResourceParser = getResources().getXml(R.xml.test);
        try {
            while(xmlResourceParser.getEventType() != XmlResourceParser.END_DOCUMENT) {
                if (xmlResourceParser.getEventType() == XmlResourceParser.START_TAG){
                    String tagName = xmlResourceParser.getName();
                    if(TextUtils.equals(tagName,"item")){
                        String id = xmlResourceParser.getAttributeValue(null,"id");
                    }
                }
            }
        } catch (XmlPullParserException e) {
                e.printStackTrace();
        }
DOM


## JSON

        //JSON
        InputStream is = getResources().openRawResource(R.raw.json);
        String jsonString = getStringByInputStream(is);

        try {
            JSONObject jsonObject = new JSONObject(jsonString);
            String title = jsonObject.getString("title");

            JSONObject userJSONObject = jsonObject.getJSONObject("user");
            userJSONObject.getLong("id");

            JSONArray jsonArray = jsonObject.getJSONArray("images");

        } catch (JSONException e) {
            e.printStackTrace();
        }


GSON
        //GSON
        Gson gson = new Gson();

        //fromJson() 把JsonString变成一个对象
        UserData userData = gson.fromJson(jsonString, UserData.class);
		
------------------------------------------------------------------
		
public class UserData {
    //json.txt中对应的属性

    //SerializedName注解对应

    @SerializedName("title")
    private String mTitle;

    @SerializedName("content")
    private String mContent;

    @SerializedName("user")
    private User mUser;

    @SerializedName("images")
    private List<String> mImages;

    public class User{
        @SerializedName("id")
        private long mID;

        @SerializedName("name")
        private String mName;

        @SerializedName("avatar")
        private String mAvatar;
    }

}




网络状态处理
	ConnectivityManager
	NetworkInfo
	
public class NetworkUtil {

    public void testNetwork(Context context){
        //回答网络连接的查询结果,并在网络连接改变时通知应用程序
        ConnectivityManager connectivityManager = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo networkInfo = connectivityManager.getNetworkInfo(ConnectivityManager.TYPE_WIFI);
        boolean isWifiConnection = networkInfo.isConnected();
//        networkInfo = connectivityManager.getAllNetworkInfo(ConnectivityManager.TYPE_MOBILE);
    }
}

