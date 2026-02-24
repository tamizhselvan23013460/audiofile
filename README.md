# Ex.No:3 Develop a simple application to play and control the audio file in android studio.

## NAME : TAMIZHSELVAN B
## REGISTER No : 212223230225


## AIM:

To develop a simple application, to play and control the audio file and to perfrom the start,pause and stop opeartion in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as audiofile and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml and create start,pause and stop button.

Step 6: Display message give in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to play and control the audio file”.
Developed by: TAMIZHSELVAN B
Registeration Number : 212223230225
*/
```

## MAIN_ACTIVITY.JAVA :

```
package com.example.audiofile;

import android.content.Context;
import android.media.AudioManager;
import android.media.MediaPlayer;
import android.os.Bundle;
import android.os.Handler;
import android.util.Log;
import android.widget.Button;
import android.widget.SeekBar;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "AudioPlayerApp";
    private MediaPlayer mediaPlayer;
    private SeekBar seekBar;
    private final Handler handler = new Handler();
    
    private final Runnable updateSeekBarRunnable = new Runnable() {
        @Override
        public void run() {
            if (mediaPlayer != null && mediaPlayer.isPlaying()) {
                seekBar.setProgress(mediaPlayer.getCurrentPosition());
                handler.postDelayed(this, 1000);
            }
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        Button btnPlay = findViewById(R.id.btnPlay);
        Button btnPause = findViewById(R.id.btnPause);
        Button btnStop = findViewById(R.id.btnStop);
        seekBar = findViewById(R.id.seekBar);
        SeekBar volumeSeekBar = findViewById(R.id.volumeSeekBar);

        AudioManager audioManager = (AudioManager) getSystemService(Context.AUDIO_SERVICE);
        
        // Setup Volume SeekBar
        int maxVolume = audioManager.getStreamMaxVolume(AudioManager.STREAM_MUSIC);
        int currentVolume = audioManager.getStreamVolume(AudioManager.STREAM_MUSIC);
        volumeSeekBar.setMax(maxVolume);
        volumeSeekBar.setProgress(currentVolume);

        volumeSeekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                audioManager.setStreamVolume(AudioManager.STREAM_MUSIC, progress, 0);
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {}

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {}
        });

        initializeMediaPlayer();

        btnPlay.setOnClickListener(v -> {
            if (mediaPlayer == null) {
                initializeMediaPlayer();
            }
            
            if (mediaPlayer != null) {
                if (!mediaPlayer.isPlaying()) {
                    mediaPlayer.start();
                    startSeekBarUpdate();
                    Toast.makeText(MainActivity.this, "Playing", Toast.LENGTH_SHORT).show();
                }
            } else {
                Toast.makeText(MainActivity.this, "File 'sample_audio.mp3' not found in res/raw", Toast.LENGTH_LONG).show();
            }
        });

        btnPause.setOnClickListener(v -> {
            if (mediaPlayer != null && mediaPlayer.isPlaying()) {
                mediaPlayer.pause();
                handler.removeCallbacks(updateSeekBarRunnable);
                Toast.makeText(MainActivity.this, "Paused", Toast.LENGTH_SHORT).show();
            }
        });

        btnStop.setOnClickListener(v -> {
            if (mediaPlayer != null) {
                try {
                    mediaPlayer.stop();
                    mediaPlayer.prepare();
                    mediaPlayer.seekTo(0);
                    seekBar.setProgress(0);
                    handler.removeCallbacks(updateSeekBarRunnable);
                    Toast.makeText(MainActivity.this, "Stopped", Toast.LENGTH_SHORT).show();
                } catch (Exception e) {
                    Log.e(TAG, "Error stopping or preparing MediaPlayer", e);
                }
            }
        });

        seekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                if (fromUser && mediaPlayer != null) {
                    mediaPlayer.seekTo(progress);
                }
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {}

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {}
        });
    }

    private void initializeMediaPlayer() {
        try {
            if (mediaPlayer != null) {
                mediaPlayer.release();
                mediaPlayer = null;
            }
            
            mediaPlayer = MediaPlayer.create(this, R.raw.sample_audio);
            
            if (mediaPlayer != null) {
                seekBar.setMax(mediaPlayer.getDuration());
                mediaPlayer.setOnCompletionListener(mp -> {
                    seekBar.setProgress(0);
                    handler.removeCallbacks(updateSeekBarRunnable);
                    Toast.makeText(MainActivity.this, "Playback Finished", Toast.LENGTH_SHORT).show();
                });
            } else {
                Log.e(TAG, "MediaPlayer.create returned null. Check your audio file.");
            }
        } catch (Exception e) {
            Log.e(TAG, "MediaPlayer initialization failed.", e);
        }
    }

    private void startSeekBarUpdate() {
        handler.removeCallbacks(updateSeekBarRunnable);
        handler.post(updateSeekBarRunnable);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        handler.removeCallbacks(updateSeekBarRunnable);
        if (mediaPlayer != null) {
            mediaPlayer.release();
            mediaPlayer = null;
        }
    }
}


```

## ACTIVITY_MAIN.XML :

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvAudioLabel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Audio"
        android:textSize="22sp"
        android:textStyle="bold"
        android:layout_marginTop="32dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <SeekBar
        android:id="@+id/seekBar"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        app:layout_constraintTop_toBottomOf="@+id/tvAudioLabel"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/tvVolumeLabel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Volume"
        android:textSize="18sp"
        android:layout_marginTop="24dp"
        app:layout_constraintTop_toBottomOf="@+id/seekBar"
        app:layout_constraintStart_toStartOf="parent" />

    <SeekBar
        android:id="@+id/volumeSeekBar"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        app:layout_constraintTop_toBottomOf="@+id/tvVolumeLabel"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <LinearLayout
        android:id="@+id/buttonContainer"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:gravity="center"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/volumeSeekBar">

        <Button
            android:id="@+id/btnPlay"
            android:layout_width="200dp"
            android:layout_height="70dp"
            android:layout_margin="8dp"
            android:textSize="18sp"
            android:text="Play" />

        <Button
            android:id="@+id/btnPause"
            android:layout_width="200dp"
            android:layout_height="70dp"
            android:layout_margin="8dp"
            android:textSize="18sp"
            android:text="Pause" />

        <Button
            android:id="@+id/btnStop"
            android:layout_width="200dp"
            android:layout_height="70dp"
            android:layout_margin="8dp"
            android:textSize="18sp"
            android:text="Stop" />

    </LinearLayout>

</androidx.constraintlayout.widget.ConstraintLayout>


```


## OUTPUT

<img width="1918" height="1012" alt="image" src="https://github.com/user-attachments/assets/1463e476-e9fb-43b5-8d78-2e51fb6cae79" />

<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/9749d30e-3c71-48de-ba8b-3b81f4f7d84e" />

<img width="1919" height="1011" alt="image" src="https://github.com/user-attachments/assets/63c404bf-4cdb-49c4-8f58-18432e44c278" />




## RESULT
   Thus a simple application, to play and control the audio file and to perfrom the start,pause and stop opeartion in Android Studio is developed and executed successfully.
