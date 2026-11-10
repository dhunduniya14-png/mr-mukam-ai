name: Build MR MUKAM AI 2.0 APK

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read

jobs:
  build:
    name: Build Android APK
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v4

      - name: Set up Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Set up Gradle 8.5
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.5'

      - name: Generate MR MUKAM AI Project
        shell: bash
        run: |
          set -e

          mkdir -p .github/workflows
          mkdir -p app/src/main/java/com/mukam/jarvis/ai
          mkdir -p app/src/main/java/com/mukam/jarvis/control
          mkdir -p app/src/main/java/com/mukam/jarvis/receiver
          mkdir -p app/src/main/java/com/mukam/jarvis/service
          mkdir -p app/src/main/java/com/mukam/jarvis/voice
          mkdir -p app/src/main/res/layout
          mkdir -p app/src/main/res/values
          mkdir -p app/src/main/res/xml

          cat > settings.gradle <<'EOF'
          pluginManagement {
              repositories {
                  google()
                  mavenCentral()
                  gradlePluginPortal()
              }
          }

          dependencyResolutionManagement {
              repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
              repositories {
                  google()
                  mavenCentral()
              }
          }

          rootProject.name = "MR-MUKAM-AI"
          include ':app'
          EOF

          cat > build.gradle <<'EOF'
          plugins {
              id 'com.android.application' version '8.1.4' apply false
              id 'org.jetbrains.kotlin.android' version '1.9.0' apply false
          }
          EOF

          cat > gradle.properties <<'EOF'
          org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8
          org.gradle.parallel=true
          android.useAndroidX=true
          kotlin.code.style=official
          android.nonTransitiveRClass=true
          EOF

          cat > app/build.gradle <<'EOF'
          plugins {
              id 'com.android.application'
              id 'org.jetbrains.kotlin.android'
          }

          android {
              namespace 'com.mukam.jarvis'
              compileSdk 34

              defaultConfig {
                  applicationId "com.mukam.jarvis"
                  minSdk 26
                  targetSdk 34
                  versionCode 2
                  versionName "2.0"

                  buildConfigField "String", "BACKEND_URL", """"
              }

              buildFeatures {
                  buildConfig true
              }

              buildTypes {
                  release {
                      minifyEnabled false
                  }
              }

              compileOptions {
                  sourceCompatibility JavaVersion.VERSION_17
                  targetCompatibility JavaVersion.VERSION_17
              }

              kotlinOptions {
                  jvmTarget = '17'
              }
          }

          dependencies {
              implementation 'androidx.core:core-ktx:1.12.0'
              implementation 'androidx.appcompat:appcompat:1.6.1'
              implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.8.7'
              implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3'
          }
          EOF

          cat > app/src/main/AndroidManifest.xml <<'EOF'
          <?xml version="1.0" encoding="utf-8"?>
          <manifest xmlns:android="http://schemas.android.com/apk/res/android">

              <uses-permission android:name="android.permission.INTERNET"/>
              <uses-permission android:name="android.permission.RECORD_AUDIO"/>
              <uses-permission android:name="android.permission.CAMERA"/>
              <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>

              <uses-feature
                  android:name="android.hardware.camera.flash"
                  android:required="false"/>

              <queries>
                  <intent>
                      <action android:name="android.speech.RecognitionService"/>
                  </intent>
              </queries>

              <application
                  android:allowBackup="false"
                  android:label="MR MUKAM AI"
                  android:icon="@android:drawable/sym_def_app_icon"
                  android:supportsRtl="true"
                  android:theme="@style/Theme.AppCompat.NoActionBar">

                  <activity
                      android:name=".MainActivity"
                      android:exported="true">

                      <intent-filter>
                          <action android:name="android.intent.action.MAIN"/>
                          <category android:name="android.intent.category.LAUNCHER"/>
                      </intent-filter>

                  </activity>

                  <receiver
                      android:name=".receiver.JarvisAdminReceiver"
                      android:permission="android.permission.BIND_DEVICE_ADMIN"
                      android:exported="true"
                      android:label="MR MUKAM AI Lock Admin">

                      <meta-data
                          android:name="android.app.device_admin"
                          android:resource="@xml/device_admin_sample"/>

                      <intent-filter>
                          <action android:name="android.app.action.DEVICE_ADMIN_ENABLED"/>
                      </intent-filter>

                  </receiver>

                  <service
                      android:name=".service.JarvisAccessibilityService"
                      android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE"
                      android:exported="true">

                      <intent-filter>
                          <action android:name="android.accessibilityservice.AccessibilityService"/>
                      </intent-filter>

                      <meta-data
                          android:name="android.accessibilityservice"
                          android:resource="@xml/accessibility_service_config"/>

                  </service>

              </application>
          </manifest>
          EOF

          cat > app/src/main/res/values/strings.xml <<'EOF'
          <resources>
              <string name="app_name">MR MUKAM AI</string>
              <string name="admin_desc">MR MUKAM AI screen lock permission</string>
              <string name="accessibility_desc">MR MUKAM AI user-authorized screen control</string>
          </resources>
          EOF

          cat > app/src/main/res/xml/device_admin_sample.xml <<'EOF'
          <?xml version="1.0" encoding="utf-8"?>
          <device-admin xmlns:android="http://schemas.android.com/apk/res/android">
              <uses-policies>
                  <force-lock/>
              </uses-policies>
          </device-admin>
          EOF

          cat > app/src/main/res/xml/accessibility_service_config.xml <<'EOF'
          <?xml version="1.0" encoding="utf-8"?>
          <accessibility-service
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:description="@string/accessibility_desc"
              android:accessibilityEventTypes="typeWindowStateChanged|typeWindowContentChanged"
              android:accessibilityFeedbackType="feedbackGeneric"
              android:notificationTimeout="100"
              android:canRetrieveWindowContent="true"
              android:canPerformGestures="true"
              android:accessibilityFlags="flagDefault"/>
          EOF

          cat > app/src/main/res/layout/activity_main.xml <<'EOF'
          <?xml version="1.0" encoding="utf-8"?>
          <ScrollView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:background="#0A0E17">

              <LinearLayout
                  android:layout_width="match_parent"
                  android:layout_height="wrap_content"
                  android:orientation="vertical"
                  android:gravity="center_horizontal"
                  android:padding="24dp">

                  <TextView
                      android:layout_width="wrap_content"
                      android:layout_height="wrap_content"
                      android:text="MR MUKAM AI"
                      android:textColor="#00E5FF"
                      android:textSize="28sp"
                      android:textStyle="bold"
                      android:layout_marginTop="30dp"/>

                  <TextView
                      android:layout_width="wrap_content"
                      android:layout_height="wrap_content"
                      android:text="J.A.R.V.I.S"
                      android:textColor="#FFFFFF"
                      android:textSize="18sp"
                      android:layout_marginTop="8dp"/>

                  <TextView
                      android:id="@+id/tvStatus"
                      android:layout_width="match_parent"
                      android:layout_height="wrap_content"
                      android:text="JARVIS ONLINE"
                      android:textColor="#00E5FF"
                      android:textSize="16sp"
                      android:gravity="center"
                      android:layout_marginTop="30dp"/>

                  <TextView
                      android:id="@+id/tvLastCommand"
                      android:layout_width="match_parent"
                      android:layout_height="wrap_content"
                      android:text="आदेश की प्रतीक्षा में..."
                      android:textColor="#FFFFFF"
                      android:textSize="15sp"
                      android:gravity="center"
                      android:padding="20dp"
                      android:layout_marginTop="20dp"/>

                  <Button
                      android:id="@+id/btnMic"
                      android:layout_width="match_parent"
                      android:layout_height="60dp"
                      android:text="MIC ON - बोलें"
                      android:backgroundTint="#00BCD4"
                      android:textColor="#000000"
                      android:layout_marginTop="20dp"/>

                  <Button
                      android:id="@+id/btnEmergencyStop"
                      android:layout_width="match_parent"
                      android:layout_height="55dp"
                      android:text="EMERGENCY STOP"
                      android:backgroundTint="#D50000"
                      android:textColor="#FFFFFF"
                      android:layout_marginTop="12dp"/>

                  <Button
                      android:id="@+id/btnFlash"
                      android:layout_width="match_parent"
                      android:layout_height="55dp"
                      android:text="FLASHLIGHT ON"
                      android:layout_marginTop="12dp"/>

                  <Button
                      android:id="@+id/btnLock"
                      android:layout_width="match_parent"
                      android:layout_height="55dp"
                      android:text="PHONE LOCK"
                      android:layout_marginTop="12dp"/>

                  <Button
                      android:id="@+id/btnAccessibility"
                      android:layout_width="match_parent"
                      android:layout_height="55dp"
                      android:text="ACCESSIBILITY SETTINGS"
                      android:layout_marginTop="12dp"/>

              </LinearLayout>
          </ScrollView>
          EOF

          cat > app/src/main/java/com/mukam/jarvis/receiver/JarvisAdminReceiver.kt <<'EOF'
          package com.mukam.jarvis.receiver

          import android.app.admin.DeviceAdminReceiver
          import android.content.Context
          import android.content.Intent
          import android.widget.Toast

          class JarvisAdminReceiver : DeviceAdminReceiver() {

              override fun onEnabled(context: Context, intent: Intent) {
                  Toast.makeText(
                      context,
                      "MR MUKAM AI Lock Activated",
                      Toast.LENGTH_SHORT
                  ).show()
              }
          }
          EOF

          cat > app/src/main/java/com/mukam/jarvis/control/HardwareController.kt <<'EOF'
          package com.mukam.jarvis.control

          import android.Manifest
          import android.app.admin.DevicePolicyManager
          import android.content.ComponentName
          import android.content.Context
          import android.content.pm.PackageManager
          import android.hardware.camera2.CameraCharacteristics
          import android.hardware.camera2.CameraManager
          import android.media.AudioManager
          import androidx.core.content.ContextCompat
          import com.mukam.jarvis.receiver.JarvisAdminReceiver

          class HardwareController(private val context: Context) {

              private val cameraManager =
                  context.getSystemService(Context.CAMERA_SERVICE) as CameraManager

              private val audioManager =
                  context.getSystemService(Context.AUDIO_SERVICE) as AudioManager

              private val policyManager =
                  context.getSystemService(Context.DEVICE_POLICY_SERVICE) as DevicePolicyManager

              private val adminComponent =
                  ComponentName(context, JarvisAdminReceiver::class.java)

              fun setFlashlight(enabled: Boolean): Boolean {
                  return try {
                      if (ContextCompat.checkSelfPermission(
                              context,
                              Manifest.permission.CAMERA
                          ) != PackageManager.PERMISSION_GRANTED
                      ) return false

                      val cameraId = cameraManager.cameraIdList.firstOrNull {
                          val characteristics =
                              cameraManager.getCameraCharacteristics(it)

                          characteristics.get(
                              CameraCharacteristics.FLASH_INFO_AVAILABLE
                          ) == true &&
                          characteristics.get(
                              CameraCharacteristics.LENS_FACING
                          ) == CameraCharacteristics.LENS_FACING_BACK
                      } ?: return false

                      cameraManager.setTorchMode(cameraId, enabled)
                      true
                  } catch (e: Exception) {
                      false
                  }
              }

              fun adjustVolume(increase: Boolean) {
                  val direction = if (increase) {
                      AudioManager.ADJUST_RAISE
                  } else {
                      AudioManager.ADJUST_LOWER
                  }

                  audioManager.adjustStreamVolume(
                      AudioManager.STREAM_MUSIC,
                      direction,
                      AudioManager.FLAG_SHOW_UI
                  )
              }

              fun lockScreen(): Boolean {
                  return try {
                      if (policyManager.isAdminActive(adminComponent)) {
                          policyManager.lockNow()
                          true
                      } else {
                          false
                      }
                  } catch (e: SecurityException) {
                      false
                  }
              }

              fun isAdminActive(): Boolean {
                  return policyManager.isAdminActive(adminComponent)
              }
          }
          EOF

          cat > app/src/main/java/com/mukam/jarvis/service/JarvisAccessibilityService.kt <<'EOF'
          package com.mukam.jarvis.service

          import android.accessibilityservice.AccessibilityService
          import android.accessibilityservice.GestureDescription
          import android.graphics.Path
          import android.view.accessibility.AccessibilityEvent
          import android.view.accessibility.AccessibilityNodeInfo

          class JarvisAccessibilityService : AccessibilityService() {

              companion object {
                  @Volatile
                  var instance: JarvisAccessibilityService? = null
                      private set
              }

              override fun onServiceConnected() {
                  super.onServiceConnected()
                  instance = this
              }

              override fun onAccessibilityEvent(event: AccessibilityEvent?) {}

              override fun onInterrupt() {}

              override fun onDestroy() {
                  instance = null
                  super.onDestroy()
              }

              fun goBack(): Boolean {
                  return performGlobalAction(GLOBAL_ACTION_BACK)
              }

              fun clickByText(target: String): Boolean {
                  val root = rootInActiveWindow ?: return false
                  val nodes = root.findAccessibilityNodeInfosByText(target)

                  for (node in nodes) {
                      if (node.isClickable) {
                          val success = node.performAction(
                              AccessibilityNodeInfo.ACTION_CLICK
                          )
                          if (success) return true
                      }
                  }

                  return false
              }

              fun swipeVertical(scrollUp: Boolean): Boolean {
                  val metrics = resources.displayMetrics

                  val startY = if (scrollUp) {
                      metrics.heightPixels * 0.75f
                  } else {
                      metrics.heightPixels * 0.25f
                  }

                  val endY = if (scrollUp) {
                      metrics.heightPixels * 0.25f
                  } else {
                      metrics.heightPixels * 0.75f
                  }

                  val path = Path().apply {
                      moveTo(metrics.widthPixels / 2f, startY)
                      lineTo(metrics.widthPixels / 2f, endY)
                  }

                  val gesture = GestureDescription.Builder()
                      .addStroke(
                          GestureDescription.StrokeDescription(
                              path, 0, 350
                          )
                      )
                      .build()

                  return dispatchGesture(gesture, null, null)
              }
          }
          EOF

          cat > app/src/main/java/com/mukam/jarvis/PermissionManager.kt <<'EOF'
          package com.mukam.jarvis

          import android.Manifest
          import android.content.Context
          import android.content.pm.PackageManager
          import androidx.core.content.ContextCompat

          object PermissionManager {

              fun hasMicrophonePermission(context: Context): Boolean {
                  return ContextCompat.checkSelfPermission(
                      context,
                      Manifest.permission.RECORD_AUDIO
                  ) == PackageManager.PERMISSION_GRANTED
              }

              fun hasCameraPermission(context: Context): Boolean {
                  return ContextCompat.checkSelfPermission(
                      context,
                      Manifest.permission.CAMERA
                  ) == PackageManager.PERMISSION_GRANTED
              }
          }
          EOF

          cat > app/src/main/java/com/mukam/jarvis/ai/GeminiBrain.kt <<'EOF'
          package com.mukam.jarvis.ai

          import kotlinx.coroutines.Dispatchers
          import kotlinx.coroutines.withContext
          import org.json.JSONObject
          import java.net.HttpURLConnection
          import java.net.URL

          class GeminiBrain(private val backendUrl: String) {

              suspend fun processUserCommand(query: String): JSONObject =
                  withContext(Dispatchers.IO) {

                      require(query.isNotBlank()) {
                          "Command खाली है"
                      }

                      require(backendUrl.startsWith("https://")) {
                          "Secure Gemini Backend URL configure नहीं है"
                      }

                      val connection =
                          URL(backendUrl).openConnection() as HttpURLConnection

                      try {
                          connection.requestMethod = "POST"
                          connection.connectTimeout = 15000
                          connection.readTimeout = 30000
                          connection.doOutput = true

                          connection.setRequestProperty(
                              "Content-Type",
                              "application/json"
                          )

                          val request = JSONObject().apply {
                              put("message", query)
                          }

                          connection.outputStream.use {
                              it.write(
                                  request.toString().toByteArray(Charsets.UTF_8)
                              )
                          }

                          val code = connection.responseCode

                          val stream = if (code in 200..299) {
                              connection.inputStream
                          } else {
                              connection.errorStream
                          }

                          val response = stream?.bufferedReader()?.use {
                              it.readText()
                          } ?: ""

                          if (code !in 200..299) {
                              throw Exception("Backend HTTP Error: $code")
                          }

                          val json = JSONObject(response)

                          if (!json.has("reply")) {
                              throw Exception("Invalid Backend Response")
                          }

                          json

                      } finally {
                          connection.disconnect()
                      }
                  }
          }
          EOF

          cat > app/src/main/java/com/mukam/jarvis/voice/JarvisVoiceEngine.kt <<'EOF'
          package com.mukam.jarvis.voice

          import android.content.Context
          import android.content.Intent
          import android.os.Bundle
          import android.os.Handler
          import android.os.Looper
          import android.speech.RecognitionListener
          import android.speech.RecognizerIntent
          import android.speech.SpeechRecognizer
          import android.speech.tts.TextToSpeech
          import java.util.Locale

          class JarvisVoiceEngine(
              context: Context,
              private val onResult: (String) -> Unit,
              private val onStatusUpdate: (String) -> Unit
          ) : RecognitionListener {

              private val appContext = context.applicationContext
              private val handler = Handler(Looper.getMainLooper())

              private var recognizer: SpeechRecognizer? = null
              private var tts: TextToSpeech? = null

              @Volatile
              private var active = false

              @Volatile
              private var continuous = false

              init {
                  if (SpeechRecognizer.isRecognitionAvailable(appContext)) {
                      recognizer = SpeechRecognizer
                          .createSpeechRecognizer(appContext)
                          .apply {
                              setRecognitionListener(this@JarvisVoiceEngine)
                          }
                  } else {
                      onStatusUpdate("Speech Recognition उपलब्ध नहीं")
                  }

                  tts = TextToSpeech(appContext) { status ->
                      if (status == TextToSpeech.SUCCESS) {
                          tts?.language = Locale("hi", "IN")
                      }
                  }
              }

              fun startListening(continuousMode: Boolean = false) {
                  handler.post {
                      if (active) return@post

                      if (recognizer == null) {
                          onStatusUpdate("Speech Recognition उपलब्ध नहीं")
                          return@post
                      }

                      active = true
                      continuous = continuousMode
                      beginRecognition()
                  }
              }

              private fun beginRecognition() {
                  if (!active) return

                  val intent = Intent(
                      RecognizerIntent.ACTION_RECOGNIZE_SPEECH
                  ).apply {
                      putExtra(
                          RecognizerIntent.EXTRA_LANGUAGE_MODEL,
                          RecognizerIntent.LANGUAGE_MODEL_FREE_FORM
                      )
                      putExtra(
                          RecognizerIntent.EXTRA_LANGUAGE,
                          "hi-IN"
                      )
                      putExtra(
                          RecognizerIntent.EXTRA_PARTIAL_RESULTS,
                          false
                      )
                  }

                  try {
                      recognizer?.startListening(intent)
                      onStatusUpdate("JARVIS सुन रहा है...")
                  } catch (e: Exception) {
                      active = false
                      onStatusUpdate("Microphone Error")
                  }
              }

              fun stopListening() {
                  handler.post {
                      active = false
                      continuous = false
                      handler.removeCallbacksAndMessages(null)

                      try {
                          recognizer?.cancel()
                      } catch (_: Exception) {
                      }

                      onStatusUpdate("Emergency Stop: Listening बंद")
                  }
              }

              fun speak(text: String) {
                  handler.post {
                      tts?.speak(
                          text,
                          TextToSpeech.QUEUE_FLUSH,
                          null,
                          "JARVIS_REPLY"
                      )
                  }
              }

              private fun continueIfEnabled() {
                  if (active && continuous) {
                      handler.postDelayed({
                          if (active && continuous) {
                              beginRecognition()
                          }
                      }, 500)
                  } else {
                      active = false
                      onStatusUpdate("आदेश पूरा हुआ")
                  }
              }

              override fun onResults(results: Bundle?) {
                  if (!active) return

                  val matches = results?.getStringArrayList(
                      SpeechRecognizer.RESULTS_RECOGNITION
                  )

                  if (!matches.isNullOrEmpty()) {
                      onResult(matches[0])
                  }

                  continueIfEnabled()
              }

              override fun onError(error: Int) {
                  if (!active) return

                  if (continuous && error != SpeechRecognizer.ERROR_INSUFFICIENT_PERMISSIONS) {
                      continueIfEnabled()
                  } else {
                      active = false
                      onStatusUpdate("Voice Error: $error")
                  }
              }

              override fun onReadyForSpeech(params: Bundle?) {}
              override fun onBeginningOfSpeech() {}
              override fun onRmsChanged(rmsdB: Float) {}
              override fun onBufferReceived(buffer: ByteArray?) {}
              override fun onEndOfSpeech() {}
              override fun onPartialResults(partialResults: Bundle?) {}
              override fun onEvent(eventType: Int, params: Bundle?) {}

              fun release() {
                  active = false
                  continuous = false
                  handler.removeCallbacksAndMessages(null)

                  recognizer?.cancel()
                  recognizer?.destroy()
                  recognizer = null

                  tts?.stop()
                  tts?.shutdown()
                  tts = null
              }
          }
          EOF

          cat > app/src/main/java/com/mukam/jarvis/MainActivity.kt <<'EOF'
          package com.mukam.jarvis

          import android.Manifest
          import android.app.admin.DevicePolicyManager
          import android.content.ComponentName
          import android.content.Intent
          import android.os.Bundle
          import android.provider.Settings
          import android.widget.Button
          import android.widget.TextView
          import androidx.activity.result.contract.ActivityResultContracts
          import androidx.appcompat.app.AppCompatActivity
          import androidx.lifecycle.lifecycleScope
          import com.mukam.jarvis.ai.GeminiBrain
          import com.mukam.jarvis.control.HardwareController
          import com.mukam.jarvis.receiver.JarvisAdminReceiver
          import com.mukam.jarvis.service.JarvisAccessibilityService
          import com.mukam.jarvis.voice.JarvisVoiceEngine
          import kotlinx.coroutines.launch

          class MainActivity : AppCompatActivity() {

              private lateinit var voiceEngine: JarvisVoiceEngine
              private lateinit var hardware: HardwareController

              private lateinit var tvStatus: TextView
              private lateinit var tvLastCommand: TextView

              private lateinit var btnMic: Button
              private lateinit var btnStop: Button
              private lateinit var btnFlash: Button
              private lateinit var btnLock: Button
              private lateinit var btnAccessibility: Button

              private var flashlightOn = false
              private lateinit var gemini: GeminiBrain

              private val microphonePermission =
                  registerForActivityResult(
                      ActivityResultContracts.RequestPermission()
                  ) { granted ->
                      if (granted) {
                          voiceEngine.startListening()
                      } else {
                          setStatus("Microphone Permission आवश्यक है")
                      }
                  }

              private val cameraPermission =
                  registerForActivityResult(
                      ActivityResultContracts.RequestPermission()
                  ) { granted ->
                      if (granted) {
                          toggleFlashlight()
                      } else {
                          setStatus("Camera Permission आवश्यक है")
                      }
                  }

              override fun onCreate(savedInstanceState: Bundle?) {
                  super.onCreate(savedInstanceState)
                  setContentView(R.layout.activity_main)

                  tvStatus = findViewById(R.id.tvStatus)
                  tvLastCommand = findViewById(R.id.tvLastCommand)

                  btnMic = findViewById(R.id.btnMic)
                  btnStop = findViewById(R.id.btnEmergencyStop)
                  btnFlash = findViewById(R.id.btnFlash)
                  btnLock = findViewById(R.id.btnLock)
                  btnAccessibility = findViewById(R.id.btnAccessibility)

                  hardware = HardwareController(this)
                  gemini = GeminiBrain(BuildConfig.BACKEND_URL)

                  voiceEngine = JarvisVoiceEngine(
                      this,
                      { text ->
                          runOnUiThread {
                              handleVoiceInput(text)
                          }
                      },
                      { status ->
                          runOnUiThread {
                              setStatus(status)
                          }
                      }
                  )

                  btnMic.setOnClickListener {
                      if (PermissionManager.hasMicrophonePermission(this)) {
                          voiceEngine.startListening()
                      } else {
                          microphonePermission.launch(
                              Manifest.permission.RECORD_AUDIO
                          )
                      }
                  }

                  btnStop.setOnClickListener {
                      voiceEngine.stopListening()
                      setStatus("Emergency Stop सक्रिय")
                  }

                  btnFlash.setOnClickListener {
                      if (PermissionManager.hasCameraPermission(this)) {
                          toggleFlashlight()
                      } else {
                          cameraPermission.launch(
                              Manifest.permission.CAMERA
                          )
                      }
                  }

                  btnLock.setOnClickListener {
                      lockPhone()
                  }

                  btnAccessibility.setOnClickListener {
                      startActivity(
                          Intent(Settings.ACTION_ACCESSIBILITY_SETTINGS)
                      )
                  }
              }

              private fun setStatus(message: String) {
                  tvStatus.text = message
              }

              private fun toggleFlashlight() {
                  val next = !flashlightOn

                  if (hardware.setFlashlight(next)) {
                      flashlightOn = next

                      btnFlash.text = if (next) {
                          "FLASHLIGHT OFF"
                      } else {
                          "FLASHLIGHT ON"
                      }

                      voiceEngine.speak(
                          if (next) "टॉर्च चालू है"
                          else "टॉर्च बंद है"
                      )
                  } else {
                      setStatus("Flashlight उपलब्ध नहीं या Permission Error")
                  }
              }

              private fun lockPhone() {
                  if (hardware.isAdminActive()) {
                      if (hardware.lockScreen()) {
                          setStatus("Phone Lock किया जा रहा है")
                      } else {
                          setStatus("Phone Lock असफल")
                      }
                  } else {
                      val component = ComponentName(
                          this,
                          JarvisAdminReceiver::class.java
                      )

                      val intent = Intent(
                          DevicePolicyManager.ACTION_ADD_DEVICE_ADMIN
                      ).apply {
                          putExtra(
                              DevicePolicyManager.EXTRA_DEVICE_ADMIN,
                              component
                          )

                          putExtra(
                              DevicePolicyManager.EXTRA_ADD_EXPLANATION,
                              "MR MUKAM AI को आपकी अनुमति से फोन लॉक करने की सुविधा दें।"
                          )
                      }

                      startActivity(intent)
                  }
              }

              private fun handleVoiceInput(text: String) {
                  tvLastCommand.text = "आदेश: $text"

                  val command = text.lowercase()

                  when {
                      command.contains("torch on") ||
                      command.contains("flashlight on") ||
                      command.contains("टॉर्च चालू") -> {
                          if (!flashlightOn) toggleFlashlight()
                      }

                      command.contains("torch off") ||
                      command.contains("flashlight off") ||
                      command.contains("टॉर्च बंद") -> {
                          if (flashlightOn) toggleFlashlight()
                      }

                      command.contains("volume up") ||
                      command.contains("आवाज बढ़ाओ") -> {
                          hardware.adjustVolume(true)
                          voiceEngine.speak("आवाज बढ़ा दी है")
                      }

                      command.contains("volume down") ||
                      command.contains("आवाज कम") -> {
                          hardware.adjustVolume(false)
                          voiceEngine.speak("आवाज कम कर दी है")
                      }

                      command.contains("lock phone") ||
                      command.contains("फोन लॉक") ||
                      command.contains("स्क्रीन लॉक") -> {
                          lockPhone()
                      }

                      command.contains("go back") ||
                      command.contains("वापस जाओ") -> {
                          val service = JarvisAccessibilityService.instance

                          if (service?.goBack() == true) {
                              voiceEngine.speak("वापस जा रहा हूँ")
                          } else {
                              voiceEngine.speak(
                                  "पहले Accessibility Permission चालू करें"
                              )
                          }
                      }

                      command.contains("scroll up") ||
                      command.contains("ऊपर स्क्रॉल") -> {
                          val service = JarvisAccessibilityService.instance

                          if (service != null) {
                              service.swipeVertical(true)
                          } else {
                              voiceEngine.speak(
                                  "Accessibility Permission चालू करें"
                              )
                          }
                      }

                      command.contains("scroll down") ||
                      command.contains("नीचे स्क्रॉल") -> {
                          val service = JarvisAccessibilityService.instance

                          if (service != null) {
                              service.swipeVertical(false)
                          } else {
                              voiceEngine.speak(
                                  "Accessibility Permission चालू करें"
                              )
                          }
                      }

                      else -> askGemini(text)
                  }
              }

              private fun askGemini(query: String) {
                  lifecycleScope.launch {
                      try {
                          setStatus("Gemini AI सोच रहा है...")

                          val response =
                              gemini.processUserCommand(query)

                          val reply = response.getString("reply")

                          setStatus("JARVIS Online")
                          voiceEngine.speak(reply)

                      } catch (e: Exception) {
                          setStatus("AI Error: ${e.message ?: "Unknown Error"}")

                          voiceEngine.speak(
                              "AI सर्वर से जुड़ नहीं पाया। कृपया Backend URL जांचें।"
                          )
                      }
                  }
              }

              override fun onDestroy() {
                  voiceEngine.release()
                  super.onDestroy()
              }
          }
          EOF

      - name: Build Debug APK
        run: gradle :app:assembleDebug --no-daemon --stacktrace

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: mr-mukam-ai-debug-apk
          path: app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error
