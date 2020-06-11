# FireBase机器学习套件的2020最新功能autoML搭建训练发布模型，并在Android上使用进行图片分类



# 将 Firebase 添加到您的 Android 项目



## 前提条件

- 安装 [Android Studio](http://developer.android.com/sdk) 或将其更新为最新版本。
- 确保您的 Android 应用符合以下条件：
  - 目标为 API 级别 16 (Jelly Bean) 或更高版本
  - 使用 Gradle 4.1 或更高版本
- 设置可用于运行应用的设备或模拟器。
  - [模拟器](https://developer.android.com/studio/run/managing-avds)必须使用具有 Google Play 的模拟器映像。
- 使用您的 Google 帐号[登录 Firebase](https://console.firebase.google.com/)。

如果您还没有 Android 应用项目，只是想试用某项 Firebase 产品，则可以下载我们的[快速入门示例](https://firebase.google.com/docs/samples/)。



## **选项 1**：使用 Firebase 控制台添加 Firebase

将 Firebase 添加到您的应用需要在 [Firebase 控制台](https://console.firebase.google.com/)中和打开的 Android 项目中执行若干任务（例如，从控制台下载 Firebase 配置文件，然后将配置文件移动到 Android 项目中）。

### **第 1 步**：创建 Firebase 项目

您必须先创建一个 Firebase 项目，并将其关联到您的 Android 应用，然后才能将 Firebase 添加到您的 Android 应用。访问[了解 Firebase 项目](https://firebase.google.com/docs/projects/learn-more)以详细了解 Firebase 项目。

![image-20200523020435134](D:\Android_Lab\Machine_Learning2\image\image-20200523020435134.png)



![image-20200523020608863](D:\Android_Lab\Machine_Learning2\image\image-20200523020608863.png)



**创建 Firebase 项目**

### **第 2 步**：在 Firebase 中注册您的应用

拥有 Firebase 项目后，您就可以向其中添加 Android 应用了。

访问[了解 Firebase 项目](https://firebase.google.com/docs/projects/learn-more#best-practices)以详细了解将应用添加到 Firebase 项目的最佳做法和注意事项，包括如何处理多个应用版本。

1. 在 [Firebase 控制台的项目概览页面](https://console.firebase.google.com/)的中心位置，点击 **Android** 图标以启动设置工作流。

   如果您已向 Firebase 项目添加了应用，请点击**添加应用**以显示平台选项。

![image-20200609160838279](D:\Android_Lab\Machine_Learning2\image\image-20200609160838279.png)

1. 在 **Android 软件包名称**字段中输入您的[应用 ID](https://developer.android.com/studio/build/application-id)。

   确保输入应用实际使用的 ID。在向 Firebase 项目注册应用后，将无法添加或修改此值。

   - “应用 ID”有时被称为“软件包名称”。
   - 在您的模块（应用级）Gradle 文件（通常是 `app/build.gradle`）中找到此应用 ID（如 ID：`com.yourcompany.yourproject`）。

   ![image-20200609161002181](D:\Android_Lab\Machine_Learning2\image\image-20200609161002181.png)

2. （可选）根据设置工作流的提示输入其他应用信息。

   别名是方便内部使用的标识符，只有您能在 Firebase 控制台中看到。

   **注意**：Firebase 身份验证（使用 [Google 登录](https://firebase.google.com/docs/auth/android/google-signin)或[电话号码登录](https://firebase.google.com/docs/auth/android/phone-auth)功能时）和 [Firebase 动态链接](https://firebase.google.com/docs/dynamic-links)需要 [SHA-1 信息](https://developers.google.com/android/guides/client-auth)。

3. 点击**注册应用**。

![image-20200609162602629](D:\Android_Lab\Machine_Learning2\image\image-20200609162602629.png)

### **第 3 步**：添加 Firebase 配置文件

1. 将 Firebase Android 配置文件添加到您的应用：

   1. 点击**下载 google-services.json** 以获取 Firebase Android 配置文件 (`google-services.json`)。
      - 您可以随时再次下载 [Firebase Android 配置文件](http://support.google.com/firebase/answer/7015592)。
      - 请确保该配置文件名未附加其他字符，如 `(2)`。
   2. 接着将配置文件移动到应用的模块（应用级）目录中。

   ![image-20200609163026110](D:\Android_Lab\Machine_Learning2\image\image-20200609163026110.png)

   **注意**：该 Firebase 配置文件包含项目的唯一、非机密标识符。
   访问[了解 Firebase 项目](https://firebase.google.com/docs/projects/learn-more#config-files-objects)以详细了解此配置文件。

2. 要在 Android 应用中启用 Firebase 产品，请将 [Google 服务插件](https://developers.google.com/android/guides/google-services-plugin)添加到 Gradle 文件中。

   1. 在根级（项目级）Gradle 文件 (`build.gradle`) 中添加相应规则，以包含 Google 服务插件。此外，请确认您是否拥有 Google 的 Maven 代码库。

      ```
      buildscript {
      
        repositories {
          // Check that you have the following line (if not, add it):
          google()  // Google's Maven repository
        }
      
        dependencies {
          // ...
      
          // Add the following line:
          classpath 'com.google.gms:google-services:4.2.0'  // Google Services plugin
        }
      }
      
      allprojects {
        // ...
      
        repositories {
          // Check that you have the following line (if not, add it):
          google()  // Google's Maven repository
          // ...
        }
      }
      ```

   2. 在您的模块（应用级）Gradle 文件（通常是 `app/build.gradle`）中，在文件末尾添加一行内容。

      ```
      apply plugin: 'com.android.application'
      
      android {
        // ...
      }
      
      // Add the following line to the bottom of the file:
      apply plugin: 'com.google.gms.google-services'  // Google Play services Gradle plugin
      ```

### **第 4 步**：将 Firebase SDK 添加到您的应用

您可以将任何[受支持的 Firebase 产品](https://firebase.google.com/docs/android/setup#available-libraries)添加到 Android 应用中。我们建议您先从添加核心 Firebase SDK (`com.google.firebase:firebase-core`) 开始，该产品提供有 Google Analytics for Firebase 功能。

1. 在您的模块（应用级）Gradle 文件（通常是 `app/build.gradle`）中，添加核心 Firebase SDK 的依赖项：

   ```
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



1. （可选）添加您要使用的其他 [Firebase 库](https://firebase.google.com/docs/android/setup#available-libraries)的依赖项。

   一些适用于 Android 的 Firebase SDK 提供了备选 [Kotlin 扩展程序库](https://firebaseopensource.com/projects/firebase/firebase-android-sdk)。

2. 同步您的应用以确保所有依赖项都具有必要的版本。

3. 运行您的应用，向 Firebase 发送您已成功集成 Firebase 的验证信息。

   您的设备日志将显示说明初始化已完成的 Firebase 验证信息。如果您是在具有网络访问权限的模拟器上运行应用，则 [Firebase 控制台](https://console.firebase.google.com/)会通知您应用连接已完成。

![image-20200609165154142](D:\Android_Lab\Machine_Learning2\image\image-20200609165154142.png)



### 准备数据集

步骤2：下载Flower-图像数据集：

我们使用[TensorFlow](https://www.tensorflow.org/datasets/catalog/tf_flowers)的flower-image数据集 创建图像分类或标签模型，在训练了该模型后，您可以将其用于应用程序中的设备上图像标签。

在我们的例子中，我们使用5种标签或类型的雏菊，蒲公英，玫瑰，向日葵和郁金香花，并且模型标识图像的标签之一。

下载[花卉图像](https://drive.google.com/file/d/1bgJqcWNHxEkVeVpjHEGmHm7HYnWETc_s/view)数据集。



第3步：上传和训练数据集：

转到Firebase控制台-> Machine Learning，然后单击“AutoML”。



![image-20200611011926456](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611011926456.png)





现在，单击添加数据集。


给数据集命名并选择第二个选项，然后单击继续。

![image-20200611012753593](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611012753593.png)




现在，单击浏览文件，然后为花朵图像数据集选择一个.zip文件，该文件已在前面的步骤2中下载。



![image-20200611012832620](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611012832620.png)


现在，等待所有三个步骤都已完成。

之后，点击训练模型-


现在选择第三个选项（高精度），然后单击开始训练。



![image-20200611014022812](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611014022812.png)


现在，等待模型训练完成。

![image-20200611014812619](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611014812619.png)

第4步：使用训练有素的模型：

在模型列表中单击第一个模型，并在模型名称上记下我们以后需要的名称-



![image-20200611122211154](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611122211154.png)


现在，点击使用模型-



![image-20200611122259623](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611122259623.png)



![image-20200611160043073](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611160043073.png)

您可以通过2种方式使用此模型-
（i）远程（发布到firebase并在运行时从您的应用程序远程加载）
（ii）本地（与应用程序下载并捆绑）

您可以看到详细的比较b / w该页面上的两个。

我们在本地和远程都使用此模型。

因此，首先让我们通过单击“发布”按钮来发布模型，然后单击“下载”并将zip文件保存在所需的位置。



![image-20200611122401940](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611122401940.png)


之后，解压缩下载的zip文件，然后复制所有三个文件。



![image-20200611122727125](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611122727125.png)

现在转到Android Studio，然后
在应用程序上单击鼠标右键，选择“新建”->“文件夹”->“资产文件夹”



![image-20200611015433832](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611015433832.png)


然后单击完成。
现在，通过右键单击assss-> New-> Directory在assets文件夹中创建目录

给出一个名字，例如model，然后点击回车。
现在，将所有三个复制的文件粘贴到此文件夹中-

![image-20200611015510937](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611015510937.png)





步骤5：为图片

添加标签：将以下内容添加到应用的build.gradle文件中，以确保Gradle在构建应用时不会压缩模型文件：

```properties
android {
    // ...
    aaptOptions {
        noCompress“ tflite”
    }
}
```


如下所示：

![image-20200611015713629](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611015713629.png)


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



现在，我建议您通过[这篇文章](https://www.developndesign.in/2020/01/select-or-capture-and-crop-image-from.html)在继续之前[如何选择（捕获图像）android studio 2020](https://www.developndesign.in/2020/01/select-or-capture-and-crop-image-from.html)。

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
```



这就是我们获得输入图像标签的方式：
1.加载模型
2.准备输入图像
3.运行图像标签器

1.对于远程模型：-1 .声明所有变量

```java
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
```

![image-20200611142825081](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611142825081.png)

-2 .加载模型-

![image-20200611143217317](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611143217317.png)

![image-20200611143418859](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611143418859.png)



3.准备输入图像-

![image-20200611143539816](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611143539816.png)



4.运行图像标签器-

![image-20200611143714580](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611143714580.png)



![image-20200611144034498](D:\Download\mlkit4firebaseautomlvisionedge\image\image-20200611144034498.png)

2.对于本地模型：-1 .声明所有变量并选择图像-2 .加载模型-3 . 运行图像标签器- 现在您可以运行应用程序，然后尝试使用其他数据集。

只需要稍作修改：

放入完整代码：

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

运行代码的准备工作，打开模拟器的相册，

从本地拖一点花的照片到模拟器相册中，用于后面的分类使用。

###### <img src="D:\Download\mlkit4firebaseautomlvisionedge\image\GIF 2020-6-11 2-28-41.gif" alt="GIF 2020-6-11 2-28-41" style="zoom:50%;" />



开始判断花的种类

###### <img src="D:\Download\mlkit4firebaseautomlvisionedge\image\GIF 2020-6-11 14-02-41.gif" alt="GIF 2020-6-11 14-02-41" style="zoom:50%;" />

###### <img src="D:\Download\mlkit4firebaseautomlvisionedge\image\Screenshot_1591855402.png" alt="Screenshot_1591855402" style="zoom:50%;" />