# ML with Android

在本文中，我们将看到如何训练模型并将其集成到android应用中。如果您已经拥有ml模型，并且想了解如何将其集成到Android中，则可以向下滚动到“实施”部分。

# **训练模型**

我们正在使用Google的Teachable Machine来训练模型。这是一个了不起的工具，它使我们能够训练模型而无需任何机器学习知识。当前，它使我们能够训练模型以识别图像中的对象，特定声音或我们正在使用图像识别项目的姿势。

Teachable Machine:  https://teachablemachine.withgoogle.com/train/image

![image-20200522214708424](D:\Android_Lab\Machine_Learning\image\image-20200522214708424.png)

现在，为了让我们的模型能够识别特定对象，我们可以提供该对象的多个图像，我们可以使用网络摄像头或上传一组图像，这里上传的图像越多，我们得到的结果越准确，请确保点击不同位置的图片，角度和环境

![image-20200523003100937](D:\Android_Lab\Machine_Learning\image\image-20200523003100937.png)



- 单击预览以外的导出模型
- 在对话框中，选择“ Tensorflow Lite”→“Floating point”，然后单击“Download My Model”

提取下载的模型会得到一个“ tflite”文件和一个“ txt”文件，我们将在Android中使用

![image-20200523005446892](D:\Android_Lab\Machine_Learning\image\image-20200523005446892.png)

# **在Android上实施**

有两种方法可以在Android上集成我们的模型

1. 使用Tensorflow Lite库
2. 使用Firebase ml套件

对于我们的项目，我们将使用Firebase ml套件作为

- 设置容易
- 模型可以托管在Firebase上，也可以与应用捆绑在一起
- 我们可以更新模型而无需更新应用程序

**让我们开始吧**

- 在android studio上创建一个新项目
- 转到工具-> Firebase
- 在Firebase导航抽屉中，转到ML KIT并选择4个选项中的任何一个

![image-20200523012630205](D:\Android_Lab\Machine_Learning\image\image-20200523012630205.png)



- 单击“Connect to Firebase”，使用firebase登录，然后选择“create new Firebase project”。

- 创建项目后，单击“Add ML Kit to your app ”，接受更改…

- 在应用程序级别的Gradle文件中添加此依赖项

  implementation 'com.google.firebase:firebase-ml-model-interpreter:22.0.1'

![image-20200523020435134](D:\Android_Lab\Machine_Learning\image\image-20200523020435134.png)



![image-20200523020608863](D:\Android_Lab\Machine_Learning\image\image-20200523020608863.png)

![image-20200523140742202](D:\Android_Lab\Machine_Learning\image\image-20200523140742202.png)

![image-20200523140840105](D:\Android_Lab\Machine_Learning\image\image-20200523140840105.png)

![image-20200523140940998](D:\Android_Lab\Machine_Learning\image\image-20200523140940998.png)



![image-20200523143132445](D:\Android_Lab\Machine_Learning\image\image-20200523143132445.png)

![image-20200523142204439](D:\Android_Lab\Machine_Learning\image\image-20200523142204439.png)



![image-20200523021308454](D:\Android_Lab\Machine_Learning\image\image-20200523021308454.png)





在同一文件中添加此文件以确保未压缩本地模型文件

```java
android { 
...aaptOptions { 
    noCompress“ tflite” //您模型的文件扩展名：“ tflite”，“ lite”等
}...}
```

至此，我们已经成功为我们的应用设置了firebase

现在，要使用它，我们将必须将模型（.tflite文件）和标签文件添加到assets folder

- 通过右键单击app → new → folder → assets folder
- 复制将两个文件粘贴到assets folder
- 更新activity_main.xml文件

![image-20200523150954102](D:\Android_Lab\Machine_Learning\image\image-20200523150954102.png)



```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvIdentifiedItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Loading..."
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

- 在drawable folder中为其中一个对象（在本例中为香蕉）添加图像

![image-20200523151335980](D:\Android_Lab\Machine_Learning\image\image-20200523151335980.png)

- 在MainActivity.kt中添加此代码





















