# This is an AndroidStudio rebuild of google SDK sample NotePad
## 项目简介
实现记事本的基本操作
### 项目功能
* 实现基本笔记的页面操作
  * 添加笔记以及显示时间戳
#### 开发环境
使用Android studio进行编译java语言，使用可视化工具，使程序能展现在手机端中
##### 程序主要代码块
*更改gradle配置
```
android {
    compileSdkVersion 30
    buildToolsVersion "30.0.3"
    defaultConfig {
        applicationId "com.example.android.notepad"
        minSdkVersion 15
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"
        testApplicationId "com.example.android.notepad.tests"
        testInstrumentationRunner "android.test.InstrumentationTestRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
        repositories {
            maven {
                url "https://maven.google.com"
            }
        }
    }
}
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'androidx.appcompat:appcompat:1.0.2'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.ext:junit:1.1.0'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.1.1'
}
```

* notelist_item.xml layout部分用于手机端备注和时间戳的布局设置
```
<TextView
android:id="@+id/text2"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:textAppearance="?android:attr/textAppearanceLarge"
android:paddingLeft="5dip"
android:singleLine="true"
android:textSize="17dp"
android:gravity="center_vertical"
    />
```
* updatenote方法
```
 private final void updateNote(String text, String title) {
        ContentValues values=new ContentValues();
        Long now = System.currentTimeMillis();
        //修改:需要将毫秒数转换为时间的形式yy.MM.dd HH:mm:ss
        Date date = new Date(now);
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yy-MM-dd HH:mm:ss");
        simpleDateFormat.setTimeZone(TimeZone.getTimeZone("GMT+08:00"));
        String dateFormat = simpleDateFormat.format(date);//转换为yy.MM.dd HH:mm:ss形式的时间
```
* insert方法
```
public Uri insert(Uri uri, ContentValues initialValues) {
        // Validates the incoming URI. Only the full provider URI is allowed for inserts.
        if (sUriMatcher.match(uri) != NOTES) {
            throw new IllegalArgumentException("Unknown URI " + uri);
        }
        // A map to hold the new record's values.
        ContentValues values;
        // If the incoming values map is not null, uses it for the new values.
        if (initialValues != null) {
            values = new ContentValues(initialValues);

        } else {
            // Otherwise, create a new value map
            values = new ContentValues();
        }
        // Gets the current system time in milliseconds
        long now = System.currentTimeMillis();
        //修改:需要将毫秒数转换为时间的形式yy.MM.dd HH:mm:ss
        Date date = new Date(now);
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yy-MM-dd HH:mm:ss");
        simpleDateFormat.setTimeZone(TimeZone.getTimeZone("GMT+08:00"));
        String dateFormat = simpleDateFormat.format(date);//转换为yy.MM.dd HH:mm:ss形式的时间
        // If the values map doesn't contain the creation date, sets the value to the current time.
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_CREATE_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_CREATE_DATE, dateFormat);
        }
        // If the values map doesn't contain the modification date, sets the value to the current
        // time.
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateFormat);
        }

        // If the values map doesn't contain a title, sets the value to the default title.
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_TITLE) == false) {
            Resources r = Resources.getSystem();
            values.put(NotePad.Notes.COLUMN_NAME_TITLE, r.getString(android.R.string.untitled));
        }

        // If the values map doesn't contain note text, sets the value to an empty string.
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_NOTE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_NOTE, "");
        }

        // Opens the database object in "write" mode.
        SQLiteDatabase db = mOpenHelper.getWritableDatabase();

        // Performs the insert and returns the ID of the new note.
        long rowId = db.insert(
            NotePad.Notes.TABLE_NAME,        // The table to insert into.
            NotePad.Notes.COLUMN_NAME_NOTE,  // A hack, SQLite sets this column value to null
                                             // if values is empty.
            values                           // A map of column names, and the values to insert
                                             // into the columns.
        );
```
#### 运行结果展示
![github](https://github.com/yunyun319/NotePad1/blob/master/notepadpicture.png "github")

