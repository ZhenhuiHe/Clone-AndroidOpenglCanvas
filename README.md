# OpenGL canvas

[English README](https://github.com/ChillingVan/android-openGL-canvas/blob/master/README-en.md) 

此项目灵感来源: 
* Android package com.android.gallery3d.glrenderer 下的源代码
* [GPUImage](https://github.com/CyberAgent/android-gpuimage)
* [grafika](https://github.com/google/grafika)
感谢这些源代码提供者！

## 功能
* 提供一个类似Android Canvas类的使用OpenGL来实现绘制的canvasGL。可以像传统自定义View那样直接继承 GLViews, 再使用这个canvas绘制需要的东西。
* 提供类似 GPUImage 里的Filter的API，可以在使用canvasGL画东西时实现图像处理。
* 提供的View是继承 GLSurfaceView 或 TextureView 的，可以使用这两种View的特性，特别是TextureView的特性。

* 另外,因为使用OpenGL在另一线程渲染，所以里面的 GLContinuousView 还提供能够实现高性能的动画的方法。
![anim](https://github.com/ChillingVan/android-openGL-canvas/raw/master/screenshots/anim-activity-example.png)


与GPUImage对比：
* 提供无限循环渲染线程的 GLContinuousView 和 GLContinuousTextureView。
* 使用TextureView来实现OpenGL的绘制，可以利用TextureView的优点--TextureView 不会创建一个分离的window，而是像一个普通的view那样显示， 这样就不会像GLSurfaceView那样，要么在所有View上方，要么被其它View遮住(看 setZOrderOnTop(boolean) 的说明)。而且像myView.setAlpha(0.5f)这种方法调用后也会有效果了。 
* 提供一个canvas，不只是图片处理，可以画上想画的其它东西。



## 使用要求
* Android API 14 以上(OpenGL ES 2.0 以上)

## 用法

### Gradle dependency

sample:
```groovy
// in root build.gradle
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
    }
}

// module build.gradle
dependencies {
    compile 'com.github.ChillingVan:android-openGL-canvas:v1.2.0.1'
}
```

### 样例代码 

* 自定义一个View.
```java
public class MyGLView extends GLView {

    public MyGLView(Context context) {
        super(context);
    }

    public MyGLView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }


    @Override
    protected void onGLDraw(ICanvasGL canvas) {
        // draw things with canvas here
    }
}
```


![canvas](https://github.com/ChillingVan/android-openGL-canvas/raw/master/screenshots/canvas-example-v1.png)

* 其中, GLContinuouslyView, GLTextureView, GLContinuousTextureView, GLSurfaceTextureProducerView and GLSharedContextView 用法相似.


* 使用CanvasGL实现绘制
```java
        canvas.drawBitmap(textBitmap, left, top);
        
        // transform
        canvas.save();
        canvas.rotate(rotateDegree, x, y);
        canvas.drawBitmap(bitmap, left, top);
        canvas.restore();
        // or
        CanvasGL.BitmapMatrix matrix = new CanvasGL.BitmapMatrix();
        matrix.postScale(2.1f, 2.1f);
        matrix.postRotate(90);
        canvas.drawBitmap(bitmap, matrix);
        
        // apply filter to the bitmap
        textureFilter = new ContrastFilter(2.8f);
        canvas.drawBitmap(bitmap, left, top, textureFilter);
```

![filters](https://github.com/ChillingVan/android-openGL-canvas/raw/master/screenshots/filter_example-v1.png)


* 可以与Camera结合，注意运行样例代码的时候尽量使用真机而不是模拟器。
![camera](https://github.com/ChillingVan/android-openGL-canvas/raw/master/screenshots/camera-example-v1.jpg)

* 如果不想使用View，可以使用 OffScreenCanvas 实现脱离屏幕的绘制，然后使用getDrawingBitmap方法获取绘制的内容。


[更多用例请查看wiki](https://github.com/ChillingVan/android-openGL-canvas/wiki)

## 注意事项
* 每一个View的onGLDraw都运行在自己的线程而非主线程。
* 目前的Filter没有GPUImage里那么多，后续会陆续增加，如果有需要，参照我的代码自己实现即可，难度不高。

## 相关博客文章
* [OpenGL绘制一张图片的流程](http://www.jianshu.com/p/40521c92ef85)
* [如何封装 opengl 流程](http://www.jianshu.com/p/c45d11627c70)
* [代替GLSurfaceView的GLTextureView](http://www.jianshu.com/p/5a127d43b39a)

## License
    Copyright 2016 ChillingVan.

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
