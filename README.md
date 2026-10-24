from pathlib import Path

readme = r"""# Cred Chain Sentinel: AI-Based Certificate Validation

An AI-assisted certificate validation prototype that extracts text from an uploaded certificate image using OCR and predicts whether the certificate is valid or invalid using a trained machine-learning model.

## Features

- Loads a certificate dataset from a CSV file.
- Preprocesses categorical fields and issue dates.
- Trains a `RandomForestClassifier` to predict the `Is_Valid` label.
- Evaluates predictions with a classification report.
- Saves the trained model, scaler, and label encoders.
- Provides a Gradio web interface to upload a certificate image.
- Uses Tesseract OCR to extract text and heuristically infer certificate fields.
- Displays the extracted information and the model's prediction.

## Project Workflow

1. Read the dataset.
2. Drop `Certificate_ID` and `Hash_Value` from the training data.
3. Encode `Issuer_Name`, `Recipient_Name`, `Certificate_Type`, and `Blockchain_Status`.
4. Convert `Date_of_Issue` to a timestamp and fill remaining missing values with `0`.
5. Use the remaining features to predict `Is_Valid`.
6. Split the data into training and test sets (80% / 20%).
7. Scale the features with `StandardScaler`.
8. Train and evaluate the Random Forest model.
9. Save the model artifacts.
10. Launch the Gradio interface, which applies OCR to an uploaded certificate image and returns a prediction.

## Tech Stack

- Python
- pandas and NumPy
- scikit-learn
- joblib
- Gradio
- Tesseract OCR / `pytesseract`
- OpenCV
- Pillow

## Dataset

The script expects the dataset file at:

```text
/content/cred_chain_sentinel_dataset.csv
```

Expected columns used by the script include:

- `Certificate_ID`
- `Issuer_Name`
- `Recipient_Name`
- `Certificate_Type`
- `Date_of_Issue`
- `Validity_Period`
- `Metadata_Text`
- `Is_Valid`
- `Anomaly_Score`
- `Hash_Value`
- `Blockchain_Status`

The target column is `Is_Valid`. The script excludes `Metadata_Text` from model features and drops `Certificate_ID` and `Hash_Value`.

**Dataset note:** This repository/script does not create or download the CSV. Place the dataset at the path above or update the path in the code.

## Run in Google Colab

1. Upload `ml_certificate_project_.py` to Google Colab, or copy its cells into a notebook.
2. Upload `cred_chain_sentinel_dataset.csv` to the Colab runtime, or mount Google Drive and update the dataset path.
3. Run the installation cells:
   ```python
   !pip install gradio joblib pytesseract opencv-python-headless
   !apt-get install tesseract-ocr -y
   ```
4. Run the data-loading, preprocessing, training, and evaluation cells.
5. Confirm that these files are created in the current working directory:
   - `certificate_validation_model.pkl`
   - `scaler.pkl`
   - `label_encoders.pkl`
6. Run the Gradio app section. Upload a certificate image to see the extracted text-derived fields and prediction.
7. When using Colab, keep the runtime running while testing the app. The script uses `launch(share=True)` to create a temporary public Gradio link; avoid uploading real certificates or personal information to a public demo.

## Output Artifacts

| File | Purpose |
|---|---|
| `certificate_validation_model.pkl` | Trained Random Forest model |
| `scaler.pkl` | Feature scaler fitted on training data |
| `label_encoders.pkl` | Encoders fitted to categorical training columns |

## Important Limitations

- **Prototype, not authoritative verification:** The result is a model prediction, not proof that a certificate is genuine. Verify certificates with the issuing institution or an authoritative verification service.
- **OCR field extraction is heuristic:** The current code guesses fields from text lines and uses defaults when it cannot parse a value. It does not reliably extract all certificate layouts.
- **Blockchain is not implemented:** The script reads a `Blockchain_Status` field from the dataset and infers a status from OCR text. It does not connect to a blockchain, verify an on-chain hash, or write transactions.
- **Some features are simulated:** `Validity_Period` is fixed to `2` and `Anomaly_Score` is fixed to `0.1` in the upload pipeline; these are not calculated from the certificate.
- **Date parsing uses a fallback:** If the issue date is not parsed as `%Y-%m-%d`, the code uses `2022-01-01`.
- **Unseen categories:** Unknown issuer, recipient, type, or blockchain values are encoded as `0`, which may not represent their actual category.
- **Feature consistency should be checked:** The training and upload-time feature construction must use exactly the same feature order and meaning. Review this before relying on predictions.
- **OCR color conversion:** The code converts a PIL image using `cv2.COLOR_BGR2GRAY`; depending on image channel order, `cv2.COLOR_RGB2GRAY` or an explicit conversion may be more appropriate.
- **Model validation:** A classification report is printed, but the script does not show a saved test set, cross-validation, or calibration analysis.

## Future Improvements

- Parse certificate fields with robust date parsing, regular expressions, or NLP.
- Add a proper unknown-category strategy instead of mapping unseen labels to `0`.
- Make the training and inference feature-building code share one preprocessing pipeline.
- Add tests for missing fields, rotated images, low-quality scans, and different certificate templates.
- Evaluate precision, recall, F1-score, confusion matrix, and cross-validation.
- Integrate a real issuer verification API or a blockchain smart contract/hash lookup if required by the project scope.
- Add clear confidence scores and a manual-review outcome for uncertain cases.

## Disclaimer

This project is intended for learning and demonstration. Do not use its prediction as the sole basis for accepting or rejecting a certificate.
"""
path = Path("/mnt/data/README.md")
path.write_text(readme, encoding="utf-8")
print(f"Created: {path}")
