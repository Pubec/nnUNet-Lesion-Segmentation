# nnUNet Lesion Segmentation

This repository contains the [nnUNet](https://github.com/MIC-DKFZ/nnUNet) model weights for lesion segmentation. The model was trained on large dataset with 3000+ cases, acquired on various scanners and field strengths. Lesion labels were acquired using semi-automatic lesion segmentation procedure.

## Dataset

Dataset used for training consisted of **FLAIR** and **T1** MRI images. Lesion masks were acquired semi-automatic.

### Preprocessing

Preprocessing consisted of:
1. T1, FLAIR, SEGMENTATION registration to [MNI ICBM 152 atlas](https://www.bic.mni.mcgill.ca/ServicesAtlases/ICBM152NLin2009)
2. BACKGROUND removal
3. CROPPING to shape divisible by two: (192,  224, 192)

## nnUNet Configs

### Dataset Configuration

- **Channel Names**:
    - 0: FLAIR
    - 1: T1
- **Labels**:
    - 0: Background
    - 1: Lesion

### Training Configuration

- **Plans Name**: nnUNetPlans
- **Original Median Spacing (after transpose)**: [1.0, 1.0, 1.0]
- **Original Median Shape (after transpose)**: [157, 183, 147]

### 3D Full Resolution Configuration
- **Data Identifier**: nnUNetPlans_3d_fullres
- **Preprocessor Name**: DefaultPreprocessor
- **Number of Epochs**: 1000
- **Batch Size**: 6
- **Patch Size**: [128, 128, 128]
- **Median Image Size in Voxels**: [157.0, 183.0, 147.0]
- **Spacing**: [1.0, 1.0, 1.0]
- **Normalization Schemes**: ZScoreNormalization (both channels)
- **Use Mask for Normalization**: True (both channels)
- **UNet Class Name**: PlainConvUNet
- **UNet Base Number of Features**: 32
- **Number of Convolutions per Stage (Encoder)**: [2, 2, 2, 2, 2, 2]
- **Number of Convolutions per Stage (Decoder)**: [2, 2, 2, 2, 2]
- **Number of Pooling Operations per Axis**: [5, 5, 5]
- **Pooling Operation Kernel Sizes**: [[1, 1, 1], [2, 2, 2], [2, 2, 2], [2, 2, 2], [2, 2, 2], [2, 2, 2]]
- **Convolution Kernel Sizes**: [[3, 3, 3], [3, 3, 3], [3, 3, 3], [3, 3, 3], [3, 3, 3], [3, 3, 3]]
- **UNet Max Number of Features**: 320
- **Resampling Function Data**: resample_data_or_seg_to_shape
- **Batch Dice**: False


## Usage

Before using this model you need **nnUNet V2**. For this, check original repository documentation on instalation [https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/installation_instructions.md](https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/installation_instructions.md)


### Predicting

For predicting dataset your call command should look like:

```bash
INPUT_FOLDER=<path-to-input-folder-in-nnunet-compatible-format>
OUTPUT_FOLDER=<path-to-output-folder>

nnUNetv2_predict -d 100 -i $INPUT_FOLDER -o $OUTPUT_FOLDER -f 0 -c 3d_fullres
```

### Fine-Tuning

For fine-tuning custom model your command should look like:

```bash
nnUNetv2_train <task-id> 3d_fullres <fold-id> -pretrained_weights "/<path-to-source>/Dataset100_FLAIR-T1/nnUNetTrainer__nnUNetPlans__3d_fullres/fold_0/checkpoint_best.pth"
```
