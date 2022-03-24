# Personality-Predection
This repository contains code for the paper Bottom-Up and Top-Down: Predicting Personality with Psycholinguistic and Language Model Features, published in IEEE International Conference of Data Mining 2020.

Here are a set of experiments written in tensorflow + pytorch to explore automated personality detection using Language Models on the Essays dataset (Big-Five personality labelled traits) and the Kaggle MBTI dataset.

Setup
Pull this repository from GitLab via:

git clone git@gitlab.com:ml-automated-personality-detection/personality.git
Creating a new conda environment is recomended. Install PyTorch GPU/CPU for your setup.

conda create -n mvenv python=3.8
conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch -c nvidia
See the requirements.txt for the list of dependent packages which can be installed via:

pip -r requirements.txt
Usage
First run the LM extractor code which passes the dataset through the language model and stores the embeddings (of all layers) in a pickle file. Creating this 'new dataset' saves us a lot of compute time and allows effective searching of the hyperparameters for the finetuning network. Before running the code, create a pkl_data folder in the repo folder. All the arguments are optional and passing no arguments runs the extractor with the default values.

python LM_extractor.py -dataset_type 'essays' -token_length 512 -batch_size 32 -embed 'bert-base' -op_dir 'pkl_data'
Next run a finetuning model to take the extracted features as input from the pickle file and train a finetuning model. We find a shallow MLP to be the best performing one

python finetune_models/MLP_LM.py
Results Table	Language Models vs Psycholinguistic Traits
	
Predicting personality on unseen text
Follow the steps below for predicting personality (e.g. the Big-Five: OCEAN traits) on a new text/essay:

You will have to train your model -- for that, first choose your training dataset (e.g. essays).
Extract features for each of the essays by passing it through a language model of your choice (e.g. BERT) by running the LM_extractor.py file. This will create a pickle file containing the training features.
Next, train the finetuning model. Let's say it is a simple MLP (this was the best performing one, as can be seen from Table 2 of the paper). Use the extracted features from the LM to train this model. Here, you can experiment with 1) different models (e.g. SVMs, Attention+RNNs, etc.) and 2) concatenating the corresponding psycholinguistic features for each of the essays.
You will have to write code to save the optimal model parameters after the training is complete.
For the new data, first pass it through the SAME language model feature extraction pipeline and save this. Load your pre-trained model into memory and run it on these extracted features.
Note: The text pre-processing (e.g. tokenization, etc.) before passing it through the language model should be the SAME for training and testing.

Running Time
LM_extractor.py
On a RTX2080 GPU, the -embed 'bert-base' extractor takes about ~2m 30s and 'bert-large' takes about ~5m 30s

On a CPU, 'bert-base' extractor takes about ~25m

python finetune_models/MLP_LM.py
On a RTX2080 GPU, running for 15 epochs (with no cross-validation) takes from 5s-60s, depending on the MLP architecture.

Literature
Deep Learning based Personality Prediction [Literature REVIEW] (Springer AIR Journal - 2020)
@article{mehta2020recent,
  title={Recent Trends in Deep Learning Based Personality Detection},
  author={Mehta, Yash and Majumder, Navonil and Gelbukh, Alexander and Cambria, Erik},
  journal={Artificial Intelligence Review},
  pages={2313–2339},
  year={2020},
  doi = {https://doi.org/10.1007/s10462-019-09770-z},
  url = {https://link.springer.com/article/10.1007/s10462-019-09770-z}
  publisher={Springer}
}
Language Model Based Personality Prediction (ICDM - 2020)
If you find this repo useful for your research, please cite it using the following:

@inproceedings{mehta2020bottom,
  title={Bottom-up and top-down: Predicting personality with psycholinguistic and language model features},
  author={Mehta, Yash and Fatehi, Samin and Kazameini, Amirmohammad and Stachl, Clemens and Cambria, Erik and Eetemadi, Sauleh},
  booktitle={2020 IEEE International Conference on Data Mining (ICDM)},
  pages={1184--1189},
  year={2020},
  organization={IEEE}
}
License
The source code for this project is licensed under the MIT license.
