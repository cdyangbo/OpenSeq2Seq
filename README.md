# OpenSeq2Seq: multi-gpu sequence to sequence learning
This is a research project, not an official NVIDIA product.

# Getting started

## Requirements
* Python 3.6
* Tensorflow r1.2 or r1.3
* NLTK v3.2.3+

## Unit tests
Checkout the code and make sure the following test pass:
You should see **OK** after each test
```
./create_toy_data.sh
python -m unittest test/data_layer_tests.py
python -m unittest test/model_tests.py
```

## Example run on toy data
When you execute ```./create_toy_data.sh`` script it will create the following toy data.

Source: random sequence of numbers

Target: reversed source sequence

Hence, the task is to learn to reverse sequences. You can test single or multi-gpu training.

**One GPU:**
```
$ python run.py --config=example_configs/toy_data_config.json --mode=train --logdir=ModelAndLogFolder
```
OR
**Two GPUs:**
```
$ python run.py --config=example_configs/toy_data_config_2GPUs.json --mode=train --logdir=ModelAndLogFolder
```

## Example inference
Once you have trained model in ``ModelAndLogFolder``, run:
```
$ python run.py --config=example_configs/toy_data_config_2GPUs.json --mode=infer --logdir=ModelAndLogFolder --inference_out=pred.txt
```

## Bleu Score Calculation using Moses Script
```
$ ./multi-bleu.perl test/toy_data/test/target.txt < pred.txt
```
If you just used provided configs, your BLUE score should be > 98 for both single and 2 gpu runs.

# Training German 2 English translation

## First, get the data:
Download and execute [this script](https://github.com/google/seq2seq/blob/master/bin/data/wmt16_en_de.sh)

Edit the 'wmt_large.json' file and replace [WMT16_DATA_LOCATION] with correct data location.

## Run training
Edit "num_gpus" section of wmt_large.json - set it to the number of GPUs you want to use.
```
python run.py --config=example_configs/wmt_large.json --mode=train --logdir=WMT_DE_EN --eval_frequency=500 --checkpoint_frequency=1000
```

## Run Inference
```
python run.py --config=example_configs/wmt_large.json --mode=infer --logdir=WMT_DE_EN --inference_out=wmt_pred.txt
```

Clean ```wmt_pred.txt``` and ```newstest2015.tok.bpe.32000.en``` from BPE segmentation

## Cleaning BPE segmentation
```
$ cat {file_with_BPE_segmentation} | sed -r 's/(@@ )|(@@ ?$)//g' > {cleaned_file}
```

Run ```multi-blue.perl``` script on cleaned data. We are able to get BLEU 25.11 after ~23 hours of training on 4 GP100.

### Authors
[Oleksii Kuchaiev](https://github.com/okuchaiev) and [Siddharth Bhatnagar](https://github.com/siddharthbhatnagar)

Contributions are welcome!

## Related resources
* [Neural Machine Translation (seq2seq) Tutorial](https://github.com/tensorflow/nmt)
* [OpenNMT (Torch)](http://opennmt.net/)
* [OpenNMT (Pytorch)](https://github.com/OpenNMT/OpenNMT-py)
* [Tf-seq2seq](https://github.com/google/seq2seq)

