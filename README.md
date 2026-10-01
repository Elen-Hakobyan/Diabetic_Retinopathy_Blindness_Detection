# 🩺 Դիաբետիկ ռետինոպաթիայի դասակարգում ResNet50-ով

Deep Learning նախագիծ՝ աչքի ցանցաթաղանթի (retinal fundus) նկարների միջոցով **դիաբետիկ ռետինոպաթիայի աստիճանի ավտոմատ դասակարգման** համար։

Նախագիծը իրականացված է **PyTorch** framework-ի միջոցով և օգտագործում է **ImageNet-ի վրա նախապես ուսուցված ResNet50** մոդելը։

Մոդելը retinal images-ը դասակարգում է **5 դասի**։

---

## 📌 Նախագծի նկարագրություն

Դիաբետիկ ռետինոպաթիան շաքարային դիաբետի հետ կապված աչքի հիվանդություն է, որը կարող է հանգեցնել տեսողության կորստի։

Այս նախագծի նպատակն է ստեղծել Deep Learning մոդել, որը կարող է retinal fundus image-ի հիման վրա կանխատեսել դիաբետիկ ռետինոպաթիայի աստիճանը։

Նախագծում օգտագործվում է **Transfer Learning** մոտեցումը։

Որպես հիմք օգտագործվում է ImageNet dataset-ի վրա նախապես ուսուցված **ResNet50** մոդելը, որի վերջնական classification layer-ը փոխարինվել է մեր խնդրին համապատասխան՝ 5 դաս ունեցող layer-ով։

---

## 🧠 Դասակարգման դասերը

| Class | Նկարագրություն                      |
| ----: | ----------------------------------- |
|     0 | Դիաբետիկ ռետինոպաթիայի նշաններ չկան |
|     1 | Թեթև դիաբետիկ ռետինոպաթիա           |
|     2 | Միջին աստիճանի դիաբետիկ ռետինոպաթիա |
|     3 | Ծանր դիաբետիկ ռետինոպաթիա           |
|     4 | Պրոլիֆերատիվ դիաբետիկ ռետինոպաթիա   |

---

## 🧠 Մոդելի ճարտարապետությունը

Նախագծում օգտագործվում է **ResNet50** ճարտարապետությունը։

Հիմնական պարամետրերը՝

* ResNet50
* Dropout՝ `0.5`
* Fully Connected Layer
* 5 output classes

### Մոդելի կառուցվածքը

```text
Մուտքային նկար
      │
      ▼
512 × 512 × 3
      │
      ▼
ResNet50
      │
      ▼
Dropout(0.5)
      │
      ▼
Linear Layer
      │
      ▼
5 Classes
      │
      ▼
Softmax
      │
      ▼
Կանխատեսված դաս
```

---

## 📊 Dataset

Նախագծում օգտագործվում է **APTOS 2019 Blindness Detection** dataset-ը։

Dataset-ի յուրաքանչյուր retinal image ունի դիաբետիկ ռետինոպաթիայի 5 աստիճաններից մեկը։

### Dataset-ի դասերը

```text
0 → No DR
1 → Mild
2 → Moderate
3 → Severe
4 → Proliferative DR
```

Dataset-ը **անհավասարակշռված է (imbalanced)**, այսինքն՝ տարբեր դասերում նկարների քանակը նույնը չէ։
<img width="636" height="477" alt="image" src="https://github.com/user-attachments/assets/1710efa0-b22f-44a2-a327-ca045dbef192" />




Այդ պատճառով Accuracy-ից բացի օգտագործվում են նաև՝

* Precision
* Recall
* Macro F1-score

---

## 🔄 Տվյալների նախամշակում

Մոդելին image փոխանցելուց առաջ այն անցնում է preprocessing-ի հետևյալ փուլերով։

```text
Original Image
      │
      ▼
Resize → 512 × 512
      │
      ▼
ToTensor
      │
      ▼
ImageNet Normalization
      │
      ▼
ResNet50
```

### Image չափը

Բոլոր նկարները մոդելին փոխանցելուց առաջ վերափոխվում են՝

```text
512 × 512
```

RGB image-ի դեպքում մոդելի մուտքը ստացվում է՝

```text
512 × 512 × 3
```

---

## 🎨 Data Augmentation

Training-ի ժամանակ օգտագործվում են augmentation տեխնիկաներ՝ model-ի generalization-ը բարելավելու համար։

Օգտագործվում են՝

* Random Resized Crop
* Random Horizontal Flip
* Random Rotation
* Tensor Conversion
* ImageNet Normalization

Validation dataset-ի վրա augmentation չի կիրառվում։

---

## ⚙️ Training-ի պարամետրերը

| Պարամետր                |             Արժեք |
| ----------------------- | ----------------: |
| Model                   |          ResNet50 |
| Image Size              |         512 × 512 |
| Classes                 |                 5 |
| Batch Size              |                16 |
| Optimizer               |              Adam |
| Learning Rate           |            `1e-4` |
| Weight Decay            |            `1e-4` |
| Dropout                 |             `0.5` |
| Epochs                  |                30 |
| Loss Function           |  CrossEntropyLoss |
| Scheduler               | ReduceLROnPlateau |
| Early Stopping Patience |                 8 |

---

## 📈 Evaluation Metrics

Model-ի աշխատանքը գնահատվում է հետևյալ metrics-ներով՝

* **Accuracy**
* **Precision**
* **Recall**
* **F1-score**
* **Confusion Matrix**

Քանի որ dataset-ը imbalanced է, որպես հիմնական validation metric օգտագործվում է **Macro F1-score-ը**։

### Ինչու Macro F1

Macro F1-ը յուրաքանչյուր class-ի F1-score-ը հաշվարկում է առանձին և հետո վերցնում է դրանց միջինը։

Այսպիսով՝ փոքր քանակով ներկայացված class-երը նույնպես կարևոր դեր են ստանում գնահատման ժամանակ։

---
## ▶️ Training

Training-ի ընթացքում ծրագիրը՝

1. Կարդում է dataset-ը
2. Բաժանում է տվյալները train և validation մասերի
3. Կատարում է image preprocessing
4. Training images-ի վրա կիրառում է augmentation
5. Ուսուցանում է ResNet50 model-ը
6. Հաշվում է validation metrics-ը
7. Պահպանում է լավագույն model-ը
8. Պահպանում է checkpoint
9. Անհրաժեշտության դեպքում շարունակում է checkpoint-ից
10. Կիրառում է early stopping

python կոդը  train.py ֆայլում
---

## 📊 Training-ի ընթացքում ցուցադրվող արդյունքները 

Յուրաքանչյուր epoch-ի համար ցուցադրվում են՝

```text
Train Loss
Train Accuracy

Validation Loss
Validation Accuracy

Validation Precision
Validation Recall
Validation F1
```

Օրինակ՝

```text
Epoch [18/30] |
Train Loss: 0.4521 |
Train Acc: 0.8214 |
Val Loss: 0.5812 |
Val Acc: 0.7853 |
Precision: 0.7215 |
Recall: 0.6982 |
F1: 0.6533
```

## 🏆 Best Model

Training-ի ընթացքում պահվում է այն model-ը, որն ունի validation dataset-ի վրա ամենաբարձր Macro F1-score-ը։


Լավագույն model-ը պահպանվում է՝

```text
best_model.pth
```

---

## 💾 Checkpoint համակարգ

Նախագիծն ունի նաև checkpoint համակարգ։

Checkpoint-ը պահպանում է՝

* Model weights
* Optimizer state
* Scheduler state
* Current epoch
* Best validation F1
* Patience counter
* Training loss history
* Validation loss history
* Training accuracy history
* Validation accuracy history
* Validation precision history
* Validation recall history
* Validation F1 history

Դրա շնորհիվ training-ը կարելի է շարունակել այն epoch-ից, որտեղ կանգնել էր։

Checkpoint-ը պահվում է՝

```text
checkpoint.pth
```

---

## 🔍 Model-ի գնահատում


| Metric          | Արդյունք |
| --------------- | -------: |
| Accuracy        |   83% |
| Macro Precision |   71% |
| Macro Recall    |   65% |
| Macro F1        |   67% |



Բացի դրանից՝ ստեղծվում է **Confusion Matrix**։

<img width="738" height="587" alt="image" src="https://github.com/user-attachments/assets/7e417308-e67f-4510-be01-f92a676c15d4" />


python կոդը eval.py ֆայլում


## 🖼️ Մեկ նկարի prediction

Նախագիծը հնարավորություն է տալիս նաև մեկ retinal image-ի համար prediction ստանալ։

Օրինակ՝

image_path = (
    r"C:\path\to\retinal_image.png"
)

predict_image(image_path)


Model-ը վերադարձնում է՝

* կանխատեսված class
* confidence
* բոլոր class-երի probabilities

```
============================================================
Կանխատեսում՝ Միջին աստիճանի դիաբետիկ ռետինոպաթիա
Վստահություն՝ 87.43%
============================================================

Բոլոր դասերի հավանականությունները:


No DR               : 1.21%
Mild                : 4.82%
Moderate            : 87.43%
Severe              : 5.13%
Proliferative DR    : 1.41%


Եզրակացությունը կարող է լինել՝


Այս նկարը, ըստ մոդելի, պատկանում է
«Միջին աստիճանի դիաբետիկ ռետինոպաթիա» դասին։

Մոդելի վստահությունը՝ 87.43%։

python կոդը  pred.py ֆայլում


## 📁 Նախագծի կառուցվածքը

Diabetic-Retinopathy-Blindness-Detection/
│
├── data/
│   └── aptos2019-data/
│       ├── train.csv
│       └── train_images/
│
├── src/
│   ├── dataset.py
│   ├── model.py
│   └── ...
│
├── train.py
├── eval.py
├── pred.py
├── notebook.ipynb
│
├── best_model.pth
├── checkpoint.pth
│
├── requirements.txt
├── .gitignore
└── README.md



## ⚠️ Սահմանափակումներ

* Image quality
* Class imbalance
* Dataset-ի առանձնահատկությունները
* Image preprocessing
* Hyperparameters
* Training-ի տևողությունը
* GPU memory-ի սահմանափակումները
___

notebook.ipynb ֆայլում պրոյեկտի կոդի կեղտոտ տարբերակն է (սեվագիրը)
___

## 👨‍💻 Հեղինակներ

Արամայիս Մեսրոպյան, Արման Կարեյան, Էլեն Հակոբյան, Վահան Սարգսյան


## 📄 License

Այս նախագիծը նախատեսված է հիմնականում **ուսումնական և հետազոտական նպատակների համար**։

Այն չի նախատեսվում որպես բժշկական ախտորոշման համակարգ։

