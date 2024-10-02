# VidDiff eval
This page explains running eval for the Viddiff benchmark, hosted on Huggingface [here](https://huggingface.co/datasets/viddiff/VidDiffBench). It was proposed in "Video Action Differencing" preprint. 

## Get the dataset
Follow the instructions on [VidDiffBench dataset](https://huggingface.co/datasets/viddiff/VidDiffBench).

## Running eval
In `eval_diff.py`, after loading the dataset, run the prediction below:

```
metrics = eval_viddiff.eval_viddiff(
		dataset,
		predictions_unmatched=predictions,
		eval_mode=2, # 0  or 2
		seed=0,
		n_differences="data/n_differences.json",
		results_dir="results")
```
The `dataset` is a huggingdace dataset from VidDiffBench, and the `predictions` are a list such that `predictions[i]` is for video pair `dataset[i]`. The closed setting is mode 2, and the open setting is mode 0. The structure for `predictions` must be:
```
[
	// list element i is a dict of difference predictions for sample i
	{
		"numericKey1": {
			// prediction details for one difference
			"description": "..." // A description of the predicted difference",
			"pred": "a|b" // Whather the description is more true of video a or b
		},
		"numericKey2": {
			// Another difference prediction for the same smaple
			// ... same structure as above ...
		}
		// There can be multiple difference predictions per sample
	},
	{
		// Another set of observations
		// ... same structure as above ...
	}
	... 
]
```

For example, here are predictions for 1 sample:
```
[	{	
    "6": {
        "prediction": "b",
        "description": "The jump is higher, allowing for a greater vertical lift during the shot."
    },
    "7": {
        "prediction": "b",
        "description": "The follow-through of the shot is more pronounced, with the shooting hand extended longer towards the basket."
    },
    "0": {
        "prediction": "a",
        "description": "The knees are bent more before the jump, providing better leverage for the shot."
    },
    "1": {
        "prediction": "a",
        "description": "The foot placement is wider, providing a more stable base for the jump shot."
    },
    "4": {
        "prediction": "a",
        "description": "The ball is held higher above the head before the jump, leading to a cleaner shot release."
    },
    ... 
]
```

In mode 2, the differences are given as input, so the prediction keys must match the ground truth keys. In mode 0, the differences are not given, so the 

## Video-LMM baselines 
The eval file makes some api cals to openaiAPI. For running GPT, set OPENAI_API_KEY variable and for Gemini set `GOOGLE_API_KEY`. Then run LMM training with a config, for example:

```
python lmms/run_lmm.py -c lmms/configs/mode2-gpt-4o_ballsports_4fps.yaml
```


## VidDiff method 
The Viddiff method is in `viddiff_method`. To run it:
```
python viddiff_method/run_viddiff.py -c viddiff_method/configs/eval2_fitness.yaml
```



