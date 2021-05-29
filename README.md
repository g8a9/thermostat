# Thermostat

> Explainability in NLP is becoming more important by the day and is getting explored on many different levels. In order to perform linguistic and statistical analysis of the output of explainability methods, expert knowledge is needed. It further induces both an implementational on the developer and a computational burden on the hardware. We propose **Thermostat**, a collection of heatmaps that can be downloaded by researchers and practitioners. This work is a dataset paper that combines popular text classification datasets and state-of-the-art language models with explainability methods commonly applied to NLP tasks. By offering pre-computed saliency maps of these datasets stemming from a variety of explainers, we help to prevent repetitive execution yielding similar results and therefore mitigate the environmental and financial cost.

**Thermostat** combines explainability methods from the `captum` library with Hugging Face's `datasets` and `transformers`.


## Usage

Thermostat datasets always consist of three basic coordinates: `<dataset>-<model>-<explainer>`.

At the moment, Thermostat is only available via this repository (it will be published to Hugging Face soon).

Clone this repo and then from the "/hf_dataset" directory (where the "thermostat" folder exists) you can call the following script:

```python
from datasets import load_dataset

# This will use the dataset script ("thermostat.py") in the "thermostat" directory
data = load_dataset("thermostat", "imdb-bert-lgxa", split="test")
# data contains IMDb-BERT-LayerGradientXActivation attributions
```

`data` then provides a subset with features/columns
* `attributions` (the attributions for each token for each data point; type: List of floats) : `[-0.18760254979133606, -0.0315956249833107, 0.04854373633861542, 0.00658783596009016, 0.017869707196950912,` ...
* `input_ids` (the token IDs of the original dataset; type: List of ints) :  `[101, 2092, 1010, 1045, 7166, 2000,` ...
* `label` (the label of the original dataset; type: int) : `1`
* `predictions` (the class logits of the classifier/downstream model; type: List of floats) : `[-3.4371631145477295, 4.042327404022217]`
* `dataset.description` provides the actual names of the dataset, the explainer and the model

At the moment, the only valid split for Thermostat datasets is "test".


## Explainers
Name | captum | Tested
--- | --- | ---
Layer Gradient x Activation (`lgxa`) | [`.attr.LayerGradientXActivation`](https://captum.ai/api/layer.html#layer-gradient-x-activation) | ✅
Layer Integrated Gradients (`lig`) | [`.attr.LayerIntegratedGradients`](https://captum.ai/api/layer.html#layer-integrated-gradients) | ✅
Occlusion (`occ`) | [`.attr.Occlusion`](https://captum.ai/api/occlusion.html) | ✅
Shapley Value Sampling (`svs`) | [`.attr.ShapleyValueSampling`](https://captum.ai/api/shapley_value_sampling.html) | ✅
LIME (`lime`) | [`.attr.LimeBase`](https://captum.ai/api/lime.html) | ✅

## Datasets + Models

✅ = Dataset is downloadable  
(✅) = Dataset is finished, but not uploaded yet  
⚠️ = Issue  
🔄 = Running on cluster (x n = number of jobs/screens)  
📜 = Config exists  
px = Priority (p1 is next)  
`-` = will probably not be provided

### IMDb

[`imdb`](https://huggingface.co/datasets/viewer/?dataset=imdb) is a sentiment analysis dataset with 2 classes (`pos` and `neg`). The `test` subset contains 25k examples.

Name | 🤗 | `lgxa` | `lig` | `lime` | `occ` | `svs`  
--- | --- | --- | --- | --- | --- | ---
ALBERT (`albert`) | [`textattack/albert-base-v2-imdb`](https://huggingface.co/textattack/albert-base-v2-imdb) | ✅ | | (✅) | | 🔄x2/5
BERT (`bert`) | [`textattack/bert-base-uncased-imdb`](https://huggingface.co/textattack/bert-base-uncased-imdb) | ✅ | ✅ | | 🔄
DistilBERT (`distilbert`) | [`textattack/distilbert-base-uncased-imdb`](https://huggingface.co/textattack/distilbert-base-uncased-imdb) | - | - | - | - | -
ELECTRA (`electra`) | [`monologg/electra-small-finetuned-imdb`](https://huggingface.co/monologg/electra-small-finetuned-imdb) | p7 | | | (✅)
RoBERTa (`roberta`) | [`textattack/roberta-base-imdb`](https://huggingface.co/textattack/roberta-base-imdb) | | p9 | | 🔄 | 🔄x5
XLNet (`xlnet`) | [`textattack/xlnet-base-cased-imdb`](https://huggingface.co/textattack/xlnet-base-cased-imdb) | | p6 | ✅


### MNLI

[`multi_nli`](https://huggingface.co/datasets/viewer/?dataset=multi_nli) is a textual entailment dataset. The `validation_matched` subset contains 9815 examples.

Name | 🤗 | `lgxa` | `lig` | `lime` | `occ` | `svs`
--- | --- | --- | --- | --- | --- | ---
ALBERT (`albert`) | [`prajjwal1/albert-base-v2-mnli`](https://huggingface.co/prajjwal1/albert-base-v2-mnli) | | p8 | | (✅)
BERT (`bert`) | [`textattack/bert-base-uncased-MNLI`](https://huggingface.co/textattack/bert-base-uncased-MNLI) | | | p5 ⚠️ | | 🔄
DistilBERT (`distilbert`) | [`textattack/distilbert-base-uncased-MNLI`](https://huggingface.co/textattack/distilbert-base-uncased-MNLI) | - | - | - | - | -
ELECTRA (`electra`) | [`howey/electra-base-mnli`](https://huggingface.co/howey/electra-base-mnli) | | (✅) | | | 🔄
RoBERTa (`roberta`) | [`textattack/roberta-base-MNLI`](https://huggingface.co/textattack/roberta-base-MNLI) | (✅) | (✅) | p1 ⚠️
XLNet (`xlnet`) | [`textattack/xlnet-base-cased-MNLI`](https://huggingface.co/textattack/xlnet-base-cased-MNLI) | (✅) | | p4 ⚠️ | 🔄


### Visualizing attributions as a heatmap
```python
from datasets import load_dataset
from thermostat import to_html

lgxa = load_dataset("thermostat", "imdb-bert-lgxa", split="test[:1%]")
to_html(lgxa, "imdb-bert-lgxa_heatmaps.html")
```
Runs the visualization script with default settings (`gamma=2.0`, `normalize=True`) on a Thermostat dataset to output an HTML file, returns nothing.  
Note: Always consider file size! Creating an HTML with more than ~100 instances might be difficult to handle for your browser when viewing.  
Recommendation: Using the `split` parameter of the `load_dataset` function.


### Helper functions
Importable via `from thermostat import x`:
* `get_coordinate(thermostat_dataset: Dataset, coordinate: str) -> str`  
  takes a Thermostat dataset and a coordinate string (`'Model'`, `'Dataset'` or `'Explainer'`, pay attention to capitalization!) and returns the requested value of the coordinate as string, e.g. `LayerIntegratedGradients` for `Explainer`
* `to_heatmap(thermostat_dataset: Dataset) -> List`  
  returns a list of tuples in the form of <token,color> for each data point of a Thermostat dataset
* `avg_attribution_stat(thermostat_dataset: Dataset) -> List`  
  calculates the average attribution for each token across a whole Thermostat dataset
* `explainer_agreement_stat(thermostat_datasets: List) -> List`  
  calculates the agreement on token attribution scores between multiple Thermostat datasets/explainers.

### Config files
jsonnet config files have the following naming convention:
`<DATASET_ID>/<EXPLAINER_ID>/<MODEL_ID>.jsonnet` where
* `<DATASET_ID>` corresponds to a dataset (from `datasets` package by default, but can be any other locally stored dataset),
* `<EXPLAINER_ID>` corresponds to an explainability method (usually provided through the `captum` package) and
* `<MODEL_ID>` corresponds to a model (from `transformers` package by default)
