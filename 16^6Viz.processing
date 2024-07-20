import ddf.minim.*;
import ddf.minim.analysis.*;

Minim minim;
AudioPlayer player;
FFT fft;
color currentColor;
float lerpFactor = 0.2;

void setup() {
  fullScreen(P3D);
  colorMode(RGB, 255);
  minim = new Minim(this);
  player = minim.loadFile("Frog Level Demo.wav", 2048);
  fft = new FFT(player.bufferSize(), player.sampleRate());
  player.play();
  currentColor = color(0, 0, 0); // initialize currentColor
}

void draw() {
  fft.forward(player.mix);
  
  int amplitudeValue = get24BitAmplitude(player.mix);
  float highestFreq = getHighestSignificantFrequency(fft);
  
  color targetColor = amplitudeToColor(amplitudeValue, highestFreq);
  currentColor = lerpColor(currentColor, targetColor, lerpFactor);
  
  background(currentColor);
  
  float progress = map(player.position(), 0, player.length(), 0, width);
  stroke(255);
  line(0, height - 5, progress, height - 5);
}

int get24BitAmplitude(AudioBuffer mix) {
  float maxAmp = 0;
  for (int i = 0; i < mix.size(); i++) {
    maxAmp = max(maxAmp, abs(mix.get(i)));
  }
  // Use a logarithmic scale to emphasize lower amplitudes
  return int(map(log(maxAmp * 9 + 1), 0, log(10), 0, 16777215));
}

color amplitudeToColor(int amplitudeValue, float highestFreq) {
  // Split the 24-bit value into RGB components
  int r = (amplitudeValue >> 16) & 0xFF;
  int g = (amplitudeValue >> 8) & 0xFF;
  int b = (amplitudeValue & 0xFF);
  
  // Optionally adjust color balance here
  r = int(r * 1.2); // Boost red
  g = int(g * 1.1); // Slightly boost green
  b = int(b * 0.8); // Reduce blue
  
  // Ensure values are within 0-255 range
  r = constrain(r, 0, 255);
  g = constrain(g, 0, 255);
  b = constrain(b, 0, 255);
  
  return color(r, g, b);
}

float getHighestSignificantFrequency(FFT fft) {
  float maxAmp = 0;
  int maxIndex = 0;
  for (int i = 0; i < fft.specSize(); i++) {
    if (fft.getBand(i) > maxAmp) {
      maxAmp = fft.getBand(i);
      maxIndex = i;
    }
  }
  return fft.indexToFreq(maxIndex);
}

void keyPressed() {
  if (key == ' ') {
    if (player.isPlaying()) player.pause();
    else player.play();
  }
}

void stop() {
  player.close();
  minim.stop();
  super.stop();
}
