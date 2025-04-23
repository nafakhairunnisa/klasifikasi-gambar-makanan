# **Proyek Klasifikasi Gambar Makanan (Food)**

![Klasifikasi Gambar Makanan](joyce-panda-lpsbMRRqMQw-unsplash.jpg)
Photo by Joyce Panda on [Unsplash](https://unsplash.com/photos/french-fries-on-white-ceramic-plate-lpsbMRRqMQw?utm_content=creditShareLink&utm_medium=referral&utm_source=unsplash)

## **Deskripsi**

Proyek ini bertujuan untuk mengklasifikasikan gambar makanan (food classification) ke dalam 10 kategori, yaitu: Baked Potato, Crispy Chicken, Hot Dog, Taco, Sandwich, Taquito, Donut, Fries, Ice Cream, dan Chicken Curry.

Model yang digunakan merupakan kombinasi dari arsitektur Convolutional Neural Network (CNN) dan transfer learning menggunakan MobileNetV2 yang telah dilatih sebelumnya pada dataset ImageNet.

MobileNetV2 digunakan sebagai feature extractor, kemudian ditambahkan beberapa layer CNN dan fully connected layer untuk meningkatkan kemampuan klasifikasi terhadap dataset yang digunakan.

Model ini dilatih dengan optimisasi Adam, fungsi loss categorical crossentropy, serta menggunakan teknik regularisasi seperti dropout dan L2 regularizer untuk mengurangi risiko overfitting.

Proyek ini diimplementasikan menggunakan TensorFlow dan dapat dikonversi ke berbagai format seperti SavedModel, TensorFlow Lite (TFLite) untuk perangkat mobile, dan TensorFlow.js (TFJS) untuk aplikasi berbasis web.

## **Dataset**

Dataset diambil dari platform Kaggle milik akun Harish Kumardatalab. Tautan ke dataset dapat diakses melalui link [ini](https://www.kaggle.com/datasets/harishkumardatalab/food-image-classification-dataset).

## **Instalasi & Menjalankan Notebook**

1. Clone this repository to your local computer

```
git@github.com:nafakhairunnisa/klasifikasi-gambar-makanan.git
```

2. Install dependencies:

```
cd klasifikasi-gambar-makanan
pip install -r requirements.txt
```

3. Jalankan Notebook
   Buka file notebook.ipynb menggunakan salah satu dari:

- Jupyter Notebook (via terminal):

```
jupyter notebook notebook.ipynb
```

- VSCode (dengan Jupyter Extension)

- Google Colab (upload langsung ke colab.research.google.com)

## **Cara Load Model**

**1. SavedModel (Format TensorFlow) pada Notebook**

```
import tensorflow as tf

# Memuat model SavedModel
loaded_model = tf.keras.models.load_model("saved_model")

# Menampilkan struktur model
loaded_model.summary()
```

**2. TensorFlow Lite (TFLite) pada Perangkat Mobile**

- Salin isi folder ‘tflite’ ke folder ‘assets’ (lokasi: path app > src > main).

- Buat file ‘TFLiteHelper.java’ di path app > src > main > java > com > example > imageclassification.

- Inisiasi Tensorflow Lite dalam file ‘TFLiteHelper.java’

```
void init() {
    try {
        Interpreter.Options options = new Interpreter.Options();
        tflite = new Interpreter(loadmodelfile(context), options);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

- Fungsi berikut untuk load file model

```
private MappedByteBuffer loadmodelfile(Activity activity) throws IOException {
    String MODEL_NAME = "model.tflite";
    AssetFileDescriptor fileDescriptor = activity.getAssets().openFd(MODEL_NAME);
    FileInputStream inputStream = new FileInputStream(fileDescriptor.getFileDescriptor());
    FileChannel fileChannel = inputStream.getChannel();
    long startOffset = fileDescriptor.getStartOffset();
    long declaredLength = fileDescriptor.getDeclaredLength();
    return fileChannel.map(FileChannel.MapMode.READ_ONLY, startOffset, declaredLength);
}
```

- Tambahkan label.txt untuk postprocessing

```
public String showresult() {
        try {
            labels = FileUtil.loadLabels(context, "label.txt");
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
        Map<String, Float> labeledProbability =
                new TensorLabel(labels, probabilityProcessor.process(outputProbabilityBuffer))
                        .getMapWithFloatValue();
        float maxValueInMap = (Collections.max(labeledProbability.values()));
        String result = null;
        for (Map.Entry<String, Float> entry : labeledProbability.entrySet()) {
            if (entry.getValue() == maxValueInMap) {
                result = entry.getKey();
            }
        }

        return result;
    }

    private TensorOperator getPostprocessNormalizeOp() {
        return new NormalizeOp(PROBABILITY_MEAN, PROBABILITY_STD);
    }
```

**3. TensorFlow.js (TFJS) pada Aplikasi berbasis Web**

- Tambahkan folder 'tfjs_model' ke dalam folder proyek.

- Tambahkan CDN TensorFlow.js:

```
<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest"></script>
```

- Load model dengan kode berikut:

```
<script>
    async function loadModel() {
        const model = await tf.loadLayersModel('path_to_model/model.json');
        model.summary();
    }

    loadModel();
</script>
```
