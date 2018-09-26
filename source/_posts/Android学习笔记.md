---
title: Android学习笔记
date: 2018-08-22 10:38:17
categories: 笔记
tags: [Android]
toc:
---

# 工程架构分解
java：我们写Java代码的地方，业务功能都在这里实现
res：存放我们各种资源文件的地方，有图片，字符串，动画，音频等，还有各种形式的XML文件
<!--more-->
## res
### 图片资源：
drawable：存放各种位图文件，(.png，.jpg，.9png，.gif等)除此之外可能是一些其他的drawable类型的XML文件
mipmap-hdpi：高分辨率，一般我们把图片丢这里
mipmap-mdpi：中等分辨率，很少，除非兼容的的手机很旧
mipmap-xhdpi：超高分辨率，手机屏幕材质越来越好，以后估计会慢慢往这里过渡
mipmap-xxhdpi：超超高分辨率，这个在高端机上有所体现
### 布局资源：
layout：该目录下存放的就是我们的布局文件，另外在一些特定的机型上，我们做屏幕适配，比如480*320这样的手机，我们会另外创建一套布局，就行：layout-480x320这样的文件夹！
### 菜单资源：
menu：在以前有物理菜单按钮，即menu键的手机上，用的较多，现在用的并不多，菜单项相关的资源xml可在这里编写，不知道谷歌会不会出新的东西来替代菜单了~
### values目录：
demens.xml：定义尺寸资源
string.xml：定义字符串资源
styles.xml：定义样式资源
colors.xml：定义颜色资源
arrays.xml：定义数组资源
attrs.xml：自定义控件时用的较多，自定义控件的属性！
theme主题文件，和styles很相似，但是会对整个应用中的Actvitiy或指定Activity起作用，一般是改变窗口外观的！可在Java代码中通过setTheme使用，或者在Androidmanifest.xml中为<application...>添加theme的属性！ PS:你可能看到过这样的values目录：values-w820dp，values-v11等，前者w代表平板设备，820dp代表屏幕宽度；而v11这样代表在API(11)，即android 3.0后才会用到的！
### raw目录
用于存放各种原生资源(音频，视频，一些XML文件等)，我们可以通过openRawResource(int id)来获得资源的二进制流！其实和Assets差不多，不过这里面的资源会在R文件那里生成一个资源id而已
### 动画
动画有两种：属性动画和补间动画：
animator：存放属性动画的XML文件
anim：存放补间动画的XML文件

## 三个重要文件
MainActivity.java
activity_main：布局文件
AndroidManifest.xml ：Android配置文件

## 签名打包
Build -> Generate Signed APK

# Android中的UI组件
## View和ViewGroup
在Android APP中，所有的用户界面元素都是由View和ViewGroup的对象构成的。
 * View：所有可视化控件的父类,提供组件描绘和时间处理方法 
 * ViewGroup： View类的子类，可以拥有子控件,可以看作是容器
Android为我们提供了一个View和ViewGroup子类的集合，集合中提供了一些常用的输入控件(比如按钮和文本域)和各种各样的布局模式（比如线性或相对布局）。
## Android中有六大布局
### LinearLayout(线性布局)
wrap_content：按比例，orientation决定哪个方向比例划分
match_parent(fill_parent):需要计算

* 分割线：view或者LinearLayout divider + image
![LinearLayout](/assets/20180822_Android/LinearLayout.jpg)

### RelativeLayout(相对布局)
* gravity ignoreGravity
* parent父容器
* 兄弟容器
* margin：针对的是容器中的组件，可以设置为负数
* padding：针对的是组件中的元素
![RelativeLayout](/assets/20180822_Android/RelativeLayout.png)

### TableLayout(表格布局) 
* android:collapseColumns:设置需要被隐藏的列的序号
* android:shrinkColumns:设置允许被收缩的列的列序号
* android:stretchColumns:设置运行被拉伸的列的列序号
`列号从0开始算`，`可以设置多个,用逗号隔开比如"0,2",如果是所有列都生效,则用"*"号即可`

* android:layout_column="2":跳过第二个,直接显示到第三个格子处,从1开始算的!
* android:layout_span="4":合并4个单元格,也就说这个组件占4个单元格

### FrameLayout(帧布局)
* android:foreground:*设置改帧布局容器的前景图像
* android:foregroundGravity:设置前景图像显示的位置

### AbsoluteLayout(绝对布局)
### GridLayout(网格布局) 
* 先定义组件的对齐方式 `android:orientation` 水平或者竖直,设置多少行与多少列`android:columnCount`，`android:rowCount`
* 设置组件所在的行或者列,记得是从0开始算的,不设置默认每个组件占一行一列
* 设置组件横跨几行`android:layout_columnSpan`或者几列;设置完毕后,需要在设置一个填充:android:layout_gravity = "fill"

## 单位
### 长度、宽度单位
* dp(dip): device independent pixels(设备独立像素). 不同设备有不同的显示效果,这个和设备硬件有关，一般我们为了支持WVGA、HVGA和QVGA 推荐使用这个，不依赖像素。 
* px: pixels(像素). 不同设备显示效果相同，一般我们HVGA代表320x480像素，这个用的比较多。 
* pt: point，是一个标准的长度单位，1pt＝1/72英寸，用于印刷业，非常简单易用； 
* sp: scaled pixels(放大像素). 主要用于字体显示best for textsize。
### 字体单位
textSize：字体大小，单位一般是用sp

## UI组件
### TextView(文本框)
* id：为TextView设置一个组件id，根据id，我们可以在Java代码中通过findViewById()的方法获取到该对象，然后进行相关属性的设置，又或者使用RelativeLayout时，参考组件用的也是id。
* layout_width：组件的宽度，一般写：**wrap_content**或者**match_parent(fill_parent)**，前者是控件显示的内容多大，控件就多大，而后者会填满该控件所在的父容器；当然也可以设置成特定的大小，比如我这里为了显示效果，设置成了200dp。
* layout_height：组件的高度，内容同上。
* gravity：设置控件中内容的对齐方向，TextView中是文字，ImageView中是图片等等。
* autoLink识别链接类型
* html
* SpannableString&SpannableStringBuilder定制文本，前者针对的是不可变文本，而后者则是针对可变文本
* marqueeRepeatLimit跑马灯效果
* 字间距：android:textScaleX：控制字体水平方向的缩放，默认值1.0f，值是float。Java中setScaleX(2.0f)。
* 行间距：android:lineSpacingExtra：设置行间距，如"3dp" android:lineSpacingMultiplier：设置行间距的倍数，如"1.2"。Java代码中可以通过: setLineSpacing方法来设置。
* 自动换行： android:singleLine 设置，默认为 false。

### EditText(输入框)
### Button(按钮)与 ImageButton(图像按钮)
### ImageView(图像视图)
* src/background
1.background通常指的都是背景,而src指的是内容
2.当使用src填入图片时,是按照图片大小直接填充,并不会进行拉伸，而使用background填入图片,则是会根据ImageView给定的宽度来进行拉伸
3.Java代码中设置blackground和src属性:
前景(对应src属性):setImageDrawable( );
背景(对应background属性):setBackgroundDrawable( );

* adjustViewBounds设置缩放是否保存原图长宽比
adjustViewBounds为true才会生效的属性：
android:maxHeight:设置ImageView的最大高度
android:maxWidth:设置ImageView的最大宽度

* scaleType设置缩放类型

### RadioButton(单选按钮)&Checkbox(复选框)
* getChildCount( )获得按钮组中的单选按钮的数目；
* getChinldAt(i):根据索引值获取我们的单选按钮
* isChecked( ):判断按钮是否选中

获得选中的值的两种方式： 
1.为每个CheckBox添加事件：setOnCheckedChangeListener
2.弄一个按钮，在点击后，对每个checkbox进行判断:isChecked()；

### 开关按钮ToggleButton和开关Switch
### ProgressBar(进度条)
### SeekBar(拖动条)
### RatingBar(星级评分条)
### ScrollView(滚动条)

## Adapter
### MVC
1.Java
```
//要显示的数据
String[] strs = {"基神","B神","翔神","曹神","J神"};
//创建ArrayAdapter
ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,android.R.layout.simple_expandable_list_item_1,strs);
//获取ListView对象，通过调用setAdapter方法为ListView设置Adapter设置适配器
ListView list_test = (ListView) findViewById(R.id.list_test);
list_test.setAdapter(adapter);
```

2.Java
```
ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(this,
R.array.myarray,android.R.layout.simple_list_item_multiple_choice );
```
arrays.xml：
```
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
<string-array name="myarray">  
<item>语文</item>  
<item>数学</item>  
<item>英语</item>  
</string-array>      
</resources>
```

```
SimpleAdapter myAdapter = new SimpleAdapter(getApplicationContext(), listitem, R.layout.list_item, new String[]{"touxiang", "name", "says"}, new int[]{R.id.imgtou, R.id.name, R.id.says});
```

### BaseAdapter及复用
复用：
```
@Override
public View getView(int position, View convertView, ViewGroup parent) {
    ViewHolder holder = null;
    if(convertView == null){
        convertView = LayoutInflater.from(mContext).inflate(R.layout.item_list_animal,parent,false);
        holder = new ViewHolder();
        holder.img_icon = (ImageView) convertView.findViewById(R.id.img_icon);
        holder.txt_aName = (TextView) convertView.findViewById(R.id.txt_aName);
        holder.txt_aSpeak = (TextView) convertView.findViewById(R.id.txt_aSpeak);
        convertView.setTag(holder);   //将Holder存储到convertView中
    }else{
        holder = (ViewHolder) convertView.getTag();
    }
    holder.img_icon.setBackgroundResource(mData.get(position).getaIcon());
    holder.txt_aName.setText(mData.get(position).getaName());
    holder.txt_aSpeak.setText(mData.get(position).getaSpeak());
    return convertView;
}

static class ViewHolder{
    ImageView img_icon;
    TextView txt_aName;
    TextView txt_aSpeak;
}
```

### 数据为空时，setEmptyView 设置空页面
### 更新数据，重新绘制
notifyDataSetChanged()：判断是否需要重新渲染，如果当前item没有必要重新渲染 是不会重新渲染的，如果某个Item的状态发生改变，都会导致View的重绘，而重绘的并不是 所有的Item，而是View状态发生变化的那个Item

## GridView(网格视图)
## Spinner(列表选项框)
## AutoCompleteTextView(自动完成文本框)
## ExpandableListView(可折叠列表)
## ViewFlipper(翻转视图)
和ViewPager不同，ViewPager是一页页的，而ViewFlipper则是一层层的
## Toast(吐司)
##  AlertDialog(对话框)
## ProgressDialog(进度条对话框)
## DatePickerDialog(日期选择对话框)与TimePickerDialog(时间选择对话框)
## PopupWindow(悬浮框)
## 菜单(Menu)
## ViewPager
## DrawerLayout(官方侧滑菜单)

# 事件处理
## 基于监听的事件处理机制
1.直接用匿名内部类
```
btnshow.setOnClickListener(new OnClickListener() {    
    //重写点击事件的处理方法onClick()    
    @Override    
    public void onClick(View v) {    
        //显示Toast信息    
        Toast.makeText(getApplicationContext(), "你点击了按钮", Toast.LENGTH_SHORT).show();    
    }    
});    
```
2.使用内部类
```
//直接new一个内部类对象作为参数    
btnshow.setOnClickListener(new BtnClickListener());    

//定义一个内部类,实现View.OnClickListener接口,并重写onClick()方法    
class BtnClickListener implements View.OnClickListener    
{    
@Override    
public void onClick(View v) {    
Toast.makeText(getApplicationContext(), "按钮被点击了", Toast.LENGTH_SHORT).show();   
}    
}    
```
3.直接使用Activity作为事件监听器
```
//直接写个this    
btnshow.setOnClickListener(this);    
  
//重写接口中的抽象方法    
@Override    
public void onClick(View v) {    
Toast.makeText(getApplicationContext(), "点击了按钮", Toast.LENGTH_SHORT).show();         
}         
```
4.直接绑定到标签
```
<Button
android:onClick="myclick"/>

setContentView(R.layout.activity_main);     

//自定义一个方法,传入一个view组件作为参数    
public void myclick(View source)    
{    
Toast.makeText(getApplicationContext(), "按钮被点击了", Toast.LENGTH_SHORT).show();    
}    
```

# 组件
## Activity 
* 1. Activity用于显示用户界面，用户通过Activity交互完成相关操作
* 2. 一个App允许有多个Activity
## Service
## BroadCastReceiver
## ContentProvider

# Drawable
## Drawable分为两种 
* 普通的图片资源，在Android Studio中我们一般放到res/mipmap目录下， 另外我们如果把工程切换成Android项目模式，我们直接 往mipmap目录下丢图片即可，AS会自动分hdpi，xhdpi...
* 编写的XML形式的Drawable资源，我们一般把他们放到res/drawable目录 下，比如最常见的按钮点击背景切换的Selector

## 使用
1.在XML我们直接通过@mipmap或者@drawable设置Drawable即可 比如: android:background = "@mipmap/iv_icon_zhu" / "@drawable/btn_back_selctor" 
2.在Java代码中我们可以通过Resource的getDrawable(R.mipmap.xxx)可以获得drawable资源 如果是为某个控件设置背景，比如ImageView，我们可以直接调用控件.getDrawale()同样 可以获得drawable对象

## Android中drawable中的资源名称的约束
* 必须是：[a-z0-9_.]，注意小写
* 不能以数字开头，否则编译会报错

## 13种Drawable
### ColorDrawable（<color>）
1）Java中定义ColorDrawable:
```
ColorDrawable drawable = new ColorDrawable(0xffff2200);  
txtShow.setBackground(drawable); 
```
2）建立一个color.xml文件
```
比如：
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
<color name="material_grey_100">#fff5f5f5</color>
<color name="material_grey_300">#ffe0e0e0</color>
<color name="material_grey_50">#fffafafa</color>
<color name="material_grey_600">#ff757575</color>
<color name="material_grey_800">#ff424242</color>
<color name="material_grey_850">#ff303030</color>
<color name="material_grey_900">#ff212121</color>
</resources> 
```
获取颜色：
```
int mycolor = getResources().getColor(R.color.mycolor);    
btn.setBackgroundColor(mycolor);  
```
注意：直接定义颜色值要加上0x,而且不能把透明度漏掉。
```
int mycolor = 0xff123456;    
btn.setBackgroundColor(mycolor); 
```
3）使用系统定义好的color:
```
btn.setBackgroundColor(Color.BLUE); 
//或者
int getcolor = Resources.getSystem().getColor(android.R.color.holo_green_light);  
btn.setBackgroundColor(getcolor);  
```
4）利用静态方法argb来设置颜色:
```
txtShow.setBackgroundColor(Color.argb(0xff, 0x00, 0x00, 0x00));
```

### NinePatchDrawable（.9图）（<nine-patch>）
Android FrameWork在显示点九图时使用了高效的 图形优化算法,我们不需要特殊的处理，就可以实现图片拉伸的自适应
1.点9图不能放在mipmap目录下，而需要放在drawable目录下！
2.AS中的.9图，必须要有黑线，不然编译都不会通过

### ShapeDrawable（<shape>）
定义基本的几何图形。

### GradientDrawable
可以实现线性渐变,发散渐变和平铺渐变效果，核心节点：<gradient/>。

### BitmapDrawable（<bitmap>）
可以设置它包装的bitmap在BitmapDrawable区域中的绘制方式,有: 平铺填充,拉伸填充或保持图片原始大小。

### InsertDrawable（<insert>）
padding表示的是Drawable的内容与Drawable本身的边距
InsetDrawable表示的是两个Drawable与容器之间的边距
当控件需要的背景比实际的边框 小的时候,比较适合使用InsetDrawable

### ClipDrawable（<clip>）
从位图上剪下一个部分

### RotateDrawable（<rotate>）
### AnimationDrawable（<animation-list>）
### LayerDrawable（<layer-list>）
### TransitionDrawable（<transition>）
LayerDrawable的一个子类，TransitionDrawable只管理两层的Drawable。并且提供了透明度变化的动画，可以控制一层Drawable过度到另一层Drawable的动画效果。

### LevelListDrawable（<level-list>）
用来管理一组Drawable的,我们可以为里面的drawable设置不同的level， 当他们绘制的时候，会根据level属性值获取对应的drawable绘制到画布上

### StateListDrawable（<selector>）
可以为按钮设置不同状态的drawable。
selctor_btn.xml：
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="true" android:drawable="@drawable/shape_btn_pressed"/>
<item android:drawable="@drawable/shape_btn_normal"/>
</selector>
```

按钮设置：android:background="@drawable/selctor_btn"


