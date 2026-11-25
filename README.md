# AgriVysor

A Flutter app that identifies crop diseases from a photo. Take a picture of a leaf, fruit, or stem, and the app tells you what it most likely is, how confident it is, and what to do about it. If you want more detail, there's a Gemini-powered chatbot built in.

It runs the detection model on the phone itself with TensorFlow Lite, so identifying a disease doesn't need an internet connection. Only the chatbot does.

## Features

- **Disease detection**: take a photo or pick one from your gallery and get a result with a confidence percentage
- **Plant library**: browse and search every disease the model knows, with descriptions, treatments, and preventive measures
- **Chatbot**: ask follow-up questions, send your own photos, or tap through from a detection result so the disease details and your image are sent over automatically
- **History**: past detections are saved on the device along with the image you scanned
- **Two languages**: English and Filipino (Tagalog), switchable from inside the app

## Supported crops

The model classifies 31 conditions across five crops, plus an `Unknown` label for anything it doesn't recognize.

| Crop | Conditions |
| --- | --- |
| Banana | Cordana, Dry Leaf Disease, Insect Pest, Mosaic, Panama, Yellow Sigatoka, Healthy |
| Coconut | Bud Root Dropping, Bud Rot, Caterpillar Spot, Gray Leaf Spot, Leaf Rot, Stem Bleeding, Fruit Infected, Fruit Healthy |
| Corn | Cercospora Leaf Spot, Common Rust, Northern Leaf Blight, Healthy |
| Pineapple | Crown Rot, Fruit Fasciation Disorder, Fruit Rot, Multiple Crown Disorder, Root Rot, Wilt, Healthy |
| Sugarcane | Mosaic, Red Rot, Rust, Yellow Leaf Disease, Healthy |

The app shows up to the top 5 predictions with a confidence of at least 20%.

## Tech stack

- [Flutter](https://flutter.dev) (Dart `>=3.3.3 <4.0.0`)
- [`tflite_v2`](https://pub.dev/packages/tflite_v2) for on-device image classification
- [`flutter_gemini`](https://pub.dev/packages/flutter_gemini) for the chatbot
- [`flutter_chat_ui`](https://pub.dev/packages/flutter_chat_ui) for the chat interface
- [`image_picker`](https://pub.dev/packages/image_picker) for camera and gallery access
- `intl` with `.arb` files for localization
- [Codemagic](https://codemagic.io) for CI builds

## Platforms

Android and iOS. The TFLite plugin only supports those two, so the web, desktop, and other platform folders in the repo won't run the detection feature.

## Project structure

```
lib/
├── main.dart             # app entry, theme, routes, Gemini init
├── splash_screen.dart
├── main_menu.dart        # navigation between the four tabs
├── detect_tab.dart       # camera/gallery + TFLite inference
├── detection_result.dart # result screen
├── plant_library.dart    # searchable disease list and data
├── disease_detail.dart   # description, treatments, prevention
├── history.dart          # saved detections (local JSON file)
├── pages/home_page.dart  # Gemini chatbot
├── consts.dart           # Gemini API key
└── l10n/                 # English and Tagalog strings (.arb)

assets/
├── model_unquant.tflite  # classification model
├── labels.txt            # class labels, in model output order
├── diseases/             # reference photos for the library
└── fonts/                # SF Pro Display
```

## Getting started

### Requirements

- Flutter SDK (stable channel)
- Android Studio or Xcode, depending on your target
- A [Gemini API key](https://aistudio.google.com/app/apikey) for the chatbot

### Run it

```bash
git clone https://github.com/yan2xme/AgriVysor-V1.git
cd AgriVysor-V1
flutter pub get
```

Open `lib/consts.dart` and set your own key:

```dart
const String GEMINI_API_KEY = "your-key-here";
```

Don't commit your real key. Then run it on a connected device or emulator:

```bash
flutter run
```

### Build an APK

```bash
flutter build apk --release
```

The repo also has a Codemagic workflow (`codemagic.yaml`) that builds a debug APK.

## Updating the model

If you retrain the model, replace `assets/model_unquant.tflite` and update `assets/labels.txt` so the labels are in the same order as the model's outputs. A new disease also needs an entry in `lib/plant_library.dart` and a reference photo in `assets/diseases/` (listed in `pubspec.yaml`).

## Adding a language

Copy `lib/l10n/intl_en.arb` to `intl_<code>.arb` and translate the values. The language buttons are hardcoded in `lib/main_menu.dart` (they call `setLocale` with `en` and `tl`), so add a button there for the new locale.
