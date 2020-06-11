
# 使用FireBase机器学习套件的新功能autoML搭建训练发布模型，并在Android上使用进行图片分类
@[TOC](目录)

## 简介
AutoML Vision Edge 为 Firebase 的一项新功能，在firebase控制台，提供一组图片和相应的标签，能够帮助我们训练模型，并发布，之后我们就可以在安卓应用中使用该模型为设备端图片加标签，支持远程管理模型，多个应用同时使用。接下来这篇博客，就是主要实现：
**搭建 Android Studio的 firebase 开发环境、上传、训练数据集、发布模型，并在Android上使用进行图片分类。**

## 将 Firebase 添加 Android 项目
要想使用FireBase的机器学习套件（ML Kit），要先把FireBase添加到Android项目中。

### 项目环境

- 需要安装 3.5 以上版本的 Android Studio。
- 在新建项目的时候，需要确保 Android 应用符合以下条件：
  - 设置API 最低版本位 16 以上。
  - 使用 Gradle 4.1 或更高版本。

### 使用 Firebase 控制台添加 Firebase

将 Firebase 添加到应用，需要在 Firebase 控制台中和打开的 Android 项目中执行若干任务（例如，从控制台下载 Firebase 配置文件，然后将配置文件移动到 Android 项目中）。

#### 第一步：创建 Firebase 项目

您必须先创建一个 Firebase 项目，并将其关联到您的 Android 应用，然后才能将 Firebase 添加到您的 Android 应用。

 <img src="https://img-blog.csdnimg.cn/20200611175834449.png"   width="70%">

 <img src="https://img-blog.csdnimg.cn/20200611180512990.png"   width="65%">

#### 第二步：在 Firebase 中注册应用

拥有 Firebase 项目后，就可以向其中添加 Android 应用了。

在Firebase刚刚建好的项目中添加应用，点击添加中间的**添加安卓应用**按钮就行。

 <img src="https://img-blog.csdnimg.cn/20200611180931592.png"   width="65%">

1. 在 **Android 软件包名称**字段中输入您的AppId。

   确保输入应用实际使用的 ID。在向 Firebase 项目注册应用后，将无法添加或修改此值。
   可以在项目的app/build中找到：

   <img src="https://img-blog.csdnimg.cn/20200611184404946.png"   width="70%">

2. （可选）根据设置工作流的提示输入其他应用信息。

   别名是方便内部使用的标识符，只有您能在 Firebase 控制台中看到。

3. 点击**注册应用**。

 <img src="https://img-blog.csdnimg.cn/20200611184729615.png"   width="70%">

#### 第三步：添加 Firebase 配置文件

1. 将 Firebase Android 配置文件添加到您的应用：

   1. 点击**下载 google-services.json** 以获取 Firebase Android 配置文件 (`google-services.json`)。
      - 我们可以随时再次下载 Firebase Android 配置文件。
      - 请确保该配置文件名未附加其他字符，如 `(2)`。
   2. 接着将配置文件移动到应用的模块（应用级）目录中。

      <img src="https://img-blog.csdnimg.cn/20200611201122134.png"   width="50%">


   **注意**：该 Firebase 配置文件包含项目的唯一、非机密标识符。


2. 要在 Android 应用中启用 Firebase 产品，请将 Google 服务插件添加到 Gradle 文件中。

   1. 在根级（项目级）Gradle 文件 (`build.gradle`) 中添加相应规则，以包含 Google 服务插件。此外，请确认您是否拥有 Google 的 Maven 代码库。

      ```properties
      buildscript {
      
        repositories {
          //  // 添加下面一行:
          google()  // Google's Maven repository
        }
      
        dependencies {
          // ...
      
          // 添加下面一行:
          classpath 'com.google.gms:google-services:4.2.0'  // Google Services plugin
        }
      }
      
      allprojects {
        // ...
      
        repositories {
          // 添加下面一行:
          google()  // Google's Maven repository
          // ...
        }
      }
      ```

   2. 在Gradle 文件（通常是 `app/build.gradle`）中，在文件末尾添加一行内容。

      ```properties
      apply plugin: 'com.android.application'
      
      android {
        // ...
      }
      
      // 添加以下内容:
      apply plugin: 'com.google.gms.google-services'  // Google Play services Gradle plugin
      ```

#### 第四步：将 Firebase SDK 添加到APP



在（应用级）Gradle 文件（通常是 `app/build.gradle`）中，添加核心 Firebase SDK 的依赖项：

   ```properties
   buildscript {
     repositories {
       // Check that you have the following line (if not, add it):
       google()  // Google's Maven repository
     }
     dependencies {
       ...
       // Add this line
       classpath 'com.google.gms:google-services:4.3.3'
     }
   }
   
   allprojects {
     ...
     repositories {
       // Check that you have the following line (if not, add it):
       google()  // Google's Maven repository
       ...
     }
   }
   ```

应用级 build.gradle（`<项目>/<应用模块>/build.gradle`）：





```properties
apply plugin: 'com.android.application'
// Add this line
apply plugin: 'com.google.gms.google-services'

dependencies {
  // add the Firebase SDK for Google Analytics
  implementation 'com.google.firebase:firebase-analytics:17.2.2'
  // add SDKs for any other desired Firebase products
  // https://firebase.google.com/docs/android/setup#available-libraries
}
```


最后，按 IDE 中显示的栏中的“立即同步”：


1. 同步您的应用以确保所有依赖项都具有必要的版本。

2. 运行应用，向 Firebase 发送已成功集成 Firebase 的验证信息。

   设备日志将显示说明初始化已完成的 Firebase 验证信息。如果我们是在具有网络访问权限的模拟器上运行应用，则 Firebase 控制台会通知说应用连接已完成。


<img src="https://img-blog.csdnimg.cn/20200611202128162.png"   width="65%">


## 使用 "ML KIT" 的 autoML 搭建训练发布模型

### 第一步：下载数据集：

我这里使用的是使用 TensorFlow 的 flower-image 数据集，创建图像分类或标签模型，在训练了该模型后，将其用于应用程序中的设备上图像标签。

这个数据集又5种标签的雏菊，蒲公英，玫瑰，向日葵和郁金香花，这样后面的 app 就可以使用模型标识图像的标签之一。

下载[花卉图像](https://drive.google.com/file/d/1bgJqcWNHxEkVeVpjHEGmHm7HYnWETc_s/view)数据集。



### 第二步：上传和训练数据集：

转到 Firebase 控制台-> Machine Learning，然后单击 “AutoML” 。


<img src="https://img-blog.csdnimg.cn/20200611203051955.png"   width="70%">



现在，单击**添加数据集**。


给数据集命名并选择第二个选项，然后单击继续。


<img src="https://img-blog.csdnimg.cn/20200611203202901.png"   width="70%">



现在，单击浏览文件，然后为花朵图像数据集选择一个.zip文件，该文件已在前面的**第一步**中下载。


<img src="https://img-blog.csdnimg.cn/20200611203310684.png"   width="70%">

现在，等待所有三个步骤都已完成。

之后，点击训练模型


现在选择第三个选项（高精度），然后单击开始训练。


<img src="https://img-blog.csdnimg.cn/20200611203414226.png"   width="70%">


现在，等待模型训练完成。



<img src="https://img-blog.csdnimg.cn/20200611235330198.png"   width="70%">

### 第三步：使用训练完成的模型：

在模型列表中单击第一个模型，并在模型名称上记下我们以后需要的名称

<img src="https://img-blog.csdnimg.cn/20200611235459335.png"   width="70%">


现在，就可以使用模型了

先查看一下刚刚训练完的模型的评估结果：

<img src="https://img-blog.csdnimg.cn/20200611203738727.png"   width="70%">

<img src="https://img-blog.csdnimg.cn/20200611203810186.png"   width="70%">

## 在 Android Studio 上编写 app 使用该模型

我们可以通过2种方式使用此模型
（1）远程（发布到firebase并在运行时从您的应用程序远程加载）
（2）本地（与应用程序下载并捆绑）


首先，通过单击“发布”按钮来发布模型，然后单击“下载”并将zip文件保存在所需的位置。


<img src="https://img-blog.csdnimg.cn/20200611203947387.png"   width="70%">

之后，解压缩下载的zip文件，然后复制所有三个文件。

<img src="https://img-blog.csdnimg.cn/20200611204026120.png"   width="70%">

现在转到Android Studio，然后

### 创建 Assets 文件夹绑定本地模型

在应用程序上单击鼠标右键，选择“NEW”->“Folder”->“Assets”


<img src="https://img-blog.csdnimg.cn/20200611204103652.png"   width="70%">


然后单击完成。
现在，通过右键单击assss-> New-> Directory在assets文件夹中创建目录

给出一个名字，例如model，然后点击回车。
现在，将所有三个复制的文件粘贴到此文件夹中。


<img src="https://img-blog.csdnimg.cn/20200611204141620.png"   width="35%">



### 修改 build.gradle 以使用模型
将以下内容添加到应用的build.gradle文件中，以确保Gradle在构建应用时不会压缩模型文件：




```properties
android {
    // ...
    aaptOptions {
        noCompress“ tflite”
    }
}
```



如下所示：

<img src="https://img-blog.csdnimg.cn/20200611204645547.png"   width="70%">


现在，在应用程序级别build.gradle中为ML Kit Android库添加以下两个依赖项：




```java
implementation 'com.google.firebase：firebase-ml-vision：24.0.1' 
implementation 'com.google.firebase：firebase-ml-vision-automl：18.0.3'
```




现在添加一个Button，ImageView和TextView来选择图像，显示图像并在活动布局中显示带有谓词百分比的标签。



```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@drawable/timg">

    <ImageView
        android:id="@+id/image"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_above="@+id/selectImage"
        android:layout_marginBottom="50dp" />

    <Button
        android:id="@+id/selectImage"
        android:layout_width="136dp"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:background="@color/orange_normal"
        android:text="Select Image !"
        android:textColor="@color/colorWite" />

    <TextView
        android:id="@+id/text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/selectImage"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="50dp"
        android:textColor="@android:color/black"
        android:textSize="16sp" />
</RelativeLayout>
```

### 从手机上获取图片

要想判断图片的标签，首先肯定是要在手机上获取图片

打开android studio并将此依赖关系粘贴到应用程序级别的build.gradle文件中，如下所示：

```xml
implementation 'com.theartofdev.edmodo:android-image-cropper:2.7.+'
```

通过添加CropImageActivity来修改AndroidMainfest.xml：

```xml
<activity
android:name="com.theartofdev.edmodo.cropper.CropImageActivity"
android:screenOrientation="portrait"
android:theme="@style/Base.Theme.AppCompat"/> 
```


打开android studio并将此依赖关系粘贴到应用程序级别的build.gradle文件中，如下所示：

```xml
implementation 'com.theartofdev.edmodo:android-image-cropper:2.7.+'
```

通过添加CropImageActivity来修改AndroidMainfest.xml：

```xml
<activity
android:name="com.theartofdev.edmodo.cropper.CropImageActivity"
android:screenOrientation="portrait"
android:theme="@style/Base.Theme.AppCompat"/> 
```

点击按钮即可打开CropImageActivity：

```java
       button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                  CropImage.activity().start(MainActivity.this);
             //   fromRemoteModel();
            }
        });
```

最后，覆盖活动结果并更新ImageView：

```java
 @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == CropImage.CROP_IMAGE_ACTIVITY_REQUEST_CODE) {
            CropImage.ActivityResult result = CropImage.getActivityResult(data);
            if (resultCode == RESULT_OK) {
                if (result != null) {
                    Uri uri = result.getUri(); // 图片在手机上的路径
                    imageView.setImageURI(uri); // 设置图片到 imageview
                    textView.setText(""); // 为了之前的文本不会被添加到新中
                      setLabelerFromLocalModel(uri);
                   // setLabelerFromRemoteLabel(uri);
                } else
                    progressDialog.cancel();
            } else
                progressDialog.cancel();
        }
    }
```






### 获得输入图像标签的方式：
**主要有以下三步：**

1.加载模型
2.准备输入图像
3.运行图像标签器

#### 1.对于远程模型：
  #####  1. 声明所有变量

```java
    FirebaseAutoMLRemoteModel remoteModel; // 用来加载远端仓库模型
    FirebaseVisionImageLabeler labeler; // 用于运行图像标签器
    FirebaseVisionOnDeviceAutoMLImageLabelerOptions.Builder optionsBuilder; // 使用哪个选项在本地或远程运行标签器
    ProgressDialog progressDialog; // 显示进度对话框时，模型正在下载…
    FirebaseModelDownloadConditions conditions; // 下载模型的conditions
    FirebaseVisionImage image; // 准备输入图片
    TextView textView; // 用于显示输入图片的标签
    Button button; // 从设备中选择图像
    ImageView imageView; // 用于显示选定的图像
    private FirebaseAutoMLLocalModel localModel;
```


<img src="https://img-blog.csdnimg.cn/20200611214941523.png"   width="70%">

#####  2. 加载模型


<img src="https://img-blog.csdnimg.cn/20200611215928323.png"   width="70%">

<img src="https://img-blog.csdnimg.cn/2020061121594045.png"   width="70%">



##### 3.准备输入图像

<img src="https://img-blog.csdnimg.cn/20200611221019569.png"   width="70%">

##### 4.运行图像标签器

<img src="https://img-blog.csdnimg.cn/20200611221148216.png"   width="70%">

<img src="https://img-blog.csdnimg.cn/20200611221148193.png"   width="70%">

#### 2.对于本地模型：
1. 声明所有变量并选择图像 
2. 加载模型
3. 运行图像标签器

只需要稍作修改：

这里放入完整代码：

```java
package com.developndesign.firebaseautomlvisionedge;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

import android.app.ProgressDialog;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.TextView;
import android.widget.Toast;

import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.OnFailureListener;
import com.google.android.gms.tasks.OnSuccessListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.FirebaseApp;
import com.google.firebase.ml.common.FirebaseMLException;
import com.google.firebase.ml.common.modeldownload.FirebaseModelDownloadConditions;
import com.google.firebase.ml.common.modeldownload.FirebaseModelManager;
import com.google.firebase.ml.vision.FirebaseVision;
import com.google.firebase.ml.vision.automl.FirebaseAutoMLLocalModel;
import com.google.firebase.ml.vision.automl.FirebaseAutoMLRemoteModel;
import com.google.firebase.ml.vision.common.FirebaseVisionImage;
import com.google.firebase.ml.vision.label.FirebaseVisionImageLabel;
import com.google.firebase.ml.vision.label.FirebaseVisionImageLabeler;
import com.google.firebase.ml.vision.label.FirebaseVisionOnDeviceAutoMLImageLabelerOptions;
import com.theartofdev.edmodo.cropper.CropImage;

import java.io.IOException;
import java.util.List;

public class MainActivity extends AppCompatActivity {
    FirebaseAutoMLRemoteModel remoteModel; // For loading the model remotely
    FirebaseVisionImageLabeler labeler; //For running the image labeler
    FirebaseVisionOnDeviceAutoMLImageLabelerOptions.Builder optionsBuilder; // Which option is use to run the labeler local or remotely
    ProgressDialog progressDialog; //Show the progress dialog while model is downloading...
    FirebaseModelDownloadConditions conditions; //Conditions to download the model
    FirebaseVisionImage image; // preparing the input image
    TextView textView; // Displaying the label for the input image
    Button button; // To select the image from device
    ImageView imageView; //To display the selected image
    private FirebaseAutoMLLocalModel localModel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        textView = findViewById(R.id.text);
        button = findViewById(R.id.selectImage);
        imageView = findViewById(R.id.image);
        progressDialog = new ProgressDialog(MainActivity.this);
        progressDialog.setMessage("Please Wait...");
        progressDialog.setCanceledOnTouchOutside(false);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                  CropImage.activity().start(MainActivity.this);
                 // fromRemoteModel();
            }
        });
    }

    private void setLabelerFromLocalModel(Uri uri) {
        localModel = new FirebaseAutoMLLocalModel.Builder()
                .setAssetFilePath("model/manifest.json")
                .build();
        try {
            FirebaseVisionOnDeviceAutoMLImageLabelerOptions options =
                    new FirebaseVisionOnDeviceAutoMLImageLabelerOptions.Builder(localModel)
                            .setConfidenceThreshold(0.0f)
                            .build();
            labeler = FirebaseVision.getInstance().getOnDeviceAutoMLImageLabeler(options);
            image = FirebaseVisionImage.fromFilePath(MainActivity.this, uri);
            processImageLabeler(labeler, image);
        } catch (FirebaseMLException | IOException e) {
            // ...
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == CropImage.CROP_IMAGE_ACTIVITY_REQUEST_CODE) {
            CropImage.ActivityResult result = CropImage.getActivityResult(data);
            if (resultCode == RESULT_OK) {
                if (result != null) {
                    Uri uri = result.getUri(); //path of image in phone
                    imageView.setImageURI(uri); //set image in imageview
                    textView.setText(""); //so that previous text don't get append with new one
                      setLabelerFromLocalModel(uri);
                   // setLabelerFromRemoteLabel(uri);
                } else
                    progressDialog.cancel();
            } else
                progressDialog.cancel();
        }
    }

    private void setLabelerFromRemoteLabel(final Uri uri) {
        FirebaseModelManager.getInstance().isModelDownloaded(remoteModel)
                .addOnCompleteListener(new OnCompleteListener<Boolean>() {
                    @Override
                    public void onComplete(@NonNull Task<Boolean> task) {
                        if (task.isComplete()) {
                            optionsBuilder = new FirebaseVisionOnDeviceAutoMLImageLabelerOptions.Builder(remoteModel);
                            FirebaseVisionOnDeviceAutoMLImageLabelerOptions options = optionsBuilder
                                    .setConfidenceThreshold(0.0f)
                                    .build();
                            try {
                                labeler = FirebaseVision.getInstance().getOnDeviceAutoMLImageLabeler(options);
                                image = FirebaseVisionImage.fromFilePath(MainActivity.this, uri);
                                processImageLabeler(labeler, image);
                            } catch (FirebaseMLException | IOException exception) {
                                Log.e("TAG", "onSuccess: " + exception);
                                Toast.makeText(MainActivity.this, "Ml exeception", Toast.LENGTH_SHORT).show();
                            }
                        } else
                            Toast.makeText(MainActivity.this, "Not downloaded", Toast.LENGTH_SHORT).show();

                    }
                }).addOnFailureListener(new OnFailureListener() {
            @Override
            public void onFailure(@NonNull Exception e) {
                Log.e("TAG", "onFailure: "+e );
                Toast.makeText(MainActivity.this, "err"+e, Toast.LENGTH_SHORT).show();
            }
        });
    }

    private void processImageLabeler(FirebaseVisionImageLabeler labeler, FirebaseVisionImage image) {
        labeler.processImage(image).addOnCompleteListener(new OnCompleteListener<List<FirebaseVisionImageLabel>>() {
            @Override
            public void onComplete(@NonNull Task<List<FirebaseVisionImageLabel>> task) {
                progressDialog.cancel();
                for (FirebaseVisionImageLabel label : task.getResult()) {
                    String eachlabel = label.getText().toUpperCase();
                    float confidence = label.getConfidence();
                    textView.append(eachlabel + " - " + ("" + confidence * 100).subSequence(0, 4) + "%" + "\n\n");
                }
//                Intent intent = new Intent();
//                intent.setAction(Intent.ACTION_VIEW);
//                intent.setData(Uri.parse("https://www.google.com/search?q=" + task.getResult().get(0).getText()));
//                startActivity(intent);
            }
        }).addOnFailureListener(new OnFailureListener() {
            @Override
            public void onFailure(@NonNull Exception e) {
                Log.e("OnFail", "" + e);
                Toast.makeText(MainActivity.this, "Something went wrong! " + e, Toast.LENGTH_SHORT).show();
            }
        });
    }

    private void fromRemoteModel() {
        progressDialog.show();                                         /* model name*/
        remoteModel = new FirebaseAutoMLRemoteModel.Builder("Flowers_2020124223430").build();
        conditions = new FirebaseModelDownloadConditions.Builder().requireWifi().build();
        //first download the model
        FirebaseModelManager.getInstance().download(remoteModel, conditions)
                .addOnCompleteListener(new OnCompleteListener<Void>() {
                    @Override
                    public void onComplete(@NonNull Task<Void> task) {
                        CropImage.activity().start(MainActivity.this); // open image crop activity
                    }
                });
    }
}
```

## 效果演示

**运行代码的准备工作：**

打开模拟器的相册，

从本地拖一点花的照片到模拟器相册中，用于后面的分类使用。

<img src="https://img-blog.csdnimg.cn/20200611221750777.gif"   width="40%">


点击按钮，选择一张相册中 TULPS 的图片，开始判断花的种类

<img src="https://img-blog.csdnimg.cn/20200611221718300.gif"   width="40%">

判断结果如下：

<img src="https://img-blog.csdnimg.cn/202006112221076.png"   width="40%">

**该花为 TULPS 的概率最大，高达0.95，可见该模型的使用很成功。**



作者：李承睿
[原文链接](https://blog.csdn.net/qq_41595467/article/details/106693513)

