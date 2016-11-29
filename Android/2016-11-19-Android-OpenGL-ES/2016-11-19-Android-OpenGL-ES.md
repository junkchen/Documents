# **Android OpenGL ES 概述** #

> 原文地址：  [https://developer.android.com/guide/topics/graphics/opengl.html](https://developer.android.com/guide/topics/graphics/opengl.html)

Android 提供了 OpenGL 去进行 2D 和 3D 图形的高效绘制。OpenGL 是一个跨平台的图形 API，对于 3D 图形硬件有一套标准的软件接口。Android 支持下面几个版本的 OpenGL ES 的 API：  

- OpenGL ES 1.0 and 1.1 - 这个版本是的 API 支持 Android 1.0 或者更高.

- OpenGL ES 2.0 - 这个版本的 API 支持 Android 2.2 (API level 8)  或者更高.

- OpenGL ES 3.0 - 这个版本的 API 支持 Android 4.3 (API level 18)  或者更高.

- OpenGL ES 3.1 - 这个版本的 API 支持 Android 5.0 (API level 21)  或者更高.

## **基础** ##

Android 对 OpenGL 的支持是通过系统层 API 和 NDK 实现的。在 Android framework 层提供了两个基础类使用 OpenGL ES 的 API 供我们创建和操作绘图。这两个类是： **GLSurfaceView** 和 **GLSurfaceView.Renderer**。  

- **GLSurfaceView**

	这个类是一个 View ，可以调用 OpenGL 相应的 API 绘制和操作图形，功能类似于 SurfaceView 。可以使用这个类创建一个 GLSurfaceView 的实例并且给它填一个 Renderer （渲染器）。当然，如果想要捕获屏幕的触摸事件，需要继承 GLSurfaceView 类去实现触摸监听。  

- **GLSurfaceView.Renderer**

	这个接口定义了在 GLSurfaceView 上绘制图形必要的方法。你必须用一个单独的类来实现这个接口，并且使用  GLSurfaceView.setRenderer() 方法将它关联到你的 GLSurfaceView 实例上。

	GLSurfaceView.Renderer 接口需要实现如下方法：

	- **onSurfaceCreated()**： 当 GLSurfaceView 创建的时候，系统会调用一次这个方法。使用这个方法去执行只需要出现一次的操作，如设置 OpenGL 的环境参数或者初始化 OpenGL 的图形对象。
	 
	- **onDrawFrame()**： 当 GLSurfaceView 每一次重绘的时候系统会调用这个方法。使用这个方法绘制图形或者重绘。
	
	- **onSurfaceChanged()**： 当 GLSurfaceView 形状改变的时候系统会调用这个方法，包括 GLSurfaceView 尺寸改变或者设备旋转屏幕。例如，当设备从竖屏旋转到横屏时系统就会调用这个方法。可以使用这个方法去处理 GLSurfaceView 容器发生的改变。

### **OpenGL ES 包** ###

当使用  GLSurfaceView 和 GLSurfaceView.Renderer 建立了一个视图容器后，就可以使用如下类调用 OpenGL 的 API 了：  

- **OpenGL ES 1.0/1.1 API Packages**

	- **android.opengl** - 这个包提供了一个静态接口访问 OpenGL ES 1.0/1.1 的类，比 *javax.microedition.khronos* 包的接口更高效。
		- GLES10
		- GLES10Ext
		- GLES11
		- GLES11Ext

	- **javax.microedition.khronos.opengles** - 这个包提供了 OpenGL ES 1.0/1.1 的标准实现。
		- GL10
		- GL10Ext
		- GL11
		- GL11Ext
		- GL11ExtensionPack

- **OpenGL ES 2.0 API Class**

	- **android.opengl.GLES20** - 这个包提供了接口到 OpenGL ES 2.0，从 Android 2.2（API level 8）开始使用。

- **OpenGL ES 3.0/3.1 API Packages**

	- **android.opengl** - 提供了接口访问 OpenGL ES 3.0/3.1 的类。 3.0 版从 Android 4.3（API level 18）开始使用，3.1 版从 Android 5.0（API level 21）开始使用。
	
		- GLES30
		- GLES31
		- GLES31Ext (Android Extension Pack)

## **声明 OpenGL 必要条件** ##

如果应用需要使用 OpenGL 特性，必须在  AndroidManifest.xml 文件中包含这些条件：  

- **OpenGL ES 版本要求** - 如果你的应用需要使用使用指定版本的 OpenGL ES，你必须在你的 manifest 文件中添加如下设置进行声明。  

	对于 OpenGL ES 2.0：  
	```.xml
	<!-- Tell the system this app requires OpenGL ES 2.0. -->
	<uses-feature android:glEsVersion="0x00020000" android:required="true" />
	```

	添加这些声明是因为 Google Play 限制你的应用在安装时不支持 OpenGL ES 2.0 。如果你的应用是仅支持 OpenGL ES 3.0,可以在 manifest 文件中指定：  

	对于 OpenGL ES 3.0：  
	```.xml
	<!-- Tell the system this app requires OpenGL ES 3.0. -->
	<uses-feature android:glEsVersion="0x00030000" android:required="true" />
	```

	对于 OpenGL ES 3.1：  
	```.xml
	<!-- Tell the system this app requires OpenGL ES 3.1. -->
	<uses-feature android:glEsVersion="0x00030001" android:required="true" />
	```

- **纹理压缩要求** - 如果你的应用使用纹理压缩格式，你必须声明格式支持，在 manifest 文件中使用 <support-gl-texture> 。
	
## **绘图对象的绘制坐标** ##

在 Android 设备上显示图形的一个基本问题就是它们的屏幕可以改变大小和形状。 OpenGL 假定一个方形，统一坐标系统，默认情况下，适当的绘制这些坐标到典型的非方形的屏幕上，假设它是理想的方形。  

<img src="./coordinates.png" />  
默认的 OpenGL 坐标系统(左)映射到典型的 Android 设备的屏幕上(右)。

上面插图的左图展示了 OpenGL 假定的统一的坐标系统，这些坐标如何映射到实际的横屏设备上的。为了解决这个问题可以使用 OpenGL 的投射模式和相机视图去转换坐标，以便绘图对象可以正确的显示比例。  

为了应用投射和相机视图，你可以创建一个投射矩阵和相机视图矩阵，然后应用它们到 OpenGL 的渲染管道。投射矩阵重新计算图像坐标让它们能够正确的映射显示在 Android 设备屏幕上。相机视图矩阵创建一个渲染对象特定视角位置的转换。  

### **OpenGL ES 1.0 中的投射 和 相机视图** ###

在 ES 1.0 的 API 中，你可以通过分别创建矩阵应用投射和相机视图，然后添加到 OpenGL 环境里。

1. **投射矩阵(Projection matrix)** - 使用屏幕上的几何形状创建一个投射矩阵，目的在于重新计算对象坐标达到显示时正确显示比例。下面的示例代码说明如何修改 onSurfaceChanged() 方法， GLSurfaceView.Renderer 实现创建一个投射矩阵基于屏幕的比例，并且应用它到渲染环境。

	```.java
	public void onSurfaceChanged(GL10 gl, int width, int height) {
	    gl.glViewport(0, 0, width, height);
	
	    // make adjustments for screen ratio
	    float ratio = (float) width / height;
	    gl.glMatrixMode(GL10.GL_PROJECTION);        // set matrix to projection mode
	    gl.glLoadIdentity();                        // reset the matrix to its default state
	    gl.glFrustumf(-ratio, ratio, -1, 1, 3, 7);  // apply the projection matrix
	}
	```
2. **相机转换矩阵(Camera transformation matrix)** - 当你使用投射矩阵调整了坐标系统，你也必须应用相机视图。下面的示例代码展示了如何修改  GLSurfaceView.Renderer 实现的 onDrawFrame() 方法，去应用一个模块视图，并且使用 GLU.gluLookAt() 去创建视图转换。

	```.java
	public void onDrawFrame(GL10 gl) {
	    ...
	    // Set GL_MODELVIEW transformation mode
	    gl.glMatrixMode(GL10.GL_MODELVIEW);
	    gl.glLoadIdentity();                      // reset the matrix to its default state
	
	    // When using GL_MODELVIEW, you must set the camera view
	    GLU.gluLookAt(gl, 0, 0, -5, 0f, 0f, 0f, 0f, 1.0f, 0.0f);
	    ...
	}
	```

### OpenGL ES 2.0 或更高版本的投射和相机视图 ###

在 ES 2.0 和 3.0 的 API 中，你应用投射和相机视图通过第一次添加一个矩阵成员到绘图对象的着色器上。用这个矩阵成员添加，你可以生成并应用投射和相机视图矩阵到你的图像上。  

1. **添加矩阵到着色器顶点(Add matrix to vertex shaders)** - 为视图投射矩阵创建一个变量。下面的示例代码，包含 uMVPMatrix 成员允许你去应用投射和相机视图矩阵到图形的坐标：

	```.java
	private final String vertexShaderCode =

    // This matrix member variable provides a hook to manipulate
    // the coordinates of objects that use this vertex shader.
    "uniform mat4 uMVPMatrix;   \n" +

    "attribute vec4 vPosition;  \n" +
    "void main(){               \n" +
    // The matrix must be included as part of gl_Position
    // Note that the uMVPMatrix factor *must be first* in order
    // for the matrix multiplication product to be correct.
    " gl_Position = uMVPMatrix * vPosition; \n" +

    "}  \n";
	```
  
2. **访问着色器矩阵(Access the shader matrix)** - 在创建了 vertex shader 应用到投射和相机视图后，你可以访问那个变量在应用的投射和相机视图矩阵中。下面的示例代码展示了如何修改 GLSurfaceView.Renderer  的  onSurfaceCreated() 实现方法去访问上面定义的 vertex shader 中的矩阵变量。  

	```.java
	public void onSurfaceCreated(GL10 unused, EGLConfig config) {
	    ...
	    muMVPMatrixHandle = GLES20.glGetUniformLocation(mProgram, "uMVPMatrix");
	    ...
	}
	```

3. **创建投射和相机视图矩阵(Create projection and camera viewing matrices)** - 生成一个投射和相机视图矩阵应用到图形对象。下面的示例代码展示了如何修改  GLSurfaceView.Renderer  中的 onSurfaceCreated() 和 onSurfaceChanged() 方法去创建相机视图矩阵基于不同比例设备的屏幕。  

	```.java
	public void onSurfaceCreated(GL10 unused, EGLConfig config) {
	    ...
	    // Create a camera view matrix
	    Matrix.setLookAtM(mVMatrix, 0, 0, 0, -3, 0f, 0f, 0f, 0f, 1.0f, 0.0f);
	}
	
	public void onSurfaceChanged(GL10 unused, int width, int height) {
	    GLES20.glViewport(0, 0, width, height);
	
	    float ratio = (float) width / height;
	
	    // create a projection matrix from device screen geometry
	    Matrix.frustumM(mProjMatrix, 0, -ratio, ratio, -1, 1, 3, 7);
	}
	```

4. **应用投射和相机视图矩阵(Apply projection and camera viewing matrices)** - 为了应用投射和相机视图转换，大量矩阵设置到着色器(vertext shader)中.下面的示例代码展示了如何修改  GLSurfaceView.Renderer 的  onDrawFrame() 方法去合并上面上面创建的投射矩阵和相机视图，然后通过 OpenGL 应用他们到图形对象的渲染器上。  

	```.java
	public void onDrawFrame(GL10 unused) {
	    ...
	    // Combine the projection and camera view matrices
	    Matrix.multiplyMM(mMVPMatrix, 0, mProjMatrix, 0, mVMatrix, 0);
	
	    // Apply the combined projection and camera view transformations
	    GLES20.glUniformMatrix4fv(muMVPMatrixHandle, 1, false, mMVPMatrix, 0);
	
	    // Draw objects
	    ...
	}
	```

## **形状的面和连线** ##

在 OpenGL 中，一个图形的面在三维空间由三个点或者更多的点组成。在三维空间的三点集合或多点集合有前后面之分（OpenGL 中称之为顶点(vertices)）。如何知道哪个面是前面还是后面？答案是去做连线，或者，你定义一个形状时的方向。  

<img src="./ccw-winding.png" />  
插图上的坐标集合绘制成图形是逆时针顺序。  

在这个例子中，三角形的点被定义为逆时针绘制。这些点的坐标定义了形状绘制时的连线方向。默认情况下，在 OpenGL 中，前面是逆时针绘制。  

为什么明确哪个面是前面很重要？这是使用 OpenGL 的一个通用特性，叫面剔除(face culling)。面剔除是 OpenGL 环境的一个选择，它允许渲染管道忽略（不计算或者绘制）形状的后面，从而节约时间、内存和处理周期：  

```.java
// enable face culling feature
gl.glEnable(GL10.GL_CULL_FACE);
// specify which faces to not draw
gl.glCullFace(GL10.GL_BACK);
```

如果你尝试使用面剔除的特性但是另一方面你不知道形状的前和后，你的 OpenGL 图形看起来就会位失真，或者不能全部显示。所以，你总是应该定义你的 OpenGL 图形为逆时针方绘制。  

> 注： OpenGL 是可以设置顺时针方向为前面的，但是，这样写了很多代码后，遇到问题去像有经验的开发者寻求帮助时可能会产生疑惑，所以不建议这样做。  

## **OpenGL 版本和设备兼容** ##

目前推荐使用 OpenGL 2.0 版。  

OpenGL ES Version| 	Distribution
:--|:--|
2.0 | 	46.0%
3.0 |	42.6%
3.1 |	11.4%
(Collected on August 1, 2016)  

OpenGL ES 1.0或1.1版 API 图像设计与 2.0或更高的版本相比有很大的区别。1.0 版有更多便利的方法和图形管道的处理，而2.0或更高的版本可使用 OpenGL 的着色器直接控制管道。所以你应该仔细考虑你的应用应该使用那个版本。  

OpenGL ES 3.0 提供了额外的特性且比 2.0 更高效，也能向下兼容。  

### **纹理压缩支持** ###

纹理压缩可以显著提高你的 OpenGL 应用的性能，它减少内存需求且使内存贷款使用更有效率。Android 框架层支持的标准压缩格式是 ETC1 ， 包含一个 ETC1Util 工具类和 *etc1tool* 压缩工具(在Android SDK下 *<sdk>/tootls/*)。  

> **警告：** 虽然大部分 Android 设备 都支持 ETC1 格式，但是不保证绝对可用。所以，你应该调用  ETC1Util.isETC1Supported() 方法去检查设备是否支持 ETC1 格式。
  
> **注：** ETC1 纹理压缩格式不支持透明度（alpha 通道）。如果你的应用需要透明度，你应该研究使用其他可用的纹理压缩格式。  

当使用 OpenGL ES 3.0 时， ETC2 或 EAC 纹理压缩保证可用。这个纹理格式提供了很好的高质量图像压缩比率，而且也支持透明度( alpha 通道)。  

Android 设备支持的纹理压缩格式依赖于 GPU 芯片和 OpenGL 的实现。为了确定你的设备支持什么压缩格式，你必须查询设备并且检验 OpenGL 扩展名(*OpenGL extension names*)，这个扩展名标识设备支持什么压缩格式。一些通用的纹理格式如下：  

- **ATITC(ATC)** - ATI 纹理压缩(ATITC 或者 ATC)在大部分设备上都是可用的，支持 RGB 纹理压缩比率，没有 alpha 通道。这个格式的 OpenGL 的扩展名向下面这样：  
	- *GL_AMD_compressed_ATC_texture*
	- *GL_ATI_texture_compression_atitc*

- **PVRTC** - PVRTC 在大部分设备上也是可用的，支持每个像素 2 位和 4位，没有 alpha 通道。这个格式的 OpenGL 扩展名代表如下：  
	- *GL_IMG_texture_compression_pvrtc*

- **S3TC (DXTn/DXTC)** S3TC（S3 texture compression）有几种变化（DXT1 到 DXT5），只有部分设备可用。这个格式支持 4 位或 8 位 alpha 通道的 RGB 纹理。该格式 OpenGL 扩展名代表如下：  
	- *GL_EXT_texture_compression_s3tc*
	
	一些设备只支持 DXT1 格式；这限制了改格式只能是如下扩展名：
	- *GL_EXT_texture_compression_dxt1*

- **3DC** - 3DC(3DC texture compression)也只有少量设备可用，支持一个 alpha 通道的 RGB 纹理。这个格式代表的 OpenGL 扩展名：  
	- *GL_AMD_compressed_3DC_texture*

> **注意：**这些纹理压缩格式不是所有的设备都支持。  
  
> **注：**一旦决定用哪种纹理压缩，确保在 manifest 文件总声明 *<supports-gl-texture>* 。使用这个声明通过外部服务进行过滤，让你的应用只安装你应用需要的格式。

### **确定 OpenGL 扩展(Determining OpenGL extensions)** ###

不同的 OpenGL 在 Android 设备上通过扩展的 OpenGL ES 的 API 得到支持。这些扩展包括纹理压缩，当然典型的也包括其他类型的 OpenGL 特性。  

确定哪些纹理压缩格式和其他的 OpenGL 扩展支持指定的设备：  

1. 在你的设备上运行下面的代码，确定哪些纹理压缩格式是支持的：  

	```.java
	String extensions = javax.microedition.khronos.opengles.GL10.glGetString(GL10.GL_EXTENSIONS);
	```
	> **警告：**在不同的设备上调用的结果是不同的。你必须在几个不同设备上进行测试以确定什么样的压缩类型是通通支持的。

2. 复查这个方法的结果，确定什么样的 OpenGL 扩展在设备上是支持的。  

#### **Android 扩展包（Android Extension Pack(AEP)）** ####

AEP 确保你的应用支持一个标准的 OpenGL 扩展集合，核心描述在 OpenGL 3.1 的规格书上。这些扩展功能支持一系列的设备，而且允许开发者最大化的利用移动的GPU设备。  

要使你的应用可以使用 AEP，在应用的 manifest 文件中声明 AEP 需求。除此之外，所在的平台也必须支持它。  

在 manifest 文件中声明 AEP 条件如下：  

```.xml
<uses feature android:name="android.hardware.opengles.aep"
              android:required="true" />
```

要验证平台的版本是否支持 AEP，使用  *hasSystemFeature(String)* 方法，传入 *FEATURE_OPENGLES_EXTENSION_PACK* 作为参数。示例代码如下：  

```.java
boolean deviceSupportsAEP = getPackageManager().hasSystemFeature
(PackageManager.FEATURE_OPENGLES_EXTENSION_PACK);
```

如果返回值为 true 则支持 AEP 。

### **检查 OpenGL ES 版本** ###

在 Android 设备上有好几个 OpenGL ES 的版本可以使用。你可以在你应用的 manifest 文件中指定你应用需要的最小的 OpenGL 版本，但与此同时你也可能使用新版本的新特性。例如，OpenGL ES 3.0 版的 API 是向下兼容 2.0 的，但你想要使用 OpenGL ES 3.0 的特性，但 3.0 版的 API 是不可用的，最后只会返回 2.0 的 API。  

在使用比你 manifest 文件中声明的 OpenGL ES 最小版本高时，你必须先检查你的设备该版本的 API 是否可以使用。你可以采用如下两种方式中的某一种：  

1. 尝试创建一个高版本的 OpenGL ES 环境(EGLContext) 然后检查结果。  
2. 创建一个 OpenGL ES 最小支持的环境然后检查版本值。  

下面的示例代码演示了如何通过创建一个 EGLContext 去检查 OpenGL ES 的可用版本并且检查结果。这个示例演示了如何检查 OpenGL ES 3.0 版本：  

```.java
private static double glVersion = 3.0;

private static class ContextFactory implements GLSurfaceView.EGLContextFactory {

  private static int EGL_CONTEXT_CLIENT_VERSION = 0x3098;

  public EGLContext createContext(
          EGL10 egl, EGLDisplay display, EGLConfig eglConfig) {

      Log.w(TAG, "creating OpenGL ES " + glVersion + " context");
      int[] attrib_list = {EGL_CONTEXT_CLIENT_VERSION, (int) glVersion,
              EGL10.EGL_NONE };
      // attempt to create a OpenGL ES 3.0 context
      EGLContext context = egl.eglCreateContext(
              display, eglConfig, EGL10.EGL_NO_CONTEXT, attrib_list);
      return context; // returns null if 3.0 is not supported;
  }
}
```

如果上面的 createContext() 方法返回 null，你的应用应该创建一个 OpenGL ES 2.0 的环境，且只是用这个版本的 API。  

下面的示例代码演示了如何通过创建一个支持最小的环境去检查 OpenGL ES 的版本，并且检查版本值：  

```.java
// Create a minimum supported OpenGL ES context, then check:
String version = javax.microedition.khronos.opengles.GL10.glGetString(GL10.GL_VERSION);
Log.w(TAG, "Version: " + version );
// The version format is displayed as: "OpenGL ES <major>.<minor>"
// followed by optional content provided by the implementation.
```

使用这个方法，如果你发现设备支持高的版本，你必须销毁这个最小的 OpenGL ES 环境，然后使用可用的高版本的 API 创建一个新的环境。  

## **选择一个 OpenGL 的 API 版本** ##

OpenGL ES 1.0版（和 1.1 扩展版）、2.0版和3.0版都提供了高性能的绘图接口去创建 3D 游戏和用户界面。OpenGL ES 2.0 和 3.0 绘图设计是非常相似的，3.0 在 2.0 的基础上多了一些特性。OpenGL ES 1.0/1.1 在编程上与 2.0 和 3.0 是有非常大的差异的，所以，在你准备开发之前应该根据以下因素仔细考虑使用那种 API：  

- **性能(Performance)** - 一般而言， OpenGL ES 2.0 和 3.0 提供了比 1.0/1.1 更快的绘制。当然，性能差异也依赖于你的应用运行的 Android 设备，由于硬件厂商实现 OpenGL ES 绘制管道的不同。 

- **设备兼容(Device Compatibility)** - 开发者应该考虑设备的类型，Android 版本和 OpenGL ES 的版本对于客户是可用的。

- **易于编程(Coding Convenience)** - OpenGL ES 1.0/1.1 提供的一个固定的功能管道和便利的功能在 2.0或者3.0上是不能使用的。一个新的 OpenGL ES 的开发者可能发现在 1.0/1.1 上开发更快而且更方便。

- **绘图控制(Graphics Control )** - OpenGL ES 2.0 和 3.0 提供高度的控制，通过使用着色器提供完全可编程的管道。要是开发者要使用 1.0/1.1 的 API 对绘图处理的管道直接进行控制是非常困难的。

- **纹理支持(Texture Support)** - OpenGL ES 3.0 对纹理支持是最好的，因为它保证了支持透明度 ETC2 压缩格式是可用的。1.0 和 2.0 通常支持 ETC1， 但这种纹理格式不支持透明度，所以在你的设备上对于其他的压缩格式你必须提供典型的资源进行支持。  

当性能、兼容性、便利、控制和其他的因素影响你的决定时，你应该选择使用能为用户提供最好体验的 OpenGL 版本。  

> **欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  
