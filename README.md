[中文说明](https://github.com/AarynBaelish/nlp_classifier_zh/blob/master/README.md) | [English](https://github.com/AarynBaelish/nlp_classifier_zh/blob/master/README_EN.md)

# nlp_classifier_zh
本项目包含了面向中文的两种分类模型，包括了multi-class和multi-label两种分类问题，采用的预训练模型为目前SOTA的[RoBERTa-zh-Large](https://github.com/brightmart/roberta_zh)模型进行finetuning。

# multi_label_classifier  
### 1. 数据准备
data文件夹中存放train.csv, dev.csv, test.csv    
文件格式：(label部分类似one-hot格式)  

| index | text | a | b | c | d | e | f |  
|-------|------|---|---|---|---|---|---|  
|  0    |   A  | 1 | 1 | 1 | 0 | 0 | 0 | 
|  1    |   B  | 1 | 0 | 1 | 0 | 0 | 0 | 
|  2    |   C  | 1 | 1 | 0 | 0 | 0 | 1 | 


### 2.代码
在bert代码的基础之上，仅需修改run_classifier.py文件，  
其中添加MultiLabelClassifierProcessor类，将其中tf.nn.softmax()改为sigmoid  
更多细节详见代码

### 3. 模型训练
运行如下代码，BERT_BASE_DIR为加载预训练模型，DATA_DIR为数据存放文件夹，TRAINED_CLASSIFIER为加载模型
```python
export BERT_BASE_DIR=./pretrain_models/roeberta_zh_L-24_H-1024_A-16
export DATA_DIR=./multi_label_classifier/data
export TRAINED_CLASSIFIER=./pretrain_models/roeberta_zh_L-24_H-1024_A-16

python run_classifier.py \
  --task_name=mlc \
  --do_train=true \
  --do_eval=true \
  --data_dir=$DATA_DIR \
  --vocab_file=$BERT_BASE_DIR/vocab.txt \
  --bert_config_file=$BERT_BASE_DIR/bert_config.json \
  --init_checkpoint=$TRAINED_CLASSIFIER \
  --max_seq_length=512 \
  --output_dir=./multi_label_classifier/mlc_output/
```

### 4. 模型预测
运行如下代码，BERT_BASE_DIR为加载预训练模型，DATA_DIR为数据存放文件夹，TRAINED_CLASSIFIER为已fine-tuning后的模型

```python
export BERT_BASE_DIR=./pretrain_models/roeberta_zh_L-24_H-1024_A-16
export DATA_DIR=./multi_label_classifier/data
export TRAINED_CLASSIFIER=./pretrain_models/multi_label_classifier/mlc_output/model.ckpt-10000

python run_classifier.py \
  --task_name=mlc \
  --do_train=true \
  --do_eval=true \
  --do_predict=true \
  --data_dir=$DATA_DIR \
  --vocab_file=$BERT_BASE_DIR/vocab.txt \
  --bert_config_file=$BERT_BASE_DIR/bert_config.json \
  --init_checkpoint=$TRAINED_CLASSIFIER \
  --max_seq_length=512 \
  --output_dir=./multi_label_classifier/mlc_output/
```

## Reference：  
1. bert https://github.com/google-research/bert
2. roberta https://github.com/brightmart/roberta_zh
