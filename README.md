# NotePad
This is an AndroidStudio rebuild of google SDK sample NotePad



### 简易版项目导入：

   此项目基于先前Android版本构造的简易版Notepad进行功能更新与改造。基于的简易版NotePad源码地址为：https://github.com/llfjfz/NotePad

### **项目架构分析**：

###### 1、**主要的类**:

NotesList类： 应用程序的入口，笔记本的首页面会显示笔记的列表
NoteEditor类 ：用于编辑笔记内容的Activity
TitleEditor类 ：用于编辑笔记标题的Activity
NoteSearch类 ：用于编辑查询笔记内容的Activity
NotePadProvider ： 这是记事本应用的ContentProvider，同时也是整个应用的关键所在

###### 2、主要的布局文件：

note_editor.xml : 笔记主页面布局
note_search.xml : 笔记内容查询布局
notelist_item.xml : 笔记主页面每个列表项布局
title_editor.xml : 修改笔记主题布局

###### 3、主要的菜单文件：

editor_options_menu.xml : 编辑笔记内容的菜单布局
list_context_menu.xml : 笔记内容编辑上下文菜单布局
list_options_menu.xml : 笔记主页面可选菜单布局



### 功能设计介绍：

###### 1.添加时间戳功能：

①：修改`noteslist_item.xml` 代码，为主页添加时间戳样式

#修改前代码：

```xml
<TextView
    android:id="@android:id/text2"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:gravity="center_vertical"
    android:paddingLeft="5dip"
    android:singleLine="true"
    android:textAppearance="?android:attr/textAppearanceLarge" />
```

#修改后代码：

```xml
<TextView
    android:id="@android:id/text2"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:gravity="center_vertical"
    android:paddingLeft="5dip"
    android:singleLine="true"
    android:textAppearance="?android:attr/textAppearanceLarge" />

<TextView
    android:id="@android:id/text1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center_vertical"
    android:paddingLeft="5dip"
    android:singleLine="true"
    android:textAppearance="?android:attr/textAppearanceLarge" />
```

②：修改在NoteList类的PROJECTION中添加COLUMN_NAME_MODIFICATION_DATE字段：

```java
private static final String[] PROJECTION = new String[] {
        NotePad.Notes._ID, // 0
        NotePad.Notes.COLUMN_NAME_TITLE, // 1
        NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,   //新增的代码
};
```



③：修改适配器内容，增加dataColumns中装配到ListView的内容，所以要同时增加一个ID标识来存放该时间参数。

```java
String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE,
        NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE //此字段为新增字段
} ;

// The view IDs that will display the cursor columns, initialized to the TextView in
// noteslist_item.xml
int[] viewIDs = { android.R.id.text1,android.R.id.text2 }; //修改后
```



④：在NoteEditor文件的updateNote方法中获取当前系统的时间，并对时间进行格式化。

```java
private final void updateNote(String text, String title) {
    ContentValues values = new ContentValues();
    long now = System.currentTimeMillis();
    Date date = new Date(now);
    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yy-MM-dd HH:mm:ss"); //修改后：
    simpleDateFormat.setTimeZone(TimeZone.getTimeZone("GMT+08:00")); //修改后：
    String dateFormat = simpleDateFormat.format(date); //修改后
    values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateFormat);
    if (mState == STATE_INSERT) {

        if (title == null) {
            int length = text.length();
            title = text.substring(0, Math.min(30, length));
            if (length > 30) {
                int lastSpace = title.lastIndexOf(' ');
                if (lastSpace > 0) {
                    title = title.substring(0, lastSpace);
                }
            }
        }
        values.put(NotePad.Notes.COLUMN_NAME_TITLE, title);
    } else if (title != null) {
        values.put(NotePad.Notes.COLUMN_NAME_TITLE, title);
    }
    values.put(NotePad.Notes.COLUMN_NAME_NOTE, text);
    getContentResolver().update(
            mUri,
            values,
            null,
            null
    );
}
```

⑤：记事本运行效果如下：

①：

![result2](https://github.com/FurMax/NotePad_Demo/blob/master/image/result2.png)

②：![result1](https://github.com/FurMax/NotePad_Demo/blob/master/image/result1.png)

③：![result3](https://github.com/FurMax/NotePad_Demo/blob/master/image/result3.png)

