---
title: Android Google Vision project example part 1
date: 2015-11-19T15:47:36+00:00
authors:
- admin

categories:
  - ALL
  - How to
  - Science
  - The Code
---
[<img class="alignleft size-medium wp-image-1509" src="https://codeandunicorns.com/wp-content/uploads/2015/11/GoogleVisionMatjazAPI2-177x300.jpg" alt="GoogleVisionMatjazAPI2" width="177" height="300" srcset="https://codeandunicorns.com/wp-content/uploads/2015/11/GoogleVisionMatjazAPI2-177x300.jpg 177w, https://codeandunicorns.com/wp-content/uploads/2015/11/GoogleVisionMatjazAPI2.jpg 463w" sizes="(max-width: 177px) 100vw, 177px" />](https://codeandunicorns.com/wp-content/uploads/2015/11/GoogleVisionMatjazAPI2.jpg)

The following article is an example of using Google Vision API which was recently released by Google. The Vision API is capable of detecting smiles, winks and multiple faces in the picture, as well as providing multiple reference points from which you can define and use your own programmable detections and behaviours. In the following example the smile probability is used together with displaying the camera live feed, detecting face/faces and displaying a preview.

Based on the smile probability it will either provide you with a joke or a quote, which will further be synthesized with a TTS (text to speech API).

P.S. The article will be split into multiple parts so it will be easier to read and digest the content. On the last page of article, you also have a Github repo where the source code is hosted.

P.S.S. Code is partially accompanied with text and partially with in-line comments for clearer understanding.

Variable initialisation in MainActivity:

```
    private static final String TAG = "FaceTracker";

    private CameraSource mCameraSource;
    private CameraSourcePreview mPreview;
    private GraphicOverlay mGraphicOverlay;
    private ImageView mImageView;
    private TextView mJokeView;
    private TextToSpeech mTts;
    private static final int CAMERA_REQUEST = 1888;
    private float smileValue = 1.0f;
    private String[] jokes; //jokes array, at the moment hardcoded but in the future there could be a much better solution, just meant as a sample for now
    private String[] quotes; //same here
```
Ok now lets move to OnCreate. I will ignore the common parts that are always used in general in Android and try to focus in most part on things related to this project.<br /> OnCreate function definition:<br /> <a href="https://codeandunicorns.com/wp-content/uploads/2015/11/GoogleVisionMatjazAPIDemo.jpg"><img class="alignright size-medium wp-image-1508" src="https://codeandunicorns.com/wp-content/uploads/2015/11/GoogleVisionMatjazAPIDemo-175x300.jpg" alt="GoogleVisionMatjazAPIDemo" width="175" height="300" srcset="https://codeandunicorns.com/wp-content/uploads/2015/11/GoogleVisionMatjazAPIDemo-175x300.jpg 175w, https://codeandunicorns.com/wp-content/uploads/2015/11/GoogleVisionMatjazAPIDemo.jpg 459w" sizes="(max-width: 175px) 100vw, 175px" /></a><br /> First we start with init of main parts as for example Admob view, if you want to monetize, i just wanted to include this as an extra. Then init of all relevant things, as mPreviw(used for previewing the camera on screen), mGraphic overlay (which is used for overlaying face preview with either the box,dots,any values you want etc.), and init of FaceDetector which helps us detect the face features such as smiling blinking and if needed around I think 24 common points of the face to detect custom features.
    
```
    public void onCreate(Bundle icicle) {
        super.onCreate(icicle);
        setContentView(R.layout.activity_main);

        AdView mAdView = (AdView) findViewById(R.id.adView);
        AdRequest adRequest = new AdRequest.Builder().build();
        mAdView.loadAd(adRequest);

        mPreview = (CameraSourcePreview) findViewById(R.id.preview);
        mGraphicOverlay = (GraphicOverlay) findViewById(R.id.faceOverlay);
        mImageView = (ImageView) findViewById(R.id.imageView);
        mJokeView = (TextView) findViewById(R.id.jokeView);
        mImageView.setVisibility(View.INVISIBLE);

        Context context = getApplicationContext();
        FaceDetector detector = new FaceDetector.Builder(context)
                .setClassificationType(FaceDetector.ALL_CLASSIFICATIONS)
                .setProminentFaceOnly(true)
                .build();
        detector.setProcessor(
                new MultiProcessor.Builder&lt;&gt;(new GraphicFaceTrackerFactory()).build());


        if (!detector.isOperational()) {
            // Note: The first time that an app using face API is installed on a device, GMS will
            // download a native library to the device in order to do detection.  Usually this
            // completes before the app is run for the first time.  But if that download has not yet
            // completed, then the above call will not detect any faces.
            //
            // isOperational() can be used to check if the required native library is currently
            // available.  The detector will automatically become operational once the library
            // download completes on device.
            Log.w(TAG, "Face detector dependencies are not yet available.");
        }
```
With continue with init of Camera, with settings of preview resolutoin, which camera to use, FPS speed etc, we also init TTS(Google text to speech system which we use in combination with our Google Vision API)
```
        mCameraSource = new CameraSource.Builder(context, detector)
                .setRequestedPreviewSize(640, 480)
                .setFacing(CameraSource.CAMERA_FACING_FRONT)
                .setRequestedFps(30.0f)
                .build();
        mTts = new TextToSpeech(MainActivity.this, new TextToSpeech.OnInitListener(){
            @Override
            public void onInit(int status) {
                // TODO Auto-generated method stub
                if(status == TextToSpeech.SUCCESS){
                    int result=mTts.setLanguage(Locale.US);
                    if(result==TextToSpeech.LANG_MISSING_DATA ||
                            result==TextToSpeech.LANG_NOT_SUPPORTED){
                        Log.e("error", "This Language is not supported");
                    }
                    else{
                        if (android.os.Build.VERSION.SDK_INT&gt;=21){
                            mTts.speak("Welcome to: Do you smile?", TextToSpeech.QUEUE_ADD, null, "ss"); //based on text, it gets transformed to voice of Google woman
                            mTts.speak("Scan your current mood! Do you smile or not is the question?\nClick and find out!", TextToSpeech.QUEUE_ADD, null, "ss");
                        }
                        else{
                            mTts.speak("Welcome to: Do you smile?", TextToSpeech.QUEUE_ADD, null);
                            mTts.speak("Scan your current mood! Do you smile or not is the question?\nClick and find out!", TextToSpeech.QUEUE_ADD, null);
                        }
                    }
                }
                else
                    Log.e("error", "Initilization Failed!");
            }
        });

        Button button =  (Button) findViewById(R.id.takepicturebutton); //take mood picture
        button.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                mImageView.setVisibility(View.INVISIBLE);
                mPreview.takeImage(); // take image function together with later main processing part. 
            }
        });

        //Main init of sample data
        initJokeStrings(); 
        initInspiriStrings();

    }
```

Several smaller relevant methods, mostly for pausing,restarting, and clearing the memory of device with some minor management:

    
```
    /**
     * Restarts the camera.
     */
    @Override
    protected void onResume() {
        super.onResume();
        startCameraSource();
    }

    /**
     * Stops the camera.
     */
    @Override
    protected void onPause() {
        super.onPause();
        mPreview.stop();
    }

    /**
     * Releases the resources associated with the camera source, the associated detector, and the
     * rest of the processing pipeline.
     */
    @Override
    protected void onDestroy() {
        super.onDestroy();
        mCameraSource.release();
    }

    //==============================================================================================
    // Camera Source Preview
    //==============================================================================================

    /**
     * Starts or restarts the camera source, if it exists.  If the camera source doesn't exist yet
     * (e.g., because onResume was called before the camera source was created), this will be called
     * again when the camera source is created.
     */
    private void startCameraSource() {
        try {
            mPreview.start(mCameraSource, mGraphicOverlay);
        } catch (IOException e) {
            Log.e(TAG, "Unable to start camera source.", e);
            mCameraSource.release();
            mCameraSource = null;
        }
    }
```
          
Graphic face tracker initialization part:


In this part we have some interesting methods for further investigation.
One of them is setImageViewPicture. I know mostly it's simple but the interesting part is the

mImageView.setImageBitmap(BitmapFactory.decodeByteArray(data, 0, data.length));

line. Since it uses the provided byte data and decodes it to an imageView. After that it goes to either get inspirational quote or get random joke, depending on the float value of your smile determined in a later covered function and class.

The second part of this code initializes its own instance for every face in the picture, but for our demo we are only utilizing the first and most prominent face.

```
    //==============================================================================================
    // Graphic Face Tracker
    //==============================================================================================

    /**
     * Factory for creating a face tracker to be associated with a new face.  The multiprocessor
     * uses this factory to create face trackers as needed -- one for each individual.
     */
    private class GraphicFaceTrackerFactory implements MultiProcessor.Factory&lt;Face&gt; {
        @Override
        public Tracker&lt;Face&gt; create(Face face) {
            return new GraphicFaceTracker(mGraphicOverlay);
        }
    }

    public void setImageViewPicture(final byte[] data){
        mImageView.setVisibility(View.VISIBLE);
        mImageView.setImageBitmap(BitmapFactory.decodeByteArray(data, 0, data.length));
        if(smileValue &gt;= 0.50f){
            getInspirationalQoute(); // You are smiling
        }
        else{
            getRandomJoke(); //You do not smile
        }
    }

    /**
     * Face tracker for each detected individual. This maintains a face graphic within the app's
     * associated face overlay.
     */
    private class GraphicFaceTracker extends Tracker&lt;Face&gt; {
        private GraphicOverlay mOverlay;
        private FaceGraphic mFaceGraphic;

        GraphicFaceTracker(GraphicOverlay overlay) {
            mOverlay = overlay;
            mFaceGraphic = new FaceGraphic(overlay);
        }

        /**
         * Start tracking the detected face instance within the face overlay.
         */
        @Override
        public void onNewItem(int faceId, Face item) {
            mFaceGraphic.setId(faceId);
        }

        /**
         * Update the position/characteristics of the face within the overlay.
         */
        @Override
        public void onUpdate(FaceDetector.Detections&lt;Face&gt; detectionResults, Face face) {
            mOverlay.add(mFaceGraphic);
            mFaceGraphic.updateFace(face);
            smileValue = mFaceGraphic.getSmileProbability();
        }

        /**
         * Hide the graphic when the corresponding face was not detected.  This can happen for
         * intermediate frames temporarily (e.g., if the face was momentarily blocked from
         * view).
         */
        @Override
        public void onMissing(FaceDetector.Detections&lt;Face&gt; detectionResults) {
            mOverlay.remove(mFaceGraphic);
        }

        /**
         * Called when the face is assumed to be gone for good. Remove the graphic annotation from
         * the overlay.
         */
        @Override
        public void onDone() {
            mOverlay.remove(mFaceGraphic);
        }
    }
```

The fun part with jokes and qoutes:

Based as seen before on Smile probability from Google Vision API we can now go to for example a getRandomJoke function. Here we first check if the Android version is bigger then 21 or smaller, since based on the version i noticed then can sometimes be problemds due to one version of TTS speak function getting deprecated in newer versions, thats why I am separating both of them. It also accesses the string arrays and picks up random string value of quote or joke, to try to make you happy if you are sad, or just make you wiser if you smile enough already.

```
   public void getRandomJoke(){
        if (android.os.Build.VERSION.SDK_INT&gt;=21){
            String joke = jokes[new Random().nextInt(jokes.length)];
            mTts.speak(joke, TextToSpeech.QUEUE_FLUSH, null, "ss");
            mJokeView.setText("Joke, cause you don't smile:" + "\n" + joke);
        }
        else{
            mTts.speak(jokes[new Random().nextInt(jokes.length)], TextToSpeech.QUEUE_FLUSH, null);
        }
    }

    public void getInspirationalQoute() {
        if (android.os.Build.VERSION.SDK_INT&gt;=21){
            String quote = quotes[new Random().nextInt(quotes.length)];
            mTts.speak(quote, TextToSpeech.QUEUE_FLUSH, null, "ss");
            mJokeView.setText("Quote, cause you smile:" + "\n" + quote);
        }
        else{
            mTts.speak(quotes[new Random().nextInt(quotes.length)], TextToSpeech.QUEUE_FLUSH, null);
        }
    }
```

Just for the sake of complete code the not so fine done array of jokes and qoutes.

```
    public void initJokeStrings(){
        jokes = new String[] {
                "The clear history button in your browser has saved more lives than Superman",
                "I bet earth makes fun of the other planets for having no life",
                "Oh no! Playstation and Xbox online services are down! Someone call an ambulance! Wii U Wii U Wii U.",
                "The sight of a woman's cleavage reduces a man's ability to think clearly by 50%... per boob!",
                "Jimmy has 42 candy bars. Jimmy eats 24. What does jimmy have now? Diabetes.. Jimmy has diabetes.",
                "What type of Bee's make milk instead of honey? Boobies",
                "I gave my number to a really hot girl at the bar and told her to text me when she got home. She must be homeless",
                "Just changed my Facebook name to ‘No one' so when I see stupid posts I can click like and it will say ‘No one likes this'.",
                "What's the difference between snowmen and snowladies? Snowballs",
                "How do you make holy water? You boil the hell out of it.",
                "If con is the opposite of pro, it must mean Congress is the opposite of progress?",
                "I wondered why the frisbee was getting bigger, and then it hit me.",
                "I used to like my neighbors, until they put a password on their Wi-Fi.",
                "Never argue with a fool, they will lower you to their level, and then beat you with experience.",
                "Why do farts smell? So deaf people can enjoy them too.",
                "Light travels faster than sound. This is why some people appear bright until they speak.",
                "What did the ocean say to the beach? Nothing, it just waved.",
                "I say no to alcohol, it just doesn't listen.",
                "Why did the skeleton go to the party alone? -- He had no body to go with him!",
                "Right now I'm having amnesia and deja vu at the same time! I think I've forgotten this before?",
                "Why is Peter Pan always flying? Because he Neverlands.",
                "I think i want a job cleaning mirrors, It's just something i could really see myself doing",
                "My girlfriend keeps going on bout the time i jokingly put superglue on her mobile phone, honestly , she just can't let it go.",
                "How do you make a tissue dance? You put a little boogie in it.",
                "For Christmas, I want Santa's list of naughty girls.",
                "Who says nothing is impossible. I've been doing nothing for years."
        };
    }

    public void initInspiriStrings(){
        quotes = new String[] {
                "Things may come to those who wait, but only the things left by those who hustle. By Abraham Lincoln",
                "The great secret of education is to direct vanity to proper objects. By Adam Smith",
                "It’s not that travel just broadens your mind, rather it enables you to see how narrow your oppressor’s minds are. By Alain de Botton",
                "A person who never made a mistake never tried anything new. By Alan Watts",
                "Better to have a short life that is full of what you like doing than a long life spent in a miserable way. By Alan Watts",
                "Life is a musical thing and you are supposed to dance and sign while it's being played. By Alan Watts",
                "Omnipotence is not knowing how everything is done; it's just doing it. By Alan Watts",
                "It is not humiliating to be unhappy. Physical suffering is sometimes humiliating, but the suffering of being cannot be, it is life. By Albert Camus",
                "Learn from yesterday, live for today, hope for tomorrow. The important thing is not to stop questioning. By Albert Einstein",
                "The secret to creativity is knowing how to hide your sources. By Albert Einstein",
                "The true sign of intelligence is not knowledge but imagination. By Albert Einstein",
                "The world as we have created it is a process of our thinking. It cannot be changed without changing our thinking. By Albert Einstein",
                "When facts don't fit the theory, change the facts. By Albert Einstein",
                "Creativity never comes under emotional stress or tension. The real creativity comes when the mind finally relaxes and is quiet and then can focus. By Amar Bose",
                "If you think something is impossible, don't disturb the person doing it. By Amar Bose",
                "If you want to build a ship, don't drum up people to collect wood and don't assign them tasks and work, but rather teach them to long for the endless immensity of the sea. By Antoine de Saint-Exupery",
                "To love is not to look at one another: it is to look, together, in the same direction. By Antoine de Saint-Exupery",
                "When you give yourself, you receive more than you give. By Antoine de Saint-Exupery",
                "Do not fear to be eccentric in opinion, for every opinion now accepted was once eccentric. By Bertrand Russell",
                "Visionaries not only believe that the impossible can be done, but that it must be done. By Bran Ferren",
                "People who have a strong sense of love and belonging believe they're worthy of it. By Brene Brown",
                "Every human being has some flavor of ‘not enough.’ You can either be stopped by it, or simply notice it, like the weather. By Brigid Schulte",
                "Anyone can complain about the world, but only a good few can fix it. By Cennydd Bowles",
                "So many people rush through life, let's take our time living it. By Christian Vuerings",
                "The more that you read, the more things you will know. The more that you learn, the more places you’ll go. By Doctor Seuss",
                "Dichotomy between sense of wonder and what is wrong. By Elon Musk",
                "If something is important enough, you should try. Even if the probable outcome is failure. By Elon Musk",
                "There's skepticism about anything new. That's normal. By Elon Musk",
                "There is only one way to happiness and that is to cease worrying about things which are beyond the power of our will. By Epictetus",
                "There are 2 ways to make a man richer, give him more money or curb his desires. By Jean-Jacques Rousseau",
                "Being deeply loved by someone gives you strength, while loving someone deeply gives you courage. By Lao Tse",
                "A leader is best when people barely know he exists, when his work is done, his aim fulfilled, they will say: we did it ourselves. By Lao Tse",
                "Silence is a source of great strength. By Lao Tse",
                "To attain knowledge, add things every day. To attain wisdom, remove things every day. By Lao Tse",
                "The best way to have a good idea is to have lots of ideas. By Linus Pauling",
                "At home I am a nice guy: but I don't want the world to know. Humble people, I've found, don't get very far. By Muhammad Ali",
                "He who is not courageous enough to take risks will accomplish nothing in life. By Muhammad Ali",
                "I am the greatest, I said that even before I knew I was. By Muhammad Ali",
                "It isn't the mountains ahead to climb that wear you out; it's the pebble in your shoe. By Muhammad Ali",
                "The man who has no imagination has no wings. By Muhammad Ali",
                "If there is no struggle, there is no progress. By Frederick Douglass",
                "Success is a state of being. Because as soon as you say you're successful, you probably start to fail. By Howard H. Stevenson",
                "The reasonable man adapts himself to the world; the unreasonable one persists in trying to adapt the world to himself. Therefore, all progress depends on the unreasonable man. By George Bernard Shaw",
                "Life would be much easier to understand if mother nature gave us the source code. By Graeme MacWilliam",
                "Coming together is a beginning; keeping together is progress; staying together is success. By Henry Ford",
                "An ounce of experience is better than a ton of theory simply. By John Dewey",
                "A journey of a thousand miles begins with a single step. By Laozi",
                "A man is but the product of his thoughts what he thinks, he becomes. By Mahatma Gandhi",
                "An ounce of practice is worth more than tons of preaching. By Mahatma Gandhi",
                "Live as if you were to die tomorrow. Learn as if you were to live forever. By Mahatma Gandhi",
                "You must be the change you wish to see in the world. By Mahatma Gandhi",
                "You must be the change you wish to see in the world. By Mahatma Gandhi",
                "I have never let my schooling interfere with my education. By Mark Twain",
                "Never let the future disturb you. You will meet it, if you have to, with the same weapons of reason which today arm you against the present. By ",
                "Never believe that a few caring people can't change the world. For, indeed, that's all who ever have. By ",
                "Keep away from those who try to belittle your ambitions. Small people always do that, but the really great make you believe that you too can become great. By ",
                "Life is short, break the rules. By ",
                "A gentleman is one who never hurts anyone's feelings unintentionally. By Oscar Wilde",
                "A man who does not think for himself does not think at all. By Oscar Wilde",
                "Always forgive your enemies - nothing annoys them so much. By Oscar Wilde",
                "America is the only country that went from barbarism to decadence without civilization in between. By Oscar Wilde",
                "Between men and women there is no friendship possible. There is passion, enmity, worship, love, but no friendship. By Oscar Wilde",
                "Education is an admirable thing, but it is well to remember from time to time that nothing that is worth knowing can be taught. By Oscar Wilde",
                "Experience is simply the name we give our mistakes. By Oscar Wilde",
                "If you want to tell people the truth, make them laugh, otherwise they’ll kill you. By Oscar Wilde",
                "There are only two tragedies in life: one is not getting what one wants, and the other is getting it. By Oscar Wilde",
                "Work is the curse of the drinking classes. By Oscar Wilde",
                "If you’re far enough ahead that people can’t figure out if you’re joking, you know you’ve innovated. By Paul Buchheit",
                "The first thing I do on day one is build something useful, then just keep improving it. By Paul Buchheit",
                "Go where you’re celebrated, not where you’re tolerated. By Paul F. Davis",
                "Maintain your spirit of curiosity, keep questioning things, and you’ll find new ways to innovate. By Richard Brandson",
                "If I look confused it is because I am thinking. By Samuel Goldwyn",
                "The harder I work, the luckier I get. By Samuel Goldwyn",
                "When someone does a good job, applaud; it makes two people happy. By Samuel Goldwyn",
                "Defenseless is the best choice for those seeking to grow. By Seth Godin",
                "Ship often. Ship lousy stuff, but ship. Ship constantly. By Seth Godin",
                "If you do something and it turns out pretty good, then you should go do something else wonderful, not dwell on it for too long. Just figure out what’s next. By Steve Jobs",
                "Do not go where the path may lead, go instead where there is no path and leave a trail. By Ralph Waldo Emerson",
                "For every minute you remain angry, you give up sixty seconds of peace of mind. By Ralph Waldo Emerson",
                "Nothing great was ever achieved without enthusiasm. By Ralph Waldo Emerson",
                "If you’re not a embarrassed by the first version of your product, you launched to late. By Reid Hoffman",
                "You earn trust by providing innovative, quality products and keeping your word. By Richard Brandson",
                "Nothing comes to a sleeper but a dream. By Richard Williams",
                "While we teach, we learn. By Seneca The Younger",
                "Being the richest man in the cemetery doesn’t matter to me … Going to bed at night saying we’ve done something wonderful… that’s what matters to me. By Steve Jobs",
                "One must learn by doing the thing; for though you think you know it, you have no certainty until you try. By Sophocles",
                "The enemy of sustainable productivity is not stress. Rather, it’s the absence of intermittent rest and renewal. By Tony Schwartz",
                "I keep going anyway. I pause and take the doubts in. I cry. I curse. I think it's unfair and that I can't continue. But then I do. I get up, brush my shoulders off, and carry on. By Tiffany Han",
                "A man is not idle because he is absorbed in thought. There is a visible labor and there is an invisible labor. By Victor Hugo",
                "He who opens a school door, closes a prison. By Victor Hugo",
                "Initiative is doing the right thing without being told. By Victor Hugo",
                "There is nothing more powerful than an idea whose time has come. By Victor Hugo",
                "When a woman is talking to you, listen to what she says with her eyes. By Victor Hugo",
                "Being male is a matter of birth. Being a man is a matter of age. But being a Gentleman is a matter of choice. By Vin Diesel",
                "Education shouldn't fill a bucket but light a fire. By William Butler Yeats",
                "There are no strangers here; Only friends you haven't yet met. By William Butler Yeats",
        };
    }
```