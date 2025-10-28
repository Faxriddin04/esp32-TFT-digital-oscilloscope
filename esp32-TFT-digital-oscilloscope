#include <TFT_eSPI.h>
#include <SPI.h>

TFT_eSPI tft = TFT_eSPI();

#define ANALOG_PIN 34
#define SAMPLE_COUNT 400
#define SAMPLE_DELAY_US 50  // 20 kS/s ≈ 1 kHz signal uchun yetarli

uint16_t samples[SAMPLE_COUNT];

void setup() {
  Serial.begin(115200);
  tft.init();
  tft.setRotation(1);
  tft.fillScreen(TFT_BLACK);
}

void loop() {
  // 1️⃣ Signalni o‘qish
  for (int i = 0; i < SAMPLE_COUNT; i++) {
    samples[i] = analogRead(ANALOG_PIN);
    delayMicroseconds(SAMPLE_DELAY_US);
  }

  // 2️⃣ Amplituda hisoblash
  uint16_t minV = 4095, maxV = 0;
  for (int i = 0; i < SAMPLE_COUNT; i++) {
    if (samples[i] < minV) minV = samples[i];
    if (samples[i] > maxV) maxV = samples[i];
  }
  float ampV = (maxV - minV) * (3.3 / 4095.0);

  // 3️⃣ Chastota hisoblash (nol kesish orqali)
  int zeroCross = 0;
  for (int i = 1; i < SAMPLE_COUNT; i++) {
    if ((samples[i - 1] < 2048) && (samples[i] >= 2048))
      zeroCross++;
  }
  float period = (SAMPLE_COUNT * SAMPLE_DELAY_US) / (float)zeroCross; // mikrosekund
  float freq = 1000000.0 / period;

  // 4️⃣ Grafik chizish
  tft.fillScreen(TFT_BLACK);
  for (int i = 1; i < SAMPLE_COUNT; i++) {
    int y1 = map(samples[i - 1], 0, 4095, 120, 0);
    int y2 = map(samples[i], 0, 4095, 120, 0);
    tft.drawLine(i - 1, y1, i, y2, TFT_GREEN);
  }

  // 5️⃣ Ma’lumot chiqarish
  tft.setCursor(0, 130);
  tft.setTextColor(TFT_WHITE, TFT_BLACK);
  tft.printf("Amplitude: %.2f V\n", ampV);
  tft.printf("Frequency: %.1f Hz\n", freq);

  delay(100);
}
