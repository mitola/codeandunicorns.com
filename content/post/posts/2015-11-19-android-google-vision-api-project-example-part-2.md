---
title: Android Google Vision API project example part 2
date: 2015-11-19T15:47:51+00:00
authors:
- admin

categories:
  - ALL
  - How to
  - The Code
---
In this part of the code we are going in depth of overlays of previews and Google Vision API. The next part of the code will be properly commented so the read out should be easier

## JAVA part

```
/**
 * Created by mitola on 22/08/15.
 */
public class FaceView extends View {
    private Bitmap mBitmap; // Initi of Bitmap
    private SparseArray&amp;lt;Face&amp;gt; mFaces; // And Faces

    public FaceView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    void setContent(Bitmap bitmap, SparseArray&amp;lt;Face&amp;gt; faces) {
        mBitmap = bitmap;
        mFaces = faces;
        invalidate();
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        if ((mBitmap != null) &amp;amp;&amp;amp; (mFaces != null)) {
            double scale = drawBitmap(canvas);
            drawFaceAnnotations(canvas, scale);
        }
    }

    private double drawBitmap(Canvas canvas) {
        //to draw the bitmap scaled to the device size
        double viewWidth = canvas.getWidth();
        double viewHeight = canvas.getHeight();
        double imageWidth = mBitmap.getWidth();
        double imageHeight = mBitmap.getHeight();
        double scale = Math.min(viewWidth / imageWidth, viewHeight / imageHeight);

        Rect destBounds = new Rect(0, 0, (int) (imageWidth * scale), (int) (imageHeight * scale));
        canvas.drawBitmap(mBitmap, null, destBounds, null);
        return scale;
    }

    private void drawFaceAnnotations(Canvas canvas, double scale) {
        //create paint to draw on the image
        Paint paint = new Paint();
        paint.setColor(Color.GREEN);
        paint.setStyle(Paint.Style.STROKE); //no fill
        paint.setStrokeWidth(5);

        //make a rectangle to follow with the face
        for (int i = 0; i &amp;lt; mFaces.size(); ++i) {
            Face face = mFaces.valueAt(i);
            float x1 = face.getPosition().x; //x coordinate of top left position of the face within the image
            float y1 = face.getPosition().y; //x coordinate of top left position of the face within the image
            float x2 = x1 + face.getWidth(); //width of the face region in pixels
            float y2 = y1 + face.getHeight(); //heightof the face region in pixels
            canvas.drawRoundRect(new RectF(x1 * (float) scale, y1 * (float) scale, x2 * (float) scale, y2 * (float) scale), 2, 2, paint);
        }
    }
}
```
        

In this part you can explore the graphical overlay, which focuses mostly on making a box around the face so you know you are getting it tracked, and also used for debugging with anything you need on the screen preview. In previous testing version I was using it for example for writing smile probability value.

```
/**
 * Graphic instance for rendering face position, orientation, and landmarks within an associated
 * graphic overlay view.
 */
class FaceGraphic extends GraphicOverlay.Graphic {
    private static final float FACE_POSITION_RADIUS = 10.0f;
    private static final float ID_TEXT_SIZE = 40.0f;
    private static final float ID_Y_OFFSET = 50.0f;
    private static final float ID_X_OFFSET = -50.0f;
    private static final float BOX_STROKE_WIDTH = 5.0f;
    public float smileValue = 1.0f;

    private static final int COLOR_CHOICES[] = {
        Color.BLUE,
        Color.CYAN,
        Color.GREEN,
        Color.MAGENTA,
        Color.RED,
        Color.WHITE,
        Color.YELLOW
    };
    private static int mCurrentColorIndex = 0;

    private Paint mFacePositionPaint;
    private Paint mIdPaint;
    private Paint mBoxPaint;

    private volatile Face mFace;
    private int mFaceId;

    FaceGraphic(GraphicOverlay overlay) {
        super(overlay);

        mCurrentColorIndex = (mCurrentColorIndex + 1) % COLOR_CHOICES.length;
        final int selectedColor = COLOR_CHOICES[mCurrentColorIndex];

        mFacePositionPaint = new Paint();
        mFacePositionPaint.setColor(selectedColor);

        mIdPaint = new Paint();
        mIdPaint.setColor(selectedColor);
        mIdPaint.setTextSize(ID_TEXT_SIZE);

        mBoxPaint = new Paint();
        mBoxPaint.setColor(selectedColor);
        mBoxPaint.setStyle(Paint.Style.STROKE);
        mBoxPaint.setStrokeWidth(BOX_STROKE_WIDTH);
    }

    void setId(int id) {
        mFaceId = id;
    }

    /**
     * Updates the face instance from the detection of the most recent frame.  Invalidates the
     * relevant portions of the overlay to trigger a redraw.
     */
    void updateFace(Face face) {
        mFace = face;
        postInvalidate();
    }

    /**
     * Draws the face annotations for position on the supplied canvas.
     */
    @Override
    public void draw(Canvas canvas) {
        Face face = mFace;
        if (face == null) {
            return;
        }

        // Draws a circle at the position of the detected face, with the face's track id below.
        float x = translateX(face.getPosition().x + face.getWidth() / 2);
        float y = translateY(face.getPosition().y + face.getHeight() / 2);
        canvas.drawCircle(x, y, FACE_POSITION_RADIUS, mFacePositionPaint);
        //USED FOR DEBUGGING , TODO: cleanup
        //canvas.drawText("id: " + mFaceId, x + ID_X_OFFSET, y + ID_Y_OFFSET, mIdPaint); //this can be used for extra debuging such as marking each of the faces with its own ID, rectangle
        smileValue=face.getIsSmilingProbability();
        //canvas.drawText(Float.toString(smileValue), x + ID_X_OFFSET, y + ID_Y_OFFSET*2, mIdPaint);

        /*if(smileValue &amp;gt;= 0.5f){
            canvas.drawText("YOU ARE SMILING", x + ID_X_OFFSET, y + ID_Y_OFFSET*3, mIdPaint);
        }*/

        // Draws a bounding box around the face.
        float xOffset = scaleX(face.getWidth() / 2.0f);
        float yOffset = scaleY(face.getHeight() / 2.0f);
        float left = x - xOffset;
        float top = y - yOffset;
        float right = x + xOffset;
        float bottom = y + yOffset;
        canvas.drawRect(left, top, right, bottom, mBoxPaint);
    }

    public float getSmileProbability(){
        return smileValue;
    }
}
```

In the CameraSourcePreview the code is gotten from part of the samples of the Google Vision API. I think the code should be quite understandable, but if not feel free to ask questions so I can answer them and add them if needed to the blog post as well.

```
public class CameraSourcePreview extends ViewGroup {
    private static final String TAG = "CameraSourcePreview";

    private Context mContext;
    private SurfaceView mSurfaceView;
    private boolean mStartRequested;
    private boolean mSurfaceAvailable;
    private CameraSource mCameraSource;

    private GraphicOverlay mOverlay;

    public CameraSourcePreview(Context context, AttributeSet attrs) {
        super(context, attrs);
        mContext = context;
        mStartRequested = false;
        mSurfaceAvailable = false;

        mSurfaceView = new SurfaceView(context);
        mSurfaceView.getHolder().addCallback(new SurfaceCallback());
        addView(mSurfaceView);
    }

    public void start(CameraSource cameraSource) throws IOException {
        if (cameraSource == null) {
            stop();
        }

        mCameraSource = cameraSource;

        if (mCameraSource != null) {
            mStartRequested = true;
            startIfReady();
        }
    }

    public void start(CameraSource cameraSource, GraphicOverlay overlay) throws IOException {
        mOverlay = overlay;
        start(cameraSource);
    }

    public void stop() {
        if (mCameraSource != null) {
            mCameraSource.stop();
        }
    }

    public void release() {
        if (mCameraSource != null) {
            mCameraSource.release();
            mCameraSource = null;
        }
    }

    private void startIfReady() throws IOException {
        if (mStartRequested &amp;amp;&amp;amp; mSurfaceAvailable) {
            mCameraSource.start(mSurfaceView.getHolder());
            if (mOverlay != null) {
                Size size = mCameraSource.getPreviewSize();
                int min = Math.min(size.getWidth(), size.getHeight());
                int max = Math.max(size.getWidth(), size.getHeight());
                if (isPortraitMode()) {
                    // Swap width and height sizes when in portrait, since it will be rotated by
                    // 90 degrees
                    mOverlay.setCameraInfo(min, max, mCameraSource.getCameraFacing());
                } else {
                    mOverlay.setCameraInfo(max, min, mCameraSource.getCameraFacing());
                }
                mOverlay.clear();
            }
            mStartRequested = false;
        }
    }

    private class SurfaceCallback implements SurfaceHolder.Callback {
        @Override
        public void surfaceCreated(SurfaceHolder surface) {
            mSurfaceAvailable = true;
            try {
                startIfReady();
            } catch (IOException e) {
                Log.e(TAG, "Could not start camera source.", e);
            }
        }

        @Override
        public void surfaceDestroyed(SurfaceHolder surface) {
            mSurfaceAvailable = false;
        }

        @Override
        public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {
        }
    }

    @Override
    protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
        int width = 320;
        int height = 240;
        if (mCameraSource != null) {
            Size size = mCameraSource.getPreviewSize();
            if (size != null) {
                width = size.getWidth();
                height = size.getHeight();
            }
        }

        // Swap width and height sizes when in portrait, since it will be rotated 90 degrees
        if (isPortraitMode()) {
            int tmp = width;
            width = height;
            height = tmp;
        }

        final int layoutWidth = right - left;
        final int layoutHeight = bottom - top;

        // Computes height and width for potentially doing fit width.
        int childWidth = layoutWidth;
        int childHeight = (int)(((float) layoutWidth / (float) width) * height);

        // If height is too tall using fit width, does fit height instead.
        if (childHeight &amp;gt; layoutHeight) {
            childHeight = layoutHeight;
            childWidth = (int)(((float) layoutHeight / (float) height) * width);
        }

        for (int i = 0; i &amp;lt; getChildCount(); ++i) {
            getChildAt(i).layout(0, 0, childWidth, childHeight);
        }

        try {
            startIfReady();
        } catch (IOException e) {
            Log.e(TAG, "Could not start camera source.", e);
        }
    }

    private boolean isPortraitMode() {
        int orientation = mContext.getResources().getConfiguration().orientation;
        if (orientation == Configuration.ORIENTATION_LANDSCAPE) {
            return false;
        }
        if (orientation == Configuration.ORIENTATION_PORTRAIT) {
            return true;
        }

        Log.d(TAG, "isPortraitMode returning false by default");
        return false;
    }

    public void takeImage(){
        mCameraSource.takePicture(null, callbackPicture);
    }
    private ImageView imageView;

    CameraSource.PictureCallback callbackPicture = new CameraSource.PictureCallback(){
        public void onPictureTaken(final byte[] data)
        {
            if(mContext instanceof MainActivity) {
                MainActivity main = (MainActivity)mContext;
                main.setImageViewPicture(data);
            }
        }
    };

}
```

Quite similar story goes for GraphicOverlay as well.

```
/**
 * A view which renders a series of custom graphics to be overlayed on top of an associated preview
 * (i.e., the camera preview).  The creator can add graphics objects, update the objects, and remove
 * them, triggering the appropriate drawing and invalidation within the view.

 *
 * Supports scaling and mirroring of the graphics relative the camera's preview properties.  The
 * idea is that detection items are expressed in terms of a preview size, but need to be scaled up
 * to the full view size, and also mirrored in the case of the front-facing camera.

 *
 * Associated {@link Graphic} items should use the following methods to convert to view coordinates
 * for the graphics that are drawn:
 *
&amp;lt;ol&amp;gt;
 *
&amp;lt;li&amp;gt;{@link Graphic#scaleX(float)} and {@link Graphic#scaleY(float)} adjust the size of the
 * supplied value from the preview scale to the view scale.&amp;lt;/li&amp;gt;

 *
&amp;lt;li&amp;gt;{@link Graphic#translateX(float)} and {@link Graphic#translateY(float)} adjust the coordinate
 * from the preview's coordinate system to the view coordinate system.&amp;lt;/li&amp;gt;

 * &amp;lt;/ol&amp;gt;

 */
public class GraphicOverlay extends View {
    private final Object mLock = new Object();
    private int mPreviewWidth;
    private float mWidthScaleFactor = 1.0f;
    private int mPreviewHeight;
    private float mHeightScaleFactor = 1.0f;
    private int mFacing = CameraSource.CAMERA_FACING_BACK;
    private Set&amp;lt;Graphic&amp;gt; mGraphics = new HashSet&amp;lt;&amp;gt;();

    /**
     * Base class for a custom graphics object to be rendered within the graphic overlay.  Subclass
     * this and implement the {@link Graphic#draw(Canvas)} method to define the
     * graphics element.  Add instances to the overlay using {@link GraphicOverlay#add(Graphic)}.
     */
    public static abstract class Graphic {
        private GraphicOverlay mOverlay;

        public Graphic(GraphicOverlay overlay) {
            mOverlay = overlay;
        }

        /**
         * Draw the graphic on the supplied canvas.  Drawing should use the following methods to
         * convert to view coordinates for the graphics that are drawn:
         *
&amp;lt;ol&amp;gt;
         *
&amp;lt;li&amp;gt;{@link Graphic#scaleX(float)} and {@link Graphic#scaleY(float)} adjust the size of
         * the supplied value from the preview scale to the view scale.&amp;lt;/li&amp;gt;

         *
&amp;lt;li&amp;gt;{@link Graphic#translateX(float)} and {@link Graphic#translateY(float)} adjust the
         * coordinate from the preview's coordinate system to the view coordinate system.&amp;lt;/li&amp;gt;

         * &amp;lt;/ol&amp;gt;

         *
         * @param canvas drawing canvas
         */
        public abstract void draw(Canvas canvas);

        /**
         * Adjusts a horizontal value of the supplied value from the preview scale to the view
         * scale.
         */
        public float scaleX(float horizontal) {
            return horizontal * mOverlay.mWidthScaleFactor;
        }

        /**
         * Adjusts a vertical value of the supplied value from the preview scale to the view scale.
         */
        public float scaleY(float vertical) {
            return vertical * mOverlay.mHeightScaleFactor;
        }

        /**
         * Adjusts the x coordinate from the preview's coordinate system to the view coordinate
         * system.
         */
        public float translateX(float x) {
            if (mOverlay.mFacing == CameraSource.CAMERA_FACING_FRONT) {
                return mOverlay.getWidth() - scaleX(x);
            } else {
                return scaleX(x);
            }
        }

        /**
         * Adjusts the y coordinate from the preview's coordinate system to the view coordinate
         * system.
         */
        public float translateY(float y) {
            return scaleY(y);
        }

        public void postInvalidate() {
            mOverlay.postInvalidate();
        }
    }

    public GraphicOverlay(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    /**
     * Removes all graphics from the overlay.
     */
    public void clear() {
        synchronized (mLock) {
            mGraphics.clear();
        }
        postInvalidate();
    }

    /**
     * Adds a graphic to the overlay.
     */
    public void add(Graphic graphic) {
        synchronized (mLock) {
            mGraphics.add(graphic);
        }
        postInvalidate();
    }

    /**
     * Removes a graphic from the overlay.
     */
    public void remove(Graphic graphic) {
        synchronized (mLock) {
            mGraphics.remove(graphic);
        }
        postInvalidate();
    }

    /**
     * Sets the camera attributes for size and facing direction, which informs how to transform
     * image coordinates later.
     */
    public void setCameraInfo(int previewWidth, int previewHeight, int facing) {
        synchronized (mLock) {
            mPreviewWidth = previewWidth;
            mPreviewHeight = previewHeight;
            mFacing = facing;
        }
        postInvalidate();
    }

    /**
     * Draws the overlay with its associated graphic objects.
     */
    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        synchronized (mLock) {
            if ((mPreviewWidth != 0) &amp;amp;&amp;amp; (mPreviewHeight != 0)) {
                mWidthScaleFactor = (float) canvas.getWidth() / (float) mPreviewWidth;
                mHeightScaleFactor = (float) canvas.getHeight() / (float) mPreviewHeight;
            }

            for (Graphic graphic : mGraphics) {
                graphic.draw(canvas);
            }
        }
    }
}
```

### XML files

And the only thing left now is the XML files such as a description of design and strings.xml file. There is also the Manifest, but I think the most important part is the application part which I am also adding at the end. Together with the Github url where you can access all the source code for this Google Vision API example.

## Activity_main

This file is called activity_main.xml
        
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/topLayout"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:columnCount="2"
    android:rowCount="2"
    xmlns:ads="http://schemas.android.com/apk/res-auto"
    android:keepScreenOn="true">

    <com.codeandunicorns.face.doyousmile.uicamera.CameraSourcePreview
        android:id="@+id/preview"
        android:layout_width="208dp"
        android:layout_height="277dp"
        android:layout_gravity="center|left|top">

    </com.codeandunicorns.face.doyousmile.uicamera.CameraSourcePreview>

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/imageView"
        android:layout_gravity="center_horizontal|left|top"
        android:layout_alignParentTop="true"
        android:layout_alignParentEnd="true"
        android:layout_above="@+id/jokeView"
        android:layout_toEndOf="@+id/preview" />

    <com.codeandunicorns.face.doyousmile.uicamera.GraphicOverlay
        android:id="@+id/faceOverlay"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_alignParentTop="true"
        android:layout_alignParentStart="true"
        android:layout_above="@+id/jokeView"
        android:layout_toStartOf="@+id/imageView" />

    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="110dp"
        android:layout_gravity="bottom"
        android:layout_alignParentBottom="true"
        android:layout_alignParentStart="true"
        android:id="@+id/linearLayout"
        android:gravity="center_horizontal"
        android:weightSum="1">

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/button_main"
            android:id="@+id/takepicturebutton"
            android:textSize="20sp" />

        <com.google.android.gms.ads.AdView
            android:id="@+id/adView"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            ads:adSize="BANNER"
            ads:adUnitId="@string/banner_ad_unit_id">
        </com.google.android.gms.ads.AdView>

    </LinearLayout>

    <TextView
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:text="@string/starting_text"
        android:id="@+id/jokeView"
        android:layout_gravity="right|top"
        android:layout_below="@+id/preview"
        android:layout_above="@+id/linearLayout"
        android:textSize="17dp"
        android:textIsSelectable="false"
        android:paddingEnd="10dp"
        android:paddingStart="10dp" />


</RelativeLayout>
```
        
## Strings.xml
        
This file is called strings.xml:
        
```
<resources>
    <string name="app_name">Do you smile?</string>

    <string name="hello_world">Hello world!</string>
    <string name="action_settings">Settings</string>
    <string name="banner_ad_unit_id">ca-app-pub-YOURID</string>
    <string name="starting_text">Scan your current mood! Do you smile or not is the question?\nClick and find out!</string>
    <string name="button_main">Take mood picture</string>
</resources>
```
        

## AndroidManifest.xml

And the AndroidManifest.xml file contents with permissions etc:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.codeandunicorns.face.doyousmile" >

    <uses-feature android:name="android.hardware.camera" />

    <uses-permission android:name="android.permission.CAMERA" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">
        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <meta-data
            android:name="com.google.android.gms.vision.DEPENDENCIES"
            android:value="face" />
        <activity android:name="com.google.android.gms.ads.AdActivity"
            android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"
            android:theme="@android:style/Theme.Translucent" />
    </application>


</manifest>
```
        

## Graddle

And the Graddle configuration:

apply plugin: 'com.android.application'

```
android {
    compileSdkVersion 23
    buildToolsVersion "21.1.2"

    defaultConfig {
        applicationId "com.codeandunicorns.face.doyousmile"
        minSdkVersion 19
        targetSdkVersion 23
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:23.0.0'
    compile 'com.google.android.gms:play-services:8.1.0'
    compile 'com.google.android.gms:play-services-ads:8.1.0'
}
```