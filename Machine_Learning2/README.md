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


**您可以使用以下选项之一将 Android 应用关联到 Firebase：**

- [**选项 1**](https://firebase.google.com/docs/android/setup#console)：（推荐）使用 Firebase 控制台设置工作流。



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

```
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



# **训练模型**

我们正在使用Google的Teachable Machine来训练模型。这是一个了不起的工具，它使我们能够训练模型而无需任何机器学习知识。当前，它使我们能够训练模型以识别图像中的对象，特定声音或我们正在使用图像识别项目的姿势。

Teachable Machine:  https://teachablemachine.withgoogle.com/train/image

![image-20200522214708424](D:\Android_Lab\Machine_Learning2\image\image-20200522214708424.png)

现在，为了让我们的模型能够识别特定对象，我们可以提供该对象的多个图像，我们可以使用网络摄像头或上传一组图像，这里上传的图像越多，我们得到的结果越准确，请确保点击不同位置的图片，角度和环境

![image-20200523003100937](D:\Android_Lab\Machine_Learning2\image\image-20200523003100937.png)



- 单击预览以外的导出模型
- 在对话框中，选择“ Tensorflow Lite”→“Floating point”，然后单击“Download My Model”

提取下载的模型会得到一个“ tflite”文件和一个“ txt”文件，我们将在Android中使用

![image-20200523005446892](D:\Android_Lab\Machine_Learning2\image\image-20200523005446892.png)

将 Android 版机器学习套件库的依赖项添加到您的模块（应用级）Gradle 文件（通常为 `app/build.gradle`）：

```java
dependencies {
  // ...

  implementation 'com.google.firebase:firebase-ml-model-interpreter:22.0.0'
}
apply plugin: 'com.google.gms.google-services'
```



## 托管或打包您的模型



如需在您的应用中使用 TensorFlow Lite 模型进行推理，您必须先确保机器学习套件能够使用该模型。机器学习套件可以使用在 Firebase 中远程托管和/或与应用二进制文件打包的 TensorFlow Lite 模型。

通过在 Firebase 上托管模型，您可以在不发布新应用版本的情况下更新模型，并且可以使用远程配置和 A/B 测试为不同的用户组动态提供不同的模型。

如果您选择仅通过使用 Firebase 托管模型来提供模型，而不是与应用一起打包，则可以缩小应用的初始下载文件大小。但请注意，如果模型未与您的应用打包，那么在应用首次下载模型之前，任何与模型相关的功能都将无法使用。

将您的模型与应用打包，可以确保当 Firebase 托管的模型不可用时，应用的机器学习功能仍可正常运行。



### 在 Firebase 上托管模型

如需在 Firebase 上托管您的 TensorFlow Lite 模型，请执行以下操作：

1. 在 [Firebase 控制台](https://console.firebase.google.com/)的**机器学习套件**部分中，点击**自定义**标签页。

2. 点击**添加自定义模型**（或**再添加一个模型**）。

3. 指定一个名称，用于在 Firebase 项目中识别您的模型，然后上传 TensorFlow Lite 模型文件（通常以 `.tflite` 或 `.lite` 结尾）。

4. 在您应用的清单中声明需具有 INTERNET 权限：

   app\src\main\AndroidManifest.xml:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

将自定义模型添加到 Firebase 项目后，您可以使用指定的名称在应用中引用该模型。您随时可以上传新的 TensorFlow Lite 模型，并且您的应用会下载新模型，然后在应用下次重启时开始使用新模型。

### 将模型与应用打包

如需将 TensorFlow Lite 模型与您的应用打包，请将模型文件（通常以 `.tflite` 或 `.lite` 结尾）复制到您应用的 `assets/` 文件夹。（您可能需要先创建此文件夹，方法是右键点击 `app/` 文件夹，然后依次点击**新建 > 文件夹 > Assets 文件夹** (New > Folder > Assets Folder)。）

然后，将以下内容添加到应用的 `build.gradle` 文件中，以确保 Gradle 在构建应用时不会压缩模型：

```
android {

    // ...

    aaptOptions {
        noCompress "tflite"  // Your model's file extension: "tflite", "lite", etc.
    }
}
```

模型文件将包含在应用软件包中，并作为原始资源提供给机器学习套件使用。

## 加载模型

如需在您的应用中使用 TensorFlow Lite 模型，请首先为机器学习套件配置模型所在的位置：使用 Firebase 远程托管、在本地存储空间，或者两者同时。如果您同时指定了本地模型和远程模型，则可以在远程模型可用时使用远程模型，并在远程模型不可用时回退为使用本地存储的模型。



### 配置 Firebase 托管的模型

如果您使用 Firebase 托管您的模型，请创建一个 `FirebaseCustomRemoteModel` 对象，并在上传模型时指定您分配给模型的名称：

```java
FirebaseCustomRemoteModel remoteModel =
        new FirebaseCustomRemoteModel.Builder("your_model").build();
```

然后，启动模型下载任务，指定您希望允许下载的条件。如果模型不在设备上，或模型有较新的版本，则任务将从 Firebase 异步下载模型：

```java
FirebaseModelDownloadConditions conditions = new FirebaseModelDownloadConditions.Builder()
        .requireWifi()
        .build();
FirebaseModelManager.getInstance().download(remoteModel, conditions)
        .addOnCompleteListener(new OnCompleteListener<Void>() {
            @Override
            public void onComplete(@NonNull Task<Void> task) {
                // Success.
            }
        });
```

许多应用会通过其初始化代码启动下载任务，但您可以在需要使用该模型之前随时启动下载任务。

### 配置本地模型

如果您将模型与应用捆绑在一起，请创建 `FirebaseCustomLocalModel` 对象，并指定 TensorFlow Lite 模型的文件名：

```
FirebaseCustomLocalModel localModel = new FirebaseCustomLocalModel.Builder()
        .setAssetFilePath("your_model.tflite")
        .build();
```

### 根据模型创建解释器

配置模型来源后，根据其中一个模型创建 `FirebaseModelInterpreter` 对象。

如果您只有本地捆绑的模型，则只需根据 `FirebaseCustomLocalModel` 对象创建一个解释器即可：

```
FirebaseModelInterpreter interpreter;
try {
    FirebaseModelInterpreterOptions options =
            new FirebaseModelInterpreterOptions.Builder(localModel).build();
    interpreter = FirebaseModelInterpreter.getInstance(options);
} catch (FirebaseMLException e) {
    // ...
}
```

如果您使用的是远程托管的模型，则必须在运行之前检查该模型是否已下载。您可以使用模型管理器的 `isModelDownloaded()` 方法检查模型下载任务的状态。

虽然您只需在运行解释器之前确认这一点，但如果您同时拥有远程托管模型和本地捆绑模型，则在实例化模型解释器时执行此检查可能是有意义的：如果已下载，则根据远程模型创建解释器，否则根据本地模型进行创建。

```java
FirebaseModelManager.getInstance().isModelDownloaded(remoteModel)
        .addOnSuccessListener(new OnSuccessListener<Boolean>() {
            @Override
            public void onSuccess(Boolean isDownloaded) {
                FirebaseModelInterpreterOptions options;
                if (isDownloaded) {
                    options = new FirebaseModelInterpreterOptions.Builder(remoteModel).build();
                } else {
                    options = new FirebaseModelInterpreterOptions.Builder(localModel).build();
                }
                FirebaseModelInterpreter interpreter = FirebaseModelInterpreter.getInstance(options);
                // ...
            }
        });
```

如果您只有远程托管的模型，则应停用与模型相关的功能（例如使界面的一部分变灰或将其隐藏），直到您确认模型已下载。这可以通过将监听器附加到模型管理器的 `download()` 方法来实现：

```java
FirebaseModelManager.getInstance().download(remoteModel, conditions)
        .addOnSuccessListener(new OnSuccessListener<Void>() {
            @Override
            public void onSuccess(Void v) {
              // Download complete. Depending on your app, you could enable
              // the ML feature, or switch from the local model to the remote
              // model, etc.
            }
        });
```

## 指定模型的输入和输出

接下来，配置模型解析器的输入和输出格式。

TensorFlow Lite 模型采用一个或多个多维数组作为输入和输出。这些数组可以包含 `byte`、`int`、`long` 或 `float` 值。您必须根据模型采用的数组个数和维度（“形状”）配置机器学习套件。

如果您不知道模型输入和输出的形状和数据类型，可以使用 TensorFlow Lite Python 解析器检查模型。例如：

```python
import tensorflow as tf

interpreter = tf.lite.Interpreter(model_path="my_model.tflite")
interpreter.allocate_tensors()

# Print input shape and type
print(interpreter.get_input_details()[0]['shape'])  # Example: [1 224 224 3]
print(interpreter.get_input_details()[0]['dtype'])  # Example: <class 'numpy.float32'>

# Print output shape and type
print(interpreter.get_output_details()[0]['shape'])  # Example: [1 1000]
print(interpreter.get_output_details()[0]['dtype'])  # Example: <class 'numpy.float32'>
```

![image-20200610213017468](D:\Android_Lab\Machine_Learning2\image\image-20200610213017468.png)