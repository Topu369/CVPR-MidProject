
# Orange Fruit Classification Using Convolutional Neural Networks (CNN)

A deep-learning project that classifies orange-fruit images into categories using custom Convolutional Neural Networks (CNNs) built with **PyTorch**. The project explores the dataset, checks for duplicate images, trains and compares two CNN architectures, and evaluates the final model on a held-out test set.

## Project Overview

The project uses image classification to investigate how well a CNN can distinguish between the categories in an orange-fruit image dataset.

Two models are developed and compared:

- **CNN_Basic:** A baseline CNN with 3 convolutional blocks.
- **CNN_Improved:** A deeper CNN with 4 convolutional blocks, Batch Normalization, Dropout, data augmentation, weight decay, and a cosine learning-rate scheduler.

The improved model is designed to reduce overfitting and improve generalization.

## Project Workflow

1. **Dataset Loading** – Locates and extracts the dataset ZIP file.
2. **Dataset Inspection** – Counts images per class, reviews sample images and image dimensions, and checks for unreadable files.
3. **Duplicate Checking** – Uses MD5 hashes to identify and remove exact duplicate images.
4. **Data Splitting** – Creates stratified training, validation, and test sets using a 70% / 15% / 15% split.
5. **Image Preprocessing** – Converts images to RGB and resizes them to 128 × 128 pixels.
6. **Model Training** – Trains the baseline CNN and improved CNN.
7. **Model Evaluation** – Compares both models and evaluates the improved model on the separate test set.
8. **Result Export** – Saves plots, evaluation tables, summaries, and model weights.

## Models

### 1. CNN_Basic

The baseline architecture contains:

- 3 convolutional blocks
- Convolution layers with ReLU activation
- Max Pooling
- Fully connected classification layers
- No Batch Normalization, Dropout, or data augmentation

**Training Configuration:**

- Epochs: 20
- Optimizer: Adam
- Learning Rate: `0.001`
- Loss Function: Cross Entropy Loss

### 2. CNN_Improved

The improved architecture contains:

- 4 convolutional blocks
- Batch Normalization
- ReLU activation
- Max Pooling
- Dropout in the classifier
- Data augmentation applied to training images only

**Data Augmentation Techniques:**

- Random resized cropping
- Random horizontal flipping
- Random vertical flipping
- Random rotation
- Brightness adjustment
- Contrast adjustment
- Saturation adjustment

**Training Configuration:**

- Epochs: 30
- Optimizer: Adam
- Initial Learning Rate: `0.0005`
- Weight Decay: `0.0001`
- Learning Rate Scheduler: Cosine Annealing
- Loss Function: Cross Entropy Loss

Both models save the weights from the epoch with the best validation accuracy.

## Dataset

The notebook expects an image dataset packaged as a ZIP file named:

`Orange Fruit Image Dataset for Classification.zip`

The dataset contains images organized into class folders. The notebook automatically detects the class folders.

The notebook is configured to check for:

- Expected total images: 15,000
- Expected images per class: 3,000

## Getting Started

### Google Colab

1. Upload the Jupyter Notebook to Google Colab.
2. Upload the dataset ZIP file to Google Drive.
3. Run the notebook cells sequentially.
4. For faster training, enable GPU acceleration.

### Local Environment

Clone the repository:

```bash
git clone https://github.com/your-username/your-repository-name.git
cd your-repository-name
```

Install the required libraries:

```bash
pip install torch torchvision numpy pandas matplotlib seaborn pillow scikit-learn torchsummary
```

Open the notebook:

```bash
jupyter notebook 23_53318_3_Orange_Fruit_CNN.ipynb
```

A CUDA-enabled GPU is recommended for faster model training.

## Model Evaluation

Both models are evaluated using their best validation checkpoints.

The evaluation includes:

- Test Accuracy
- Macro Precision
- Macro Recall
- Macro F1-Score
- Per-Class Accuracy
- Confusion Matrix
- Normalized Confusion Matrix
- ROC Curves and AUC
- Sample Predictions with Confidence Scores
- Misclassified Image Analysis
- Training and Validation Accuracy Comparison
- Overfitting Analysis

## Output Files

The notebook generates a `results/` directory containing:

| File | Description |
|---|---|
| `cnn_basic_best.pth` | Best baseline CNN weights |
| `cnn_improved_best.pth` | Best improved CNN weights |
| `results_comparison.csv` | Comparison of both models |
| `classification_report.csv` | Classification performance report |
| `results_summary.txt` | Experiment summary |
| Confusion Matrix Plots | Visualization of classification performance |
| Training Curves | Training and validation accuracy/loss |
| ROC Curves | One-vs-rest ROC analysis |
| Per-Class Accuracy Plot | Accuracy for individual classes |
| Sample Predictions | Visualization of model predictions |
| Misclassified Images | Examples of incorrect predictions |
| Feature Visualizations | Learned filters and feature maps |

The notebook also packages the generated results into `orange_cnn_results.zip` for convenient downloading.

## Technologies Used

- Python
- PyTorch
- Torchvision
- NumPy
- Pandas
- Matplotlib
- Seaborn
- Pillow (PIL)
- Scikit-learn
- Google Colab
- Jupyter Notebook


