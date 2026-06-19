# CSSM530 Turkish Labor Action Detection, Linking, and Extraction Pipeline

This upload contains the final notebook, paper, environment record, validation file, and pipeline outputs for the CSSM530 project on automated Turkish labor action event construction from news articles.

## Files

The upload is split into four ZIP files because of the 25 MB upload limit.

### Part 1: `part1_core_and_all_articles.zip`

Contains the main project files:

- `CSSM530_Detection_Linking_FINAL_BEST_CONFIGS_LLM_REASON.ipynb`  
  Final executable notebook. It loads the three newspaper sources, runs detection, linking, cluster summarization, and LLM extraction.

- `Detecting_Linking_Extracting_Turkish_Labor_Actions_Final_Appendices.docx`  
  Final paper with revised appendices.

- `LLM_validation_accuracy_results_70_revised.xlsx`  
  Manual validation results for 70 extracted clusters.

- `requirements_exact.txt`  
  Environment snapshot exported from the final Colab runtime.

- `FINAL_all_articles_with_predictions_and_clusters.xlsx`  
  Full article-level output with detection predictions and final cluster IDs.

### Part 2: `part2_detection_predictions.zip`

Contains detection-stage and evaluation outputs:

- `all_files_with_detection_predictions.xlsx`  
  All articles with detector probabilities and predicted labels.

- `entity_extraction_dataset_STRICT.xlsx`  
  Dataset used for extracting entity candidates for linking.

- `detection_summary.xlsx`  
  Summary of final detection performance.

- `detection_cv_fold_metrics.xlsx`  
  Fold-level detection metrics.

- `detection_eval_by_source.xlsx`  
  Detection evaluation by newspaper source.

- `selected_linking_configuration.xlsx`  
  Final selected linking configuration.

- `selected_linking_evaluation.xlsx`  
  Evaluation of the selected linking configuration.

### Part 3: `part3_final_source_clusters.zip`

Contains final clustered outputs by source and cluster diagnostics:

- `FINAL_EVR_articles_with_predictions_and_clusters.xlsx`  
  Evrensel articles with predictions and final clusters.

- `FINAL_BIR_articles_with_predictions_and_clusters.xlsx`  
  BirGün articles with predictions and final clusters.

- `FINAL_SES_articles_with_predictions_and_clusters.xlsx`  
  Sendika.org articles with predictions and final clusters.

- `manual_event_id_labeling_template.xlsx`  
  Template for manual event-linking labels.

- `cluster_summary_selected_hybrid.xlsx`  
  Summary of generated candidate event clusters.

- `cluster_diagnostics.xlsx`  
  Diagnostics for cluster size, duration, and possible over-merging.

- `detection_false_negatives_examples.xlsx`  
  Examples of false negatives from the detection model.

- `detection_false_positives_examples.xlsx`  
  Examples of false positives from the detection model.

### Part 4: `part4_source_detection_and_labels.zip`

Contains source-specific detection outputs and labeled detection evaluation rows:

- `EVR_with_detection_predictions.xlsx`  
  Evrensel detection predictions.

- `BIR_with_detection_predictions.xlsx`  
  BirGün detection predictions.

- `SES_with_detection_predictions.xlsx`  
  Sendika.org detection predictions.

- `labeled_detection_oof_results.xlsx`  
  Out-of-fold detection predictions for labeled rows.

## Pipeline Overview

The project builds a three-stage pipeline:

1. **Detection**  
   A BERTurk classifier identifies articles that report labor actions. The final model uses `dbmdz/bert-base-turkish-cased`, title plus article text, a recall-oriented configuration, and an out-of-fold threshold selected to meet a minimum recall target of 0.90.

2. **Linking**  
   Articles predicted or manually labeled as positive are embedded and linked into candidate event clusters. The final linking rule uses `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2`, cosine similarity, date windows, entity overlap, and title-token overlap. Connected components are treated as candidate event clusters.

3. **LLM Extraction**  
   Multi-article clusters are passed to an LLM extraction stage using the OpenAI Responses API with `gpt-5.4-mini` and a Pydantic schema. The schema extracts outcome, employer, sector, workplace worker count, union presence, union authority status, organization name, employment type, action type, action reason, dates, location, worker participation, evidence document IDs, outcome evidence, and confidence.

## Important Methodological Notes

The final detector performs well as a high-recall article retrieval model. The linking stage remains the main bottleneck. The selected linking configuration was useful for generating candidate clusters but produced over-merging in the full final corpus, including very large connected components. Therefore, the event clusters should be treated as candidate groupings for review rather than a fully reliable event database.

The extraction stage was applied only to clusters with at least two articles. Singleton clusters were excluded for resource reasons. As a result, the extraction file is not a complete event-level database of all detected labor actions.

The manual validation file scores 70 extracted clusters at the field level. Accuracy values use 1 for correct, 0.5 for partially correct or close, and 0 for incorrect. The employer-name field was excluded from the aggregate accuracy calculation.

## Environment

The final recorded runtime used:

- Python 3.12.13
- Google Colab
- Linux 6.6.122+
- PyTorch 2.11.0+cu128
- CUDA 12.8
- cuDNN 9.19.0
- NVIDIA A100-SXM4 40 GB GPU

The exact package environment is recorded in `requirements_exact.txt`.

## Reproducibility Notes

The notebook records the data filenames, random seed, model names, detection hyperparameters, linking thresholds, candidate rules, extraction schema, and output files. For full reproduction, the original labeled input files must be placed in the expected Google Drive folder or the notebook file paths must be updated.

The LLM extraction call uses the model string `gpt-5.4-mini` and does not set an explicit temperature. Because hosted model behavior can change over time, exact reproduction of LLM outputs may require preserving the raw API responses and extraction date.

## Suggested Reading Order

1. Read the final paper:  
   `Detecting_Linking_Extracting_Turkish_Labor_Actions_Final_Appendices.docx`

2. Inspect the notebook:  
   `CSSM530_Detection_Linking_FINAL_BEST_CONFIGS_LLM_REASON.ipynb`

3. Review the main article-level output:  
   `FINAL_all_articles_with_predictions_and_clusters.xlsx`

4. Review validation:  
   `LLM_validation_accuracy_results_70_revised.xlsx`

5. Inspect diagnostics:  
   `cluster_diagnostics.xlsx` and `selected_linking_evaluation.xlsx`
