# General-Activities

This repository hosts general files and resources related to microbial ecology workflows. It is intended for sharing reusable data objects such as classifiers, metadata files, and workflow components for QIIME 2 and related tools.

## 📌 Available Files

### `sh1_classifier.qza`
- **Type**: QIIME 2 Artifact (`.qza`)
- **Description**: A pre-trained ITS classifier for fungal community analysis.
- **Format**: QIIME 2 `FeatureData[Classifier]`
- **Notes**: Trained on the [UNITE] ITS region.
- **Usage**:
  ```bash
````
  qiime feature-classifier classify-sklearn \
    --i-classifier sh1_classifier.qza \
    --i-reads your_rep_seqs.qza \
    --o-classification taxonomy.qza
````

⚠️ Git LFS Required
This repository uses Git Large File Storage (LFS) for tracking large files.
To properly download and use the files, make sure you have Git LFS installed:

````git lfs install
git clone https://github.com/Micro-informatics/General-Activities.git
````


