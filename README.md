# Android 项目规范
---------------------------

本文档的目的是定义项目规范。这些应遵循整个 Android 项目以帮助我们保持整洁和统一的代码库。 🙂
此文大部分来自于翻译 [原文地址][1] 

## 1. 项目规范

### 1.1 项目结构

一个完整的项目，应保持下列结构：

	src/androidTest
	src/test
	src/commonTest
	src/main


**androidTest** - 此目录存放功能测试代码。    
**test** - 此目录存放单元测试代码。  
**commonTest** - 此目录存放单元测试与单元测试的代码集合。
**main** - 此目录存放主应用代码。

当你修改或者增加新的功能时，都应该遵循上述的目录结构。
采用这种目录结构使我们能够保持从任何测试代码与主应用程序代码分离。


### 1.2  文件命名

#### 1.2.1 Class 命名

任何类名都应该使用 UpperCamelCase （驼峰法）命名, 例如:

	AndroidActivity, NetworkHelper, UserFragment, PerActivity

任何android组件类都应该把**组件名**放在最末尾。例如：

	UserFragment, SignUpActivity, RateAppDialog, PushNotificationServer, NumberView

我们使用 UpperCamelCase 方法命名，是因为这样有助于我们单独创建名称类时，使得它更容易阅读。将组件名放在末尾，让使用者能够无比清晰，这个类是什么用途。例如，我们要寻找修改一个叫 RegistrationDialog 的文件，那么通过这个命名就能让我们容易地找到这个类。

#### 1.2.1 Resource 命名

当命名的资源文件，我们应该确保使用小写字母来命名他们，并下划线代替空格，例如：

	activity_main, fragment_user, item_post

这样命名使得我们可以很容易地找到自己要找的具体布局文件。在Android Studio中，资源文件是按字母顺序排序，意味着 Activity，Fragment 和其他类型的布局组合如同分组一样，所以我们知道从什么位置开始寻找一个文件。


#### 1.2.2.1 Drawable 命名


图片文件应该使用 `ic_` 做前缀与功能、大小、颜色一起命名。例如，在尺寸为24dp、白色的、“accept”、图标命名为：

	ic_accept_24dp_white

而在48dp、黑色的、“取消”图标则命名为：

	ic_cancel_48dp_black

我们使用这个命名约定，这样一个 drawable 文件则可以通过它的名字识别。如果颜色和大小没有出现在名称中，则开发者需要打开 drawable 文件以查找出这个信息。因此这节省了我们一点点时间。 :)

其他 drawable 文件应该使用相应的前缀，例如：

| 类型       | 前缀      | 例如                   |
|------------|-----------|------------------------|
| Selector   | selector_ | selector_button_cancel |
| Background | bg_       | bg_rounded_button      |
| Circle     | circle_   | circle_white           |
| Progress   | progress_ | progress_circle_purple |
| Divider    | divider_  | divider_grey           |

与上面 Resource 名件一致，在 Android Studio 中 drawable 也会进行类似分组。这也使得它一目了然。举个反例，当我们命名一个资源 button_cancel ，它可能意味着什么呢？这到底是一个选择资源或圆形按钮的背景？正确的命名有助于清除任何可能出现的歧义。

当创建选择不同点击的 selector state 资源文件时，应使用相应的后缀命名：

| State    | 后缀    | 举例             |
|----------|-----------|---------------------|
| Normal   | _normal   | btn_accept_normal   |
| Pressed  | _pressed  | btn_accept_pressed  |
| Focused  | _focused  | btn_accept_focused  |
| Disabled | _disabled | btn_accept_disabled |
| Selected | _selected | btn_accept_selected |

使用清晰的后缀,如上述有助于使它明显资源用于选择器（selector）的状态。同时也可以让颜色或其他标识符做为前缀。

#### 1.2.2.2 Layout 命名

在layout命名布局文件时，我们要以对应创建的 Android 组件的名称命名。 例如：

| 组件名           | Class Name      | Layout Name       |
|------------------|-----------------|-------------------|
| Activity         | MainActivity    | activity_main     |
| Fragment         | MainFragment    | fragment_main     |
| Dialog           | RateDialog      | dialog_rate       |
| Widget           | UserProfileView | view_user_profile |
| AdapterView Item | N/A             | item_follower     |

**Note:** 如果使用 merge 标签创建布局则应该使用 layout_ 前缀。


#### 1.2.2.3 Menu 命名

菜单文件不需要将 `menu_` 做前缀。这是因为它们都已经在menu包的资源目录中，因此它不做要求。

#### 1.2.2.4 Values 命名

所有的 values 文件下的文件名都应该为复数，例如：

	attrs.xml, strings.xml, styles.xml, colors.xml, dimens.xml
 

## 2. 代码规范

### 2.1 Java 语言规范

#### 2.1.1 不要忽略异常

要避免出现捕获异常，但不处理的情况。 例如：

	public void setUserId(String id) {
    	try {
        	mUserId = Integer.parseInt(id);
    	} catch (NumberFormatException e) { }
	}

当发生异常时，这段代码里没有任何信息提供给开发者与用户，因此这样不易调试，同时也会让用户感到迷惑。我们应当捕获异常，但同时也应该让错误打印到控制台用于调试，如有必要还需给用户提示。例如：

	public void setCount(String count) {
    	try {
        	count = Integer.parseInt(id);
    	} catch (NumberFormatException e) {
    		count = 0;
        	Log.e(TAG, "There was an error parsing the count " + e);
        	DialogFactory.showErrorMessage(R.string.error_message_parsing_count);
    	}
	}


在这里我们做了一些适当的处理：
- 通知用户，显示错误提示。
- 设置错误后变量默认值。
- 抛出对应的异常。
- 将错误打印到控制台。


#### 2.1.2 不要轻易使用 Exception 捕获异常 

不要轻易使用 Exception 捕获异常 :

	public void openCustomTab(Context context, Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (Exception e) {
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	}
	}

为什么?
使用 Exception 捕获异常非常不正确的，因为意味着你从来没有想到（包括如 RuntimeExceptions 中的 ClassCastException 异常）异常会被夹在应用程序级别的错误进行处理。它掩盖了我们处理异常代码的效率，也意味着如果有人添加了一个新的类型，我们在调用代码异常时，IDE 不会帮提示我们需要对这个错误进行不同的处理。而在大多数情况下，我们应该处理这样不同类型的异常。

因此当捕获我们可预知的异常时，我们应该采取相应的措施：

	public void openCustomTab(Context context, Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (ActivityNotFoundException e) {
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	}
	}


#### 2.1.3 多组异常

一段代码中，可能会出现多种异常。为了可读性和避免重复代码进行分组。例如，

你可以这么做：

	public void openCustomTab(Context context, @Nullable Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (ActivityNotFoundException e) {
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	} catch (NullPointerException e) {
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	} catch (SomeOtherException e) {
    		// Show some dialog
        }
	}

同样也可以这么做:

	public void openCustomTab(Context context, @Nullable Uri uri) {
    	Intent intent = buildIntent(context, uri);
    	try {
        	context.startActivity(intent);
    	} catch (ActivityNotFoundException e | NullPointerException e) {
        	Log.e(TAG, "There was an error opening the custom tab " + e);
    	} catch (SomeOtherException e) {
    		// Show some dialog
        }
	}


#### 2.1.4  全包声明

当我们声明引用时，应用使用全包声明。例如：

错误的用法:

    import android.support.v7.widget.*;

正确的用法:

    import android.support.v7.widget.RecyclerView;


#### 2.1.5 不要保留未使用的引用

有时，我们会删除一些代码，可能意味着一些引用已不再需要。如果是这种情况，那么相应的引用代码也应该被删除。


### 2.2 java 的 Style 规范

#### 2.2.1 fields 声明 & 命名

所有字段应该在文件的顶部声明，并遵循以下规则：


- private,非 static 字段命名不应用以m做前缀。如这样：

    userSignedIn, userNameText, acceptButton

而不是:

    mUserSignedIn, mUserNameText, mAcceptButton


- private，static字段名并不需要在开始加一个s。如这样：

    someStaticField, userNameText

而不是:

	sSomeStaticField, sUserNameText


- 所有其他的字段命名首字母也是用小写：

    int numOfChildren;
    String username;


- Static final (静态固定常量)字体命名，所有字母大写加 `_` 连接。


    private static final int PAGE_COUNT = 0;

不应使用那些没有意义的字段名。例如，

    int e; //列表中的元素数量

为什么不把给该字段有意义的名字摆在首位，而不是加上注释！

    int numberOfElements;

这样就很好啊!


#### 2.2.1.2 View 命名

当我们给视图命名，`view` 应该放在字段名最后，例如：

| View           | Name              |
|----------------|-------------------|
| TextView       | usernameView      |
| Button         | acceptLoginView   |
| ImageView      | profileAvatarView |
| RelativeLayout | profileLayout     |



#### 2.2.2 避免用集合类型命名

我们对集合创建变量时，避免用容器类型名称。例如，假如我们有一个包含用户 id 列表的 ArrayList 集合:

正确的做法：

    List<String> userIds = new ArrayList<>();

错误的做法：

    List<String> userIdList = new ArrayList<>();

因为集合名称很容易变化，这些命名往往被人遗忘 ，它不是完全必要的。


#### 2.2.3 避免类似的命名

当方法或者类的名称太过类似时，会影响其他开发者在阅读你的代码时产生一些混淆。

	hasUserSelectedSingleProfilePreviously

	hasUserSelectedSignedProfilePreviously

这样的代码在乍看之下可能很难理解，不容易区别。使用一个更清晰的方式命名这些，可以使开发人员更容易阅读你的代码。

#### 2.2.4 多个同类型参数的命名

当使用 Android Studio 自动生成代码的我们，很容易留下一些不规则的东西——无实意的命名参数！例如：

	public void doSomething(String s1, String s2, String s3)

阅读代码时我们很难理解这些参数的含义。那么正确的做法是：

	public void doSomething(String userName, String userEmail, String userId)

一目了解。 🙂

#### 2.2.5 关于命名

当我们在对 methods，classes , fields 命名时，我们应该：

是可读的：高效的命名意味着我们可以看名字并立即理解它，减少破译名字含义，意味着减少认知负荷。
是可说的：是可口述了，避免出现太过于尴尬的命名。
是可搜索的：前提不要拼错单词。例如我们试图找到搜索用户相关的内容，那么对于「user」关键字搜索，应该可以搜出我们想要的内容。
不要用拼音！


#### 2.2.6 注意命名的大小写问题

对于一个单词而言，只需大写首字母即可。例如：

| Do              | Don't           |
|-----------------|-----------------|
| setUserId       | setUserID       |
| String uri      | String URI      |
| int id          | int ID          |
| parseHtml       | parseHTML       |
| generateXmlFile | generateXMLFile |


#### 2.2.7 避免特殊的变量声明

变量的任何声明不应该使用任何特殊形式对齐方式，例如：

正常对齐方式:

    private int userId = 8;
    private int count = 0;
    private String username = "hitherejoe";

避免出现:

    private String username = "hitherejoe";
    private int userId      = 8;
    private int count       = 0;

这样的阅读并没有给我们太多便利。相反这样的对齐会让我们花费很多额外的时间，格式化整个类时，也会出现一些分歧。

#### 2.2.8 使用正确的空格缩进
  
如果是一个块的分段，应该使用4个空格缩进：

    if (userSignedIn) {
        count = 1;
    }

如果是一条语句太长分段，使用8个空格缩进：

    String userAboutText =
            "This is some text about the user and it is pretty long, can you see!"


### 2.2.9 if 相关

#### 2.2.9.1 用标准括号风格（右括号）

括号应始终在同一行之后的代码中使用。例如：

	class SomeClass {
    	private void someFunction() {
        	if (isSomething) {

        	} else if (!isSomethingElse) {

        	} else {

        	}
    	}
	}
    

避免这样做：

	class SomeClass
    {
    	private void someFunction()
    	{
        	if (isSomething)
        	{

        	}
        	else if (!isSomethingElse)
        	{

        	}
        	else
        	{

        	}
    	}
	}

其实两种方法并无对错可言，但既然说规范，那Android语言应该选择前者。

#### 2.2.9.2 单条件的 if 语句

对于简单的单行语句，我们可以不使用括号与换行，例如:

    if (user == null) return false;

然而，它仅适用于简单的操作，像下面这样内容比较多，就更适合括号：

    if (user == null) throw new IllegalArgumentExeption("Oops, user object is required.");

#### 2.2.9.3 嵌套if语句

在可预知的情况下，我们可以将条件语句合并：
如这个：

    if (userSignedIn) {
        if (userId != null) {

        }
    }
    
合并后：

    if (userSignedIn && userId != null) {

    }



#### 2.2.9.4 三元运算符。

在适当的情况下，三元运算符可以简洁代码：

    userStatusImage = signedIn ? R.drawable.ic_tick : R.drawable.ic_cross;

而正常的 if 语句则显示有点肿。

    if (signedIn) {
        userStatusImage = R.drawable.ic_tick;
    } else {
        userStatusImage = R.drawable.ic_cross;
    }

**Note:** 有一些时候不应使用三元运算符。如果if语句的逻辑比较复杂或则处理的内容比较多时。

### 2.2.10 Annotations（注解）

#### 2.2.10.1 注解的含义

我们比较常见的两种注解

**@Override:** 这个是 java document 的标识。含义就是此方法重载了父类的该方法。当我们重载了父类方法必须加上此注解

**@SuppressWarnings:** @SuppressWarnings 我们将它用于不可能消除的警告使用。如果警告通过这个“不可能消除”检验，@SuppressWarnings 注释必须使用，以便确保所有警告反映在代码的实际问题。

有关注解指引的更多信息可以在这里找到。

----------

注解应被经常使用。例如，使用 @Nullable 注释应该在一个字段可以预期为空的情况下使用。例如：

    @Nullable TextView userNameText;

    private void getName(@Nullable String name) { }

#### 2.2.10.2 注解 style

在应用中对 method 或类的注解，应该总是在声明前定义，且每行只有一个：

    @Annotation
    @AnotherAnnotation
    public class SomeClass {

      @SomeAnotation
      public String getMeAString() {

      }

    }

用字段中的注释，你应该确保注释保持在同一条线上。 例如：

    @Bind(R.id.layout_coordinator) CoordinatorLayout coordinatorLayout;

    @Inject MainPresenter mainPresenter;


我们这样做是因为它使语句更易于阅读。

#### 2.2.11 限制变量的作用域

局部变量的范围应保持在最低限度。通过这样做，你增加你的代码的可读性和可维护性，减少出错的可能性。每个变量都应该在封闭变量的所有用途最里面块声明。

局部变量应当在其第一次使用点声明。几乎每一个局部变量声明应包含一个初始化。如果你还没有足够的信息来合理地初始化变量，你应该推迟此声明。


#### 2.2.12 减少未使用的元素

所有未使用 **fields**， **imports**， **methods** 和 **classes** 都 应该从代码库移除，除非你具体的理由保证它还是有用的。

#### 2.2.13 import 语句排序

由于我们使用 Android Studio，所以 import 应始终自动排序。然而，在某些情况下，他们没有排序，那么他们应该这样被排序，如下：

1.Android 库的引入
2.从第三方库的引入
3.Java 和 javax 库引入
4.当前项目的其他类的引入

**Note:**

- 引入应在每个组内按字母顺序排列，用大写字母排在小写字母之前（例如Z在a前）
- 各样主要类别之间最好加上一个空行（android, com, JUnit, net, org, java, javax）

#### 2.2.14 log日志

Log 日志记录应用于在开发过程中，可能出现有用的错误消息或者需要输出在控制台其它信息。


| Log                               | Reason      |
|-----------------------------------|-------------|
| Log.v(String tag, String message) | verbose     |
| Log.d(String tag, String message) | debug       |
| Log.i(String tag, String message) | information |
| Log.w(String tag, String message) | warning     |
| Log.e(String tag, String message) | error       |


我们声明 Log 中的 Tag 时，应该放在类的顶部，并加上“static final”属性，例如：

    private static final String TAG = MyActivity.class.getName();

在一般情况下，我们发布正式版本时，Log 日志是必须被禁用：

    if (BuildConfig.DEBUG) {
        Log.d(TAG, "Here's a log message");
    }

**Note:** 当然如需要收集一些关键性的 bug，或者需要其他信息，也可以放开一部分。

#### 2.2.15 变量字段排序

在类文件的顶部声明的任何字段应该按以下顺序进行排序：

（从上致下）
1.枚举
2.常量
3.Dagger 注入
4.Butterknife 绑定
5.私有全局变量
6.公共全局变量

For example:

	public static enum {
		ENUM_ONE, ENUM_TWO
	}

	public static final String KEY_NAME = "KEY_NAME";
	public static final int COUNT_USER = 0;

	@Inject SomeAdapter someAdapter;

	@BindView(R.id.text_name) TextView nameText;
	@BindView(R.id.image_photo) ImageView photoImage;

	private int userCount;
	private String errorMessage;

	public int someCount;
	public String someString;

使用该排序规则有助于保持字段声明分组。

#### 2.2.16 method 成员排序


为了提高代码的可读性，将method成员如何排序方式也是很重要的。排序规范如下：

1.常量
2.字段
3.构造method
4.覆盖的方法和回调（公共或私营）
5.公共方法
6.私有方法
7.内部类或接口

例如:


    public class MainActivity extends Activity {

        private int count;

        public static newInstance() { }

        @Override
        public void onCreate() { }

        public setUsername() { }

        private void setupUsername() { }

        static class AnInnerClass { }

        interface SomeInterface { }

    }

在 Android 框架类中使用任何生命周期的方法应该在相应的生命周期排列。例如：


    public class MainActivity extends Activity {

        // Field and constructors

        @Override
        public void onCreate() { }

        @Override
        public void onStart() { }

        @Override
        public void onResume() { }

        @Override
        public void onPause() { }

        @Override
        public void onStop() { }

        @Override
        public void onRestart() { }

        @Override
        public void onDestroy() { }

        // public methods, private methods, inner classes and interfaces

    }

#### 2.2.17 方法中的参数排序

当我们定义一个 methods 时，参数按以下约定：

    public Post loadPost(Context context, int postId);


    public void loadPost(Context context, int postId, Callback callback);

**Context** 总是在第一位 ， **Callback** 放在最后一位。

#### 2.2.18 字符串常量，命名和值

当使用字符串常量，它们应该被声明为final static,并大写如下：

    public final static String TAG = “StringUtils”;

#### 2.2.19 枚举

首先，如果不是不得不实际需要，请不要使用枚举。如果其他的方法也可行的，那这个方法应该被首选：

如枚举：

    public enum SomeEnum {
        ONE, TWO, THREE
    }

可以这个替代：

    private static final int VALUE_ONE = 1;
    private static final int VALUE_TWO = 2;
    private static final int VALUE_THREE = 3;

#### 2.2.20 fragment & activity 参数传递

当我们通过使用 Intent 或 Bundle 传递数据时，key 值必须使用如下定义：

将数据在 Activity 或 Fragment 传递时，key值必须得静态常量字符，并包括**“EXTRA_”**做前缀。

    private static final String EXTRA_NAME = "EXTRA_NAME";
    

创建涉及有数据传递 Fragment 或 activity 的情况下，我们应该在对应的类里提供一个静态方法来获取新的实例，以参数的方法传递数据。例如：

**Activity**

    public static Intent getStartIntent(Context context, Post post) {
        Intent intent = new Intent(context, CurrentActivity.class);
        intent.putParcelableExtra(EXTRA_POST, post);
        return intent;
    }

**Fragment**

    public static PostFragment newInstance(Post post) {
        PostFragment fragment = new PostFragment();
        Bundle args = new Bundle();
        args.putParcelable(ARGUMENT_POST, post);
        fragment.setArguments(args)
        return fragment;
    }

#### 2.2.21 行长度限制

代码的行数应该不超过 100 个字符，这使得代码更易读。有时为了达到这个要求，我们可能需要：

- 提取数据到一个局部变量
- 提取逻辑到外部的方法
- 将单行换成多行

**Note:** 对于代码中的注释和 import 语句是没有 100 个字符的限制。

#### 2.2.21.1 换行技巧

当需要换行，在某些情况下，也是有规律的：

**运算符分隔**

当一行长代码中有运算符时，我们可以在运算符前进行分隔，例如：

    int count = countOne + countTwo - countThree + countFour * countFive - countSix
            + countOnANewLineBecauseItsTooLong;

我们可能在**“=”**后进行分隔：

    int count =
            countOne + countTwo - countThree + countFour * countFive + countSix;

**链接 method**

当涉及到链接的 method ，每个 method 的调用应该是在新的一行。

错误的做法:

    Picasso.with(context).load("someUrl").into(imageView);

正确的做法：

    Picasso.with(context)
            .load("someUrl")
            .into(imageView);

**多参数**

在这种method中包含多个参数的情况下，我们应该换行符放在适当的长度下。例如：对于这样多参数声明的方法中，我们应该适合参数的最后一个逗号后的分隔：

    private void someMethod(Context context, String someLongStringName, String text,
                                long thisIsALong, String anotherString) {               
    }             

当然在调用 method 时，我们也可以在每个参数的逗号后的分隔：

    someMethod(context,
            "thisIsSomeLongTextItsQuiteLongIsntIt",
            "someText",
            01223892365463456,
            "thisIsSomeLongTextItsQuiteLongIsntIt");


#### 2.2.22 Method 间距

两个 Method 之间只需要留一个空行的间隔就行。例如:

正确的做法:

    public String getUserName() {
        // Code
    }

    public void setUserName(String name) {
        // Code
    }

    public boolean isUserSignedIn() {
        // Code
    }

错误的做法（多个空行）:


    public String getUserName() {
        // Code
    }


    public void setUserName(String name) {
        // Code
    }


    public boolean isUserSignedIn() {
        // Code
    }

### 2.2.23 注释

#### 2.2.23.1 行注释

我们写注释时，尽可能的使用简单直白的语句。🙂
**Note:** 行注释并没有 100 字符限制，但也尽可能不要超过。

#### 2.2.23.2 JavaDoc 风格注释


虽然 Method 名称通常应足以了解 Method 的功能，但有时还是需要提供的 JavaDoc 注释。这有助于读者容易理解的方法的功能，以及 Methods 中的所有参数的含义。


    /**
     * 设置一个标题名
     * @param title 标题名
     */
    public void setTitle(String title) {
        this.title = title;
    }

#### 2.2.23.3 类注释

当创建类的注释，注释应该都是有意义的，描述性的，可以使用链接方法。例如：

    /**
      * RecyclerView adapter to display a list of {@link Post}.
      * Currently used with {@link PostRecycler} to show the list of Post items.
      */


### 2.2.24 代码分段

#### 2.2.24.1 Java code

如果你想在代码中的进行分段，你可以像这样:


    public void method() { }

    public void someOtherMethod() { }

    /********* Mvp Method Implementations  ********/

    public void anotherMethod() { }

    /********* Helper Methods  ********/

    public void someMethod() { }

但不要这样:

    public void method() { }

    public void someOtherMethod() { }

    // Mvp Method Implementations

    public void anotherMethod() { }

这使得分段落区分的更明显。

#### 2.2.24.2 字符串文件

在string.xml文件内定义的字符串资源应该以功能区分，例如：

    // User Profile Activity
    <string name="button_save">Save</string>
    <string name="button_cancel">Cancel</string>

    // Settings Activity
    <string name="message_instructions">...</string>

这不仅有助于保持字符串文件的整齐，而且它使得当我们字符需要改变时，更容易找到对应的字符串。

#### 2.2.24.3 RxJava链接

当使用RxJava链接时，我们用`.`做段落分隔。例如：


    return dataManager.getPost()
                .concatMap(new Func1<Post, Observable<? extends Post>>() {
                    @Override
                     public Observable<? extends Post> call(Post post) {
                         return mRetrofitService.getPost(post.id);
                     }
                })
                .retry(new Func2<Integer, Throwable, Boolean>() {
                     @Override
                     public Boolean call(Integer numRetries, Throwable throwable) {
                         return throwable instanceof RetrofitError;
                     }
                });

这使得更容易理解的 Rxjava 链接中的响应与事件。

## 2.3 XML样式规则

### 2.3.1 self=-closing样式

当我们在View布局视图中没有任何子views.应使用self=-closing关闭tags

正确的做法:

    <ImageView
        android:id="@+id/image_user"
        android:layout_width="90dp"
        android:layout_height="90dp" />

错误的做法

    <ImageView
        android:id="@+id/image_user"
        android:layout_width="90dp"
        android:layout_height="90dp">
    </ImageView>


### 2.3.2 Resource 命名

所有的资源名称和 ID 应该使用小写和下划线，例如：

    text_username, activity_main, fragment_user, error_message_network_connection


#### 2.3.2.1 ID 命名

所有的 ID 都应该使用该控件元素的名称作为前缀。

| Element        | Prefix    |
|----------------|-----------|
| ImageView      | image_    |
| Fragment       | fragment_ |
| RelativeLayout | layout_   |
| Button         | button_   |
| TextView       | text_     |
| View           | view_     |

例如:

    <TextView
        android:id="@+id/text_username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

而在有些时候，整个项目中的某个控件 view 通常只需要一个，如“toolbar”，我们也可以简单地给它的view定义一个简单的ID。 例如：```toolbar```

#### 2.3.2.2 Strings

所有的字符串名称应该与他们正在从引用的应用程序类型的一部分的前缀做开头。例如：

| Screen                | String         | Resource Name             |
|-----------------------|----------------|---------------------------|
| Registration Fragment | “Register now” | registration_register_now |
| Sign Up Activity      | “Cancel”       | sign_up_cancel            |
| Rate App Dialog       | “No thanks”    | rate_app_no_thanks        |

如果上面的前缀不能满足你，我们也可以使用下面的规则：

| 前缀    | 说明                                  |
|---------|----------------------------------------------|
| error_  | Used for error messages                      |
| title_  | Used for dialog titles                       |
| action_ | Used for option menu actions                 |
| msg_    | Used for generic message such as in a dialog |
| label_  | Used for activity labels                     |

有两个重要的原则需要注意：

 - 字符串资源不应该在整个应用上重复使用。这可能会导致一个问题，当涉及到我们只想改变一个字符串在特定的位置时,却使得对应的整个app里文字改变。

 - 字符串资源应**始终**在 String.xml 文件中定义，不要在 layout，或 class 类里出现。

#### 2.3.2.3 Styles and themes

所有 Styles & Themes,都应该使用 UpperCamelCase （驼峰法）命名。例如：

    AppTheme.DarkBackground.NoActionBar
    AppTheme.LightBackground.TransparentStatusBar

    ProfileButtonStyle
    TitleTextStyle


### 2.3.3 控件的属性排序

将属性排序不仅使得整洁，但它可以帮助我们更快寻找布局文件中的属性。有一些基本的规则：

1.view id
2.style
3.布局的宽度和高度
4.以`layout_`属性开头，并按字母顺序
5.剩余的属性，按字母顺序排序

例如：

    <Button
        android:id="@id/button_accept"
        style="@style/ButtonStyle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentStart="true"
        android:padding="16dp"
        android:text="@string/button_skip_sign_in"
        android:textColor="@color/bluish_gray" />



## 2.4 测试样式规则

### 2.4.1 单元测试

任何单元测试类应写入匹配测试的目标类，都应该加上“test”后缀名。例如：

| Class                | Test Class               |
|----------------------|--------------------------|
| DataManager          | DataManagerTest          |
| UserProfilePresenter | UserProfilePresenterTest |
| PreferencesHelper    | PreferencesHelperTest    |

所有的测试的 Method 都应加上`@ Test`注解：


    @Test
    public void methodNamePreconditionExpectedResult() { }

如果我们正在测试类包含许多不同的方法，那么测试类应该被拆分成多个测试类 - 这有助于保持测试更易于维护，更容易找到。例如，一个 DatabaseHelper 类可能需要被分成多个测试类如：

    DatabaseHelperUserTest
    DatabaseHelperPostsTest
    DatabaseHelperDraftsTest


# 3. Gradle 样式
## 3.1 依赖库

### 3.1.1 版本

有一些情况下，多个依赖库的版本号是一致的。例如：

    final SUPPORT_LIBRARY_VERSION = '23.4.0'

    compile "com.android.support:support-v4:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:recyclerview-v7:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:support-annotations:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:design:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:percent:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:customtabs:$SUPPORT_LIBRARY_VERSION"

这样我们只需要一次更改版本号，则能更新多个依赖库。

### 3.1.2 compile 分组

我们可以将依赖库按相关功能进行分组，例如：

    compile "com.android.support:percent:$SUPPORT_LIBRARY_VERSION"
    compile "com.android.support:customtabs:$SUPPORT_LIBRARY_VERSION"

    compile 'io.reactivex:rxandroid:1.2.0'
    compile 'io.reactivex:rxjava:1.1.5'

    compile 'com.jakewharton:butterknife:7.0.1'
    compile 'com.jakewharton.timber:timber:4.1.2'

    compile 'com.github.bumptech.glide:glide:3.7.0'


  [1]: https://github.com/bufferapp/android-guidelines/blob/master/project_style_guidelines.md
